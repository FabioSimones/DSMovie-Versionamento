# DSMovie-Versionamento

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/FabioSimones/DSMovie-Versionamento/actions)
[![Maven Central](https://img.shields.io/maven-central/v/org.apache.maven/apache-maven)](https://search.maven.org/)
[![Java](https://img.shields.io/badge/Java-17-blue.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.1.0-brightgreen)](https://spring.io/projects/spring-boot)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)


Implementação demonstrativa de **versionamento de APIs** em Java, ilustrando duas estratégias principais: via **URI** e via **Media Type (custom header / content negotiation)**.

---

## 🧩 Visão do Projeto

Este projeto tem o propósito educativo e prático de demonstrar como implementar versionamento de APIs em uma aplicação de filmes (“DSMovie”), comparando abordagens usadas no mercado:

- **Versionamento por URI**: incluir a versão diretamente no caminho do endpoint (ex: `/v1/movies`, `/v2/movies`)  
- **Versionamento por Media Type**: usar cabeçalhos HTTP (Accept / Content-Type) e negociar a versão via *content negotiation* (ex: `application/vnd.company.app-v1+json`, `application/vnd.company.app-v2+json`)  

O código serve como referência para arquitetos, desenvolvedores backend e times que precisam escolher ou migrar estratégias de versionamento em APIs REST.

---

## 📂 Estrutura do Projeto

DSMovie-Versionamento/
- ├── src/
- │ ├── main/
- │ │ ├── java/ # código-fonte principal
- │ │ └── resources/ # arquivos de configuração, propriedades, etc.
- │ └── test/ # testes automatizados
- ├── create.sql # script de criação de banco de dados (se aplicável)
- ├── pom.xml # dependências Maven
- └── README.md


Principais pacotes esperados (dependendo da implementação concreta):

- `controller` – classes REST controllers que demonstram os endpoints versionados  
- `service` – lógica de negócios (ex: recuperar filmes, filtros)  
- `model` / `entity` – classes de domínio / entidades  
- `config` – configuração de versionamento (resolvers, interceptors, media types)  
- `exception` – tratamento de erros da API  

---

## 🚀 Como Funciona o Versionamento

### 1. Versionamento por URI

Exemplos de endpoints:

- GET /api/v1/movies
- GET /api/v2/movies


Você mantém controladores distintos ou roteamento que mapeia `/v1/…` para a versão 1, `/v2/…` para a versão 2, etc.  

Prós:
- Simples de entender e consumir  
- Visibilidade clara da versão na URL  

Contras:
- URLs “poluem” com versão  
- Pode haver duplicação de lógica entre versões  

### 2. Versionamento por Media Type (via Headers / Content Negotiation)

Neste modelo você usa cabeçalhos HTTP para indicar a versão:

```http
GET /api/movies
Accept: application/vnd.company.app-v1+json
```

Ou no Content-Type (quando for POST/PUT):

```xml
Content-Type: application/vnd.company.app-v2+json
```

### A aplicação identifica a versão pelo cabeçalho e roteia internamente para o controlador/serviço correto.

#### Prós:

- URLs mais limpas (sem versão explícita)
- Evolução da API pode ser feita sem mudar caminho de chamada

#### Contras:

- Consome mais configuração (resolvers, interceptors)
- Nem todas bibliotecas clientes lidam bem com tipos de mídia customizados

## 📋 Exemplos de Uso
### Requisição via URI (versão 1)

```http
GET /api/v1/movies/123
Accept: application/json
```

### Requisição via Media Type (versão 2)

```http
GET /api/movies/123
Accept: application/vnd.company.app-v2+json
```

- O servidor identifica v2 pelo cabeçalho <strong>Accept </strong> e retorna o formato correspondente.

## ⚙️ Configurações & Dependências

### Projeto gerenciado com Maven

- Dependências típicas: <strong>spring-boot-starter-web, spring-boot-starter-test, jackson </strong>, etc.
- Configuração de versionamento (resolvers, mapeamento de controllers) provavelmente está em pacote <strong>config</strong>
- Você pode parametrizar por <strong>application.properties</strong> ou <strong>application.yml</strong>, caso disponibilize:

#### Exemplo de configuração custom
```xml
api.versioning.strategy = uri  # ou mediaType
```

## 🧪 Testes

<p>O projeto inclui testes unitários/integrados para demonstrar que ambas as versões retornam dados esperados, e para garantir compatibilidade. Se ainda não existirem, recomendo criar:</p>

- Testes para cada versão (v1 e v2) dos endpoints
- Casos de fallback / erro (cliente solicita versão inexistente)
- Testes de content negotiation (aceitação de diferentes media types)

## 📈 Quando Adotar Cada Estratégia

| Estratégia     | Ideal quando...                                   |
| -------------- | ------------------------------------------------- |
| **URI**        | Quer simplicidade e visibilidade clara da versão  |
| **Media Type** | Deseja URLs estáveis e evoluir sem mudar caminhos |
