# 🛠️ AWS Services with .NET 8

## 📜 Descrição

Este repositório demonstra como integrar os principais serviços da **Amazon Web Services (AWS)** com **.NET 8**. Ele fornece exemplos práticos de como utilizar serviços como **Amazon S3**, **Amazon DynamoDB**, **Amazon SNS**, **Amazon SQS**, **AWS Lambda**, **AWS IAM**, entre outros, para criar soluções escaláveis, seguras e de alta disponibilidade. Este guia tem como objetivo tornar o uso da AWS acessível para desenvolvedores que desejam aproveitar ao máximo os recursos da nuvem em suas aplicações **.NET 8**.

## 🚀 Funcionalidades

- **Amazon S3**: Armazenamento de arquivos em nuvem com alta disponibilidade e durabilidade.
- **Amazon DynamoDB**: Banco de dados NoSQL gerenciado, ideal para dados em tempo real.
- **Amazon SNS**: Serviço de mensagens e notificações assíncronas.
- **Amazon SQS**: Fila de mensagens para desacoplamento de componentes de sistemas distribuídos.
- **AWS Lambda**: Execução de código sem necessidade de servidores, escalabilidade automática.
- **Amazon EC2**: Computação em nuvem com servidores virtuais altamente escaláveis.
- **IAM (Identity and Access Management)**: Controle de permissões e acesso aos recursos da AWS.

## 🛠️ Tecnologias

