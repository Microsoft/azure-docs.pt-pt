---
title: Utilização de APIs DE REPOUSO DE BANCADA Azure Blockchain
description: Cenários para como usar a bancada de trabalho Azure Blockchain Preview REST API
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672739"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Utilização da bancada de trabalho Azure Blockchain Preview REST API

A Azure Blockchain Workbench Preview REST API fornece aos desenvolvedores e trabalhadores da informação uma forma de construir integrações ricas em aplicações blockchain. Este artigo destaca vários cenários de como utilizar a Workbench REST API. Por exemplo, suponha que quer criar um cliente blockchain personalizado que permita que os utilizadores assinados vejam e interajam com as suas aplicações blockchain atribuídas. O cliente pode usar a API da bancada de trabalho blockchain para ver casos de contratos e tomar ações em contratos inteligentes.

## <a name="blockchain-workbench-api-endpoint"></a>Ponto final da API da bancada de trabalho blockchain

As APIs da bancada blockchain são acedidas através de um ponto final para a sua implementação. Para obter o URL final da API para a sua implementação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel de navegação à esquerda, selecione **Grupos de Recursos**.
1. Escolha o nome do grupo de recursos a sua bancada de trabalho blockchain implantada.
1. Selecione a direção da coluna **TYPE** para classificar a lista alfabeticamente por tipo.
1. Existem dois recursos com serviço de **aplicações**tipo. Selecione o recurso do serviço de **aplicações** *tipo com* o sufixo "-api".
1. Na visão **geral**do serviço de aplicações, copie o valor **URL,** que representa o URL final da API para a sua bancada de trabalho blockchain implantada.

    ![URL final de serviço de aplicativo API](media/use-api/app-service-api.png)

## <a name="authentication"></a>Autenticação

Os pedidos à bancada de trabalho blockchain REST API estão protegidos com o Azure Ative Directory (Azure AD).

