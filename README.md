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
<p align="center">Obtem login/autenticação do oauth2 da API e salvando state para validar na session redirecionando no fim para uma rota de callback</p>
