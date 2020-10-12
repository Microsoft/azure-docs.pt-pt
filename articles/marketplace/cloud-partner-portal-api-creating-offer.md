---
title: Criar ou modificar uma oferta - Azure Marketplace
description: API para criar uma nova ou atualizar uma oferta existente.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87420231"
---
# <a name="create-or-modify-an-offer"></a>Criar ou modificar uma oferta

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as alterações listadas no [Cloud Partner Portal API Reference](./cloud-partner-portal-api-overview.md) para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

Esta chamada atualiza uma oferta específica dentro do espaço de nome da editora ou cria uma nova oferta.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros URI

|  **Nome**         |  **Descrição**                      |  **Tipo de dados**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  Identificador de editor, por exemplo `contoso` |   Cadeia |
| offerId           |  Identificar oferta                     |   Cadeia        |
| api-version       |  Última versão da API            |   Date           |
|  |  |  |

## <a name="header"></a>Cabeçalho

|  **Nome**        |  **Valor**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| Autorização    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Exemplo corporal

O exemplo a seguir cria uma oferta com oferta DEID de `contosovirtualmachine` .

### <a name="request"></a>Pedir

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
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
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Resposta

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
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
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Para modificar esta oferta, adicione um cabeçalho **If-Match** definido para * ao pedido acima. Use o mesmo corpo de pedido que acima, mas modifique os valores desejados. 

### <a name="response-status-codes"></a>Códigos de estado de resposta

| **Código**  |  **Descrição**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. O pedido foi processado com sucesso e a oferta foi modificada com sucesso.           |
|  201      | `Created`. O pedido foi processado com sucesso e a oferta foi criada com sucesso.   |
|  400      | `Bad/Malformed request`. O corpo de resposta a erros pode fornecer mais informações.            |
|  403      | `Forbidden`. O cliente não tem acesso ao espaço de nome solicitado.                     |
|  404      | `Not found`. A entidade referida pelo cliente não existe.                           |
|  412      | O servidor não satisfaz uma das condições prévias especificadas pelo solicitador no pedido. O cliente deve verificar o ETAG enviado com o pedido. |
|  |  |

## <a name="uploading-artifacts"></a>Carregar artefactos

Os artefactos, como imagens e logótipos, devem ser partilhados enviando-os para um local acessível na web, incluindo depois cada um como URI no pedido PUT, como no exemplo acima. O sistema irá detetar que estes ficheiros não estão presentes no armazenamento do Azure Marketplace e descarregar estes ficheiros para armazenamento.  Como resultado, você vai descobrir que futuros pedidos GET irão devolver um URL de serviço Azure Marketplace para estes ficheiros.

## <a name="categories-and-industries"></a>Categorias e indústrias

Ao criar uma nova oferta, deve especificar uma categoria para a sua oferta no mercado. Opcionalmente, para alguns tipos de oferta também pode especificar indústrias. Com base no tipo de oferta, forneça as categorias/indústrias aplicáveis à oferta utilizando valores-chave específicos das seguintes tabelas.

### <a name="azure-marketplace-categories"></a>Categorias Azure Marketplace

Estas categorias e respetivas chaves são aplicáveis para apps Azure, Máquinas Virtuais, Máquinas Virtuais Core, Contentores, aplicações de contentores, módulos IoT Edge e tipos de oferta SaaS. Os itens em itálico arrojado (como ***analíticos)*** são categorias e os itens de texto padrão (como insights de dados) são subcategorias abaixo deles. Utilize os valores-chave exatos, sem alterar o espaçamento ou a capitalização.

