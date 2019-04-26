---
title: Introdução ao armazenamento do Azure e o Visual Studio ligados (projetos de trabalho Web) de serviços
description: Serviços ligados de como começar a utilizar o armazenamento de tabelas do Azure num projeto de WebJobs do Azure no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a9a4475465fefb01ec53e6e0eb814f9b8f192a1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390835"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Introdução ao Azure Storage (projetos de trabalho Web do Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo fornece c# exemplos de código que mostram como utilizar a versão do SDK de WebJobs do Azure 1.x com o serviço de armazenamento de tabelas do Azure. O código de exemplo utilize o [SDK do WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) versão 1.x.

O serviço de armazenamento de tabelas do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da cloud do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.  Ver [introdução ao armazenamento de tabelas do Azure com o .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) para obter mais informações.

Alguns do show de trechos de código a **tabela** atributo utilizado na funções que são chamados manualmente, ou seja, não utilizando um dos atributos de Acionador.

## <a name="how-to-add-entities-to-a-table"></a>Como adicionar entidades a uma tabela
Para adicionar entidades a uma tabela, utilize o **tabela** atributo com um **ICollector<T>**  ou **IAsyncCollector<T>**  parâmetro onde **T** Especifica o esquema das entidades que pretende adicionar. O construtor de atributo assume um parâmetro de cadeia de caracteres que especifica o nome da tabela.

O código de exemplo seguinte adiciona **pessoa** entidades a uma tabela chamada *entrada*.

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

Normalmente, o tipo que utiliza com o **ICollector** deriva **TableEntity** ou implementa **ITableEntity**, mas ele não precisa. Um dos seguintes **pessoa** classes de trabalho com o código mostrado na anterior **entrada** método.

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

Se quiser trabalhar diretamente com a API de armazenamento do Azure, pode adicionar um **CloudStorageAccount** parâmetro para a assinatura do método.

## <a name="real-time-monitoring"></a>Monitorização em tempo real
Uma vez que as funções de entrada de dados, muitas vezes, processam grandes volumes de dados, o dashboard de WebJobs SDK fornece dados de monitorização em tempo real. O **registo de invocação** secção indica se a função ainda está em execução.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

O **detalhes de invocação** página relatórios de progresso da função (número de entidades escrito) enquanto ele está em execução e dá-lhe uma oportunidade para abortá-lo.

![Função de entrada em execução](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Quando a função estiver concluída, o **detalhes de invocação** página relata o número de linhas escritas.

![Função de entrada foi concluída](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Como ler várias entidades de uma tabela
Para ler uma tabela, utilize o **tabela** atributo com um **IQueryable<T>**  parâmetro onde escrever **T** deriva de **TableEntity**ou implementa **ITableEntity**.

O código de exemplo seguinte lê e registra todas as linhas do **entrada** tabela:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Como ler uma única entidade a partir de uma tabela
Há uma **tabela** construtor de atributo com dois parâmetros adicionais que permitem especificar a chave de partição e a chave de linha, quando deseja vincular a uma entidade única tabela.

O seguinte código de exemplo lê uma linha de tabela para uma **pessoa** entidade com base na partição chave e a linha de valores de chave recebidas uma mensagem de fila:  

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


O **pessoa** classe neste exemplo não tem de implementar **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Como utilizar a API de armazenamento .NET diretamente para trabalhar com uma tabela
Também pode utilizar o **tabela** atributo com um **CloudTable** objeto para obter mais flexibilidade para trabalhar com uma tabela.

O seguinte código de exemplo utiliza um **CloudTable** objeto para adicionar uma única entidade para o *entrada* tabela.

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

Para obter mais informações sobre como utilizar o **CloudTable** objeto, consulte [introdução ao armazenamento de tabelas do Azure com o .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Tópicos relacionados abrangidos pelo artigo que mostra como as filas
Para obter informações sobre como lidar com o processamento da tabela acionado por uma mensagem de fila, ou para cenários de SDK do WebJobs não específicos do processamento da tabela, consulte [introdução ao armazenamento de filas do Azure e o Visual Studio ligados (projetos de trabalho Web) de serviços ](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Passos Seguintes
Este artigo fornece exemplos de código que mostram como lidar com cenários comuns para trabalhar com tabelas do Azure. Para obter mais informações sobre como utilizar o WebJobs do Azure e o SDK de WebJobs, consulte [recursos de documentação de WebJobs do Azure](https://go.microsoft.com/fwlink/?linkid=390226).

