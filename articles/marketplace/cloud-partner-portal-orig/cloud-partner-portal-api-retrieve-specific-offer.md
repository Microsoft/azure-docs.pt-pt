---
title: Obter uma API de oferta específico | Documentos da Microsoft
description: API obtém a oferta especificada dentro do espaço de nomes do publicador.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9484cf0f549db94be8f1ac2363addca952a3cff3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61096068"
---
<a name="retrieve-a-specific-offer"></a>Obter uma oferta específica
=========================

Obtém a oferta especificada dentro do espaço de nomes do publicador.  

Também pode obter uma versão específica da oferta, ou obter a oferta no rascunho, vista ou ranhuras de produção. Se não for especificado um bloco, a predefinição é `draft`. Tentar obter uma oferta que não foi visualizada ou publicada resultará num `404 Not Found` erro.

> [!WARNING]
> Não serão possível obter os valores secretos para campos de tipo secreta por esta API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parâmetros do URI
--------------


| **Nome**    | **Descrição**                                                                          | **Tipo de dados** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Por exemplo, Contoso                                                        | String        |
| IDoferta     | GUID que identifica exclusivamente a oferta.                                                 | String        |
| versão     | Versão da oferta que está a ser recuperada. Por predefinição, a versão mais recente da oferta é recuperada. | Número inteiro       |
| slotId      | A ranhura a partir do qual a oferta é a obter, pode ser um de:      <br/>  - `Draft` (predefinição) obtém a versão da oferta atualmente no rascunho.  <br/>  -  `Preview` obtém a versão de oferta, atualmente em pré-visualização.     <br/>  -  `Production` obtém a versão de oferta, atualmente em produção.          |      Enum |
| versão de API | Versão mais recente da API                                                                    | Date          |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**          |   **Valor**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Autorização     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Exemplo de corpo
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
|  offerTypeId    | Identifica o tipo de oferta                                                                                                    |
|  publisherId    | Identificador exclusivo do publicador                                                                                              |
|  status         | Estado da oferta. Para obter a lista de valores possíveis, consulte [stav Nabídky](#offer-status) abaixo.                                  |
|  Id             | GUID que identifica exclusivamente a oferta                                                                                         |
|  versão        | Versão atual da oferta. A propriedade de versão não pode ser modificada pelo cliente. Ele é incrementado após cada publicação.    |
|  definição     | Definição real da carga de trabalho                                                                                               |
|  changedTime    | Datetime UTC quando a oferta foi modificado pela última vez.                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  | **Descrição**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -O pedido foi processado com êxito e todas as ofertas sob o publicador foram devolvidas ao cliente.               |
|  400      | `Bad/Malformed request` -O corpo da resposta de erro pode conter mais informações.                                                 |
|  403      | `Forbidden` -O cliente não tem acesso ao espaço de nomes especificado.                                                        |
|  404      | `Not found` -A entidade especificada não existe. Cliente verificaria a publisherId offerId e versão (se especificada).      |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Nome**                   |   **Descrição**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Oferta nunca foi publicada.               |
|  NotStarted                 | Oferta é nova, mas não foi iniciada.              |
|  WaitingForPublisherReview  | Oferta está à espera de aprovação do publicador.      |
|  A executar                    | Submissão de oferta está a ser processado.          |
|  Bem-sucedido                  | Submissão de oferta terminou de processar.    |
|  Cancelado                   | Submissão de oferta foi cancelada.                |
|  Com Falhas                     | Falha na submissão de oferta.                      |
|  |  |