| Categoria | Chaves SaaS | Chaves da App Azure | Máquina virtual, contentores, aplicativos de contentores, módulo de borda IoT, teclas de máquina virtual core |
| --- | --- | --- | --- |
| ***Análise*** | ***análise*** | ***analíticos-azure-apps*** | ***analítico-amp*** |
| Insights de Dados | insights de dados | insights de dados | insights de dados |
| Análise de Dados | análise de dados | análise de dados | análise de dados |
| Macrodados | big-data | bigData | big-data |
| Análise Preditiva | análise preditiva | análise preditiva | análise preditiva |
| Análise em tempo real/streaming | análise em tempo real | análise em tempo real | análise em tempo real |
| Outro | other | outras análises | other |
| ***IA + Machine Learning*** | ***Inteligência Artificial*** | ***ai-plus-máquina-aprendizagem*** | ***ai-plus-máquina-aprendizagem*** |
| Serviços bot | bot-serviços | bot-serviços | bot-serviços |
| Serviços Cognitivos | serviços cognitivos | serviços cognitivos | serviços cognitivos |
| Serviço ML | ml-serviço | ml-serviço | ml-serviço |
| ML Automatizado | automatizado-ml | automatizado-ml | automatizado-ml |
| Automação de processos de negócio/robótica | business-robotic-process-automation | business-robotic-process-automation | business-robotic-process-automation |
| Rotulagem de dados | rotulagem de dados | rotulagem de dados | rotulagem de dados |
| Preparação de Dados | preparação de dados | preparação de dados | preparação de dados |
| Mineração de Conhecimento | mineração de conhecimento | mineração de conhecimento | mineração de conhecimento |
| Operações do ML | ml-operações | ml-operações | ml-operações |
| Outro | outra-IA-plus-machine-learning | other | other |
| ***Blockchain*** | ***blockchain*** | ***blockchain*** | ***blockchain*** |
| Aceleradores de aplicativos | aceleradores de aplicações | aceleradores de aplicações | aceleradores de aplicações |
| Ledger de nó único | single-nó-livro-livro | single-nó-livro-livro | single-nó-livro-livro |
| Ledger multi-nódoa | multi-nó-livro | multi-nó-livro | multi-nó-livro |
| Ferramentas | tools | tools | tools |
| Outro | other | other | other |
| ***Computação*** | ***compute-saas*** | ***compute-azure-apps*** | ***cálculo*** |
| Infraestrutura de Aplicação | appInfra | appInfrastructure | infraestrutura de aplicações |
| Sistemas Operativos | clienteOS | clienteOS | sistemas operativos |
| Cache | cache | cache | cache |
| Outro | outro cálculo | outro cálculo | other |
| ***Contentores*** | ***contentores*** | ***contentores*** | ***contentores*** |
| Aplicativos de contentores | container-apps | container-apps | container-apps |
| Imagens de contentores | imagens de contentores | imagens de contentores | imagens de contentores |
| Começar com contentores | começar-com-contentores | começar-com-contentores | começar-com-contentores |
| Outro | other | other | other |
| ***Bases de dados*** | ***bases de dados-saas*** | ***base de dados*** | ***bases de dados*** |
| Bases de Dados NoSQL | nosql-bases de dados | nosql-bases de dados | nosql-bases de dados |
| Bases de Dados Relacionais | bases de dados relacionais | bases de dados relacionais | bases de dados relacionais |
| Bases de dados de livros/blockchain | bases de dados de contabilidade-blockchain | bases de dados de contabilidade-blockchain | bases de dados de contabilidade-blockchain |
| Lagos de Dados | data-lagos | data-lagos | data-lagos |
| Operations Manager | armazém de dados | armazém de dados | armazém de dados |
| Outro | outras bases de dados | outras bases de dados | other |
| ***Ferramentas de Programação*** | ***desenvolvedor-ferramentas-saas*** | ***desenvolvedor-ferramentas-azure-apps*** | ***ferramentas de desenvolvimento*** |
| Ferramentas | ferramentas-ferramentas de desenvolvedor | ferramentas-ferramentas de desenvolvedor | ferramentas-ferramentas de desenvolvedor |
| Scripts | scripts | scripts | scripts |
| Serviço de Desenvolvedores | devService | devService | serviço de desenvolvedores |
| Outro | outras ferramentas de desenvolvedor | outras ferramentas de desenvolvedor | other |
| ***DevOps*** | ***devotos*** | ***devotos*** | ***devotos*** |
| Outro | other | other | other |
| ***Identidade*** | ***identidade*** | ***identidade*** | ***identidade*** |
| Gestão de acesso | gestão de acessos | gestão de acessos | gestão de acessos |
| Outro | other | other | other |
| ***Integração*** | ***integração*** | ***integração*** | ***integração*** |
| Mensagens | mensagens | mensagens | mensagens |
| Outro | other | other | other |
| ***Internet das Coisas*** | ***IoT*** | ***internet-of-things-azure-apps*** | ***internet-of-things*** |
| Serviços IoT Core | N/D | iot-core-serviços | iot-core-serviços |
| Módulos IoT Edge | N/D | módulos iot-edge | módulos iot-edge |
| Soluções IoT | iot-soluções | iot-soluções | iot-soluções |
| Visualização & de análise de dados | análise de dados e visualização | análise de dados e visualização | análise de dados e visualização |
| Conectividade IoT | iot-conectividade | iot-conectividade | iot-conectividade |
| Outro | outras internet-de-coisas | outras internet-de-coisas | other |
| ***Ferramentas de gestão de & de TI*** | ***ITandAdministration*** | ***it-and-management-tools-azure-apps*** | ***it-and-management-tools*** |
| Soluções de Gestão | soluções de gestão | soluções de gestão | soluções de gestão |
| Aplicações Empresariais | businessApplicação | businessApplicação | aplicações empresariais |
| Outro | outras ferramentas de gestão | outras ferramentas de gestão | other |
| ***Acompanhamento & Diagnósticos*** | ***monitorização e diagnósticos*** | ***monitorização e diagnósticos*** | ***monitorização e diagnósticos*** |
| Outro | other | other | other |
| ***Media*** | ***meios de comunicação*** | ***meios de comunicação*** | ***meios de comunicação*** |
| Serviços de Multimédia | media-services | media-services | media-services |
| Proteção de Conteúdo | proteção de conteúdos | proteção de conteúdos | proteção de conteúdos |
| Live & On-Demand Streaming | live-and-on-demand-streaming | live-and-on-demand-streaming | live-and-on-demand-streaming |
| Outro | other | other | other |
| ***Migração*** | ***migração*** | ***migração*** | ***migração*** |
| Migração de Dados | migração de dados | migração de dados | migração de dados |
| Outro | other | other | other |
| ***Realidade Mista*** | ***realidade mista*** | ***realidade mista*** | ***realidade mista*** |
| Outro | other | other | other |
| ***Redes*** | ***networking*** | ***networking*** | ***networking*** |
| Gestores de Eletrodomés | gestores de eletrodomésticos | gestores de eletrodomésticos | gestores de eletrodomésticos |
| Conectividade | conetividade | conetividade | conetividade |
| Firewalls | firewalls | firewalls | firewalls |
| Balanceador de Carga | equilibradores de carga | equilibradores de carga | equilibradores de carga |
| Outro | other | other | other |
| ***Segurança*** | ***segurança*** | ***segurança*** | ***segurança*** |
| Gestão de Acesso & Identidade | gestão de identidade e acesso | gestão de identidade e acesso | gestão de identidade e acesso |
| Proteção Contra Ameaças | proteção contra ameaças | proteção contra ameaças | proteção contra ameaças |
| Information Protection | proteção da informação | proteção da informação | proteção da informação |
| Outro | other | other | other |
| ***Armazenamento*** | ***armazenamento-saas*** | ***storage-azure-apps*** | ***armazenamento*** |
| Recuperação de & de apoio | cópia de segurança | cópia de segurança | backup-e-recuperação |
| Armazenamento Híbrido da Empresa | empresa-híbrido-armazenamento | empresa-híbrido-armazenamento | empresa-híbrido-armazenamento |
| Partilha de ficheiros | partilha de ficheiros | partilha de ficheiros | partilha de ficheiros |
| Gestão do ciclo de vida de dados | gestão de ciclos de dados | gestão de ciclos de dados | gestão de ciclos de dados |
| Outro | outro armazenamento | outro armazenamento | other |
| ***Web*** | ***teia*** | ***teia*** | ***teia*** |
| Blogs & CMSs | blogs-e-cmss | blogs-e-cmss | blogs-e-cmss |
| Aplicativos Web de início | starter-web-apps | starter-web-apps | starter-web-apps |
| Ecommerce | ecommerce | ecommerce | ecommerce |
| Quadros de aplicações web | web-apps-frameworks | web-apps-frameworks | web-apps-frameworks |
| Aplicações Web | aplicativos web | aplicativos web | aplicativos web |
| Outro | other | other | other |
||||

