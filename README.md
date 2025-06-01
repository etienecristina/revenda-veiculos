## Gerenciamento de Veículos API
Este projeto é uma API RESTful para gerenciamento de veículos, desenvolvida usando Java e Spring Boot. 
Ela permite o cadastro, consulta, atualização e venda de veículos, integrando-se com um banco de dados PostgreSQL e utilizando Keycloak para autenticação e autorização do cliente.

### Tecnologias Utilizadas
* Spring Boot
* Spring Data JPA
* Spring Security
* PostgreSQL
* Keycloak
* Docker Compose
* Lombok

### Estrutura do Projeto
O projeto segue uma estrutura padrão de aplicações Spring Boot, utilizando a arquitetura MVC:

* **adapter:** Contém a configuração de segurança (SecurityConfig).
* **config:** Contém a configuração de resolvers para argumentos de requisição (ResolverConfig).
* **controller:** Define os endpoints da API para operações de CRUD em veículos (VeiculoController).
* **dto:** Contém o objeto de transferência de dados (VeiculoDto) para as requisições.
* **enums:** Define o status dos veículos (VeiculoStatus).
* **model:** Representa a entidade Veiculo no banco de dados (VeiculoModel).
* **repository:** Interface do repositório para acesso aos dados (VeiculoRepository).
* **service:** Camada de serviço que implementa a lógica de negócio (VeiculoService, VeiculoServiceImpl).
* **specifications:** Define especificações para consultas dinâmicas (SpecificationTemplate).

### Configuração de Desenvolvimento
#### Pré-requisitos
Antes de iniciar, certifique-se de ter as seguintes ferramentas instaladas:

* Java Development Kit (JDK) 17 ou superior
* Maven
* Docker e Docker Compose

### Variáveis de Ambiente
As configurações sensíveis e de conexão estão presentes no arquivo application.yml. 
Algumas delas podem ser sobrescritas por variáveis de ambiente:

* spring.security.oauth2.resourceserver.jwt.jwk-set-uri: 
URI para o JWK Set do Keycloak.
* spring.security.oauth2.resourceserver.jwt.issuer-uri: URI do emissor do Keycloak.
* spring.datasource.url: URL de conexão com o banco de dados PostgreSQL.
* spring.datasource.username: Usuário do banco de dados PostgreSQL.
* spring.datasource.password: Senha do banco de dados PostgreSQL.

### Executando com Docker Compose
O arquivo docker-compose.yml orquestra os seguintes serviços:

* Keycloak: Servidor de autenticação na porta 7080 (HTTP) e 7443 (HTTPS).
* PostgreSQL: Banco de dados na porta 5432.
* PgAdmin: Interface web para gerenciamento do PostgreSQL na porta 5050.
* LocalStack: Emulador de serviços AWS na porta 4566.

### Para iniciar os serviços, execute no terminal na raiz do projeto:

`docker-compose up -d`

### Executando a Aplicação Spring Boot
Após subir os serviços com Docker Compose e garantir que o Keycloak está devidamente configurado, você poderá iniciar a aplicação:

`mvn spring-boot:run`

A API estará disponível em http://localhost:8080.

### Endpoints da API
A API expõe os seguintes endpoints sob o prefixo "/veiculos":

* **POST /veiculos -** 
Cria um novo veículo.
  * Resposta: 201 Created com o Location do novo recurso.
      * Exemplo do corpo de uma requisição (JSON): 
  
`{
"marca": "Toyota",
"modelo": "Corolla",
"ano": 2023,
"cor": "Preto",
"preco": 120000.00,
"veiculoStatus": "DISPONIVEL"
}`




* **GET /veiculos -** 
Lista todos os veículos com suporte a paginação e filtro por especificações.

    **Parâmetros de Consulta:**
`page, size, sort: Para paginação (ex: ?page=0&size=10&sort=preco,asc).
marca, modelo, ano, cor, veiculoStatus` 

  **Resposta:**
  * 200 OK com uma página de VeiculoModel.


* **GET /veiculos/{veiculoId}** - Busca um veículo por ID.

    **Resposta:**
    * 200 OK com VeiculoModel se encontrado.
    * 404 Not Found se o veículo não existir.


* **PUT /veiculos/{veiculoId}** - Atualiza um veículo existente.

    **Resposta:**
    * 200 OK com o VeiculoModel atualizado se encontrado.
    * 404 Not Found se o veículo não existir.
   
      Exemplo do corpo de uma requisição (JSON):

`{
"marca": "Toyota",
"modelo": "Corolla",
"ano": 2023,
"cor": "Preto",
"preco": 120000.00,
"veiculoStatus": "DISPONIVEL"
}`
  
  


* **PATCH /veiculos/{veiculoId}** - Efetua a simulação de uma compra do veículos, alterando seu status para VENDIDO.

    **Resposta:**
    * 200 OK com mensagem de sucesso se o veículo for vendido.
    * 404 Not Found se o veículo não existir.
    * 409 Conflict se o veículo já estiver vendido.

### Segurança
Esta API utiliza Spring Security com OAuth2 Resource Server para proteger os endpoints. Todas as requisições exigem um token JWT válido no cabeçalho Authorization (Bearer Token).

A configuração de segurança permite:

Requisições autenticadas para qualquer endpoint.
CORS configurado para permitir **PUT**, **DELETE**, **GET** e **POST** de qualquer origem.
