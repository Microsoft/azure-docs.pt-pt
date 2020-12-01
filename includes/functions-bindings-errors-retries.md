---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356198"
---
Os erros levantados numa Função Azure podem vir de qualquer uma das seguintes origens:

- Utilização de funções Azure [incorporadas e encadernações](..\articles\azure-functions\functions-triggers-bindings.md)
- Chamadas para APIs dos serviços Azure subjacentes
- Chamadas para pontos finais REST
- Chamadas para bibliotecas de clientes, pacotes ou APIs de terceiros

Seguir boas práticas de manuseamento de erros é importante evitar a perda de dados ou mensagens perdidas. As práticas recomendadas de tratamento de erros incluem as seguintes ações:

- [Ativar o Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Utilizar o tratamento de erros estruturados](#use-structured-error-handling)
- [Design para idempotency](../articles/azure-functions/functions-idempotent.md)
- [Implementar políticas de reecaçação](#retry-policies-preview) (se for caso disso)

### <a name="use-structured-error-handling"></a>Utilizar o tratamento de erros estruturados

Capturar e registar erros é fundamental para monitorizar a saúde da sua aplicação. O nível mais alto de qualquer código de função deve incluir um bloco de tentativa/captura. No bloco de captura, pode capturar e registar erros.

## <a name="retry-policies-preview"></a>Políticas de retíria (pré-visualização)

Uma política de repetição pode ser definida em qualquer função para qualquer tipo de gatilho na sua aplicação de função.  A política de re-tentativa re-executa uma função até que a execução seja bem sucedida ou até que ocorra o número máximo de retrações.  As políticas de relícuro podem ser definidas para todas as funções numa aplicação ou para funções individuais.  Por predefinição, uma aplicação de função não recandidulará mensagens (para além dos [gatilhos específicos que têm uma política de repetição na fonte de gatilho).](#using-retry-support-on-top-of-trigger-resilience)  Uma política de repetição é avaliada sempre que uma execução resulta numa exceção não conseguida.  Como uma boa prática, deve pegar todas as exceções no seu código e relançar quaisquer erros que devam resultar numa nova corrida.  Os pontos de verificação de Eventos Hubs e Azure Cosmos DB não serão escritos até que a política de reagem para a execução esteja concluída, o que significa que a progressão nessa partição é interrompida até que o lote atual esteja concluído.

### <a name="retry-policy-options"></a>Redatar opções políticas

Estão disponíveis as seguintes opções para a definição de uma política de recandidologia.

**Max Retry Count** é o número máximo de vezes que uma execução é novamente julgada antes de uma eventual falha. Um valor de `-1` meios para tentar indefinidamente. A contagem atual de repetição é armazenada em memória do caso. É possível que um caso tenha um fracasso entre tentativas de repetição.  Quando um caso falha durante uma nova tentativa, a contagem de repetição é perdida.  Quando há falhas de caso, gatilhos como Os Centros de Eventos, Azure Cosmos DB e armazenamento de fila são capazes de retomar o processamento e re-tentar o lote em uma nova instância, com a contagem de repetição reset para zero.  Outros gatilhos, como HTTP e temporizador, não retomam num novo caso.  Isto significa que a contagem máxima de repetição é um melhor esforço, e em alguns casos raros uma execução pode ser novamente julgada mais do que o máximo, ou para gatilhos como HTTP e temporizador ser novamente julgado do que o máximo.

**A Estratégia de Retíria** controla o comportamento das retrórias.  Seguem-se duas opções de relíndi apoiadas:

| Opção | Descrição|
|---|---|
|**`fixedDelay`**| É permitido decorrer uma quantidade de tempo especificada entre cada relemgar,|
| **`exponentialBackoff`**| A primeira repetição aguarda o atraso mínimo. Nas retreições subsequentes, o tempo é adicionado exponencialmente à duração inicial de cada relagem, até que o atraso máximo seja atingido.  O back-off exponencial adiciona alguma pequena aleatoriedade a atrasos em retrações escalonantes em cenários de alta produção.|

#### <a name="app-level-configuration"></a>Configuração do nível de aplicativo

Uma política de repetição pode ser definida para todas as funções numa aplicação [que utiliza o `host.json` ficheiro](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Configuração do nível de função

Uma política de relícuro pode ser definida para uma função específica.  A configuração específica da função tem prioridade sobre a configuração do nível da aplicação.

#### <a name="fixed-delay-retry"></a>Redação de atraso fixo

# <a name="c"></a>[C#](#tab/csharp)

As retrações requerem pacote NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aqui está a política de repetição no *function.jsarquivado:*

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui está a política de repetição no *function.jsarquivado:*


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Aqui está a política de repetição no *function.jsarquivado:*

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Aqui está a política de repetição no *function.jsarquivado:*


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aqui está a política de repetição no *function.jsarquivado:*


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Relemisso de recuo exponencial

# <a name="c"></a>[C#](#tab/csharp)

As retrações requerem pacote NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aqui está a política de repetição no *function.jsarquivado:*

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aqui está a política de repetição no *function.jsarquivado:*

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Aqui está a política de repetição no *function.jsarquivado:*

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Aqui está a política de repetição no *function.jsarquivado:*

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aqui está a política de repetição no *function.jsarquivado:*

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.jsna propriedade  |Propriedade atributo | Descrição |
|---------|---------|---------| 
|estratégia|n/a|Obrigatório. A estratégia de repetição a utilizar. Valores válidos são `fixedDelay` ou `exponentialBackoff` .|
|maxRetryCount|n/a|Obrigatório. O número máximo de retrós assim que é permitido por execução de função. `-1` significa voltar a tentar indefinidamente.|
|atrasoInterval|n/a|O atraso que será usado entre as retretes ao utilizar a `fixedDelay` estratégia.|
|mínimoInterval|n/a|O mínimo de atraso na utilização da `exponentialBackoff` estratégia.|
|máximoInterval|n/a|O máximo de atraso na utilização da `exponentialBackoff` estratégia.| 

### <a name="retry-limitations-during-preview"></a>Redaçar limitações durante a pré-visualização

- Para projetos .NET, poderá ser necessário introduzir manualmente uma versão do [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23.
- No plano de consumo, a aplicação pode ser reduzida a zero enquanto relemissa as mensagens finais numa fila.
- No plano de consumo, a aplicação pode ser reduzida enquanto executa as retrósmas.  Para obter os melhores resultados, escolha um intervalo de repetição <= 00:01:00 e <= 5 retrírias.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Usando o suporte de retíria em cima da resiliência do gatilho

A política de relagem da aplicação de função é independente de qualquer retrete ou resiliência que o gatilho fornece.  A política de relemissão da função só irá camada em cima de uma repetição resiliente do gatilho.  Por exemplo, se utilizar o Azure Service Bus, por predefinição as filas têm uma contagem de entrega de mensagens de 10.  A contagem de entrega por defeito significa que após 10 tentativas de entrega de uma mensagem de fila, a Service Bus irá enviar a mensagem.  Pode definir uma política de repetição para uma função que tenha um gatilho de Service Bus, mas as retrações irão colocar-se em cima das tentativas de entrega do Service Bus.  

Por exemplo, se usou a contagem de entregas de 10 padrão do Service Bus e definiu uma política de relemis de função de 5.  A mensagem seria primeiro deques, aumentando a conta de entrega de autocarros de serviço para 1.  Se cada execução falhasse, após cinco tentativas de desencadear a mesma mensagem, essa mensagem seria marcada como abandonada.  A Service Bus requeiria imediatamente a mensagem, ativaria a função e aumentaria a contagem de entrega para 2.  Finalmente, após 50 tentativas eventuales (10 entregas de autocarros de serviço * cinco recauchutagens por entrega), a mensagem seria abandonada e desencadearia uma carta morta no autocarro de serviço.

> [!WARNING]
> Não é aconselhável definir a contagem de entrega para um gatilho como as filas de autocarros de serviço para 1, o que significa que a mensagem seria escrita imediatamente após um ciclo de retráfama de função única.  Isto porque os gatilhos proporcionam resiliência com retrações, enquanto a política de relemisão da função é o melhor esforço e pode resultar em menos do que o número total de retrações desejadas.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Gatilhos com resiliência adicional ou retréis

Os seguintes aciona as recauchutagens de suporte na fonte do gatilho:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento da fila Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Autocarro de serviço Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, a maioria dos pedidos de repetição de pedidos até cinco vezes. Após a quinta repetição, ambos os armazenamentos da Fila Azure escreverão uma mensagem para uma [fila de venenos](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).  A fila padrão do Service Bus e a política de tópicos escreverão uma mensagem para uma [fila de letras mortas](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) após 10 tentativas.
