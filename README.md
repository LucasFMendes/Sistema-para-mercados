<<<<<<< HEAD
# Sistema-para-mercados
=======
# Ponto De Vendas - PDV 

## Descrição do Projeto
Este projeto consiste no desenvolvimento de uma API RESTful para um sistema de Ponto de Venda (PDV). O sistema foi projetado para gerenciar de forma eficiente o cadastro de usuários, produtos, categorias, clientes, e pedidos, fornecendo funcionalidades essenciais para operações comerciais.

A API permite a realização de operações CRUD (Create, Read, Update, Delete) em diversas entidades do sistema, como produtos, clientes e pedidos, garantindo a integridade dos dados e a segurança das operações por meio de autenticação de usuários. Além disso, o sistema suporta a funcionalidade de upload de imagens de produtos, permitindo uma melhor gestão do catálogo.

O projeto foi desenvolvido utilizando Node.js e PostgreSQL, seguindo as melhores práticas de desenvolvimento, como a separação de responsabilidades e o tratamento adequado de erros. Ele foi projetado para ser escalável e fácil de manter, sendo uma base sólida para futuras expansões, como a integração com sistemas de pagamento e a geração de relatórios detalhados.

---
## Endpoint: Listar Categorias

### Descrição
Este endpoint retorna uma lista de todas as categorias cadastradas no banco de dados. Apenas as descrições das categorias são retornadas.

### URL
Método: GET 
```bash
/categoria
```
### Parâmetros da Requisição
Este endpoint não requer parâmetros na URL ou no corpo da requisição.

#### Respostas
Sucesso (200 OK)
- Descrição: Lista de categorias recuperada com sucesso.
- Corpo da resposta:
```bash
[
	{
		"descricao": "Informática"
	},
	{
		"descricao": "Celulares"
	},
	{
		"descricao": "Beleza e Perfumaria"
	},
	{
		"descricao": "Mercado"
	},
	{
		"descricao": "Livros e Papelaria"
	},
	{
		"descricao": "Brinquedos"
	},
	{
		"descricao": "Moda"
	},
	{
		"descricao": "Bebê"
	},
	{
		"descricao": "Games"
	}
]
```
#### Erros
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do Servidor"
}
```

### Lógica Interna
- Consulta ao Banco de Dados: O endpoint realiza uma consulta na tabela categorias para selecionar a coluna descricao de todas as categorias cadastradas.
- Retorno das Categorias: As categorias são retornadas no formato JSON, contendo apenas a descrição de cada uma.
### Verificações de Segurança
- Tratamento de erros: Em caso de falha na consulta ao banco de dados, um erro genérico é retornado ao cliente para evitar vazamento de detalhes internos do servidor.

---
## Endpoint: Cadastrar Usuário
### Descrição
Este endpoint permite o cadastro de novos usuários na base de dados. Ele realiza validações para garantir que o email seja único, criptografa a senha antes de armazená-la, e retorna os detalhes do usuário cadastrado. Em caso de falhas, erros apropriados são retornados para o cliente.

### URL
Método: POST 
```bash
/usuario
```
### Parâmetros da Requisição
A requisição deve conter os seguintes campos no corpo:

- nome: (string) Nome completo do usuário. Obrigatório.
- email: (string) Endereço de email do usuário. Deve ser único no banco de dados. Obrigatório.
- senha: (string) Senha do usuário. Obrigatório.
#### Corpo da Requisição

Exemplo de corpo da requisição:

Padrão JSON

```bash
{
    "nome": "Maria Souza",
    "email": "maria.souza@example.com",
    "senha": "senhaSegura123"
}
```
#### Respostas
Sucesso (201 Created)
- Descrição: Usuário cadastrado com sucesso.
- Corpo da resposta:
```bash
{
    "id": 1,
    "nome": "Maria Souza",
    "email": "maria.souza@example.com",
    "senha": "$2b$10$abcdefghij1234567890abcdefghijk"
}
```
#### Erros
400 Bad Request
- Condição: O email já existe.
- Corpo da resposta:
```bash
{
    "mensagem": "O email já existe."
}
```
- Condição: O usuário não foi cadastrado devido a um erro inesperado.
- Corpo da resposta:
```bash
{
    "mensagem": "O usuário não foi cadastrado."
}
```
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do Servidor."
}
```

### Lógica Interna
- O middleware `validarCorpoRequisicao` valida o corpo da requisição com base no esquema `schemaCadastrar`, assegurando que todos os campos necessários estão presentes e formatados corretamente.
- Validação de Unicidade do Email: O endpoint verifica se o email já está cadastrado. Se já existir, uma resposta de erro é retornada.
- Criptografia da Senha: Antes de salvar a senha no banco de dados, ela é criptografada utilizando o algoritmo bcrypt com um fator de custo de 10.
- Inserção no Banco de Dados: Se todas as validações forem aprovadas, o usuário é inserido na tabela usuarios e os dados completos do usuário cadastrado são retornados.
### Verificações de Segurança
- Criptografia de Senha: As senhas são criptografadas antes de serem armazenadas para garantir a segurança dos dados dos usuários.
- Validação de entrada: O corpo da requisição deve ser validado para garantir que todos os campos obrigatórios sejam fornecidos.
- Tratamento de erros: Erros relacionados à unicidade do email e outros erros internos são tratados de maneira que informações sensíveis não sejam expostas.

