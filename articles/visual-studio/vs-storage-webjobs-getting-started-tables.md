---
title: Começar com o armazenamento Azure usando o Visual Studio (projetos WebJob)
description: Como começar a usar o armazenamento da Mesa Azure num projeto Azure WebJobs no Estúdio Visual depois de se ligar a uma conta de armazenamento usando serviços conectados do Estúdio Visual
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e4d8299c06bfa5b0f33bff8fa592a2fa549c695c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74707609"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Começar com o Armazenamento Azure (Projetos Azure WebJob)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece amostras de código C# que mostram como usar a versão 1.x Azure WebJobs SDK com o serviço de armazenamento de mesa Azure. As amostras de código utilizam a versão 1.x do [WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

O serviço de armazenamento de mesa azure permite armazenar grandes quantidades de dados estruturados. O serviço é uma loja de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.  Consulte [O Get start with Azure Table storage using .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) for more information.

Alguns dos fragmentos de código mostram o atributo da **Tabela** utilizado em funções que são chamadas manualmente, isto é, não utilizando um dos atributos do gatilho.

## <a name="how-to-add-entities-to-a-table"></a>Como adicionar entidades a uma mesa

Para adicionar entidades a uma tabela, utilize o atributo da **Tabela** com um **iCollector\<T>** ou **iAsyncCollector\<T>** parâmetro onde **t** especifica o esquema das entidades que pretende adicionar. O construtor de atributos pega num parâmetro de corda que especifica o nome da mesa.

A amostra de código seguinte adiciona as entidades **pessoas** a uma tabela chamada *Ingress*.

```csharp
[NoAutomaticTrigger]
public static void IngressDemo(
    [Table("Ingress")] ICollector<Person> tableBinding)
{
    for (int i = 0; i < 100000; i++)
    {
        tableBinding.Add(
            new Person() {
                PartitionKey = "Test",
                RowKey = i.ToString(),
                Name = "Name" }
            );
    }
}
```

Normalmente, o tipo que utiliza com **o ICollector** deriva da **TableEntity** ou implementa **o ITableEntity,** mas não tem de o fazer. Qualquer uma das seguintes aulas **de pessoa** trabalha com o código mostrado no método **Ingress** anterior.

```csharp
public class Person : TableEntity
{
    public string Name { get; set; }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Se quiser trabalhar diretamente com a API de armazenamento Azure, pode adicionar um parâmetro **CloudStorageAccount** à assinatura do método.

## <a name="real-time-monitoring"></a>Monitorização em tempo real

Como as funções de ingresso de dados muitas vezes processam grandes volumes de dados, o dashboard WebJobs SDK fornece dados de monitorização em tempo real. A secção Registo de **Invocação** diz-lhe se a função ainda está em funcionamento.

![Funcionamento da função ingresso](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A página De detalhes de **invocação** relata o progresso da função (número de entidades escritas) enquanto está em execução e dá-lhe a oportunidade de abortá-la.

![Funcionamento da função ingresso](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Quando a função termina, a página **Informação de Informação** informa o número de linhas escritas.

![Função ingressa terminada](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Como ler várias entidades a partir de uma mesa

Para ler uma tabela, utilize o atributo da **Tabela** com um parâmetro **IQueryable\<T>** onde o tipo **T** deriva da **TableEntity** ou implementa **a ITableEntity**.

A seguinte amostra de código lê e regista todas as linhas da tabela **Ingress:**

```csharp
public static void ReadTable(
    [Table("Ingress")] IQueryable<Person> tableBinding,
    TextWriter logger)
{
    var query = from p in tableBinding select p;
    foreach (Person person in query)
    {
        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
            person.PartitionKey, person.RowKey, person.Name);
    }
}
```

### <a name="how-to-read-a-single-entity-from-a-table"></a>Como ler uma única entidade a partir de uma mesa

Existe um construtor de **atributos tabela** com dois parâmetros adicionais que lhe permitem especificar a chave de partição e a chave de linha quando pretende ligar-se a uma única entidade de tabela.

A seguinte amostra de código lê uma linha de tabela para uma entidade **pessoa** com base na chave de partilha e valores-chave da linha recebidos numa mensagem de fila:

```csharp
public static void ReadTableEntity(
    [QueueTrigger("inputqueue")] Person personInQueue,
    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
    TextWriter logger)
{
    if (personInTable == null)
    {
        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                personInQueue.PartitionKey, personInQueue.RowKey);
    }
    else
    {
        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
    }
}
```

A classe **Pessoa** neste exemplo não tem de implementar **o ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Como utilizar a API de Armazenamento .NET diretamente para trabalhar com uma tabela

Também pode utilizar o atributo da **Tabela** com um objeto **CloudTable** para uma maior flexibilidade no trabalho com uma tabela.

A amostra de código seguinte utiliza um objeto **CloudTable** para adicionar uma única entidade à tabela *Ingress.*

```csharp
public static void UseStorageAPI(
    [Table("Ingress")] CloudTable tableBinding,
    TextWriter logger)
{
    var person = new Person()
        {
            PartitionKey = "Test",
            RowKey = "100",
            Name = "Name"
        };
    TableOperation insertOperation = TableOperation.Insert(person);
    tableBinding.Execute(insertOperation);
}
```

Para obter mais informações sobre como utilizar o objeto **CloudTable,** consulte Iniciar com [armazenamento de mesa Azure utilizando .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tópicos relacionados cobertos pelas filas como-fazer artigo

Para obter informações sobre como lidar com o processamento de tabelas desencadeado por uma mensagem de fila, ou para cenários WebJobs SDK não específicos para o processamento de tabelas, consulte [Getting started with Azure Queue storage and Visual Studio connected services (WebJob Projects)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu amostras de código que mostram como lidar com cenários comuns para trabalhar com tabelas Azure. Para obter mais informações sobre como utilizar o Azure WebJobs e o WebJobs SDK, consulte [os recursos de documentação azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).
