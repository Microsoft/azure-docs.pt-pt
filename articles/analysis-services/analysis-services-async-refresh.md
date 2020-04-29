---
title: Atualização assíncrona para os modelos azure analysis services [ Microsoft Docs
description: Descreve como usar os Serviços de Análise Azure REST API para codificar a atualização assíncrona dos dados do modelo.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5f6cec8b7fd1169a4f04649fcaf7bb7ada33833
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406291"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Atualização assíncrona com a API REST

Ao utilizar qualquer linguagem de programação que suporte as chamadas REST, pode realizar operações de atualização de dados assíncronas nos seus modelos tabular dos Serviços de Análise Azure. Isto inclui sincronização de réplicas apenas de leitura para consulta scale-out. 

As operações de atualização de dados podem demorar algum tempo dependendo de uma série de fatores, incluindo o volume de dados, o nível de otimização usando divisórias, etc. Estas operações têm sido tradicionalmente invocadas com métodos existentes, tais como a utilização de [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Modelo de Objeto Tabular), cmdlets [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) ou [TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (Linguagem de Scriptscript modelo tabular). No entanto, estes métodos podem exigir muitas vezes ligações HTTP pouco fiáveis e de longa duração.

A API REST para serviços de análise azure permite que as operações de atualização de dados sejam realizadas de forma assíncrona. Ao utilizar a API REST, não são necessárias ligações http de aplicações de clientes a longo prazo. Existem também outras funcionalidades incorporadas para a fiabilidade, tais como retrys automáticos e compromissos em lotes.

## <a name="base-url"></a>URL Base

O URL base segue este formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Por exemplo, considere um modelo chamado `myserver`AdventureWorks num servidor chamado , localizado na região west us azure. O nome do servidor é:

```
asazure://westus.asazure.windows.net/myserver 
```

O URL base para este nome de servidor é:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Utilizando o URL base, os recursos e operações podem ser anexados com base nos seguintes parâmetros: 

![Atualização asincronizada](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Tudo o que acaba em **s** é uma coleção.
- Tudo o que termina com **()** é uma função.
- Qualquer outra coisa é um recurso/objeto.

Por exemplo, pode utilizar o verbo POST na coleção Refreshes para realizar uma operação de atualização:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token de Diretório Ativo Azure (OAuth 2) válido no cabeçalho da Autorização e devem satisfazer os seguintes requisitos:

- O símbolo deve ser um símbolo do utilizador ou um diretor de serviço de aplicação.
- O símbolo deve ter o `https://*.asazure.windows.net`público correto definido para .
- O utilizador ou aplicação deve ter permissões suficientes no servidor ou modelo para efelo a chamada solicitada. O nível de permissão é determinado por funções dentro do modelo ou pelo grupo de administração no servidor.

    > [!IMPORTANT]
    > Atualmente, são necessárias permissões de função **de administração** do servidor.

## <a name="post-refreshes"></a>POST /refreshes

Para efetuar uma operação de atualização, utilize o verbo POST na coleção /refreshes para adicionar um novo item de atualização à coleção. O cabeçalho de localização na resposta inclui o ID de atualização. A aplicação do cliente pode desligar e verificar o estado mais tarde, se necessário, porque é assíncrono.

Apenas uma operação de atualização é aceite de cada vez para um modelo. Se houver uma operação de atualização atual e outra for submetida, o código de estado do Conflito HTTP 409 é devolvido.

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

Não são necessários parâmetros de especificação. O predefinido é aplicado.

| Nome             | Tipo  | Descrição  |Predefinição  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | O tipo de processamento a realizar. Os tipos estão alinhados com os tipos de comando de [atualização](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) TMSL: completos, clarosValores, calcular, dadosApenas, automáticos e desfragmentados. Adicionar tipo não é suportado.      |   automático      |
| `CommitMode`     | Enum  | Determina se os objetos serão cometidos em lotes ou apenas quando estiverem completos. Os modos incluem: padrão, transacional, parcialmenteBatch.  |  transacional       |
| `MaxParallelism` | int   | Este valor determina o número máximo de fios para executar comandos de processamento em paralelo. Este valor alinhado com a propriedade MaxParallelism que pode ser definida no comando da [Sequência](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl) TMSL ou usando outros métodos.       | 10        |
| `RetryCount`     | int   | Indica o número de vezes que a operação voltará a tentar antes de falhar.      |     0    |
| `Objects`        | Matriz | Uma série de objetos a serem processados. Cada objeto inclui: "mesa" ao processar toda a tabela ou "mesa" e "partição" ao processar uma partição. Se não forem especificados objetos, todo o modelo é refrescado. |   Processar todo o modelo      |

CommitMode é igual a batch parcial. É usado quando se faz uma carga inicial de grandes conjuntos de dados que podem levar horas. Se a operação de atualização falhar depois de cometer com sucesso um ou mais lotes, os lotes bem sucedidos permanecerão comprometidos (não voltará a rolar com sucesso em lotes comprometidos).

> [!NOTE]
> No momento da escrita, o tamanho do lote é o valor MaxParallelism, mas este valor pode mudar.

### <a name="status-values"></a>Valores de estado

|Valor do estado  |Descrição  |
|---------|---------|
|`notStarted`    |   A operação ainda não começou.      |
|`inProgress`     |   Operação em curso.      |
|`timedOut`     |    Funcionamento cronometrado com base no tempo limite especificado do utilizador.     |
|`cancelled`     |   Operação cancelada pelo utilizador ou pelo sistema.      |
|`failed`     |   A operação falhou.      |
|`succeeded`      |   A operação foi bem sucedida.      |

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Para verificar o estado de uma operação de atualização, utilize o verbo GET no ID de atualização. Aqui está um exemplo do corpo de resposta. Se a operação estiver `inProgress` em curso, será devolvido em estado de situação.

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

Para cancelar uma operação de atualização em curso, utilize o verbo DELETE no ID de atualização.

## <a name="post-sync"></a>POST /sincronização

Depois de realizar operações de atualização, pode ser necessário sincronizar os novos dados com réplicas para consulta de escala. Para efetuar uma operação sincronizada para um modelo, utilize o verbo POST na função /sincronização. O cabeçalho de localização na resposta inclui o ID de operação de sincronização.

## <a name="get-sync-status"></a>GET /estado de sincronização

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

Valores `syncstate`para:

- 0: Replicação. Os ficheiros da base de dados estão a ser replicados para uma pasta-alvo.
- 1: Reidratação. A base de dados está a ser rehidratada em exemplos de servidores apenas de leitura.
- 2: Concluído. A operação de sincronização concluída com sucesso.
- 3: Falhou. A operação de sincronização falhou.
- 4: Finalizar. A operação de sincronização está concluída, mas está a realizar etapas de limpeza.

## <a name="code-sample"></a>Exemplo de código

Aqui está uma amostra de código C# para começar, [RestApiSample no GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Para utilizar a amostra de código

1.    Clone ou baixe o repo. Abra a solução RestApiSample.
2.    Encontre o cliente da **linha. Endereço base = ...** e fornecer o seu [URL base](#base-url).

A amostra de código utiliza a autenticação [do principal de serviço.](#service-principal)

### <a name="service-principal"></a>Service principal (Principal de serviço)

Consulte o [diretor de serviço Create - Portal Azure](../active-directory/develop/howto-create-service-principal-portal.md) e Adicione um principal de serviço ao papel de administrador do [servidor](analysis-services-addservprinc-admins.md) para obter mais informações sobre como configurar um diretor de serviço e atribuir as permissões necessárias no Azure AS. Depois de concluído os passos, complete os seguintes passos adicionais:

1.    Na amostra de código, encontre a autoridade de **cordas = ... ,** substitua o **comum** pela identificação do inquilino da sua organização.
2.    Comentário/não comentário para que a classe ClientCredential seja usada para instantaneamente o objeto de cred. Certifique-se de que os \<valores> e \<> chave da aplicação da App Key são acedidos de forma segura ou utilizam a autenticação baseada em certificados para os diretores de serviço.
3.    Execute o exemplo.


## <a name="see-also"></a>Consulte também

[Amostras](analysis-services-samples.md)   
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