---
## Endpoint: Login de Usuário
### Descrição
Este endpoint permite que usuários autenticados façam login, validando suas credenciais e retornando um token JWT para autenticação em requisições subsequentes.

### URL
Método: POST 
```bash
/login
```
### Parâmetros da Requisição
A requisição deve conter os seguintes campos no corpo:

- email: (string) Endereço de email do usuário. Obrigatório.
- senha: (string) Senha do usuário. Obrigatório.  
#### Corpo da Requisição
Exemplo de corpo da requisição:

Padrão JSON

```bash
{
    "email": "maria.souza@example.com",
    "senha": "senhaSegura123"
}
```
#### Respostas
Sucesso (200 OK)
- Descrição: Login realizado com sucesso. Retorna os dados do usuário (exceto a senha) e um token JWT.
- Corpo da resposta:
```bash
{
    "usuario": {
        "id": 1,
        "nome": "Maria Souza",
        "email": "maria.souza@example.com"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MTcsImlhdCI6MTcyMzgyNjM2MSwiZXhwIjoxNzIzODU1MTYxfQ.SGAUW4aP6Wije2jwwWeMa2XBhqgAmroQHMN_ajILCQQ"
}
```
#### Erros
404 Not Found

- Condição: O email fornecido não corresponde a nenhum usuário cadastrado.
- Corpo da resposta:
```bash
  {
    "mensagem": "O usuario não foi encontrado."
  }
```
400 Bad Request

- Condição: A senha fornecida está incorreta.
- Corpo da resposta:
```bash
  {
    "mensagem": "Email ou senha não confere."
  }
```
500 Internal Server Error

- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
  {
    "mensagem": "Erro interno do Servidor."
  }
```
### Lógica Interna
- O middleware `validarCorpoRequisicao` valida o corpo da requisição com base no esquema `schemaLogin`, assegurando que todos os campos necessários estão presentes e formatados corretamente.
- Validação de Existência do Usuário: O endpoint verifica se o email fornecido está cadastrado. Se não estiver, uma resposta de erro é retornada.
- Verificação da Senha: A senha fornecida é comparada com a senha criptografada armazenada no banco de dados usando bcrypt. Se a senha não corresponder, um erro é retornado.
- Geração do Token JWT: Se a senha for validada com sucesso, um token JWT é gerado e assinado usando uma chave secreta (SENHA_JWT), com expiração de 8 horas.
- Retorno dos Dados do Usuário: Os dados do usuário, exceto a senha, são retornados junto com o token JWT.
### Verificações de Segurança
- Armazenamento Seguro de Senhas: As senhas dos usuários são armazenadas de forma criptografada utilizando bcrypt.
- Token JWT: O token JWT é assinado e expira após 8 horas, garantindo segurança adicional para o usuário.
- Tratamento de Erros: Erros genéricos são retornados para evitar o vazamento de informações sensíveis.
  
---
## Endpoint: Redefinir Senha de Usuário
### Descrição
Este endpoint permite que usuários autenticados redefinam sua senha, validando a senha antiga, garantindo que a nova senha não seja igual à antiga, atualizando a senha no banco de dados, e enviando um email de notificação sobre a mudança.

### URL
Método: PUT 
```bash
/redefinir
```
### Parâmetros da Requisição
A requisição deve conter os seguintes campos no corpo:

- email: (string) Endereço de email do usuário. Obrigatório.
- senha_antiga: (string) Senha atual do usuário. Obrigatório.
- senha_nova: (string) Nova senha do usuário. Obrigatório.
#### Corpo da Requisição
Exemplo de corpo da requisição:

Padrão JSON

```bash
{
    "email": "maria.souza@example.com",
    "senha_antiga": "senhaSegura123",
    "senha_nova": "novaSenhaSegura123"
}
```
#### Respostas
Sucesso (200 OK)
- Descrição: Senha alterada com sucesso. Um email de notificação é enviado ao usuário.
- Corpo da resposta:
```bash
{
    "mensagem": "Senha alterada com sucesso."
}
```
#### Erros
400 Bad Request
- Condição: A nova senha é igual à antiga.
- Corpo da resposta:
```bash
{
    "mensagem": "A senha nova não pode ser igual à senha antiga."
}
```
404 Not Found
- Condição: O email fornecido não corresponde a nenhum usuário cadastrado.
- Corpo da resposta:
```bash
{
    "mensagem": "Usuário não encontrado."
}
```
401 Unauthorized
- Condição: A senha antiga fornecida está incorreta.
- Corpo da resposta:
```bash
{
    "mensagem": "Senha antiga incorreta."
}
```
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do Servidor."
}
```
### Lógica Interna
- O middleware `validarCorpoRequisicao` valida o corpo da requisição com base no esquema `schemaRedefinirSenha`, assegurando que todos os campos necessários estão presentes e formatados corretamente.
- Validação de Diferenciação de Senhas: O endpoint verifica se a nova senha é diferente da senha antiga. Caso sejam iguais, um erro é retornado.
- Validação de Existência do Usuário: O endpoint verifica se o email fornecido está cadastrado no sistema.
- Verificação da Senha Antiga: A senha antiga fornecida é comparada com a senha armazenada no banco de dados usando bcrypt. Se a senha não corresponder, um erro é retornado.
- Criptografia da Nova Senha: A nova senha é criptografada usando bcrypt antes de ser armazenada no banco de dados.
- Envio de Email de Notificação: Após a alteração bem-sucedida da senha, um email de notificação é enviado ao usuário informando sobre a mudança.
### Verificações de Segurança
- Criptografia de Senha: As senhas dos usuários são armazenadas de forma segura utilizando bcrypt.
- Notificação de Alteração: Um email é enviado ao usuário após a alteração da senha para garantir que ele seja informado de qualquer mudança em sua conta.
- Validação de Diferenciação de Senhas: Evita que o usuário defina a nova senha igual à senha antiga, promovendo melhores práticas de segurança.

