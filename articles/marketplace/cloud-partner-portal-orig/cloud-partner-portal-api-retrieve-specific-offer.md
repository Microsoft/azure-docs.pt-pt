---
title: Recupere uma oferta específica API [ Mercado Azure
description: A API recupera a oferta especificada dentro do espaço de nome da editora.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: a83b664bb770a88f3c4c13a672655e736a46ca75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280461"
---
<a name="retrieve-a-specific-offer"></a>Recuperar uma oferta específica
=========================

Recupera a oferta especificada dentro do espaço de nome da editora.  

Você também pode recuperar uma versão particular da oferta, ou recuperar a oferta em slots de rascunho, visualização ou produção. Se uma ranhura não for especificada, a predefinição é `draft`. Tentar recuperar uma oferta que não tenha sido pré-visualizada ou publicada resultará num `404 Not Found` erro.

> [!WARNING]
> Os valores secretos para campos secretos não serão recuperados por esta API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parâmetros URI
--------------


| **Nome**    | **Descrição**                                                                          | **Tipo de dados** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| editorId | editorId. Por exemplo, Contoso                                                        | Cadeia        |
| offerId     | Guid que identifica exclusivamente a oferta.                                                 | Cadeia        |
| versão     | Versão da oferta que está a ser recuperada. Por padrão, a versão mais recente da oferta é recuperada. | Número inteiro       |
| slotId      | O slot a partir do qual a oferta deve ser recuperada, pode ser um dos:      <br/>  - `Draft`(padrão) recupera a versão de oferta atualmente em projeto.  <br/>  -  `Preview`recupera a versão de oferta atualmente em pré-visualização.     <br/>  -  `Production`recupera a versão de oferta atualmente em produção.          |      enum |
| api-version | Versão mais recente da API                                                                    | Date          |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**          |   **Valor**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Autorização     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Exemplo do corpo
------------

### <a name="response"></a>Resposta

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**       |   **Descrição**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  ofertaTypeId    | Identifica o tipo de oferta                                                                                                    |
|  editorId    | Identificador único da editora                                                                                              |
|  status         | Estado da oferta. Para a lista de valores possíveis, consulte o [estado da Oferta](#offer-status) abaixo.                                  |
|  Id             | GUID que identifica exclusivamente a oferta                                                                                         |
|  versão        | Versão atual da oferta. A propriedade da versão não pode ser modificada pelo cliente. É incrementado após cada publicação.    |
|  definição     | Definição real da carga de trabalho                                                                                               |
|  mudouTempo    | UtC data de data em que a oferta foi alterada pela última vez                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  | **Descrição**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- O pedido foi processado com sucesso e todas as ofertas sob a editora foram devolvidas ao cliente.               |
|  400      | `Bad/Malformed request`- O corpo de resposta a erros pode conter mais informações.                                                 |
|  403      | `Forbidden`- O cliente não tem acesso ao espaço de nome especificado.                                                        |
|  404      | `Not found`- A entidade especificada não existe. O cliente deve verificar o editorId, offerId e versão (se especificado).      |
|  |  |


### <a name="offer-status"></a>Estado da oferta

|  **Nome**                   |   **Descrição**                             |
| --------------------------- |  -------------------------------------------- |
|  Nunca Publicado             | A oferta nunca foi publicada.               |
|  Não Iniciado                 | A oferta é nova, mas não começou.              |
|  WaitingForPublisherReview  | A oferta está à espera da aprovação do editor.      |
|  A executar                    | A submissão da oferta está a ser processada.          |
|  Bem-sucedido                  | A submissão da oferta concluiu o processamento.    |
|  Cancelado                   | A submissão da oferta foi cancelada.                |
|  Falhou                     | A submissão da oferta falhou.                      |
|  |  |
