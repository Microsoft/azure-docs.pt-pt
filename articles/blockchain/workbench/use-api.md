---
title: Usando a API REST de visualização do Azure Blockchain Workbench
description: Cenários de como usar a API REST de visualização do Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 412b78363be70918969734cd8890a8ee940df431
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843824"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Usando a API REST de visualização do Azure Blockchain Workbench

A API REST de visualização do Azure Blockchain Workbench fornece aos desenvolvedores e operadores de informações uma maneira de criar integrações avançadas para aplicativos Blockchain. Este documento explica vários métodos essenciais da API REST do Workbench. Por exemplo, suponha que um desenvolvedor queira criar um cliente blockchain personalizado. Esse cliente blockchain permite que usuários conectados exibam e interajam com seus aplicativos blockchain atribuídos. O cliente permite aos utilizadores ver instâncias de contrato e executar ações em contratos inteligentes. O cliente usa a API REST do Workbench no contexto do usuário conectado para realizar as seguintes ações:

* Listar aplicações
* Listar fluxos de trabalho para uma aplicação
* Listar instâncias de contrato inteligente para um fluxo de trabalho
* Listar ações disponíveis para um contrato
* Executar uma ação para um contrato

Os aplicativos de exemplo blockchain usados nos cenários podem ser [baixados do GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Listar aplicações

Depois que um usuário tiver entrado no cliente do blockchain, a primeira tarefa é recuperar todos os aplicativos do Blockchain Workbench para o usuário. Neste cenário, o utilizador tem acesso a duas aplicações:

1. [Transferência de ativos](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Transporte refrigerado](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Utilize a [API GET de Aplicações](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

A resposta lista todos os aplicativos blockchain aos quais um usuário tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm todos os aplicativos Blockchain. Os administradores que não são de Workbench recebem todos os blockchains para os quais têm pelo menos uma função de aplicativo associada ou uma função de instância de contrato inteligente associada.

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

Quando um usuário seleciona o aplicativo blockchain aplicável (como **transferência de ativos**), o cliente blockchain recupera todos os fluxos de trabalho do aplicativo blockchain específico. Em seguida, os utilizadores podem selecionar o fluxo de trabalho aplicável antes de serem apresentadas todas as instâncias de contrato inteligente para o fluxo de trabalho. Cada aplicação de blockchain tem um ou mais fluxos de trabalho e cada fluxo de trabalho tem zero ou instâncias de contrato inteligente. Para um aplicativo cliente blockchain que tem apenas um fluxo de trabalho, é recomendável ignorar o fluxo de experiência do usuário que permite aos usuários selecionar o fluxo de trabalho apropriado. Nesse caso, a **transferência de ativos** tem apenas um fluxo de trabalho, também chamado de **transferência de ativos**.

Utilize a [API Workflows GET de Aplicações](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

A resposta apresenta uma lista de todos os fluxos de trabalho da aplicação de blockchain especificada, aos quais um utilizador tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm todos os fluxos de trabalho do Blockchain. Os administradores que não são de Workbench obtêm todos os fluxos de trabalho para os quais têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

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

## <a name="list-smart-contract-instances-for-a-workflow"></a>Listar instâncias de contrato inteligente para um fluxo de trabalho

Depois que um usuário selecionar o fluxo de trabalho aplicável, essa **transferência de ativo**de caso, o cliente blockchain recuperará todas as instâncias de contrato inteligente para o fluxo de trabalho especificado. Você pode usar essas informações para mostrar todas as instâncias de contrato inteligente para o fluxo de trabalho. Ou você pode permitir que os usuários se aprofundem em qualquer uma das instâncias de contrato inteligente mostradas. Neste exemplo, considere que um utilizador pretende interagir com uma das instâncias de contrato inteligente para tomar medidas.

Utilize a [API GET de Contratos](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

A resposta apresenta uma lista de todas as instâncias de contrato inteligente do fluxo de trabalho especificado. Os administradores do Workbench obtêm todas as instâncias de contrato inteligente. Os administradores que não são do Workbench obtêm todas as instâncias de contratos inteligentes para as quais têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

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

Quando um usuário decide aprofundar-se em um contrato, o cliente blockchain pode mostrar as ações de usuário disponíveis de acordo com o estado do contrato. Neste exemplo, o utilizador está a ver todas as ações disponíveis para um novo contrato inteligente que criou:

* Alterar Permite que o usuário modifique a descrição e o preço de um ativo.
* Encerrar Permite que o usuário finalize o contrato do ativo.

Utilize a [API Contract Action GET](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

A resposta apresenta uma lista de todas as ações que o utilizador pode executar, tendo em conta o estado atual da instância de contrato inteligente especificada. Os utilizadores obtêm todas as ações aplicáveis se o utilizador tiver uma função de aplicação associada ou estiver associado a uma função de instância de contrato inteligente para o estado atual da instância de contrato inteligente especificada.

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

Em seguida, um utilizador pode decidir agir em relação à instância de contrato inteligente especificada. Nesse caso, considere o cenário em que um usuário gostaria de modificar a descrição e o preço de um ativo para a seguinte ação:

* Descrição: "Meu carro atualizado"
* Preço: 54321

Utilize a [API Contract Action POST](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

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

Os utilizadores apenas poderão executar a ação tendo em conta o estado atual da instância de contrato inteligente especificada e a função de aplicação ou a função de instância de contrato inteligente associada do utilizador. Se a publicação for bem sucedida, é devolvida uma resposta de HTTP 200 OK sem corpo da resposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Referência da API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)