---
title: 'Tutorial: Executar funções do Azure com tarefas do Azure Stream Analytics | Documentos da Microsoft'
description: Neste tutorial, irá aprender a configurar Funções do Azure como um sink de saída para tarefas do Stream Analytics.
services: stream-analytics
author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.workload: data-services
ms.date: 04/09/2018
ms.author: mamccrea
ms.reviewer: jasonh
ms.openlocfilehash: 80977c13aa9851ea5df9a15f5b9580dd1a931259
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569200"
---
# <a name="run-azure-functions-from-azure-stream-analytics-jobs"></a>Executar Funções do Azure a partir de tarefas do Azure Stream Analytics 

Pode executar Funções do Azure a partir do Azure Stream Analytics ao configurar as Funções como um dos sinks de saída para a tarefa do Stream Analytics. As Funções são uma experiência de cálculo a pedido orientada por eventos que lhe permite implementar o código que é acionado pelos eventos que ocorrem no Azure ou em serviços de terceiros. Esta capacidade que as Funções têm de responder a acionadores torna-as numa saída natural para as tarefas do Stream Analytics.

O Stream Analytics invoca Funções através de acionadores HTTP. O adaptador de saída das Funções permite aos utilizadores ligarem Funções ao Stream Analytics, para que os eventos possam ser acionados com base em consultas do Stream Analytics. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma tarefa do Stream Analytics
> * Criar uma função do Azure
> * Configurar a função do Azure como saída para a sua tarefa

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Configurar uma tarefa do Stream Analytics para executar uma função 

Esta secção demonstra como configurar uma tarefa de Stream Analytics para executar uma função que escreve dados para o Azure Cache de Redis. A tarefa do Stream Analytics lê eventos a partir dos Hubs de Eventos do Azure e executa uma consulta que invoca a função. Esta função lê os dados da tarefa do Stream Analytics e escreve-os à Cache do Azure para Redis.

![O diagrama mostra as relações entre os serviços do Azure](./media/stream-analytics-with-azure-functions/image1.png)

