---
title: Introdução com o armazenamento do Azure usando o Visual Studio (projetos de trabalho Web)
description: Como começar a usar o armazenamento de tabelas do Azure em um projeto Azure WebJobs no Visual Studio depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
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
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707609"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Introdução com o armazenamento do Azure (projetos do Azure WebJob)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral
Este artigo fornece C# exemplos de código que mostram como usar o SDK do Azure WebJobs versão 1. x com o serviço de armazenamento de tabelas do Azure. Os exemplos de código usam o [SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki) Web versão 1. x.

O serviço de armazenamento de tabelas do Azure permite que você armazene grandes quantidades de dados estruturados. O serviço é um repositório de armazenamento NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.  Consulte [introdução ao armazenamento de tabelas do Azure usando o .net](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) para obter mais informações.

Alguns dos trechos de código mostram o atributo **Table** usado em funções que são chamadas manualmente, ou seja, não usando um dos atributos Trigger.

## <a name="how-to-add-entities-to-a-table"></a>Como adicionar entidades a uma tabela

Para adicionar entidades a uma tabela, use o atributo **Table** com um parâmetro **ICollector\<t >** ou **IAsyncCollector\<t >** , em que **t** especifica o esquema das entidades que você deseja adicionar. O construtor de atributo usa um parâmetro de cadeia de caracteres que especifica o nome da tabela.

O exemplo de código a seguir adiciona entidades **Person** a uma tabela chamada *ingress*.

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

Normalmente, o tipo usado com **ICollector** deriva de **TableEntity** ou implementa **ITableEntity**, mas não é necessário. Uma das seguintes classes **Person** funciona com o código mostrado no método de **entrada** anterior.

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

Se você quiser trabalhar diretamente com a API de armazenamento do Azure, poderá adicionar um parâmetro **CloudStorageAccount** à assinatura do método.

## <a name="real-time-monitoring"></a>Monitoramento em tempo real

Como as funções de entrada de dados geralmente processam grandes volumes de dados, o painel do SDK de trabalhos Web fornece dados de monitoramento em tempo real. A seção **log de invocação** informa se a função ainda está em execução.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A página de **detalhes de invocação** informa o progresso da função (número de entidades gravadas) enquanto ela está em execução e lhe dá a oportunidade de abortar.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Quando a função for concluída, a página de **detalhes de invocação** relatará o número de linhas gravadas.

![Função de entrada concluída](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Como ler várias entidades de uma tabela

Para ler uma tabela, use o atributo **Table** com um **parâmetro IQueryable\<t >** em que o tipo **t** deriva de **TableEntity** ou implementa **ITableEntity**.

O exemplo de código a seguir lê e registra em log todas as linhas da tabela de **entrada** :

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Como ler uma única entidade de uma tabela

Há um construtor de atributo de **tabela** com dois parâmetros adicionais que permitem especificar a chave de partição e a chave de linha quando você deseja associar a uma única entidade de tabela.

O exemplo de código a seguir lê uma linha de tabela para uma entidade **Person** com base nos valores de chave de partição e de linha recebidos em uma mensagem da fila:

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

A classe **Person** neste exemplo não precisa implementar **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Como usar a API de armazenamento do .NET diretamente para trabalhar com uma tabela

Você também pode usar o atributo **Table** com um objeto **cloudtable** para obter mais flexibilidade ao trabalhar com uma tabela.

O exemplo de código a seguir usa um objeto **cloudtable** para adicionar uma única entidade à tabela de *entrada* .

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

Para obter mais informações sobre como usar o objeto **cloudtable** , consulte Introdução [ao armazenamento de tabelas do Azure usando o .net](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tópicos relacionados abordados no artigo de instruções sobre filas

Para obter informações sobre como tratar o processamento de tabela disparado por uma mensagem da fila ou para cenários do SDK de trabalhos Web não específicos do processamento de tabelas, consulte [introdução ao armazenamento de filas do Azure e aos serviços conectados do Visual Studio (projetos de trabalho Web)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com tabelas do Azure. Para obter mais informações sobre como usar Azure WebJobs e o SDK de trabalhos Web, consulte [Azure WebJobs recursos de documentação](https://go.microsoft.com/fwlink/?linkid=390226).
