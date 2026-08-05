# API do Yearbook — Documentação de Endpoints

Base URL (produção): `https://yearbook-backend.vercel.app`

## Convenções

- Todas as respostas são em JSON
- Rotas protegidas exigem header `Authorization: Bearer <token>`
- O campo `senhaHash` nunca é retornado em nenhuma resposta
- Erros seguem o formato `{ "erro": "mensagem descritiva" }`

## CORS

Esta API tem CORS habilitado para qualquer origem. Você pode consumi-la
de qualquer domínio (localhost, Vercel, etc.) sem configuração adicional
no cliente.

## Auth

### POST /auth/register

Cria uma nova conta de aluno.

- **Autenticação:** Não
- **Body:**

```json
{
"nome": "Maria Silva",
"email": "maria@email.com",
"senha": "minhasenha123",
"cidade": "Salinas",
"frase": "Aqui começa o futuro.",
"planosFuturos": "Cursar Ciência da Computação na UFMG"
}
```

- **Resposta de sucesso:** `201 Created`

```json
{
"id": 1,
"nome": "Maria Silva",
"email": "maria@email.com",
"cidade": "Salinas",
"frase": "Aqui começa o futuro.",
"planosFuturos": "Cursar Ciência da Computação na UFMG",
"fotoUrl": null,
"role": "USER",
"criadoEm": "2026-04-03T10:30:00.000Z"
}
```

- **Erros:**
- `400` — Campos obrigatórios ausentes
- `409` — Email já cadastrado

### POST /auth/login

Autentica um aluno e retorna um token JWT.

- **Autenticação:** Não
- **Body:**

```json
{
"email": "maria@email.com",
"senha": "minhasenha123"
}
```

- **Resposta de sucesso:** `200 OK`

```json
{
"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

- **Erros:**
- `401` — Credenciais inválidas (email não existe ou senha incorreta)

## Alunos

### GET /alunos

Lista todos os alunos cadastrados.

- **Autenticação:** Não
- **Body:** Nenhum

- **Resposta de sucesso:** `200 OK`

```json
[
    {
    "id": 1,
    "nome": "Maria Silva",
    "email": "maria@email.com",
    "cidade": "Salinas",
    "frase": "Aqui começa o futuro.",
    "planosFuturos": "Cursar Ciência da Computação na UFMG",
    "fotoUrl": null,
    "role": "USER",
    "criadoEm": "2026-04-03T10:30:00.000Z"
    }
]
```

### GET /alunos/:id

Busca um aluno pelo ID.

- **Autenticação:** Não
- **Body:** Nenhum

- **Resposta de sucesso:** `200 OK`

```json
{
"id": 1,
"nome": "Maria Silva",
"email": "maria@email.com",
"cidade": "Salinas",
"frase": "Aqui começa o futuro.",
"planosFuturos": "Cursar Ciência da Computação na UFMG",
"fotoUrl": null,
"role": "USER",
"criadoEm": "2026-04-03T10:30:00.000Z"
}
```

- **Erros:**
- `404` – Aluno não encontrado

### PUT /alunos/:id

Atualiza o próprio perfil.

- **Autenticação:** Bearer token
- **Body:** 

```json
{
"nome": "Maria Silva Santos",
"cidade": "Belo Horizonte",
"frase": "Nova frase de efeito",
"planosFuturos": "Trabalhar com IA",
"fotoUrl": "https://imagem.com/foto.jpg"
}
```

- **Resposta de sucesso:** `200 OK`

```json
{
"id": 1,
"nome": "Maria Silva Santos",
"email": "maria@email.com",
"cidade": "Belo Horizonte",
"frase": "Nova frase de efeito",
"planosFuturos": "Trabalhar com IA",
"fotoUrl": "https://imagem.com/foto.jpg",
"role": "USER",
"criadoEm": "2026-04-03T10:30:00.000Z"
}
```

- **Erros:**
- `401` – Não autenticado
- `403` – Tentativa de atualizar perfil de outra pessoa

### DELETE /alunos/:id

Remove um aluno.

- **Autenticação:** Bearer token (admin)
- **Body:** Nenhum

- **Resposta de sucesso:** `204 No Content`

- **Erros:**
- `401` – Não autenticado
- `403` – Usuário não é administrador

## Mensagens

### GET /mensagens

Lista todas as mensagens do mural.

- **Autenticação:** Não
- **Body:** Nenhum

- **Resposta de sucesso:** `200 OK`

```json
[
    {
    "id": 10,
    "texto": "Parabéns a todos pela formatura!",
    "imageUrl": "https://exemplo.com/foto-formatura.jpg",
    "autorId": 1,
    "autor": 
        {
        "id": 1,
        "nome": "Maria Silva",
        "fotoUrl": null
        }
    }
]
```

### POST /mensagens

Cria uma nova mensagem no mural.

- **Autenticação:** Bearer token
- **Body:** 

```json
{
"texto": "Minha mensagem de despedida",
"imageUrl": "https://link-da-imagem.com/foto.jpg"
}
```

- **Resposta de sucesso:** `201 Created`

```json
{
"id": 11,
"texto": "Minha mensagem de despedida",
"imageUrl": "https://link-da-imagem.com/foto.jpg",
"autorId": 1,
"criadoEm": "2026-05-11T21:00:00.000Z"
}
```

- **Erros:**
- `400` – Texto ausente
- `401` – Não autenticado

### DELETE /mensagens/:id

Exclui uma mensagem.

- **Autenticação:** Bearer token
- **Body:** Nenhum

- **Resposta de sucesso:** `204 No Content`

- **Erros:**
- `401` – Não autenticado
- `403` – Não é o dono da mensagem nem administrador