Os seguintes passos são precisos para realizar esta tarefa:
* [Criar uma tarefa do Stream Analytics com os Hubs de Eventos como entrada](#create-a-stream-analytics-job-with-event-hubs-as-input)  
* Criar uma Cache do Azure para a instância de Redis  
* Criar uma função nas funções do Azure que pode escrever dados para a Cache do Azure para Redis    
* [Atualizar a tarefa do Stream Analytics com a função como saída](#update-the-stream-analytics-job-with-the-function-as-output)  
* Verifique a Cache do Azure para Redis para obter resultados  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Criar uma tarefa do Stream Analytics com os Hubs de Eventos como entrada

Siga o tutorial [Deteção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) para criar um hub de eventos, inicie a aplicação do gerador de eventos e crie uma tarefa do Stream Analytics. (Ignore os passos para criar a consulta e a saída. Em vez disso, veja as secções seguintes para configurar a saída de Funções.)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma Cache do Azure para a instância de Redis

1. Criar uma cache na Cache do Azure para Redis com os passos descritos em [criar uma cache](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Depois de criar a cache em **Definições**, selecione **Chaves de Acesso**. Anote a **Cadeia de ligação primária**.

   ![Captura de ecrã do Azure Cache para a cadeia de ligação de Redis](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Criar uma função nas funções do Azure que possa escrever dados no Cache do Azure para Redis

1. Veja a secção [Criar uma aplicação de funções](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) na documentação das Funções. Esta secção explica-lhe como criar uma aplicação de funções e uma [função acionada por HTTP nas Funções do Azure](../azure-functions/functions-create-first-azure-function.md#create-function), através da linguagem de CSharp.  

2. Navegue para a função **run.csx**. Atualize-a com o seguinte código. (Certifique-se substituir "\<sua Cache do Azure para a cadeia de ligação de Redis aqui\>" com a Cache do Azure para a cadeia de ligação primária do Redis que obteve na secção anterior.)  

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Quando o Stream Analytics recebe a exceção "Entidade do Pedido HTTP Demasiado Grande" da função, reduz o tamanho dos lotes que envia para as Funções. Na sua função, utilize o seguinte código para verificar que o Stream Analytics não envia lotes demasiado grandes. Certifique-se de que os valores de contagem e tamanho de lote máximo utilizados na função são consistentes com os valores introduzidos no portal do Stream Analytics.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. Num editor de texto à sua escolha, crie um ficheiro JSON designado **project.json**. Utilize o seguinte código e guarde-o no seu computador local. Este ficheiro contém as dependências do pacote NuGet precisas para a função de C#.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Regresse ao portal do Azure. A partir do separador **Funcionalidades da plataforma**, navegue até à sua função. Em **Ferramentas de Desenvolvimento**, selecione **Editor do Serviço de Aplicações**. 
 
   ![Captura de ecrã do Editor do Serviço de Aplicações](./media/stream-analytics-with-azure-functions/image3.png)

5. No Editor do Serviço de Aplicações, clique com o botão direito do rato no seu diretório de raiz e carregue o ficheiro **project.json**. Depois de o carregamento ser bem-sucedido, atualize a página. Deverá ver um ficheiro gerado automaticamente com o nome **project.lock.json**. O ficheiro gerado automaticamente contém referências aos ficheiros .dll que são especificados no ficheiro project.json.  

   ![Captura de ecrã do Editor do Serviço de Aplicações](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Atualizar a tarefa do Stream Analytics com a função como saída

1. Abra a sua tarefa do Stream Analytics no portal do Azure.  

2. Navegue para a sua função e selecione **Descrição Geral** > **Saídas** > **Adicionar**. Para adicionar uma nova saída, selecione **Função do Azure** para a opção de sink. O adaptador de saída das funções tem as seguintes propriedades:  

   |**Nome da propriedade**|**Descrição**|
   |---|---|
   |Alias de saída| Um nome de utilizador amigável que utiliza na consulta da tarefa para a referência de saída. |
   |Opção de Importar| Pode utilizar a função da subscrição atual ou fornecer as definições manualmente se a função estiver localizada noutra subscrição. |
   |Aplicação de Funções| Nome da sua aplicação de Funções. |
   |Função| Nome da função na sua Aplicação de funções (nome da sua função run.csx).|
   |Tamanho Máx. de Lote|Define o tamanho máximo para cada lote de saída que é enviado para a sua função em bytes. Por predefinição, este valor é definido como 262.144 bytes (256 KB).|
   |Contagem Máx. de Lotes|Especifica o número máximo de eventos em cada lote que é enviado para a função. O valor predefinido é 100. Esta propriedade é opcional.|
   |Chave|Permite-lhe utilizar uma função a partir de outra subscrição. Indique o valor da chave para aceder à sua função. Esta propriedade é opcional.|

3. Indique um nome para o alias de saída. Neste tutorial, designamos de **saop1** (pode utilizar qualquer nome à sua escolha). Preencha outros detalhes.  

4. Abra a sua tarefa do Stream Analytics e atualize a consulta para o seguinte. (Certifique-se de que substitui o texto "saop1" se tiver dado outra designação ao sink de saída.)  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Inicie a aplicação telcodatagen.exe ao executar o seguinte comando na linha de comandos (utilize o formato `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Inicie a tarefa do Stream Analytics.

## <a name="check-azure-cache-for-redis-for-results"></a>Verifique a Cache do Azure para Redis para obter resultados

1. Navegue para o portal do Azure e encontre a sua Cache do Azure para Redis. Selecione **Consola**.  

2. Uso [Cache do Azure para os comandos da Redis](https://redis.io/commands) para verificar que os dados estão na Cache do Azure para Redis. (O comando assume o formato Get {key}.) Por exemplo:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Este comando deve imprimir o valor para a chave especificada:

   ![Captura de ecrã de Cache do Azure para saída de Redis](./media/stream-analytics-with-azure-functions/image5.png)
   
## <a name="error-handling-and-retries"></a>Processamento de erros e tentativas
Se ocorrer uma falha ao enviar eventos para as Funções do Azure, o Stream Analytics tentará novamente concluir a operação com êxito. No entanto, existem algumas falhas para as quais não são executadas tentativas, que são as seguintes:

 1. HttpRequestExceptions
 2. Entidade do Pedido Demasiado Grande (código de erro http 413)
 3. ApplicationExceptions

## <a name="known-issues"></a>Problemas conhecidos

No portal do Azure, quando tenta repor o Tamanho Máx. de Lote/Contagem Máx. de Lotes para vazio (predefinição), o valor regressa ao valor introduzido anteriormente após guardar. Introduza manualmente os valores predefinidos para estes campos neste caso.

A utilização de [encaminhamento Http](https://docs.microsoft.com/sandbox/functions-recipes/routes?tabs=csharp) nas suas funções do Azure não é atualmente suportada pelo Stream Analytics.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou.  
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, irá criar uma tarefa do Stream Analytics simples que executa uma Função do Azure, para obter mais informações sobre tarefas do Stream Analytics, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Executar funções definidas pelo utilizador do JavaScript nas tarefas do Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
