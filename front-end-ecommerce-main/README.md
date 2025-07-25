# Laboratório de E-commerce Serverless na AWS

Este projeto demonstra a construção de uma aplicação de e-commerce completa e funcional utilizando uma arquitetura 100% Serverless na AWS. O objetivo foi colocar em prática os conceitos de computação sem servidor para criar uma solução escalável, resiliente e com custo otimizado, que gerencia todo o fluxo de um pedido, desde a seleção de produtos até a confirmação do pagamento e notificação.

Este laboratório foi desenvolvido com a orientação do Prof. Tomas Alric da Escola da Nuvem.

## Arquitetura da Solução

A arquitetura foi desenhada para ser desacoplada e orientada a eventos, onde cada serviço possui uma responsabilidade única, comunicando-se de forma assíncrona.

O fluxo principal do usuário é o seguinte:

1. O cliente acessa o site, hospedado no `S3` e distribuído globalmente com baixa latência pelo `CloudFront`
2. As interações do front-end (como iniciar um pedido, buscar o histórico ou confirmar um pagamento) são enviadas para o `API Gateway`
3. O `API Gateway` atua como uma porta de entrada, direcionando as requisições para as `Funções Lambda` apropriadas
4. Ao iniciar um pedido, a `AWS Step Functions` orquestra um fluxo de trabalho complexo que valida o pagamento, trata possíveis fraudes e finaliza o pedido
5. Serviços como `SQS` e `SNS` são utilizados para comunicação assíncrona e notificações, enquanto o `DynamoDB` armazena de forma persistente os dados dos pedidos

## Como Reproduzir o Projeto

### 1. Configuração do Back-end

Utilizando o guia do laboratório, provisione os seguintes recursos na sua conta AWS:

- **IAM Roles**: Crie as permissões necessárias para que os serviços AWS possam se comunicar entre si
- **Funções Lambda**: Crie as 6 funções Lambda (`inicia-pedido`, `aguarda-pagamento`, etc.) utilizando o código da pasta `back-end-ecommerce-main`
- **Amazon SQS**: Crie uma fila padrão para processar as mensagens de pagamento de forma assíncrona
- **Amazon SNS**: Crie um tópico padrão para notificar sobre tentativas de fraude
- **DynamoDB**: Crie uma tabela para armazenar os dados dos pedidos
- **API Gateway**: Crie uma API REST com as rotas `/pedidos` e `/pagamentos`, integrando-as com as funções Lambda correspondentes
- **Step Functions**: Crie uma Máquina de Estado Padrão e cole a definição do arquivo `.asl.json` para orquestrar o fluxo
- **Variáveis de Ambiente**: Configure as variáveis de ambiente em cada função Lambda para conectar os recursos (URLs de SQS, ARNs de Step Functions, nomes de tabelas, etc.)

### 2. Configuração do Front-end

1. Atualize a URL da API: Nos arquivos `.js` dentro da pasta `front-end-ecommerce-main/js`, substitua o placeholder pela URL de invocação do seu API Gateway
2. **Bucket S3**: Crie um bucket S3 para hospedar os arquivos estáticos do site
3. **Upload**: Faça o upload de todos os arquivos e pastas de `front-end-ecommerce-main` para a raiz do seu bucket S3
4. **CloudFront**: Crie uma distribuição do CloudFront apontando para o seu bucket S3, configurando o OAC (Origin Access Control) para garantir que o acesso ao bucket seja feito apenas através do CloudFront

## Evidências da Implementação

| Serviço           | Evidência                                                                 |
|--------------------|---------------------------------------------------------------------------|
| IAM Roles         | ![Evidência de IAM Roles](./evidencias/iam-roles.png)                    |
| Funções Lambda    | ![Evidência de Funções Lambda](./evidencias/funcoes-lambda.png)          |
| Tabela DynamoDB   | ![Evidência da Tabela DynamoDB](./evidencias/dynamodb.png)               |
| Buckets S3        | ![Evidência dos Buckets S3](./evidencias/s3.png)                        |
| Tópico SNS        | ![Evidência do Tópico SNS](./evidencias/sns.png)                        |
| API Gateway       | ![Evidência do API Gateway](./evidencias/api-gateway.png)               |
| CloudFront        | ![Evidência da Distribuição CloudFront](./evidencias/cloudfront.png)    |

## Demonstração em Vídeo

![Animação mostrando fluxo completo de compra: seleção de produtos, checkout e confirmação de pagamento](evidencias/demo-ecommerce.gif)