---
## Middleware: Validar token
### Descrição
Função de Midlleware (Intermediário) de Rota, responsável por autenticar o acesso às rotas protegidas da aplicação. Ele gera um Token JWT (JSON Web Token) na rota de Login do Usuário e nas demais rotas verifica se o Token foi fornecido no header da requisição, assegurando que o usuário está logado. Todas as rotas após o uso desta função somente irão funcionar se a validação do token for realizada. A configuração desta função deixa o Token gerado válido por 8 horas.

### URL
Este middleware é aplicado a rotas protegidas e não possui uma URL específica.

### Parâmetros da Requisição
Na rota de login do Usuário o Token é gerado através da decodificação de uma chave secreta (`SENHA_JWT`). Após o Login do Usuário nas demais rotas o Token deve ser informado na Authentication da rota em formato Bearer Token para poder prosseguir com as requisições.
- Cabeçalho (Authorization): Bearer <token>: O token JWT que autoriza o acesso à rota. Obrigatório.
- Exemplo de Cabeçalho
```bash
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```
#### Respostas
Sucesso (201 Created)
- Descrição: Validando o Token gerado no Login de Usuário. O Token é validado no header authentication Bearer Token e a requisição é autorizada a continuar.
- Corpo da resposta:
```bash
{
	"usuario": {
		"id": 1,
		"nome": "Lucas Ramos",
		"email": "lucas@email.com"
	},
	"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwiaWF0IjoxNzIzOTA2NjQ2LCJleHAiOjE3MjM5MzU0NDZ9.DpA0QnmxqrH0suzmhLJjcR5CwXO0sx5XUxzmQSI5vow"
}
```
#### Erros
401 Unauthorized
- Condição: O cabeçalho de autorização não foi fornecido, o token é inválido, expirado ou o usuário não foi encontrado.
- Corpo da Resposta:
```bash
{
    "mensagem": "Não autorizado."
}
```
- Condição: O token JWT é válido, mas o usuário correspondente não foi encontrado no banco de dados.
- Corpo da Resposta:
```bash
{
    "mensagem": "Login inválido ou expirado."
}
```
500 Internal Server Error
- Condição: Ocorreu um erro inesperado durante a verificação do token ou na consulta ao banco de dados.
- Corpo da Resposta:
```bash
{
    "mensagem": "Erro interno do servidor."
}
```
### Lógica Interna
- Consulta ao Banco de Dados: No Login do Usuário o midlleware consulta a tabela usuários para verificar se o usuário correspondente ao ID existe. Na validação do Usuário Logado o midlleware verifica se o Token informado no header authentication em formato Bearer Token é valido.
- Verificação do Cabeçalho de Autorização: O middleware verifica se o cabeçalho de autorização está presente e se segue o formato Bearer <token>.
- Validação do Token JWT: O token é decodificado e validado utilizando a chave SENHA_JWT definida nas variáveis de ambiente.
- Verificação da Existência do Usuário: O ID extraído do token é usado para consultar o banco de dados e verificar se o usuário ainda existe.
### Verificações de Segurança
- Tratamento de erros: Em caso de falha na consulta ao banco de dados, um erro genérico é retornado ao cliente para evitar vazamento de detalhes internos do servidor.
- Autenticação JWT: Somente usuários autenticados com um token JWT válido podem acessar as rotas protegidas.
- Verificação de Existência do Usuário: Mesmo com um token válido, a existência do usuário no banco de dados é verificada para garantir que o acesso seja concedido apenas a usuários ativos.
- Proteção de Dados Sensíveis: A senha do usuário é removida dos dados antes de serem anexados ao objeto de requisição, garantindo que informações sensíveis não sejam expostas.

---
## Endpoint: Detalhar Usuário Logado
### Descrição
Este endpoint permite ao usuário logado visualizar os dados de seu próprio perfil. Ele retorna o ID, nome e email do usuário autenticado. Somente usuários autenticados podem acessar este recurso.

### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### URL
Método: GET
```bash
/usuario
```
### Parâmetros da Requisição
- Nenhum parâmetro é necessário na URL. A requisição utiliza o token de autenticação do usuário logado no cabeçalho.
#### Corpo da Requisição
- Nenhum dado precisa ser enviado no corpo da requisição.

