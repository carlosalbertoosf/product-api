# Product API

API REST para cadastro e consulta de produtos, construída com Spring Boot, Spring Data JPA e SQLite.

O projeto expõe operações CRUD para produtos e endpoints de busca por nome, preço, quantidade e status. O banco local é um arquivo SQLite chamado `meu_banco_de_dados.db`, criado/atualizado automaticamente pelo Hibernate conforme a configuração atual.

## Tecnologias

- Java 22
- Spring Boot 4.0.5
- Spring Web
- Spring Data JPA
- Hibernate Community Dialects
- SQLite JDBC
- Maven

## Estrutura Principal

```text
src/main/java/com/productapi
├── ProductAPIApplication.java
├── controller/ProdutoController.java
├── model/Produto.java
├── repository/ProdutoRepository.java
├── service/ProdutoService.java
├── runner/StartupRestClientRunner.java
└── client/
    ├── CRUDJavaClient.java
    ├── client-web-crud.html
    └── client-web-listagem.html
```

## Requisitos

- JDK 22 instalado
- Maven instalado e disponível no terminal

> Observação: este repositório possui a configuração do Maven Wrapper em `.mvn/wrapper`, mas não possui o script `mvnw`. Por isso, os comandos abaixo usam `mvn`.

## Configuração

As configurações principais ficam em `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:sqlite:meu_banco_de_dados.db
spring.datasource.driver-class-name=org.sqlite.JDBC
spring.jpa.database-platform=org.hibernate.community.dialect.SQLiteDialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
server.servlet.context-path=/
```

Com `spring.jpa.hibernate.ddl-auto=update`, a tabela da entidade `Produto` é criada ou atualizada automaticamente no SQLite.

## Como Executar

Instale as dependências e inicie a aplicação:

```bash
mvn spring-boot:run
```

A API ficará disponível em:

```text
http://localhost:8080
```

Para executar os testes:

```bash
mvn test
```

## Modelo de Produto

```json
{
  "id": 1,
  "nome": "Notebook",
  "quantidade": 10,
  "preco": 3500.0,
  "status": "Disponível"
}
```

Campos:

- `id`: identificador gerado automaticamente.
- `nome`: nome do produto. Campo obrigatório.
- `quantidade`: quantidade em estoque.
- `preco`: preço do produto.
- `status`: status do produto, por exemplo `Disponível` ou `Indisponível`.

## Endpoints

Base URL:

```text
http://localhost:8080/produtos
```

### CRUD

| Método | Endpoint | Descrição |
| --- | --- | --- |
| `GET` | `/produtos` | Lista todos os produtos |
| `GET` | `/produtos/{id}` | Busca um produto por ID |
| `POST` | `/produtos` | Cadastra um produto |
| `PUT` | `/produtos/{id}` | Atualiza um produto existente |
| `DELETE` | `/produtos/{id}` | Remove um produto |
| `POST` | `/produtos/salvarLista` | Cadastra uma lista de produtos |

### Buscas por Nome

| Método | Endpoint | Parâmetros |
| --- | --- | --- |
| `GET` | `/produtos/buscarPorNome` | `valor` |
| `GET` | `/produtos/buscarPorNomeContendo` | `valor` |
| `GET` | `/produtos/buscarPorNomeEStatus` | `nome`, `status` |
| `GET` | `/produtos/buscarPorNomeComecandoCom` | `valor` |
| `GET` | `/produtos/buscarPorNomeTerminandoCom` | `valor` |

### Buscas por Preço

| Método | Endpoint | Parâmetros |
| --- | --- | --- |
| `GET` | `/produtos/buscarPorPreco` | `valor` |
| `GET` | `/produtos/buscarPorPrecoMaiorQue` | `valor` |
| `GET` | `/produtos/buscarPorPrecoMenorQue` | `valor` |
| `GET` | `/produtos/buscarTotalPreco` | Nenhum |
| `GET` | `/produtos/buscarPorPrecoEStatus` | `preco`, `status` |

### Buscas por Quantidade

| Método | Endpoint | Parâmetros |
| --- | --- | --- |
| `GET` | `/produtos/buscarPorQuantidade` | `valor` |
| `GET` | `/produtos/buscarPorQuantidadeMenorQue` | `valor` |
| `GET` | `/produtos/buscarPorQuantidadeMaiorQue` | `valor` |

### Buscas por Status

| Método | Endpoint | Parâmetros |
| --- | --- | --- |
| `GET` | `/produtos/buscarPorStatus` | `valor` |
| `GET` | `/produtos/buscarPorStatusNulos` | Nenhum |
| `GET` | `/produtos/buscarPorStatusPadrao` | `valor`, opcional. Padrão: `Disponível` |
| `GET` | `/produtos/contarTotalDeProdutos` | Nenhum |

## Exemplos com cURL

### Criar Produto

```bash
curl -X POST http://localhost:8080/produtos \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Notebook",
    "quantidade": 5,
    "preco": 3500.0,
    "status": "Disponível"
  }'
```

### Listar Produtos

```bash
curl http://localhost:8080/produtos
```

### Buscar por ID

```bash
curl http://localhost:8080/produtos/1
```

### Atualizar Produto

```bash
curl -X PUT http://localhost:8080/produtos/1 \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Notebook Gamer",
    "quantidade": 3,
    "preco": 5200.0,
    "status": "Disponível"
  }'
```

### Remover Produto

```bash
curl -X DELETE http://localhost:8080/produtos/1
```

### Buscar por Nome Contendo Texto

```bash
curl "http://localhost:8080/produtos/buscarPorNomeContendo?valor=Note"
```

### Buscar Produtos com Preço Maior que um Valor

```bash
curl "http://localhost:8080/produtos/buscarPorPrecoMaiorQue?valor=1000"
```

### Contar Total de Produtos

```bash
curl http://localhost:8080/produtos/contarTotalDeProdutos
```

## Clientes de Exemplo

O projeto inclui exemplos de clientes em `src/main/java/com/productapi/client`:

- `CRUDJavaClient.java`: cliente Java usando `RestTemplate`.
- `client-web-crud.html`: página HTML para operações CRUD.
- `client-web-listagem.html`: página HTML para listagem.

Também existe o `StartupRestClientRunner`, que executa ao iniciar a aplicação e faz uma chamada para `http://localhost:8080/produtos`, imprimindo os produtos no console.

## Observações

- O CORS está liberado para qualquer origem com `@CrossOrigin(origins = "*")`.
- O arquivo `meu_banco_de_dados.db` é o banco SQLite local usado pela aplicação.
- A validação atual exige apenas que `nome` não seja vazio.
