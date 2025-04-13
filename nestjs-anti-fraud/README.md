# Gateway de Pagamento - Microserviço Anti-Fraude (NestJS)

Este é o microsserviço de Anti-Fraude desenvolvido em NestJS, parte do projeto Gateway de Pagamento criado durante a [Imersão Full Stack & Full Cycle](https://imersao.fullcycle.com.br).

## Aviso Importante

Este projeto foi desenvolvido exclusivamente para fins didáticos como parte da Imersão Full Stack & Full Cycle.

## Sobre o Projeto

O Gateway de Pagamento é um sistema distribuído composto por:
- Frontend em Next.js
- API Gateway em Go
- Sistema de Antifraude em Nest.js (este repositório)
- Apache Kafka para comunicação assíncrona

## Arquitetura da aplicação
[Visualize a arquitetura completa aqui](https://link.excalidraw.com/readonly/Nrz6WjyTrn7IY8ZkrZHy)

## Pré-requisitos

- [Docker](https://www.docker.com/get-started)
  - Para Windows: [WSL2](https://docs.docker.com/desktop/windows/wsl/) é necessário
- [Extensão REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) (opcional, para testes via api.http)

## Importante!

⚠️ **É necessário executar primeiro o serviço go-gateway** antes deste projeto, pois este microserviço utiliza a rede Docker criada pelo go-gateway.

## Setup do Projeto

1. Clone o repositório:
```bash
git clone https://github.com/devfullcycle/imersao22.git
cd imersao22/nestjs-anti-fraud
```

2. Verifique se o serviço go-gateway já está em execução

3. Inicie os serviços com Docker Compose:
```bash
docker compose up -d
```

4. Execute as migrations do Prisma dentro do container:
```bash
docker compose exec nestjs bash
npx prisma migrate dev
```

## Executando a aplicação

Você pode rodar a aplicação em dois modos diferentes dentro do container:

### 1. API REST + Consumidor Kafka (padrão)
```bash
docker compose exec nestjs bash
npm run start:dev
```

### 2. Apenas o Consumidor Kafka
```bash
docker compose exec nestjs bash
npm run start:dev -- --entryFile cmd/kafka.cmd
```

## Estrutura do Projeto

O projeto usa:
- NestJS como framework
- Prisma ORM para acesso ao banco de dados PostgreSQL
- Integração com Apache Kafka para processamento assíncrono
- TypeScript para tipagem estática

## Comunicação via Kafka

O serviço de Anti-Fraude se comunica com o API Gateway via Apache Kafka:

### Consumo de eventos
- **Tópico**: `pending_transactions`
- **Formato**: JSON com os dados completos da transação

### Produção de eventos
- **Tópico**: `transactions_result`
- **Formato**: JSON com o resultado da análise e score de risco

## Regras de Análise

O sistema aplica regras para detectar possíveis fraudes, como:

1. **Valor da transação**:
   - Transações acima de determinados limites recebem pontuação de risco mais alta

2. **Frequência de transações**:
   - Muitas transações em curto período aumentam o risco

3. **Comportamento do cartão**:
   - Uso de múltiplos cartões com padrões suspeitos

## API Endpoints

O projeto inclui um arquivo `api.http` que pode ser usado com a extensão REST Client do VS Code para testar os endpoints da API:

1. Instale a extensão REST Client no VS Code
2. Abra o arquivo `api.http`
3. Clique em "Send Request" acima de cada requisição

Os endpoints disponíveis estão documentados neste arquivo para fácil teste e referência.

## Acesso ao Banco de Dados

O PostgreSQL do serviço de Anti-Fraude está configurado para evitar conflitos com o banco do go-gateway.

Para acessar o Prisma Studio (interface visual do banco de dados):

```bash
docker compose exec nestjs bash
npx prisma studio
```

## Logs e Monitoramento

Para visualizar os logs do serviço:

```bash
# Logs do serviço NestJS
docker logs -f nestjs-anti-fraud-nestjs-1
```

## Desenvolvimento

Para desenvolvimento, você pode executar comandos dentro do container:

```bash
# Acessar o shell do container
docker compose exec nestjs bash

# Exemplo de comandos disponíveis dentro do container
npm run start:dev  # Iniciar em modo de desenvolvimento
npm run start:dev -- --entryFile cmd/kafka.cmd  # Iniciar apenas o consumidor Kafka
npx prisma studio  # Interface visual do banco de dados
npx prisma migrate dev  # Executar migrations
```

Para modificar o código, edite os arquivos localmente - eles são montados como volume no container Docker, que reinicia automaticamente ao detectar mudanças.