#### Respostas
Sucesso (201 Created)
- Descrição: Requisição bem-sucedida e o perfil do usuário logado foi retornado.
- Corpo da Resposta:
```bash
{
    "id": 1,
    "nome": "José",
    "email": "jose@email.com"
}
```
#### Erros:
500 Internal Server Error
- Condição: Este erro indica que ocorreu um problema no servidor durante o processamento da requisição. Pode ocorrer devido a falhas na leitura dos dados do usuário ou outras exceções inesperadas.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do Servidor."
}
```
### Lógica Interna
- O método detalharUsuario extrai os dados do usuário logado a partir do objeto req.usuario, que é previamente populado pelo middleware de autenticação.
- Retorna o id, nome e email do usuário autenticado no formato JSON.
### Verificações de Segurança
- Autenticação: O acesso a este endpoint é protegido por um middleware que verifica a autenticidade do token JWT no cabeçalho da requisição. O usuário só consegue acessar seus próprios dados se estiver autenticado corretamente.
- Privacidade: Somente os dados do usuário logado (ID, nome e email) são retornados, garantindo que nenhuma informação sensível de outros usuários seja exposta.

---
## Endpoint: Editar Usuário Logado
### Descrição
Este endpoint permite ao usuário logado editar as informações de seu próprio perfil, como nome, email e senha. Somente usuários autenticados podem acessar este recurso.

### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### URL
Método: PUT
```bash
/usuario
```

### Parâmetros da Requisição
Nenhum parâmetro é necessário na URL. A requisição deve conter o token de autenticação do usuário logado no cabeçalho.

A requisição deve conter os seguintes campos no corpo:
- **nome** (string, obrigatório): O novo nome do usuário.
- **email** (string, obrigatório): O novo email do usuário.
- **senha** (string, obrigatório): A senha atual do usuário (necessária para confirmar a alteração).
#### Corpo da Requisição
Exemplo de corpo da requisição

Padrão JSON

```bash
{
    "nome": "Novo Nome",
    "email": "novoemail@email.com",
    "senha": "senhaAtual"
}
```
#### Respostas
Sucesso (204 No Content)
- Condição: O perfil do usuário foi atualizado com sucesso.
- Corpo da resposta: Nenhum conteúdo adicional é retornado.

#### Erros
400 (Bad Request)
- Condição: O email informado já está em uso por outro usuário.
- Corpo da resposta:
```bash
{
    "mensagem": "O e-mail informado já está sendo utilizado por outro usuário."
}
```
401 (Unauthorized)
- Condição: A senha fornecida está incorreta e, portanto, a alteração não foi autorizada.
- Corpo da resposta:
```bash
 {
     "mensagem": "Senha incorreta."
 }
```
404 (Not Found)
 - Condição: O usuário não foi encontrado na base de dados.
 - Corpo da resposta:
```bash
 {
     "mensagem": "Usuário não encontrado."
 }
