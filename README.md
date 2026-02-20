# Mobile Gateway API

Backend da aplicação mobile, construído em Laravel, com autenticação via Laravel Sanctum e login por `callsign`.

## Objetivo

Este projeto fornece uma API para o app mobile com:

- autenticação por token (Bearer);
- login sem e-mail (usa `callsign`);
- integração com tabela de usuários existente (`gvausers`).

## Stack

- PHP 8.2+
- Laravel 12
- Laravel Sanctum
- MySQL

## Requisitos

- PHP com extensões necessárias para Laravel
- Composer
- MySQL

## Configuração local

1. Clonar o projeto e instalar dependências:

```bash
composer install
```

2. Criar/ajustar `.env`:

```bash
cp .env.example .env
php artisan key:generate
```

3. Configurar banco no `.env`:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=seu_banco
DB_USERNAME=seu_usuario
DB_PASSWORD=sua_senha
```

4. Rodar migrations de suporte:

```bash
php artisan migrate --force
```

> Observação: a tabela `gvausers` já existe fora das migrations deste projeto e não é criada automaticamente.

5. Subir a API:

```bash
php artisan serve
```

## Autenticação

- Provider de usuário: model `App\Models\User`
- Tabela de usuário: `gvausers`
- Campo de login: `callsign`
- Sessão/token: Laravel Sanctum (`personal_access_tokens`)

### Senha legada (MD5)

O projeto possui compatibilidade com senha armazenada em MD5 na `gvausers`.

## Rotas da API

Base URL local: `http://127.0.0.1:8000`

### Login (gera token)

`POST /api/login`

Body:

```json
{
	"callsign": "GLD548",
	"password": "sua_senha",
	"device_name": "android"
}
```

Resposta de sucesso:

```json
{
	"token_type": "Bearer",
	"access_token": "<token>",
	"user": {
		"id": 1,
		"name": "...",
		"callsign": "GLD548"
	}
}
```

### Usuário autenticado

`GET /api/me`

Header:

```http
Authorization: Bearer <token>
```

### Logout (revoga token atual)

`POST /api/logout`

Header:

```http
Authorization: Bearer <token>
```

## Exemplo rápido (PowerShell)

```powershell
$body = @{ callsign = 'GLD548'; password = 'sua_senha'; device_name = 'mobile' } | ConvertTo-Json
$login = Invoke-RestMethod -Method Post -Uri 'http://127.0.0.1:8000/api/login' -ContentType 'application/json' -Body $body
$token = $login.access_token
Invoke-RestMethod -Method Get -Uri 'http://127.0.0.1:8000/api/me' -Headers @{ Authorization = "Bearer $token" }
```

## Testes

```bash
php artisan test
```
