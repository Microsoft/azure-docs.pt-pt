---
title: Utilizar a API REST do Azure Blockchain Workbench
description: Cenários para saber como utilizar a API REST do Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: eb4b87a008b9e43de7e7a5f7895449303f1e44a6
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593986"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Utilizar a API REST do Azure Blockchain Workbench

A API REST do Azure Blockchain Workbench proporciona aos programadores e técnicos de informação uma forma de criar integrações avançadas para aplicações de blockchain. Este documento explica vários métodos essenciais da API REST do Workbench. Por exemplo, suponha que um desenvolvedor deseja criar um cliente de blockchain personalizado. Este cliente de blockchain permite que os utilizadores com sessão iniciada ver e interagir com as aplicações de blockchain atribuído. O cliente permite aos utilizadores ver instâncias de contrato e executar ações em contratos inteligentes. O cliente utiliza a API de REST do Workbench no contexto do utilizador com sessão iniciada para executar as seguintes ações:

* Listar aplicações
* Listar fluxos de trabalho para uma aplicação
* Listar instâncias de contrato inteligente para um fluxo de trabalho
* Listar ações disponíveis para um contrato
* Executar uma ação para um contrato

As aplicações de blockchain de exemplo utilizadas em cenários, podem ser [transferido a partir do GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Listar aplicações

Quando um utilizador tiver iniciado sessão no cliente de blockchain, a primeira tarefa é recuperar todas as aplicações de Blockchain Workbench para o utilizador. Neste cenário, o utilizador tem acesso a duas aplicações:

1. [Transferência de recurso](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Transporte refrigerated](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Utilize a [API GET de Aplicações](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

A resposta apresenta uma lista de todas as aplicações de blockchain para os quais um utilizador tem acesso na bancada de trabalho de Blockchain. Os administradores de Blockchain Workbench obtém todas as aplicações de blockchain. Os administradores da bancada de trabalho de não obtém todos os blockchains para os quais têm pelo menos uma função de aplicação associada ou uma função de instância de contrato inteligente associados.

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

Assim que um usuário selecionar a aplicação de blockchain aplicável (como **Asset transferir**), o cliente de blockchain obtém todos os fluxos de trabalho da aplicação de blockchain específico. Em seguida, os utilizadores podem selecionar o fluxo de trabalho aplicável antes de serem apresentadas todas as instâncias de contrato inteligente para o fluxo de trabalho. Cada aplicação de blockchain tem um ou mais fluxos de trabalho e cada fluxo de trabalho tem zero ou instâncias de contrato inteligente. Para uma aplicação de cliente de blockchain com apenas um fluxo de trabalho, recomendamos que a ignorar o fluxo de experiência de utilizador que permite aos utilizadores selecionar o fluxo de trabalho apropriado. Neste caso, **Asset transferência** tem apenas um fluxo de trabalho, também denominado **Asset transferir**.

Utilize a [API Workflows GET de Aplicações](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

A resposta apresenta uma lista de todos os fluxos de trabalho da aplicação de blockchain especificada, aos quais um utilizador tem acesso no Blockchain Workbench. Os administradores de Blockchain Workbench obtém cada fluxo de trabalho de blockchain. Os administradores da bancada de trabalho de não obtém todos os fluxos de trabalho para o qual ter, pelo menos, uma função de aplicação associada ou está associado uma função de instância de contrato inteligente.

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

Assim que um utilizador seleciona o aplicável fluxo de trabalho, neste caso **Asset transferir**, o cliente de blockchain irá obter todas as instâncias de contrato inteligente para o fluxo de trabalho especificado. Pode usar essas informações para mostrar todas as instâncias de contrato inteligente para o fluxo de trabalho. Ou pode permitir que os utilizadores de forma mais aprofundada em qualquer uma das instâncias do contrato inteligente mostrado. Neste exemplo, considere que um utilizador pretende interagir com uma das instâncias de contrato inteligente para tomar medidas.

Utilize a [API GET de Contratos](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

A resposta apresenta uma lista de todas as instâncias de contrato inteligente do fluxo de trabalho especificado. Os administradores da bancada de trabalho obtém todas as instâncias de contrato inteligente. Os administradores da bancada de trabalho de não obtém todas as instâncias de contrato inteligente para as quais ter, pelo menos, uma função de aplicação associada ou está associado uma função de instância de contrato inteligente.

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

Assim que um usuário decide de forma mais aprofundada iniciarem um contrato, o cliente de blockchain, em seguida, pode mostrar as ações de usuário disponíveis dadas o estado do contrato. Neste exemplo, o utilizador está a ver todas as ações disponíveis para um novo contrato inteligente que criou:

* Modificar: Permite ao utilizador modificar a descrição e o preço de um ativo.
* Termine: Permite ao utilizador terminar o contrato do ativo.

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

Em seguida, um utilizador pode decidir agir em relação à instância de contrato inteligente especificada. Neste caso, considere o cenário em que um utilizador gostaria de modificar a descrição e o preço de um recurso para a seguinte ação:

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