```
500 (Internal Server Error)
- Condição: Ocorreu um erro inesperado no servidor ao processar a requisição.
- Corpo da resposta:
```bash
{
     "mensagem": "Erro interno do Servidor."
}
```
### Lógica Interna
- O middleware `validarCorpoRequisicao` valida o corpo da requisição com base no esquema `schemaEditarUsuarioLogado`, assegurando que todos os campos necessários estão presentes e formatados corretamente.
- O método `editarUsuarioLogado` realiza as seguintes verificações e ações:
  1. Verifica se o email fornecido já está em uso por outro usuário.
  2. Verifica se o usuário existe no banco de dados.
  3. Verifica se a senha fornecida corresponde à senha atual do usuário.
  4. Atualiza o nome e o email do usuário no banco de dados.
### Verificações de Segurança
- Validação de Dados: A estrutura do corpo da requisição é validada utilizando o Joi para garantir que todos os campos obrigatórios sejam fornecidos e que estejam no formato correto.
- Autenticação: O endpoint só pode ser acessado por usuários autenticados, verificados por meio de um token JWT.
- Autorização: O usuário deve fornecer a senha atual para confirmar as alterações, garantindo que somente o próprio usuário possa modificar seus dados.

---
## Endpoint: Cadastrar Produto
### Descrição
Este endpoint permite o cadastro de um novo produto no sistema, incluindo informações como descrição, quantidade em estoque, valor e categoria. Somente usuários autenticados podem acessar este recurso.

### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### URL
Método: POST
```bash
/produto
```
### Parâmetros da Requisição
Nenhum parâmetro é necessário na URL. A requisição deve conter o token de autenticação do usuário logado no cabeçalho.

A requisição deve conter os seguintes campos no corpo:
- **descricao** (string, obrigatório): A descrição do produto.
- **quantidade_estoque** (number, obrigatório): A quantidade de itens em estoque. Deve ser um número inteiro maior que zero.
- **valor** (number, obrigatório): O preço do produto. Deve ser um número positivo.
- **categoria_id** (number, obrigatório): O ID da categoria à qual o produto pertence. Deve ser um número inteiro.
#### Corpo da Requisição
Exemplo de corpo da requisição:

Padrão JSON

```bash
{
    "descricao": "Descrição do Produto",
    "quantidade_estoque": 10,
    "valor": 99.99,
    "categoria_id": 1
}
```
#### Respostas
Sucesso (201 Created)
- Condição: O produto foi cadastrado com sucesso e os dados do novo produto são retornados.
- Corpo da resposta:
```bash
{
     "id": 1,
     "descricao": "Descrição do Produto",
     "quantidade_estoque": 10,
     "valor": 99.99,
     "categoria_id": 1
}
```
#### Erros
404 (Not Found)
- Condição: A categoria especificada não foi encontrada no banco de dados.
- Corpo da resposta:
```bash
{
     "mensagem": "Categoria não encontrada."
}
```
500 (Internal Server Error)
- Condição: Ocorreu um erro inesperado no servidor ao processar a requisição.
- Corpo da resposta:
```bash
{
     "mensagem": "Erro interno do Servidor."
}
```
### Lógica Interna
- O middleware `validarCorpoRequisicao` valida o corpo da requisição com base no esquema `schemaCadastrarProduto`, assegurando que todos os campos necessários estão presentes e formatados corretamente.
- O método `cadastrarProduto` realiza as seguintes verificações e ações:
  1. Verifica se o ID da categoria fornecido existe no banco de dados.
  2. Caso a categoria exista, o produto é inserido na tabela `produtos` com os dados fornecidos.
  3. Retorna os dados do produto recém-cadastrado.
### Verificações de Segurança
- Validação de Dados: A estrutura do corpo da requisição é validada utilizando o Joi para garantir que todos os campos obrigatórios sejam fornecidos e que estejam no formato correto.
- Autenticação: O endpoint só pode ser acessado por usuários autenticados, verificados por meio de um token JWT.
- Autorização: O acesso ao endpoint é restrito a usuários autenticados que têm permissão para cadastrar novos produtos.

---
## Endpoint: Upload de Imagem do Produto
### Descrição
Este endpoint permite o upload de uma nova imagem para um produto existente, ou a remoção de uma imagem associada ao produto. A imagem pode ser enviada como um arquivo na requisição, e se já existir uma imagem anterior, esta será substituída. Somente usuários autenticados podem acessar esse recurso.

### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### URL
Método: PATCH
```bash
/produto/:id/imagem
```
### Parâmetros da Requisição
- **URL**:
  - `id` (obrigatório): O ID do produto para o qual a imagem será carregada ou removida.
#### Corpo da Requisição
- **Arquivo** (opcional): Um arquivo de imagem deve ser enviado com o campo `imagem` no corpo da requisição utilizando `multipart/form-data`.

### Respostas
Sucesso (201 Created)
- Condição: A imagem foi carregada e associada ao produto com sucesso.
- Corpo da resposta:
```bash
{
     "mensagem": "Imagem carregada com sucesso!",
     "imagem_url": "https://link-da-imagem-carregada.com"
}
```
Sucesso (200 OK)
- Condição: A imagem anterior foi removida com sucesso, e o produto agora está sem imagem associada.
- Corpo da resposta:
```bash
{
    "mensagem": "Imagem antiga removida com sucesso!",
    "imagem_url": null
}
```
#### Erros
404 (Not Found)
- Condição: Nenhuma imagem ou dados foram enviados na requisição.
- Corpo da resposta:
```bash
{
     "mensagem": "Nenhuma informação fornecida."
}
```
ou
- Condição: O produto com o ID fornecido não foi encontrado no banco de dados.
- Corpo da resposta:
```bash
{
     "mensagem": "Produto não encontrado."
}
```
500 (Internal Server Error)
- Condição: Ocorreu um erro inesperado no servidor ao processar a requisição, como falha ao atualizar o banco de dados ou ao fazer o upload/remover a imagem.
- Corpo da resposta:
```bash
{
     "mensagem": "Erro interno do Servidor."
}
```
### Lógica Interna
- Validação de Dados: A requisição valida se um arquivo de imagem foi enviado ou se existem dados no corpo da requisição.
- Busca do Produto: O produto é buscado no banco de dados usando o ID fornecido. Se o produto não for encontrado, uma resposta 404 é retornada.
- Upload de Imagem: Se um novo arquivo de imagem é enviado, ele é carregado, e a URL da nova imagem substitui a URL da imagem antiga no banco de dados. Se já houver uma imagem anterior, ela será removida.
- Remoção de Imagem: Se nenhum arquivo for enviado e o produto já tiver uma imagem, essa imagem é removida tanto do armazenamento quanto do banco de dados.
#### Verificações de Segurança
- Verificação de Existência do Produto: Antes de qualquer operação, o código verifica se o produto com o ID fornecido realmente existe no banco de dados.
- Remoção Segura de Imagens: Se uma imagem já existe no produto e uma nova imagem é carregada, a imagem antiga é removida de forma segura para evitar acúmulo de arquivos desnecessários no sistema.

---
## Endpoint: Cadastrar Cliente
### Descrição
Este endpoint permite o cadastro de novos clientes na base de dados. Ele realiza validações para garantir que o email e o CPF sejam únicos, normaliza o CPF antes de armazená-lo, e retorna os detalhes do cliente cadastrado. Em caso de falhas, erros apropriados são retornados para o cliente. Somente usuários autenticados podem acessar esse recurso.

### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### URL
Método: POST 
```bash
/cliente
```
### Parâmetros da Requisição
A requisição deve conter os seguintes campos no corpo:

- nome: (string) Nome completo do cliente. Obrigatório.
- email: (string) Endereço de email do cliente. Deve ser único no banco de dados. Obrigatório.
- cpf: (string) CPF do cliente. Deve ser único no banco de dados. Obrigatório.
- cep: (string) CEP do endereço do cliente. Obrigatório.
- rua: (string) Nome da rua do endereço do cliente. Obrigatório.
- numero: (string) Número do endereço do cliente. Obrigatório.
- bairro: (string) Bairro do endereço do cliente. Obrigatório.
- cidade: (string) Cidade do endereço do cliente. Obrigatório.
- estado: (string) Estado do endereço do cliente. Obrigatório.
#### Corpo da Requisição
Exemplo de corpo da requisição:

Padrão JSON

```bash
{
    "nome": "João da Silva",
    "email": "joao.silva@example.com",
    "cpf": "123.456.789-09",
    "cep": "12345-678",
    "rua": "Rua das Flores",
    "numero": "123",
    "bairro": "Centro",
    "cidade": "São Paulo",
    "estado": "SP"
}
```
#### Respostas
Sucesso (201 Created)
- Descrição: Cliente cadastrado com sucesso.
- Corpo da resposta:
```bash
{
    "id": 1,
    "nome": "João da Silva",
    "email": "joao.silva@example.com",
    "cpf": "12345678909",
    "cep": "12345-678",
    "rua": "Rua das Flores",
    "numero": "123",
    "bairro": "Centro",
    "cidade": "São Paulo",
    "estado": "SP"
}
```
#### Erros
400 Bad Request
- Condição:
O email já está em uso por outro cliente.
- Corpo da resposta:
```bash
{
    "mensagem": "O email já está em uso por outro cliente."
}
```
- Condição:
O CPF já está em uso por outro cliente.
- Corpo da resposta:
```bash
{
    "mensagem": "O CPF já está em uso por outro cliente."
}
```
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do Servidor"
}
```