- **.NET 8** (C#)
- **AWS SDK for .NET** (AWSSDK)
- **Amazon S3** 🗂️
- **Amazon DynamoDB** 🗃️
- **Amazon SNS** 📢
- **Amazon SQS** 📥
- **AWS Lambda** 🐑
- **AWS IAM** 🔑
- **Amazon EC2** 🌐
- **Docker** 🐳 (opcional, para executar Lambda localmente)

## 🧑‍💻 Requisitos

Antes de começar, certifique-se de ter os seguintes requisitos configurados:

- **.NET 8 SDK** ou superior.
- **AWS CLI** configurado corretamente com suas credenciais.
- **Conta AWS** com permissões para os serviços que você vai usar.
- **AWS SDK for .NET** (AWSSDK) instalado via NuGet.

### Instalando o SDK da AWS

Para instalar o AWS SDK para .NET, execute o comando:

```
dotnet add package AWSSDK.Core
dotnet add package AWSSDK.S3
dotnet add package AWSSDK.DynamoDBv2
dotnet add package AWSSDK.SQS
dotnet add package AWSSDK.SNS
dotnet add package AWSSDK.Lambda
dotnet add package AWSSDK.EC2
```

## 📥 Como Usar

**1. Clonando o Repositório**

Clone o repositório para sua máquina local:

```
git clone https://github.com/usuario/aws-services-dotnet8.git
cd aws-services-dotnet8
```

**2. Configuração do AWS CLI**

Se ainda não configurou o AWS CLI, faça isso executando:

```
aws configure
```

Isso pedirá sua Access Key, Secret Key, Região e Formato de saída (recomenda-se json).

**3. Configuração do Projeto**

No arquivo appsettings.json, adicione as configurações de acesso à AWS:

```
{
  "AWS": {
    "AccessKey": "YOUR_ACCESS_KEY",
    "SecretKey": "YOUR_SECRET_KEY",
    "Region": "us-west-2",
    "S3BucketName": "my-bucket",
    "DynamoDBTableName": "MyTable",
    "SQSQueueUrl": "https://sqs.us-west-2.amazonaws.com/1234567890/my-queue",
    "SNSTopicArn": "arn:aws:sns:us-west-2:1234567890:my-topic",
    "LambdaFunctionName": "MyLambdaFunction"
  }
}
```

**4. Rodando o Projeto**

Para rodar o projeto localmente:

```
dotnet run
```

## 📡 Exemplos de Uso

**1. Amazon S3: Upload de Arquivos**

O Amazon S3 oferece armazenamento altamente durável e escalável para arquivos. Aqui está um exemplo de como enviar um arquivo para o S3:

```
using Amazon.S3;
using Amazon.S3.Model;
using Microsoft.Extensions.Configuration;

public class S3Service
{
    private readonly IAmazonS3 _s3Client;
    private readonly string _bucketName;

    public S3Service(IConfiguration configuration)
    {
        _s3Client = new AmazonS3Client(
            configuration["AWS:AccessKey"],
            configuration["AWS:SecretKey"],
            RegionEndpoint.GetBySystemName(configuration["AWS:Region"])
        );
        
        _bucketName = configuration["AWS:S3BucketName"];
    }

    public async Task UploadFileAsync(string filePath)
    {
        var fileTransferUtility = new TransferUtility(_s3Client);

        // Enviar o arquivo
        await fileTransferUtility.UploadAsync(filePath, _bucketName);
        
        Console.WriteLine($"Arquivo {filePath} enviado para o S3 com sucesso!");
    }
}
```

**2. Amazon DynamoDB: Inserção de Dados**

O Amazon DynamoDB é um banco de dados NoSQL rápido e escalável. Abaixo, um exemplo de como inserir dados na tabela do DynamoDB:

```
using Amazon.DynamoDBv2;
using Amazon.DynamoDBv2.Model;
using Microsoft.Extensions.Configuration;

public class DynamoDBService
{
    private readonly IAmazonDynamoDB _dynamoDbClient;
    private readonly string _tableName;

    public DynamoDBService(IConfiguration configuration)
    {
        _dynamoDbClient = new AmazonDynamoDBClient(
            configuration["AWS:AccessKey"],
            configuration["AWS:SecretKey"],
            RegionEndpoint.GetBySystemName(configuration["AWS:Region"])
        );
        
        _tableName = configuration["AWS:DynamoDBTableName"];
    }

    public async Task InsertItemAsync(string id, string name)
    {
        var request = new PutItemRequest
        {
            TableName = _tableName,
            Item = new Dictionary<string, AttributeValue>
            {
                { "Id", new AttributeValue { S = id } },
                { "Name", new AttributeValue { S = name } }
            }
        };

        await _dynamoDbClient.PutItemAsync(request);
        Console.WriteLine("Item inserido no DynamoDB!");
    }
}
```

**3. Amazon SNS: Envio de Notificação**

O Amazon SNS permite que você envie mensagens para tópicos que podem ser subscritos. Aqui está como enviar uma notificação:

```
using Amazon.SimpleNotificationService;
using Amazon.SimpleNotificationService.Model;
using Microsoft.Extensions.Configuration;

public class SNSService
{
    private readonly IAmazonSimpleNotificationService _snsClient;
    private readonly string _topicArn;

    public SNSService(IConfiguration configuration)
    {
        _snsClient = new AmazonSimpleNotificationServiceClient(
            configuration["AWS:AccessKey"],
            configuration["AWS:SecretKey"],
            RegionEndpoint.GetBySystemName(configuration["AWS:Region"])
        );
        
        _topicArn = configuration["AWS:SNSTopicArn"];
    }

    public async Task PublishMessageAsync(string message)
    {
        var request = new PublishRequest
        {
            Message = message,
            TopicArn = _topicArn
        };

        await _snsClient.PublishAsync(request);
        Console.WriteLine($"Mensagem enviada para o tópico SNS: {message}");
    }
}
```

**4. Amazon SQS: Envio e Recebimento de Mensagens**

O Amazon SQS é uma fila de mensagens que facilita a comunicação assíncrona entre sistemas. Veja um exemplo de envio e recebimento de mensagens:

Enviar Mensagem para SQS:

```
using Amazon.SQS;
using Amazon.SQS.Model;
using Microsoft.Extensions.Configuration;

public class SQSService
{
    private readonly IAmazonSQS _sqsClient;
    private readonly string _queueUrl;

    public SQSService(IConfiguration configuration)
    {
        _sqsClient = new AmazonSQSClient(
            configuration["AWS:AccessKey"],
            configuration["AWS:SecretKey"],
            RegionEndpoint.GetBySystemName(configuration["AWS:Region"])
        );
        
        _queueUrl = configuration["AWS:SQSQueueUrl"];
    }

    public async Task SendMessageAsync(string messageBody)
    {
        var request = new SendMessageRequest
        {
            QueueUrl = _queueUrl,
            MessageBody = messageBody
        };

        await _sqsClient.SendMessageAsync(request);
        Console.WriteLine("Mensagem enviada para a fila SQS!");
    }
}
```

Receber Mensagens de SQS:

```
public async Task ReceiveMessagesAsync()
{
    var request = new ReceiveMessageRequest
    {
        QueueUrl = _queueUrl,
        MaxNumberOfMessages = 10
    };

    var response = await _sqsClient.ReceiveMessageAsync(request);

    foreach (var message in response.Messages)
    {
        Console.WriteLine($"Mensagem recebida: {message.Body}");

        // Processar a mensagem e removê-la da fila
        await _sqsClient.DeleteMessageAsync(new DeleteMessageRequest
        {
            QueueUrl = _queueUrl,
            ReceiptHandle = message.ReceiptHandle
        });
    }
}
```

**5. AWS Lambda: Invocação de Funções Sem Servidor**

O AWS Lambda permite que você execute funções sem precisar gerenciar servidores. Aqui está um exemplo de como invocar uma função Lambda:

```
using Amazon.Lambda;
using Amazon.Lambda.Model;
using Microsoft.Extensions.Configuration;

public class LambdaService
{
    private readonly IAmazonLambda _lambdaClient;

    public LambdaService(IConfiguration configuration)
    {
        _lambdaClient = new AmazonLambdaClient(
            configuration["AWS:AccessKey"],
            configuration["AWS:SecretKey"],
            RegionEndpoint.GetBySystemName(configuration["AWS:Region"])
        );
    }

    public async Task InvokeLambdaFunctionAsync(string functionName, string payload)
    {
        var request = new InvokeRequest
        {
            FunctionName = functionName,
            Payload = payload
        };

        var response = await _lambdaClient.InvokeAsync(request);
        Console.WriteLine($"Função Lambda invocada com sucesso: {response.StatusCode}");
    }
}
```

## 📈 Escalabilidade e Arquitetura

A AWS oferece recursos para dimensionar automaticamente os serviços de acordo com a demanda. Por exemplo:

Amazon EC2 permite que você crie instâncias de servidores sob demanda com escalabilidade automática.
Amazon S3 e DynamoDB são projetados para escalar sem precisar de configurações manuais.
Lambda permite a execução de funções sem a necessidade de gerenciar servidores, escalando conforme a quantidade de requisições.

## 🤝 Contribuição
Gostaria de contribuir? Siga as etapas abaixo:

Faça um fork deste repositório.
Crie uma branch para sua feature (git checkout -b minha-feature).
Faça commit das suas alterações (git commit -am 'Adiciona nova feature').
Envie suas alterações para o repositório remoto (git push origin minha-feature).
Abra um pull request explicando suas mudanças.

## 📝 Licença
Este projeto está licenciado sob a MIT License - veja o arquivo LICENSE.
