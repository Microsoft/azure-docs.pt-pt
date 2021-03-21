---
title: Atualização assíncrona para os modelos Azure Analysis Services | Microsoft Docs
description: Descreve como utilizar os Serviços de Análise Azure REST API para codificar atualização assíncrona dos dados do modelo.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: e9fd20fd42e9fe1eb0e98766798e5c759c974c97
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92013904"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Atualização assíncrona com a API REST

Ao utilizar qualquer linguagem de programação que suporte chamadas REST, pode executar operações assíncronas de atualização de dados nos seus modelos tabulares dos Azure Analysis Services. Isto inclui a sincronização de réplicas apenas de leitura para a escala de consulta. 

As operações de atualização de dados podem demorar algum tempo, dependendo de uma série de fatores, incluindo o volume de dados, o nível de otimização usando divisórias, etc. Estas operações têm sido tradicionalmente invocadas com métodos existentes, tais como a utilização de [TOM](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Modelo de Objeto Tabular), cmdlets [PowerShell](/analysis-services/powershell/analysis-services-powershell-reference) ou [TMSL](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (Linguagem de Scripting de Modelo Tabular). No entanto, estes métodos podem exigir muitas vezes ligações HTTP de longa duração e pouco fiáveis.

A API REST para serviços de análise Azure permite que as operações de atualização de dados sejam realizadas de forma assíncrona. Ao utilizar a API REST, não são necessárias ligações HTTP de longa duração a partir de aplicações do cliente. Existem também outras características incorporadas para a fiabilidade, tais como auto-retréis e compromissos em massa.

## <a name="base-url"></a>URL Base

O URL base segue este formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Por exemplo, considere um modelo chamado AdventureWorks num servidor chamado `myserver` , localizado na região de West US Azure. O nome do servidor é:

```
asazure://westus.asazure.windows.net/myserver 
```

O URL base para este nome do servidor é:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Utilizando o URL de base, os recursos e operações podem ser anexados com base nos seguintes parâmetros: 

![Refrescamento assínc](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Tudo o que acaba em **s** é uma coleção.
- Qualquer coisa que termine com **()** é uma função.
- Qualquer outra coisa é um recurso/objeto.

Por exemplo, pode utilizar o verbo POST na coleção Refreshes para realizar uma operação de atualização:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token de Azure Ative (Oauth 2) válido no cabeçalho de autorização e devem satisfazer os seguintes requisitos:

- O símbolo deve ser um símbolo do utilizador ou um diretor de serviço de aplicação.
- O símbolo deve ter o público correto definido para `https://*.asazure.windows.net` .
- O utilizador ou aplicação deve ter permissões suficientes no servidor ou modelo para esemer a chamada solicitada. O nível de permissão é determinado por funções dentro do modelo ou do grupo de administração no servidor.

    > [!IMPORTANT]
    > Atualmente, as permissões de função **de administração** do servidor são necessárias.

## <a name="post-refreshes"></a>POST /refreshes

Para realizar uma operação de atualização, utilize o verbo POST na coleção /refreshes para adicionar um novo item de atualização à coleção. O cabeçalho de localização na resposta inclui o ID de atualização. A aplicação do cliente pode desligar e verificar o estado mais tarde, se necessário, porque é assíncronos.

Apenas uma operação de atualização é aceite de cada vez para um modelo. Se houver uma operação atual de atualização em execução e outra for submetida, o código de estado 409 Conflict HTTP é devolvido.

O corpo pode assemelhar-se ao seguinte:

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

| Nome             | Tipo  | Description  |Predefinição  |
|------------------|-------|--------------|---------|
| `Type`           | Enumeração  | O tipo de processamento a realizar. Os tipos estão alinhados com os tipos de [comando de atualização](/analysis-services/tmsl/refresh-command-tmsl) TMSL: full, clearValues, calcular, dataOnly, automático e desfragment. O tipo de adicionar não é suportado.      |   automático      |
| `CommitMode`     | Enumeração  | Determina se os objetos serão cometidos em lotes ou apenas quando estiverem completos. Os modos incluem: predefinitivo, transacional, parcialbatch.  |  transacional       |
| `MaxParallelism` | int   | Este valor determina o número máximo de fios para executar comandos de processamento em paralelo. Este valor alinhado com a propriedade MaxParallelism que pode ser definido no comando TMSL [Sequence](/analysis-services/tmsl/sequence-command-tmsl) ou usando outros métodos.       | 10        |
| `RetryCount`     | int   | Indica o número de vezes que a operação tentará novamente antes de falhar.      |     0    |
| `Objects`        | Matriz | Uma série de objetos a serem processados. Cada objeto inclui: "tabela" ao processar toda a mesa ou "mesa" e "partição" ao processar uma partição. Se não forem especificados objetos, todo o modelo é atualizado. |   Processar todo o modelo      |

CommitMode é igual a ParcialBatch. É usado ao fazer uma carga inicial de grandes conjuntos de dados que podem demorar horas. Se a operação de atualização falhar após a emissão de um ou mais lotes com sucesso, os lotes bem comprometidos permanecerão comprometidos (não reverterá os lotes comprometidos com sucesso).

> [!NOTE]
> No momento da escrita, o tamanho do lote é o valor MaxParallelism, mas este valor pode mudar.

### <a name="status-values"></a>Valores de estado

|Valor do estado  |Description  |
|---------|---------|
|`notStarted`    |   A operação ainda não começou.      |
|`inProgress`     |   Operação em curso.      |
|`timedOut`     |    Tempo de funcionamento esgotado com base no tempo limite especificado pelo utilizador.     |
|`cancelled`     |   Operação cancelada por utilizador ou sistema.      |
|`failed`     |   A operação falhou.      |
|`succeeded`      |   A operação foi bem sucedida.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Para verificar o estado de uma operação de atualização, utilize o verbo GET no ID de atualização. Aqui está um exemplo do corpo de resposta. Se a operação estiver em curso, `inProgress` é devolvida em estado.

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

## <a name="get-refreshes"></a>GET /refreshes

Para obter uma lista de operações históricas de atualização para um modelo, use o verbo GET na coleção /refreshes. Aqui está um exemplo do corpo de resposta. 

> [!NOTE]
> No momento da escrita, os últimos 30 dias de operações de atualização são armazenados e devolvidos, mas este número pode mudar.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-07T02:06:57.1838734Z",
        "endTime": "2017-12-07T02:07:00.4929675Z",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T01:05:54.157324Z",
        "endTime": "2017-12-07T01:05:57.353371Z",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Para cancelar uma operação de atualização em curso, utilize o verbo DELETE no ID de atualização.

## <a name="post-sync"></a>POST /sincronização

Depois de ter realizado operações de atualização, pode ser necessário sincronizar os novos dados com réplicas para consulta à escala. Para efetuar uma operação de sincronização para um modelo, utilize o verbo POST na função /sync. O cabeçalho de localização na resposta inclui o ID de operação de sincronização.

## <a name="get-sync-status"></a>Estado GET /sync

Para verificar o estado de uma operação de sincronização, utilize o verbo GET passando o ID de funcionamento como parâmetro. Aqui está um exemplo do corpo de resposta:

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

Valores `syncstate` para:

- 0: Replicação. Os ficheiros da base de dados estão a ser replicados numa pasta alvo.
- 1: Reidratação. A base de dados está a ser rehidratada em instâncias de servidor apenas de leitura.
- 2: Concluído. A operação de sincronização foi concluída com sucesso.
- 3: Falhou. A operação de sincronização falhou.
- 4: Finalização. A operação de sincronização terminou mas está a executar etapas de limpeza.

## <a name="code-sample"></a>Exemplo de código

Aqui está uma amostra de código C# para começar, [RestApiSample no GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Para utilizar a amostra de código

1.    Clone ou descarregue o repo. Abra a solução RestApiSample.
2.    Encontre o cliente da **linha. BaseAddress = ...** e fornecer o seu [URL base](#base-url).

A amostra de código utiliza a autenticação [principal do serviço.](#service-principal)

### <a name="service-principal"></a>Service principal (Principal de serviço)

Consulte [Criar o principal do serviço - Portal Azure](../active-directory/develop/howto-create-service-principal-portal.md) e Adicionar um principal de serviço à [função de administrador do servidor](analysis-services-addservprinc-admins.md) para obter mais informações sobre como configurar um principal de serviço e atribuir as permissões necessárias no Azure AS. Uma vez concluídos os passos, complete os seguintes passos adicionais:

1.    Na amostra de código, encontre **a autoridade das cordas = ... ,** substitua o **comum** com a identificação do inquilino da sua organização.
2.    Comentário/descompromisso para que a classe ClientCredential seja usada para instantaneamente o objeto de crédito. Certifique-se de que os \<App ID> \<App Key> valores e valores são acedidos de forma segura ou utilize a autenticação baseada em certificados para os principais serviços.
3.    Execute o exemplo.


## <a name="see-also"></a>Ver também

[Amostras](analysis-services-samples.md)   
[API REST](/rest/api/analysisservices/servers)