### Lógica Interna
- O middleware `validarCorpoRequisicao` valida o corpo da requisição com base no esquema `schemaCadastroCliente`, assegurando que todos os campos necessários estão presentes e formatados corretamente.
- Normalização do CPF: O CPF é normalizado removendo todos os caracteres não numéricos antes de ser verificado e armazenado.
- Validação de Unicidade: O endpoint verifica se o email e o CPF já estão cadastrados. Se qualquer um dos dois já existir, uma resposta de erro é retornada.
- Inserção no Banco de Dados: Se todas as validações forem aprovadas, o cliente é inserido na tabela clientes e os dados completos do cliente cadastrado são retornados.
### Verificações de Segurança
- Validação de entrada: O corpo da requisição deve ser validado para garantir que todos os campos obrigatórios sejam fornecidos.
- Tratamento de erros: Erros de banco de dados relacionados à unicidade (como tentativas de cadastro com CPF duplicado) são capturados e tratados corretamente para evitar vazamento de informações sensíveis.

---
## Endpoint: Editar Dados do Cliente
### Descrição
Este endpoint permite a atualização dos dados de um cliente existente. Ele valida a existência do cliente pelo ID, verifica a unicidade do email e CPF, normaliza o CPF removendo caracteres não numéricos, e atualiza as informações no banco de dados. Somente usuários autenticados podem acessar este recurso.

### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### URL
Método: PUT 
```bash
/cliente/:id
```
### Parâmetros da URL
id: (integer) ID do cliente a ser atualizado. Obrigatório.
### Parâmetros da Requisição
A requisição deve conter os seguintes campos no corpo:

- nome: (string) Nome do cliente. Obrigatório.
- email: (string) Endereço de email do cliente. Obrigatório.
- cpf: (string) CPF do cliente. Obrigatório.
- cep: (string) CEP do endereço do cliente. Obrigatório.
- rua: (string) Rua do endereço do cliente. Obrigatório.
- numero: (string) Número do endereço do cliente. Obrigatório.
- bairro: (string) Bairro do endereço do cliente. Obrigatório.
- cidade: (string) Cidade do endereço do cliente. Obrigatório.
- estado: (string) Estado do endereço do cliente. Obrigatório.
#### Corpo da Requisição
Exemplo de corpo da requisição:

Padrão JSON

```bash
{
    "nome": "João Silva",
    "email": "joao.silva@example.com",
    "cpf": "123.456.789-00",
    "cep": "12345-678",
    "rua": "Rua Exemplo",
    "numero": "100",
    "bairro": "Centro",
    "cidade": "São Paulo",
    "estado": "SP"
}
```
#### Respostas
Sucesso (200 OK)
- Descrição: Dados do cliente atualizados com sucesso.
- Corpo da resposta:
```bash
{
    "id": 1,
    "nome": "João Silva",
    "email": "joao.silva@example.com",
    "cpf": "12345678900",
    "cep": "12345-678",
    "rua": "Rua Exemplo",
    "numero": "100",
    "bairro": "Centro",
    "cidade": "São Paulo",
    "estado": "SP"
}
```
#### Erros
400 Bad Request
- Condição: O email ou CPF informado já está em uso por outro cliente.
- Corpo da resposta:
```bash
{
    "mensagem": "O e-mail informado já está sendo utilizado por outro usuário."
}
```
ou
```bash
{
    "mensagem": "O CPF informado já está sendo utilizado por outro usuário."
}
```
- Condição: O cliente não foi atualizado devido a algum problema interno.

