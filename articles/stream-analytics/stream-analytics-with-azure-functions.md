---
title: Tutorial - Executar funções Azure em trabalhos Azure Stream Analytics
description: Neste tutorial, irá aprender a configurar Funções do Azure como um sink de saída para tarefas do Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: 74e09e61a6132858d716686bdb6687bb670f0d33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879515"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>Tutorial: Executar funções Azure a partir de trabalhos Azure Stream Analytics 

Pode executar Funções do Azure a partir do Azure Stream Analytics ao configurar as Funções como um dos sinks de saída para a tarefa do Stream Analytics. As Funções são uma experiência de cálculo a pedido orientada por eventos que lhe permite implementar o código que é acionado pelos eventos que ocorrem no Azure ou em serviços de terceiros. Esta capacidade que as Funções têm de responder a acionadores torna-as numa saída natural para as tarefas do Stream Analytics.

O Stream Analytics invoca Funções através de acionadores HTTP. O adaptador de saída das Funções permite aos utilizadores ligarem Funções ao Stream Analytics, para que os eventos possam ser acionados com base em consultas do Stream Analytics. 

> [!NOTE]
> A ligação às funções do Azure dentro de uma rede virtual (VNet) a partir de um trabalho stream Analytics que está a funcionar num cluster multi-inquilino não é suportada.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e executar um trabalho stream Analytics
> * Criar uma Cache Azure para a instância Redis
> * Criar uma Função do Azure
> * Verifique a cache de Azure para obter resultados

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Configurar uma tarefa do Stream Analytics para executar uma função 

Esta secção demonstra como configurar um trabalho stream Analytics para executar uma função que escreve dados para Azure Cache para Redis. A tarefa do Stream Analytics lê eventos a partir dos Hubs de Eventos do Azure e executa uma consulta que invoca a função. Esta função lê dados do trabalho stream Analytics, e escreve-os para Azure Cache para Redis.

