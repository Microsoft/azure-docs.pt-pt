---
title: Atualização assíncrona para modelos do Azure Analysis Services | Documentos da Microsoft
description: Saiba como codificar atualização assíncrona com REST API.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e9558eae43b351aa198b64bb2a7903c756064c2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168022"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Atualização assíncrona com a API REST

Ao utilizar qualquer linguagem de programação que suporte chamadas REST, pode efetuar operações de atualização de dados assíncrona em seus modelos em tabela do Azure Analysis Services. Isto inclui a sincronização de réplicas só de leitura para aumentar horizontalmente a consulta. 

Operações de atualização de dados podem demorar algum tempo dependendo de vários fatores, incluindo o volume de dados, ao nível da otimização com partições, etc. Estas operações têm tradicionalmente foi invocadas com métodos existentes, como o uso [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (modelo de objeto em tabela), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) cmdlets, ou [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (modelo em tabela Linguagem de script). No entanto, esses métodos podem exigir, muitas vezes, as ligações de HTTP não confiáveis, de longa execução.

A API de REST para o Azure Analysis Services permite operações de atualização de dados a ser executadas assincronamente. Ao utilizar a API REST, as ligações HTTP de longa execução de aplicativos de cliente não necessárias. Também existem outros recursos internos de confiabilidade, como repetições automática e em lote consolidações.

## <a name="base-url"></a>URL Base

O URL base segue este formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Por exemplo, considere um modelo com o nome AdventureWorks num servidor com o nome myserver, localizada na região do Azure do Oeste-nos. O nome do servidor é:

```
asazure://westus.asazure.windows.net/myserver 
```

O URL de base para este nome de servidor é:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Ao utilizar o URL de base, recursos e operações podem ser anexadas com base nos parâmetros seguintes: 

![Atualização de Async](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Tudo o que termina em **s** é uma coleção.
- Tudo o que termina com **()** é uma função.
- Qualquer outra coisa é um recurso/objeto.

Por exemplo, pode utilizar o verbo POST na coleção de atualizações para efetuar uma operação de atualização:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Todas as chamadas têm de ser autenticadas com um token válido do Azure Active Directory (OAuth 2) no cabeçalho de autorização e tem de cumprir os seguintes requisitos:

- O token tem de ser um token de utilizador ou um principal de serviço de aplicações.
- O token tem de ter o público-alvo correto definido como `https://*.asazure.windows.net`.
- O utilizador ou aplicação tem de ter permissões suficientes no servidor ou modelo para fazer a chamada de pedido. O nível de permissão é determinado pelas funções dentro do modelo ou do grupo de administrador no servidor.

    > [!IMPORTANT]
    > Atualmente, **administrador do servidor** são necessárias permissões de função.

## <a name="post-refreshes"></a>POST /refreshes

Para efetuar uma operação de atualização, utilize o verbo POST na coleção /refreshes para adicionar um novo item de atualização para a coleção. O cabeçalho de localização na resposta inclui o ID de atualização. A aplicação cliente pode desligar e verificar o estado mais tarde, se necessário, porque é assíncrona.

Operação de atualização apenas uma é aceite por vez para um modelo. Se existe uma operação de atualização em execução atual e o outro é submetido, é devolvido o código de estado 409 Conflito HTTP.

O corpo pode se parecer com o seguinte:

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

| Name             | Tipo  | Descrição  |Predefinição  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | O tipo de processamento para realizar. Os tipos estão alinhados com o TMSL [atualizar o comando](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) tipos: completa, clearValues, calcular, dataOnly, automática e desfragmentar. Adicione o tipo não é suportado.      |   automática      |
| `CommitMode`     | Enum  | Determina se os objetos serão consolidados em lotes ou apenas quando terminar. Incluem modos: partialBatch transacional, do padrão,.  |  transacional       |
| `MaxParallelism` | Int   | Este valor determina o número máximo de threads para executar comandos de processamento em paralelo. Este valor alinhado com a propriedade MaxParallelism que pode ser definida no TMSL [de sequência de comando](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) ou usando outros métodos.       | 10        |
| `RetryCount`     | Int   | Indica o número de vezes que a operação será repetida antes de falhar.      |     0    |
| `Objects`        | Array | Uma matriz de objetos a serem processados. Cada objeto inclui: "tabela" ao processar a tabela inteira ou "tabela" e "partition" ao processar uma partição. Se forem especificados não existem objetos, o modelo de todo é atualizado. |   A todo o modelo de processo      |

CommitMode é igual ao partialBatch. É utilizado ao fazer uma carga inicial de grandes conjuntos de dados que o processo poderá demorar horas. Se a operação de atualização falhar após a confirmação com êxito um ou mais lotes, serão permanecem confirmados, os lotes consolidados com êxito (ele não irá reverter lotes consolidadas com êxito).

> [!NOTE]
> No momento da escrita, o tamanho do lote é o valor de MaxParallelism, mas pode alterar este valor.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Para verificar o estado de uma operação de atualização, utilize o verbo GET no ID de atualização. Eis um exemplo do corpo da resposta. Se a operação está em curso **inProgress** é retornado no estado.

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

## <a name="get-refreshes"></a>OBTER /refreshes

Para obter uma lista de operações de atualização de histórico para um modelo, utilize o verbo GET na coleção /refreshes. Eis um exemplo do corpo da resposta. 

> [!NOTE]
> No momento da escrita, os últimos 30 dias de operações de atualização são armazenados e devolvidos, mas pode alterar este número.

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

Para cancelar uma operação de atualização em curso, usam o verbo de eliminação no ID de atualização do.

## <a name="post-sync"></a>POST /sync

Terem efetuado a operações de atualização, poderá ser necessário sincronizar os novos dados com réplicas para aumentar horizontalmente a consulta. Para efetuar uma operação de sincronização para um modelo, utilize o verbo POST na função /sync. O cabeçalho de localização na resposta inclui o ID de operação de sincronização.

## <a name="get-sync-status"></a>OBTER o estado de /sync

Para verificar o estado de uma operação de sincronização, utilize o verbo GET, passando o ID da operação como um parâmetro. Eis um exemplo do corpo da resposta:

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

Os valores de `syncstate`:

- 0: A replicar. Ficheiros de base de dados estão a ser replicados para uma pasta de destino.
- 1: Reativar. A base de dados está a ser reativado na instância (s) do servidor só de leitura.
- 2: Concluído. A operação de sincronização foi concluída com êxito.
- 3: Falhou. A operação de sincronização falhou.
- 4: A finalizar. A operação de sincronização foi concluída, mas está a efetuar os passos de limpeza.

## <a name="code-sample"></a>Exemplo de código

Eis um exemplo de código c# para começar, [RestApiSample no GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Para utilizar o código de exemplo

1.  Clonar ou transferir o repositório. Abra a solução de RestApiSample.
2.  Localize a linha **cliente. BaseAddress="NET.TCP://localhost:6080/vmmhelperservice/ =...** e fornecer sua [URL de base](#base-url).

O exemplo de código pode utilizar o início de sessão interativo, o nome de utilizador/palavra-passe, ou [principal de serviço](#service-principal).

#### <a name="interactive-login-or-usernamepassword"></a>Início de sessão interativo ou nome de utilizador/palavra-passe

Essa forma de autenticação requer uma aplicação do Azure ser criada com as permissões API necessárias atribuídas. 

1.  No portal do Azure, clique em **criar um recurso** > **do Azure Active Directory** > **registos das aplicações**  >   **Novo registo de aplicação**.

    ![Novo registo de aplicação](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  Na **Create**, escreva um nome, selecione **nativo** tipo de aplicação. Para **URI de redirecionamento**, introduza **urn: ietf:wg:oauth:2.0:oob**e, em seguida, clique em **criar**.

    ![Definições](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Selecione a sua aplicação e, em seguida, copie e guarde o **ID da aplicação**.

    ![Copie o ID da aplicação](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  Na **configurações**, clique em **permissões obrigatórias** > **adicionar**.

    ![Adicionar acesso à API](./media/analysis-services-async-refresh/aas-async-add.png)

5.  Na **selecionar uma API**, tipo **Azure Analysis Services** para a pesquisa caixa e, em seguida, selecioná-lo.

    ![Selecionar API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Selecione **ler e escrever todos os modelos**e, em seguida, clique em **selecione**. Quando ambos estiverem selecionadas, clique em **feito** para adicionar as permissões. Pode demorar alguns minutos para propagar.

    ![Selecione a ler e escrever todos os modelos](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  O exemplo de código, localize a **UpdateToken()** método. Observe o conteúdo deste método.
8.  Encontrar **clientID de cadeias de caracteres =...** e, em seguida, introduza o **ID da aplicação** que copiou no passo 3.
9.  Execute o exemplo.

#### <a name="service-principal"></a>Principal de serviço

Ver [criar principal de serviço - portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md) e [adicionar um principal de serviço para a função de administrador de servidor](analysis-services-addservprinc-admins.md) para obter mais informações sobre como configurar um principal de serviço e atribuir as permissões necessárias nas do Azure . Depois de concluir os passos, conclua os seguintes passos adicionais:

1.  No exemplo de código, encontrar **autoridade de cadeias de caracteres =...** , substitua **comuns** com da sua organização inquilino ID.
2.  Comentário/anule os comentários para que a classe de ClientCredential é usada para instanciar o objeto de cred. Certifique-se a \<ID da aplicação > e \<chave da aplicação > valores são acessados de maneira segura ou utiliza a autenticação baseada em certificados para principais de serviço.
3.  Execute o exemplo.


## <a name="see-also"></a>Consulte também

[Exemplos](analysis-services-samples.md)   
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