Para fazer um pedido autenticado às APIs rest, o código do cliente requer autenticação com credenciais válidas antes de poder ligar para a API. A autenticação é coordenada entre os vários atores pela Azure AD, e fornece ao seu cliente um sinal de [acesso](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) como prova da autenticação. O símbolo é então enviado no cabeçalho de autorização http dos pedidos da API REST. Para saber mais sobre a autenticação da Azure AD, consulte [o Azure Ative Directory para programadores.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

Consulte as amostras de [API REST,](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) por exemplo, de como autenticar.

## <a name="using-postman"></a>Utilizar o Postman

Se quiser testar ou experimentar APIs da Bancada de Trabalho, pode usar [o Carteiro](https://www.postman.com) para fazer chamadas aPi para a sua implementação. [Faça o download de uma amostra de pedidos de API da Workbench API](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) do GitHub. Consulte o ficheiro README para obter detalhes sobre a autenticação e utilizando os pedidos de API.

## <a name="create-an-application"></a>Criar uma aplicação

Usa duas chamadas API para criar uma aplicação blockchain Workbench. Este método só pode ser realizado por utilizadores que sejam administradores da Bancada workbench.

Utilize a [API post](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) de aplicações para fazer o upload do ficheiro JSON da aplicação e obter um ID de aplicação.

### <a name="applications-post-request"></a>Pedido de pedido post de pedidos

Utilize o parâmetro **appFile** para enviar o ficheiro de configuração como parte do organismo de pedido.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Resposta post aplicações

O ID da aplicação criada é devolvido na resposta. Precisa do ID da aplicação para associar o ficheiro de configuração ao ficheiro de código quando ligar para a próxima API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Pedido de correio de código de contrato

Utilize o código de contrato de [Aplicações POST API,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) passando o ID da aplicação para fazer o upload do ficheiro de código solidez da aplicação. A carga útil pode ser um único ficheiro Solidity ou um ficheiro com fecho de ficheiros Solidity.

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| {applicationId} | Valor de devolução das aplicações POST API. |
| {ledgerId} | Índice do livro de contabilidade. O valor é geralmente 1. Também pode verificar o valor da [tabela Ledger.](data-sql-management-studio.md) |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Resposta do código contratual POST

Se for bem sucedida, a resposta inclui o código de identificação do código de contrato criado da [tabela ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Atribuir funções a utilizadores

Utilize as atribuições de funções de [aplicações POST API,](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) passando o ID da aplicação, o ID do utilizador e o ID da função de aplicação para criar um mapeamento user-to-role na aplicação blockchain especificada. Este método só pode ser realizado por utilizadores que sejam administradores da Bancada workbench.

### <a name="role-assignments-post-request"></a>Pedido de atribuição de funções POST

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| {applicationId} | Valor de devolução da API Post Applications. |
| {userId} | Valor de ID do utilizador a partir da [tabela Utilizador](data-sql-management-studio.md). |
| {aplicaçãoRoleId} | Valor de ID da função de aplicação associado ao ID da aplicação a partir da [tabela ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Atribuição de funções Resposta POST

Se for bem sucedida, a resposta inclui o ID de atribuição de funções criado da [tabela RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Listar aplicações

Utilize as [Aplicações GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) para recuperar todas as aplicações blockchain Workbench para o utilizador. Neste exemplo, o utilizador inscrito tem acesso a duas aplicações:

- [Transferência de ativos](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Transporte refrigerado](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Pedidos GET pedido

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Aplicações Resposta GET

A resposta lista todas as aplicações blockchain às quais um utilizador tem acesso na Bancada de Trabalho blockchain. Os administradores da bancada blockchain obtêm todas as aplicações blockchain. Os administradores não-workbench obtêm todas as aplicações blockchain para as quais têm pelo menos uma função de aplicação associada ou uma função de instância de contrato inteligente associada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Listar fluxos de trabalho para uma aplicação

Utilizar [aplicações Fluxos de trabalho GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) para listar todos os fluxos de trabalho de uma aplicação blockchain especificada a que um utilizador tem acesso na Bancada de Trabalho blockchain. Cada aplicação de blockchain tem um ou mais fluxos de trabalho e cada fluxo de trabalho tem zero ou instâncias de contrato inteligente. Para uma aplicação de cliente blockchain que tenha apenas um fluxo de trabalho, recomendamos que salte o fluxo de experiência do utilizador que permite aos utilizadores selecionar o fluxo de trabalho apropriado.

### <a name="application-workflows-request"></a>Pedido de fluxos de trabalho de aplicação

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Resposta de fluxos de trabalho de aplicação

Os administradores da bancada blockchain obtêm todos os fluxos de trabalho blockchain. Os administradores não-workbench obtêm todos os fluxos de trabalho para os quais têm pelo menos uma função de candidatura associada ou estão associados a um papel de instância de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>Criar uma instância contratual

Utilize [contratos V2 POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) para criar um novo contrato inteligente para um fluxo de trabalho. Os utilizadores só podem criar uma nova instância de contrato inteligente se o utilizador estiver associado a uma função de aplicação, o que pode iniciar uma instância de contrato inteligente para o fluxo de trabalho.

> [!NOTE]
> Neste exemplo, a versão 2 da API é utilizada. As APIs de contrato da versão 2 proporcionam mais granularidade para os campos de ProvisioningStatus associados.

### <a name="contracts-post-request"></a>Pedido de post de contratos

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| {workflowId} | O valor de ID do fluxo de trabalho é o ConstructorID do contrato a partir da [tabela Workflow](data-sql-management-studio.md). |
| {contractCodeId} | Valor de ID do código contratual da [tabela ContractCode](data-sql-management-studio.md). Correlacionar o ID da aplicação e o ID do livro para a instância contratual que pretende criar. |
| {connectionId} | Valor de ID de ligação da [tabela De Ligação](data-sql-management-studio.md). |

Para o organismo de pedido, defino valores utilizando as seguintes informações:

| Parâmetro | Valor |
|-----------|-------|
| workflowFunctionID | ID da [tabela WorkflowFunction](data-sql-management-studio.md). |
| fluxo sobraActionParameters | Nome par de parâmetros passados para o construtor. Para cada parâmetro, utilize o valor funçãodeparâmetro de funcionamento da tabela [WorkflowFunctionParameter.](data-sql-management-studio.md) |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Contratos POST resposta

Se for bem sucedido, as atribuições de funções AAD devolve o ContractActionID da [tabela ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Listar instâncias de contrato inteligente para um fluxo de trabalho

Utilize [contratos GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) para mostrar todas as instâncias de contrato inteligentes para um fluxo de trabalho. Ou pode permitir que os utilizadores mergulhem profundamente em qualquer uma das instâncias de contrato seletos.

### <a name="contracts-request"></a>Pedido de contratos

Neste exemplo, considere que um utilizador pretende interagir com uma das instâncias de contrato inteligente para tomar medidas.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Resposta de contratos

A resposta enumera todas as instâncias inteligentes de contrato sinuosos do fluxo de trabalho especificado. Os administradores da bancada de trabalho têm todos os casos de contratos inteligentes. Os administradores não-workbench obtêm todas as instâncias de contrato inteligente para as quais têm pelo menos uma função de candidatura associada ou estão associados a um papel de exemplo de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Listar ações disponíveis para um contrato

Utilizar [a Ação Contratual GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) para mostrar as ações de utilizador disponíveis dado o estado do contrato. 

### <a name="contract-action-request"></a>Pedido de ação contratual

Neste exemplo, o utilizador está a analisar todas as ações disponíveis para um novo contrato inteligente que criaram.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Resposta à ação contratual

A resposta apresenta uma lista de todas as ações que o utilizador pode executar, tendo em conta o estado atual da instância de contrato inteligente especificada.

* Modificar: permite que o utilizador modifique a descrição e o preço de um recurso.
* Rescisão: Permite ao utilizador terminar o contrato do ativo.

Os utilizadores obtêm todas as ações aplicáveis se o utilizador tiver uma função de aplicação associada ou estiver associado a uma função de instância de contrato inteligente para o estado atual da instância de contrato inteligente especificada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Executar uma ação para um contrato

Utilize a [API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) de Ação Contratual PARA tomar medidas para a instância de contrato inteligente especificada.

### <a name="contract-action-post-request"></a>Pedido de ação contratual POST

Neste caso, considere o cenário em que um utilizador gostaria de modificar a descrição e o preço de um ativo.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Os utilizadores apenas poderão executar a ação tendo em conta o estado atual da instância de contrato inteligente especificada e a função de aplicação ou a função de instância de contrato inteligente associada do utilizador.

### <a name="contract-action-post-response"></a>Ação contratual Resposta POST

Se a publicação for bem sucedida, é devolvida uma resposta de HTTP 200 OK sem corpo da resposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre as APIs da bancada de trabalho blockchain, consulte a referência da Bancada de [Trabalho do Azure Blockchain REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).