![O diagrama mostra as relações entre os serviços do Azure](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Criar uma tarefa do Stream Analytics com os Hubs de Eventos como entrada

Siga o tutorial [Deteção de fraudes em tempo real](stream-analytics-real-time-fraud-detection.md) para criar um hub de eventos, inicie a aplicação do gerador de eventos e crie uma tarefa do Stream Analytics. Salte os passos para criar a consulta e a saída. Em vez disso, consulte as seguintes secções para configurar uma saída de Funções Azure.

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma Cache Azure para a instância Redis

1. Criar uma cache em Cache Azure para Redis utilizando os passos descritos na [Criar uma cache](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Depois de criar a cache em **Definições**, selecione **Chaves de Acesso**. Anote a **Cadeia de ligação primária**.

   ![Screenshot de Azure Cache para fio de ligação Redis](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Criar uma função em Funções Azure que pode escrever dados para Azure Cache para Redis

1. Veja a secção [Criar uma aplicação de funções](../azure-functions/functions-get-started.md) na documentação das Funções. Esta secção explica-lhe como criar uma aplicação de função e uma [função acionada por HTTP em Funções Azure](../azure-functions/functions-get-started.md), utilizando o idioma CSharp.  

2. Navegue para a função **run.csx**. Atualize-a com o seguinte código. Substitua **" \<your Azure Cache for Redis connection string goes here\> "** com a cache Azure para a cadeia de ligação primária Redis que recuperou na secção anterior. 

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

   Quando o Stream Analytics recebe a exceção "Entidade do Pedido HTTP Demasiado Grande" da função, reduz o tamanho dos lotes que envia para as Funções. O seguinte código garante que o Stream Analytics não envia lotes de grandes dimensões. Certifique-se de que os valores de contagem e tamanho de lote máximo utilizados na função são consistentes com os valores introduzidos no portal do Stream Analytics.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. Num editor de texto à sua escolha, crie um ficheiro JSON designado **project.json**. Cole o seguinte código e guarde-o no computador local. Este ficheiro contém as dependências do pacote NuGet precisas para a função de C#.  
   
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
 
   ![O Screenshot mostra o separador de funcionalidades da Plataforma com o Editor de Serviço de Aplicações selecionado.](./media/stream-analytics-with-azure-functions/image3.png)

5. No Editor do Serviço de Aplicações, clique com o botão direito do rato no seu diretório de raiz e carregue o ficheiro **project.json**. Depois de o carregamento ser bem-sucedido, atualize a página. Deverá ver um ficheiro gerado automaticamente com o nome **project.lock.json**. O ficheiro gerado automaticamente contém referências aos ficheiros .dll que são especificados no ficheiro project.json.  

   ![A screenshot mostra upload Files selecionados a partir do menu.](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Atualizar a tarefa do Stream Analytics com a função como saída

1. Abra a sua tarefa do Stream Analytics no portal do Azure.  

2. Navegue pela sua função e selecione  >  **Overview Outputs**  >  **Add**. Para adicionar uma nova saída, selecione **Função do Azure** para a opção de sink. O adaptador de saída de Funções tem as seguintes propriedades:  

   |**Nome da propriedade**|**Descrição**|
   |---|---|
   |Alias de saída| Um nome de utilizador amigável que utiliza na consulta da tarefa para a referência de saída. |
   |Opção de Importar| Pode utilizar a função da subscrição atual ou fornecer as definições manualmente se a função estiver localizada noutra subscrição. |
   |Aplicação de Funções| Nome da sua aplicação de Funções. |
   |Função| Nome da função na sua Aplicação de funções (nome da sua função run.csx).|
   |Tamanho Máx. de Lote|Define o tamanho máximo para cada lote de saída, que é enviado para a sua função em bytes. Por padrão, este valor é definido para 262.144 bytes (256 KB).|
   |Contagem Máx. de Lotes|Especifica o número máximo de eventos em cada lote que é enviado para a função. O valor predefinido é 100. Esta propriedade é opcional.|
   |Chave|Permite-lhe utilizar uma função a partir de outra subscrição. Indique o valor da chave para aceder à sua função. Esta propriedade é opcional.|

3. Indique um nome para o alias de saída. Neste tutorial, chama-se **saop1,** mas pode usar qualquer nome à sua escolha. Preencha outros detalhes.

4. Abra a sua tarefa do Stream Analytics e atualize a consulta para o seguinte. Se não nomeou o **saop1 da** pia de saída, lembre-se de alterá-la na consulta.  

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

5. Inicie a aplicação telcodatagen.exe executando o seguinte comando na linha de comando. O comando utiliza o formato `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]` .  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Inicie a tarefa do Stream Analytics.

## <a name="check-azure-cache-for-redis-for-results"></a>Verifique a cache de Azure para obter resultados

1. Navegue pelo portal Azure e encontre o seu Cache Azure para Redis. Selecione **Consola**.  

2. Utilize [a Cache Azure para comandos Redis](https://redis.io/commands) para verificar se os seus dados estão em Cache Azure para Redis. (O comando toma o formato Get {key}.) Por exemplo:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Este comando deve imprimir o valor para a chave especificada:

   ![Screenshot de Azure Cache para saída redis](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>Processamento de erros e tentativas

Se ocorrer uma falha durante o envio de eventos para as Funções Azure, o Stream Analytics retrição na maioria das operações. Todas as exceções http são novamente julgadas até ao sucesso, com exceção do erro http 413 (entidade demasiado grande). Uma entidade demasiado grande é tratada como um erro de dados que está sujeito à [política de re-tentativa ou de abandono.](stream-analytics-output-error-policy.md)

> [!NOTE]
> O tempo limite para pedidos HTTP de Stream Analytics para Funções Azure está definido para 100 segundos. Se a sua aplicação Azure Functions demorar mais de 100 segundos a processar um lote, o Stream Analytics falha e vai voltar a recorrer ao lote.

A reagem aos intervalos pode resultar em eventos duplicados escritos na pia de saída. Quando stream Analytics recauchutado para um lote falhado, ele retrimba para todos os eventos no lote. Por exemplo, considere um lote de 20 eventos que são enviados para as Funções Azure da Stream Analytics. Assuma que as Funções Azure demoram 100 segundos a processar os primeiros 10 eventos nesse lote. Após o passe de 100 segundos, o Stream Analytics suspende o pedido uma vez que não recebeu uma resposta positiva da Azure Functions, e outro pedido é enviado para o mesmo lote. Os primeiros 10 eventos no lote são processados novamente pela Azure Functions, o que provoca uma duplicação. 

## <a name="known-issues"></a>Problemas conhecidos

No portal do Azure, quando tenta repor o Tamanho Máx. de Lote/Contagem Máx. de Lotes para vazio (predefinição), o valor regressa ao valor introduzido anteriormente após guardar. Introduza manualmente os valores predefinidos para estes campos neste caso.

A utilização do [encaminhamento HTTP](/sandbox/functions-recipes/routes?tabs=csharp) nas suas Funções Azure não é atualmente suportada pelo Stream Analytics.

O suporte para ligar às Funções Azure hospedadas numa rede virtual não está ativado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou.  
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um simples trabalho de Stream Analytics, que executa uma Função Azure. Para saber mais sobre tarefas do Stream Analytics, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Executar funções definidas pelo utilizador do JavaScript nas tarefas do Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