### <a name="microsoft-appsource-categories"></a>Categorias Microsoft AppSource

Estas categorias e respetivas chaves são aplicáveis para SaaS, app PowerBI, Dynamics 365 business central, Dynamics 365 para envolvimento com o cliente, e Dynamics 365 para tipos de oferta de operação. Os itens em itálico arrojado (como ***analíticos)*** são categorias e os itens de texto padrão (como análise avançada) são subcategorias abaixo deles. Utilize os valores-chave exatos, sem alterar o espaçamento ou a capitalização.

| Categoria | Chaves SaaS | Dinâmica 365 centro de negócios, Dinâmica 365 para envolvimento com o cliente, Dynamics 365 para chaves de operação | Chaves de aplicativo PowerBI |
| --- | --- | --- | --- |
| ***Análise*** | ***análise*** | ***Análise*** | ***Análise*** |
| Análise Avançada | análise avançada | análise avançada | análise avançada |
| Relatório de & de Visualização | visualização-reporte | visualização-reporte | visualização-reporte |
| Outro | other | outras análises | outras análises |
| ***IA + Machine Learning*** | ***Inteligência Artificial*** | ***ai-plus-máquina-aprendizagem-dinâmica*** | ***ai-plus-máquina-learning-appsource*** |
| IA para Negócios | ai-para-negócio | ai-para-negócio | ai-para-negócio |
| Aplicativos bot | bot-apps | bot-apps | bot-apps |
| Outro | outra-ai-plus-máquina-aprendizagem | outra-ai-plus-máquina-aprendizagem | outra-ai-plus-máquina-aprendizagem |
| ***Colaboração*** | ***Colaboração*** | ***Colaboração*** | ***colaboração*** |
| Contato & Pessoas | pessoas de contacto | pessoas de contacto | contacto e pessoas |
| Gestão de Reuniões | gestão de reuniões | gestão de reuniões | gestão de reuniões |
| Gestão de & de Design de Site | gestão de design de site | gestão de design de site | site-design-e-management |
| Gestão de projetos de & de tarefas | gestão de projetos de tarefas | gestão de projetos de tarefas | gestão de tarefas e projetos |
| Conferência de vídeo & de voz | voice-video-conferencia | voice-video-conferencia | voz e videoconferência |
| Outro | outra colaboração | outra colaboração | other |
| ***Conformidade & Legal*** | ***conformidade*** | ***conformidade*** | ***conformidade e legal*** |
| Auditoria & Fiscal | auditoria fiscal | auditoria fiscal | impostos e auditorias |
| Legal | Legal | Legal | legal |
| Dados, Governação & Privacidade | dados-governação-privacidade | dados-governação-privacidade | dados-governação-e-privacidade |
| Segurança & de Saúde | segurança sanitária | segurança sanitária | saúde e segurança |
| Outro | outra conformidade-legal | outra conformidade-legal | other |
| ***Customer Service*** | ***Serviço ao Cliente*** | ***Serviço ao Cliente*** | ***atendimento ao cliente*** |
| Centro de Contacto | centro de contacto | centro de contacto | centro de contacto |
| Serviço cara a cara | face-a-face-serviço | face-a-face-serviço | face-a-face-serviço |
| Serviço de empregados de & de back office | back-office-employee-service | back-office-employee-service | back-office-and-employee-service |
| Gestão de Casos de & Conhecimento | gestão de casos de conhecimento | gestão de casos de conhecimento | gestão de conhecimentos e casos |
| Redes sociais & Envolvimento Omnicanal | social-media-omnicanal-engagement | social-media-omnicanal-engagement | social-media-e-omnicanal-engagement |
| Outro | outros clientes-serviço | outros clientes-serviço | other |
| ***Departamento Financeiro*** | ***Departamento Financeiro*** | ***Departamento Financeiro*** | ***finanças*** |
| Contabilidade | contabilidade | contabilidade | contabilidade |
| Gestão de Recursos | gestão de ativos | gestão de ativos | gestão de ativos |
| Reportagem de & de analítica, consolidação | relatórios de consolidação analítica | relatórios de consolidação analítica | analíticos-consolidação e reporte |
| Credit & Collections | cobranças de crédito | cobranças de crédito | crédito e cobranças |
| Conformidade & Gestão de Riscos | gestão do risco de conformidade | gestão do risco de conformidade | gestão do cumprimento e risco |
| Outro | outras finanças | outras finanças | other |
| ***Human Resources*** | ***Recursos Humanos*** | ***Recursos Humanos*** | ***recursos humanos*** |
| Aquisição de Talento | aquisição de talento | aquisição de talento | aquisição de talento |
| Gestão de Talentos | gestão de talentos | gestão de talentos | gestão de talentos |
| Operações de RH | operações de rh | operações de rh | operações de rh |
| Planeamento da força de trabalho & Analytics | mão-de-obra-planeamento-analytics | mão-de-obra-planeamento-analytics | planeamento e análise da força de trabalho |
| Outro | outros recursos humanos | outros recursos humanos | other |
| ***Internet das Coisas*** | ***IoT*** | ***internet-of-things-dynamics*** | ***internet-of-things-appsource*** |
| Operações de & de Gestão de Ativos | operações de gestão de ativos | operações de gestão de ativos | gestão de ativos e operações |
| Produtos Conectados | produtos conectados | produtos conectados | produtos conectados |
| Cadeia de abastecimento inteligente | cadeia de fornecimento inteligente | cadeia de fornecimento inteligente | cadeia de fornecimento inteligente |
| Manutenção Preditiva | manutenção preditiva | manutenção preditiva | manutenção preditiva |
| Monitorização Remota | monitorização remota | monitorização remota | monitorização remota |
| Segurança & Segurança | segurança | segurança | segurança |
| Recursos & de Infraestrutura Inteligente | recursos de infraestrutura inteligente | recursos de infraestrutura inteligente | infraestruturas inteligentes e recursos |
| Veículos & Mobilidade | veículos-mobilidade | veículos-mobilidade | veículos e mobilidade |
| Outro | outras internet-de-coisas | outras internet-de-coisas | other |
| ***Ferramentas de gestão de & de TI*** | ***ITandAdministration*** | ***ITandAdministration*** | ***it-and-management-tools*** |
| Soluções de Gestão | soluções de gestão | soluções de gestão | soluções de gestão |
| Aplicações Empresariais | businessApplicação | businessApplicação | aplicações empresariais |
| Outro | outras ferramentas de gestão | outras ferramentas de gestão | other |
| ***Marketing*** | ***Marketing*** | ***Marketing*** | ***marketing*** |
| Publicidade | publicidade | publicidade | publicidade |
| Análise | marketing analítico | marketing analítico | marketing analítico |
| Gestão de Campanha & Automação | automatização de gestão de campanhas | automatização de gestão de campanhas | gestão de campanhas e automação |
| E-mail Marketing | e-mail-marketing | e-mail-marketing | e-mail-marketing |
| L2 - Gestão de Recursos & Eventos | gestão de recursos de eventos | gestão de recursos de eventos | gestão de eventos e recursos |
| Análise & de Investigação | investigação-análise | investigação-análise | investigação e análise |
| Mídia Social | redes sociais | redes sociais | redes sociais |
| Outro | outro marketing | outro marketing | other |
| ***Cadeia de Abastecimento & operações*** | ***OperaçõesSupplyChain*** | ***OperaçõesSupplyChain*** | ***operações e cadeia de abastecimento*** |
| Gestão de Produção de & De Ativos | gestão da produção de ativos | gestão da produção de ativos | gestão de ativos e de produção |
| Previsão de Procura | previsão da procura | previsão da procura | previsão da procura |
| Gestão de Informação & Conectividade | conectividade de gestão de informação | conectividade de gestão de informação | gestão da informação e conectividade |
| Planeamento, Compras & Reporte | planeamento-compra-reporte | planeamento-compra-reporte | planeamento-compra e reporte |
| Gestão de Serviços de qualidade & | gestão de serviços de qualidade | gestão de serviços de qualidade | gestão da qualidade e do serviço |
| Gestão de Encomendas & Vendas | gestão de encomendas de vendas | gestão de encomendas de vendas | gestão de vendas e encomendas |
| Gestão de Armazéns & Transportes | gestão de armazéns de transporte | gestão de armazéns de transporte | gestão de transportes e armazéns |
| Outro | outras operações-cadeia de abastecimento | outras operações-cadeia de abastecimento | other |
| ***Produtividade*** | ***Produtividade*** | ***Produtividade*** | ***produtividade*** |
| Criação de Conteúdos & Gestão | gestão de criação de conteúdos | gestão de criação de conteúdos | criação e gestão de conteúdos |
| Tradução & Linguagem | tradução linguística | tradução linguística | linguagem e tradução |
| Document Management | gestão documental | gestão documental | gestão documental |
| Gestão de e-mail | gestão de e-mails | gestão de e-mails | gestão de e-mails |
| Consulte a & Referência | referência de pesquisa | referência de pesquisa | pesquisa e referência |
| Outro | outra produtividade | outra produtividade | other |
| Gamificação | Gamificação | Gamificação | gamificação |
| ***Vendas*** | ***Vendas*** | ***Vendas*** | ***Vendas*** |
| Televendas | televendas | televendas | televendas |
| Configuração, Preço, Cotação (CPQ) | configuração-preço-cotação | configuração-preço-cotação | configuração-preço-cotação |
| Gestão de Contratos | gestão de contratos | gestão de contratos | gestão de contratos |
| CRM | crm | crm | crm |
| E-commerce | e-commerce | e-commerce | e-commerce |
| Enriquecimento de Dados empresariais | enriquecimento de dados empresariais | enriquecimento de dados empresariais | enriquecimento de dados empresariais |
| Habilitação de Vendas | habilitação para vendas | habilitação para vendas | habilitação para vendas |
| Outro | outras vendas | outras vendas | outras vendas |
| ***Geolocalização*** | ***geolocalização*** | ***geolocalização*** | ***geolocalização*** |
| Mapas | maps | maps | maps |
| Notícias & Tempo | notícias e tempo | notícias e tempo | notícias e tempo |
| Outro | outra geolocalização | outra geolocalização | outra geolocalização |
||||

