---
title: Atualização assíncrona para modelos de Azure Analysis Services | Microsoft Docs
description: Descreve como usar a API REST do Azure Analysis Services para codificar a atualização assíncrona de dados de modelo.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2281f9d493edf955881772ec174c82b527f1b6fa
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029872"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Atualização assíncrona com a API REST

Usando qualquer linguagem de programação que dê suporte a chamadas REST, você pode executar operações assíncronas de atualização de dados em seus Azure Analysis Services modelos de tabela. Isso inclui a sincronização de réplicas somente leitura para expansão de consulta. 

As operações de atualização de dados podem levar algum tempo, dependendo de vários fatores, incluindo o volume de dados, o nível de otimização usando partições, etc. Essas operações têm sido tradicionalmente invocadas com métodos existentes, como o uso de [Tom](https://docs.microsoft.com/bi-reference/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (modelo de objeto de tabela), cmdlets do [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) ou [TMSL](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference) (linguagem de script de modelo de tabela). No entanto, esses métodos podem exigir conexões HTTP de longa execução confiáveis com frequência.

A API REST para Azure Analysis Services permite que as operações de atualização de dados sejam executadas de forma assíncrona. Usando a API REST, as conexões HTTP de execução longa de aplicativos cliente não são necessárias. Também há outros recursos internos de confiabilidade, como tentativas automáticas e confirmações em lote.

## <a name="base-url"></a>URL Base

A URL base segue este formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Por exemplo, considere um modelo chamado AdventureWorks em um servidor chamado `myserver`, localizado na região oeste dos EUA do Azure. O nome do servidor é:

```
asazure://westus.asazure.windows.net/myserver 
```

A URL base para esse nome de servidor é:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Usando a URL base, os recursos e as operações podem ser acrescentados com base nos seguintes parâmetros: 

![Atualização assíncrona](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Tudo o que termina em **s** é uma coleção.
- Tudo que termina com **()** é uma função.
- Qualquer outra coisa é um recurso/objeto.

Por exemplo, você pode usar o verbo POST na coleção refreshs para executar uma operação de atualização:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token de Azure Active Directory (OAuth 2) válido no cabeçalho de autorização e devem atender aos seguintes requisitos:

- O token deve ser um token de usuário ou uma entidade de serviço de aplicativo.
- O token deve ter a audiência correta definida como `https://*.asazure.windows.net`.
- O usuário ou aplicativo deve ter permissões suficientes no servidor ou no modelo para fazer a chamada solicitada. O nível de permissão é determinado por funções dentro do modelo ou do grupo de administradores no servidor.

    > [!IMPORTANT]
    > Atualmente, as permissões de função de **administrador do servidor** são necessárias.

## <a name="post-refreshes"></a>POSTAR/refreshes

Para executar uma operação de atualização, use o verbo POST na coleção/refreshes para adicionar um novo item de atualização à coleção. O cabeçalho de local na resposta inclui a ID de atualização. O aplicativo cliente pode se desconectar e verificar o status mais tarde, se necessário, porque ele é assíncrono.

Apenas uma operação de atualização é aceita por vez para um modelo. Se houver uma operação atual de atualização em execução e outra for enviada, o código de status HTTP de conflito 409 será retornado.

O corpo pode ser semelhante ao seguinte:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parâmetros

Não é necessário especificar parâmetros. O padrão é aplicado.

| Nome             | Tipo  | Descrição  |Predefinição  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | O tipo de processamento a ser executado. Os tipos são alinhados com os tipos de [comando de atualização](https://docs.microsoft.com/bi-reference/tmsl/refresh-command-tmsl) TMSL: Full, clearValues, calcula, datasomente, Automatic e unfragment. Não há suporte para adicionar tipo.      |   automático      |
| `CommitMode`     | Enum  | Determina se os objetos serão confirmados em lotes ou somente quando forem concluídos. Os modos incluem: padrão, transacional, partialBatch.  |  transacional       |
| `MaxParallelism` | Int   | Esse valor determina o número máximo de threads nos quais executar comandos de processamento em paralelo. Esse valor é alinhado com a propriedade MaxParallelism que pode ser definida no [comando TMSL Sequence](https://docs.microsoft.com/bi-reference/tmsl/sequence-command-tmsl) ou usando outros métodos.       | 10        |
| `RetryCount`     | Int   | Indica o número de vezes que a operação tentará novamente antes de falhar.      |     0    |
| `Objects`        | Matriz | Uma matriz de objetos a ser processada. Cada objeto inclui: "tabela" ao processar a tabela inteira ou "tabela" e "partição" ao processar uma partição. Se nenhum objeto for especificado, todo o modelo será atualizado. |   Processar todo o modelo      |

CommitMode é igual a partialBatch. Ele é usado ao fazer uma carga inicial de conjuntos de grandes volumes que podem levar horas. Se a operação de atualização falhar depois de confirmar com êxito um ou mais lotes, os lotes confirmados com êxito permanecerão confirmados (eles não serão revertidos com êxito em lotes confirmados).

> [!NOTE]
> No momento da gravação, o tamanho do lote é o valor de MaxParallelism, mas esse valor pode ser alterado.

### <a name="status-values"></a>Valores de status

|Valor do estado  |Descrição  |
|---------|---------|
|`notStarted`    |   Operação ainda não iniciada.      |
|`inProgress`     |   Operação em andamento.      |
|`timedOut`     |    A operação atingiu o tempo limite com base no tempo limite especificado pelo usuário.     |
|`cancelled`     |   Operação cancelada pelo usuário ou pelo sistema.      |
|`failed`     |   Falha na operação.      |
|`succeeded`      |   Operação bem-sucedida.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Para verificar o status de uma operação de atualização, use o verbo GET na ID de atualização. Aqui está um exemplo do corpo da resposta. Se a operação estiver em andamento, `inProgress` será retornado no status.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>OBTER/refreshes

Para obter uma lista de operações de atualização históricas para um modelo, use o verbo GET na coleção/refreshes. Aqui está um exemplo do corpo da resposta. 

> [!NOTE]
> No momento da gravação, os últimos 30 dias de operações de atualização são armazenados e retornados, mas esse número pode ser alterado.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Para cancelar uma operação de atualização em andamento, use o verbo DELETE na ID de atualização.

## <a name="post-sync"></a>POSTAR/Sync

Após realizar operações de atualização, pode ser necessário sincronizar os novos dados com réplicas para expansão de consulta. Para executar uma operação de sincronização para um modelo, use o verbo POST na função/Sync. O cabeçalho de local na resposta inclui a ID da operação de sincronização.

## <a name="get-sync-status"></a>OBTER status de/Sync

Para verificar o status de uma operação de sincronização, use o verbo GET passando a ID da operação como um parâmetro. Veja um exemplo do corpo da resposta:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Valores para `syncstate`:

- 0: replicando. Os arquivos de banco de dados estão sendo replicados para uma pasta de destino.
- 1: reidratar. O banco de dados está sendo alimentado em instância (s) de servidor somente leitura.
- 2: concluído. A operação de sincronização foi concluída com êxito.
- 3: falha. Falha na operação de sincronização.
- 4: finalizando. A operação de sincronização foi concluída, mas está executando etapas de limpeza.

## <a name="code-sample"></a>Exemplo de código

Aqui está um C# exemplo de código para ajudá-lo a começar, [RestApiSample no GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Para usar o exemplo de código

1.  Clone ou baixe o repositório. Abra a solução RestApiSample.
2.  Localize o cliente de linha **. BaseAddress =..** . e forneça sua [URL base](#base-url).

O exemplo de código usa a autenticação de [entidade de serviço](#service-principal) .

### <a name="service-principal"></a>Principal de serviço

Consulte [criar entidade de serviço-portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md) e [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md) para obter mais informações sobre como configurar uma entidade de serviço e atribuir as permissões necessárias no Azure as. Depois de concluir as etapas, conclua as seguintes etapas adicionais:

1.  No exemplo de código, Find **String Authority =...** , substitua **Common** pela ID de locatário da sua organização.
2.  Comentar/remover marca de comentário para que a classe ClientCredential seja usada para instanciar o objeto cred. Certifique-se de que a ID do aplicativo \<> e \<valores de > de chave de aplicativo sejam acessados de forma segura ou use a autenticação baseada em certificado para entidades de serviço.
3.  Execute o exemplo.


## <a name="see-also"></a>Ver também

[Exemplos](analysis-services-samples.md)   
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