- Corpo da resposta:
```bash
{
    "mensagem": "O cliente não foi atualizado."
}
```
404 Not Found
- Condição: O cliente com o ID fornecido não foi encontrado.
- Corpo da resposta:
```bash
{
    "mensagem": "Cliente não encontrado."
}
```
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do servidor.",
    "error": "Detalhes do erro"
}
```
### Lógica Interna
- O middleware `validarCorpoRequisicao` valida o corpo da requisição com base no esquema `schemaEditarDadosCliente`, assegurando que todos os campos necessários estão presentes e formatados corretamente.
- Validação de Existência do Cliente: O endpoint verifica se o cliente com o ID fornecido existe no banco de dados.
- Validação de Unicidade de Email e CPF: O endpoint verifica se o email ou CPF já estão em uso por outro cliente.
- Normalização do CPF: O CPF é normalizado para remover caracteres não numéricos antes de ser armazenado.
- Atualização dos Dados: Se todas as validações forem bem-sucedidas, os dados do cliente são atualizados no banco de dados.
### Verificações de Segurança
- Validação de Dados: O email e CPF são verificados para garantir a unicidade, evitando conflitos de dados.
- Normalização de CPF: O CPF é tratado para garantir a consistência dos dados armazenados.

---
## Endpoint: Listar Clientes
### Descrição
Este endpoint permite listar todos os clientes cadastrados no sistema. Ele retorna uma lista de clientes com os campos essenciais: id, nome, email e cpf. Caso nenhum cliente seja encontrado, uma resposta de erro é enviada. Somente usuários autenticados podem acessar este recurso.

### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### URL
Método: GET 
```bash
/cliente
```
### Parâmetros da Requisição
Este endpoint não requer parâmetros na URL ou no corpo da requisição.

#### Respostas
Sucesso (200 OK)
- Descrição: Lista de clientes retornada com sucesso.
- Corpo da resposta:
```bash
[
    {
        "id": 1,
        "nome": "João Silva",
        "email": "joao.silva@example.com",
        "cpf": "12345678900"
    },
    {
        "id": 2,
        "nome": "Maria Oliveira",
        "email": "maria.oliveira@example.com",
        "cpf": "98765432100"
    }
]
```
#### Erros
404 Not Found
- Condição: Nenhum cliente foi encontrado no banco de dados.
- Corpo da resposta:
```bash
{
    "mensagem": "Nenhum cliente encontrado."
}
```
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do Servidor."
}
```
### Lógica Interna
- Consulta ao Banco de Dados: O endpoint busca todos os clientes cadastrados, retornando os campos id, nome, email e cpf.
- Validação de Resultados: Se não houver clientes cadastrados, uma resposta 404 é enviada.
- Retorno dos Dados: Se os clientes forem encontrados, a lista é retornada com uma resposta 200.
### Varificações de Segurança
- Validação de Resultados: O retorno 404 para consultas vazias evita a exposição de informações desnecessárias.

---
## Endpoint: Detalhar Cliente
### Descrição
Este endpoint permite obter os detalhes de um cliente específico, utilizando o ID como parâmetro de pesquisa. Retorna todas as informações do cliente armazenadas no banco de dados. Somente usuários autenticados podem acessar este recurso.

### URL
Método: GET 
```bash
/clientes/:id
```
### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### Parâmetros da URL
id (obrigatório): O ID do cliente deve ser declarado na url.

#### Respostas
Sucesso (200 OK)
- Descrição: Detalhes do cliente retornados com sucesso.
- Corpo da resposta:
```bash
{
    "id": 1,
    "nome": "João Silva",
    "email": "joao.silva@example.com",
    "cpf": "12345678900",
    "cep": "12345-678",
    "rua": "Rua Exemplo",
    "numero": "123",
    "bairro": "Centro",
    "cidade": "São Paulo",
    "estado": "SP"
}
```
#### Erros
404 Not Found
- Condição: Nenhum cliente foi encontrado com o ID fornecido.
- Corpo da resposta:
```bash
{
    "mensagem": "Cliente não encontrado."
}
```
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do servidor."
}
```
### Lógica Interna
- Busca no Banco de Dados: O endpoint busca no banco de dados o cliente com o ID especificado. Se o cliente não for encontrado, uma resposta 404 é enviada.
- Retorno dos Dados: Se o cliente for encontrado, suas informações são retornadas com uma resposta 200.
### Verificações de Segurança
- Autenticação JWT: Somente usuários autenticados podem acessar o endpoint.
- Validação de Resultados: A resposta 404 garante que dados inexistentes não sejam expostos.

---
## Endpoint: Cadastrar Pedido
### Descrição
Este endpoint permite cadastrar um novo pedido para um cliente. O pedido deve conter ao menos um produto vinculado. A quantidade disponível em estoque é verificada antes da confirmação do pedido, e o estoque é atualizado após o cadastro. Somente usuários autenticados podem acessar este recurso.

### URL
Método: POST 
```bash
/pedido
```
### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### Parâmetros da Requisição
- cliente_id (obrigatório): ID do cliente associado ao pedido.
- observacao (opcional): Observações sobre o pedido.
- pedido_produtos (obrigatório): Array de objetos contendo os produtos do pedido.
- produto_id (obrigatório): ID do produto.
- quantidade_produto (obrigatório): Quantidade do produto no pedido.
#### Corpo da Requisição
Exemplo de corpo da requisição:
```bash
{
    "cliente_id": 1,
    "observacao": "Entrega rápida, por favor.",
    "pedido_produtos": [
        {
            "produto_id": 2,
            "quantidade_produto": 3
        },
        {
            "produto_id": 4,
            "quantidade_produto": 1
        }
    ]
}
```
#### Respostas
Sucesso (201 Created)
- Descrição: Pedido cadastrado com sucesso.
- Corpo da resposta:
```bash
{
    "id": 1,
    "cliente_id": 1,
    "observacao": "Entrega rápida, por favor.",
    "valor_total": 150.00
}
```
#### Erros
400 Bad Request
- Condição: Não foi fornecido o cliente_id ou pedido_produtos está vazio.
- Corpo da resposta:
```bash
{
    "mensagem": "Cliente_id é obrigatório."
}
```
ou
```bash
{
    "mensagem": "É necessário adicionar ao menos um produto ao pedido."
}
```
404 Not Found
- Condição: O cliente ou produto associado não foi encontrado.
- Corpo da resposta:
```bash
{
    "mensagem": "Cliente não encontrado."
}
```
ou
```bash
{
    "mensagem": "Produto com ID 2 não encontrado."
}
```
400 Bad Request
- Condição: Quantidade de produto solicitada maior que a disponível em estoque.
- Corpo da resposta:
```bash
{
    "mensagem": "Quantidade insuficiente em estoque para o produto Exemplo. Disponível: 2. Solicitado: 3."
}
```
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do servidor."
}
```
### Lógica Interna
- Autenticação: O token JWT fornecido no cabeçalho da requisição é validado. Se o token for inválido ou não fornecido, a requisição é rejeitada com uma resposta 401.
- Validação de Dados: Verifica se o cliente_id e pedido_produtos foram fornecidos e se o cliente existe no banco de dados.
- Verificação de Estoque: Para cada produto no pedido, verifica-se a quantidade disponível em estoque. Se a quantidade solicitada for maior que a disponível, a requisição falha.
- Cadastro do Pedido: O pedido é cadastrado, e a tabela de relacionamento entre pedido e produtos (pedido_produtos) é populada.
- Atualização do Estoque: O estoque dos produtos é atualizado com base nas quantidades solicitadas no pedido.
- Retorno de Resposta: Se o pedido for cadastrado com sucesso, o endpoint retorna os detalhes do pedido.
### Verificações de Segurança
- Autenticação JWT: Somente usuários autenticados podem acessar o endpoint.
- Validação de Resultados: O retorno 404 para consultas vazias evita a exposição de informações desnecessárias.

