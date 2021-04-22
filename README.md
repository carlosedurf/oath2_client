# Implementação de oauth 2.0 do lado do client

Feito como forma de consulta para exemplificar como pode ser implementado do lado do client api laravel com passport oauth 2.0

<hr>

### Arquivos utilizados 
* <strong>/views/welcome.blade.php:</strong> Arquivo com botão de login que redireciona para fazer o login utilizando oauth2.0

```
<a href="127.0.0.1:8001/prepare-to-login" target="__blank">Login</a>
```

* <strong>/routes/web.php:</strong> 
<pre>
    Route::get('/', function ()
    {
        return view('welcome');
    });
</pre>
<p align="center">Rota para obter acesso a uma página do arquivo só para liberar acesso ao welcome</p>

<pre>
    Route::get('prepare-to-login', function () {

        $state = Str::random(40);

        session([
            'state' => $state,
        ]);

        $query = http_build_query([
            'client_id'     => env('CLIENT_ID'),
            'redirect_url'  =>  env('REDIRECT_URL'),
            'response_type' =>  'code',
            'scope'         =>  '',
            'state'         =>  $state,
        ]);

        return redirect(env('API_URL') . 'oauth/authorize?' . $query);

    })->name('prepare.login');
</pre>
<p align="center">Obtem login/autenticação do oauth2 da API e salvando state para validar na session redirecionando no fim para uma rota de callback</p>

<pre>
    Route::get('callback', function (Request $request){

        $response = Http::post(env('API_URL') . 'oauth/token', [
            'grant_type'    =>  'authorization_code',
            'client_id'     =>  env('CLIENT_ID'),
            'client_secret' =>  env('CLIENT_SECRET'),
            'redirect_url'  =>  env('REDIRECT_URL'),
            'code'          =>  $request->code,
        ]);

        dd($response->json());

    });
</pre>
<p align="center">Obtem Informação de token e state</p>

<pre>
    Route::get('grant-password', function (){

        $response = Http::post(env('API_URL') . 'oauth/token', [
            'grant_type'    =>  'password',
            'client_id'     =>  4,
            'client_secret' =>  '5XYIIka04idSAO3kQV2egBowO0ba1ST2QsOvKhZb',
            'username'      =>  'suporte@mail.com',
            'password'      =>  'password',
            'scope'         =>  '',
        ]);

        dd($response->json());

    });
</pre>
<p align="center">Outro tipo de Grant onde envia-se o email e confirguração do client e obtém as informações de acesso como token. OBS: username e password meramente lustrativo esse deve ser o user/password do seu backend e client_id e client_secret tbm, isso é quando você gerar o cliente no back end com modo de grant</p>

<pre>
    Route::get('grant-client', function (){

        $response = Http::post(env('API_URL') . 'oauth/token', [
            'grant_type'    =>  'client_credentials',
            'client_id'     =>  5,
            'client_secret' =>  'l5ncaQODTIGjPOVjTtoXX98WkRrtZVtbRcGcAek2',
            'scope'         =>  '',
        ]);

        dd($response->json());

    });
</pre>
<p align="center">Outro tipo de Grant utilizado bastante entre empresas para compartilhar algumas funcionalidades e informações, onde só é necessário o client_id e client_secret, OBS: Esse client_secret pode ser criptografado para melhor segurança na transição de informações, mas deve ser feito no backend.</p>
* <strong>.env</strong>
<pre>
    CLIENT_ID=3
    CLIENT_SECRET=0sQk9wuFUjZTg4zORKAudBC0nfmxjwUo5nf1MQVA
    REDIRECT_URL=http://127.0.0.1:8001/callback
    API_URL=http://127.0.0.1:8000/
</pre>
<p align="center">Informações do client que ira consumir API, pois o client com dados de autenticação precisa dessas configurações. Este é apenas um exmeplo</p>