### <a name="microsoft-appsource-industries"></a>Indústrias Microsoft AppSource

Estas indústrias e respetivas chaves são aplicáveis para SaaS, app PowerBI, Dynamics 365 business central, Dynamics 365 para envolvimento com o cliente, e Dynamics 365 para tipos de oferta de operação. Os itens em itálico arrojado (como ***o Automotive)*** são categorias e os itens de texto padrão (como o AutomotiveL2) são subcategorias abaixo deles. Utilize os valores-chave exatos, sem alterar o espaçamento ou a capitalização.

| Setor | SaaS, Dynamics 365 centro de negócios, Dynamics 365 para envolvimento com o cliente, Dynamics 365 para chaves de operação | Chaves de aplicativos PowerBI |
| --- | --- | --- |
| ***Automóvel*** | ***Automóvel*** | ***automóvel*** |
| Automóvel | AutomotiveL2 | AutomotiveL2 |
| ***Agricultura*** | ***Agricultura*** | ***agricultura*** |
| Outros - Nãoegmentado | Agricultura \_ Outros EstadosEgmentais | outros não-escamado |
| ***Distribuição*** | ***Distribuição*** | ***distribuição*** |
| Atacado | Atacado | por grosso |
| Envio de pacotes & de encomendas | ParcelAndPackageShipping | encomenda e pacote-envio |
| ***Education*** | ***Education*** | ***educação*** |
| Ensino Superior | Maior Euscção | ensino superior |
| Ensino Secundário & Primário / K-12 | Primário ESegucatórioK12 | ensino primário e secundário |
| Bibliotecas & Museus | Bibliotecas Emuseums | bibliotecas e museus |
| ***Serviços Financeiros*** | ***Serviços Financeiros*** | ***serviços financeiros*** |
| Mercados de Capitais & Bancários | BancaAndCapitalMarkets | mercados bancários e de capitais |
| Seguro | Seguro | seguro |
| ***Administração Pública*** | ***Administração Pública*** | ***governo*** |
| Inteligência & Defesa | Defesa EIntelligence | defesa e inteligência |
| Segurança Pública & Justiça | PublicSafetyAndJustice | segurança pública e justiça |
| Governo Civil | Governo Civil | governo civil |
| ***Cuidados de saúde*** | ***HealthCareandLifeSciences*** | ***cuidados de saúde*** |
| Pagador de Saúde | HealthPayor | pagador de saúde |
| Provedor de Saúde | HealthProvider | prestador de saúde |
| Produtos farmacêuticos | Produtos farmacêuticos | produtos farmacêuticos |
| ***Recursos & De Fabrico*** | ***Fabrico*** | ***fabrico e recursos*** |
| Químico & Agroquímico | QuímicaAndAgroquímica | químico e agroquímico |
| Fabrico discreto | DiscretaMente Manfacturagem | fabrico discreto |
| Energia | Energia | energy |
| ***Retalho & Bens de Consumo*** | ***Retalho e Comércio De Produtos*** | ***retalho e bens de consumo*** |
| Bens de Consumo | Bens de Consumo | bens de consumo |
| Retalhistas | Retalhistas | retalhistas |
| ***Comunicação & Media*** | ***Comunicações media*** | ***meios de comunicação*** |
| Media & Entertainment | MediaandEntertainment | media-e-entretenimento |
| Telecomunicações | Telecomunicações | telecomunicações |
| ***Serviços Profissionais*** | ***Serviços Profissionais*** | ***serviços profissionais*** |
| Legal | Legal | legal |
| Serviços Profissionais de Parceiros | PartnerProfessionalServices | serviços profissionais-parceiros |
| ***Construção de & arquitetura*** | ***Arquitetura EConstrução*** | ***arquitetura e construção*** |
| Outros - Nãoegmentado | Arquitetura EConstrução \_ OutrosUnsegmented | outros não-escamado |
| ***Hospitalidade & Viagem*** | ***HospitalidadeandTravel*** | ***hospitalidade e viagens*** |
|    Hotéis & Lazer | HotéisAndLeisure | hotéis e lazer |
| Transporte & de viagem | ViagensAndTransportation | viagens e transporte |
| Restaurantes & Serviços Alimentares | RestaurantesAndFoodServices | restaurantes e serviços alimentares |
| ***Outras Indústrias do Sector Público*** | ***Outras Indústrias Farmacêuticas*** | ***outras indústrias do sector público*** |
| Pesca & Florestal | Silvicultura e Pesca | silvicultura e pesca |
| Sem fins lucrativos | Sem fins lucrativos | sem fins lucrativos |
| ***Setor Imobiliário*** | ***RealEstate*** | ***imobiliário*** |
| Outros - Nãoegmentado | RealEstate \_ OtherUnsegmented | outros não-escamado |
|||