---
## Endpoint: Listar Pedidos
### Descrição
Este endpoint permite listar todos os pedidos cadastrados na base de dados. O usuário pode filtrar os pedidos por cliente utilizando o parâmetro de consulta cliente_id. Caso o parâmetro não seja fornecido, todos os pedidos serão retornados. Somente usuários autenticados podem acessar este recurso.

### URL
Método: GET
```bash
/pedido
```
### Autenticação
Este endpoint requer autenticação via token JWT (JSON Web Token).

### Parâmetros da Requisição
cliente_id: (string, opcional) ID do cliente para filtrar os pedidos associados a ele. Se não fornecido, todos os pedidos serão retornados.
#### Respostas
Sucesso (200 OK)
- Descrição: Lista de pedidos com detalhes dos produtos incluídos.
- Corpo da resposta:
```bash
[
    {
        "pedido": {
            "id": 1,
            "valor_total": 250.00,
            "observacao": "Observação do pedido",
            "cliente_id": 123
        },
        "pedido_produtos": [
            {
                "id": 1,
                "quantidade_produto": 2,
                "valor_produto": 100.00,
                "pedido_id": 1,
                "produto_id": 456
            }
        ]
    }
]
```
#### Erros
500 Internal Server Error
- Condição: Erro inesperado no servidor.
- Corpo da resposta:
```bash
{
    "mensagem": "Erro interno do servidor."
}
```
### Lógica Interna
- Construção da Query: A query inicial é montada para obter pedidos e seus produtos relacionados. Se o parâmetro cliente_id for fornecido, a query é ajustada para filtrar pelos pedidos desse cliente.
- Agrupamento dos Dados: Os dados retornados pela query são agrupados por pedido. Cada pedido inclui detalhes e uma lista de produtos associados.
- Conversão de Tipos: O valor total do pedido e o valor dos produtos são convertidos para o tipo float para garantir precisão no cálculo.
### Critérios de Aceite
- Filtragem por Cliente: Se o parâmetro cliente_id for enviado e corresponder a um cliente existente, a lista de pedidos será filtrada para mostrar apenas os pedidos desse cliente.
- Listagem Completa: Se o parâmetro cliente_id não for informado, todos os pedidos cadastrados serão retornados.
### Verificações de Segurança
- Autenticação e Autorização: O endpoint deve ser protegido para garantir que apenas usuários autenticados possam acessar as informações de pedidos. Isso pode ser feito verificando o token JWT na requisição e garantindo que o usuário tenha permissões adequadas para visualizar os pedidos.
- Validação de Parâmetros: O parâmetro cliente_id é opcional e a ausência dele resulta na listagem de todos os pedidos. A inclusão de cliente_id é tratada na lógica da query.
- Tratamento de Erros: Erros durante a execução da query são capturados e uma resposta genérica de erro 500 é retornada. Nenhuma informação sensível é exposta nos erros.
- Proteção contra Injeção de SQL: A biblioteca Knex é utilizada para construir as consultas de forma segura, minimizando o risco de injeção de SQL.
- Paginação: Se a lista de pedidos for muito grande, considere implementar a paginação para melhorar o desempenho e a usabilidade.

---
>>>>>>> ff4a1c7 (first commit)
