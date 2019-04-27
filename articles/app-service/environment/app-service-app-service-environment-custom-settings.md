---
title: Definições personalizadas para ambientes de serviço de aplicações - Azure
description: Definições de configuração personalizada para ambientes de serviço de aplicações
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 6463759dbd217cd054f838c09c7cfcf99a06aa2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765079"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Definições de configuração personalizada para ambientes de serviço de aplicações
## <a name="overview"></a>Descrição geral
Como os ambientes do serviço de aplicações (ASEs) são isolados para um único cliente, há determinadas definições de configuração que podem ser aplicadas exclusivamente para ambientes de serviço de aplicações. Este artigo documenta as diversas personalizações específicas que estão disponíveis para ambientes de serviço de aplicações.

Se não tiver um ambiente de serviço de aplicações, consulte [como criar um ambiente de serviço de aplicações](app-service-web-how-to-create-an-app-service-environment.md).

Pode armazenar as personalizações de ambiente de serviço de aplicações através de uma matriz na nova **clusterSettings** atributo. Este atributo for encontrado no dicionário de "Propriedades" a *hostingEnvironments* entidade do Azure Resource Manager.

O seguinte abreviado fragmento mostra modelo do Resource Manager a **clusterSettings** atributo:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

O **clusterSettings** atributo pode ser incluído num modelo do Resource Manager para atualizar o ambiente de serviço de aplicações.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilize o Explorador de recursos do Azure para atualizar um ambiente de serviço de aplicações
Em alternativa, pode atualizar o ambiente de serviço de aplicações, utilizando [Explorador de recursos do Azure](https://resources.azure.com).  

1. No Explorador de recursos, vá para o nó para o ambiente de serviço de aplicações (**subscrições** > **resourceGroups** > **fornecedores**  >  **Microsoft. Web** > **hostingEnvironments**). Em seguida, clique o ambiente de serviço de aplicações específicos que pretende atualizar.
2. No painel da direita, clique em **leitura/escrita** na barra de ferramentas superior, para permitir interação de edição no Explorador de recursos.  
3. Clique a azul **editar** botão para fazer com que o modelo do Resource Manager editável.
4. Desloque-se para a parte inferior do painel direito. O **clusterSettings** atributo está na parte inferior, onde pode criar ou atualizar o seu valor.
5. Escreva (ou copie e cole) a matriz de valores de configuração que pretende no **clusterSettings** atributo.  
6. Clique no verde **colocar** botão que tem na parte superior do painel direito, para confirmar a alteração para o ambiente de serviço de aplicações.

No entanto, submeter a alteração, demora aproximadamente 30 minutos, multiplicados pelo número de front-ends no ambiente de serviço de aplicações para que a alteração tenha efeito.
Por exemplo, se um ambiente de serviço de aplicações tem quatro front-ends, demorará cerca de duas horas para a atualização de configuração concluir. Embora a alteração de configuração está a ser lançada, sem outras operações de dimensionamento ou operações de alteração de configuração podem ocorrer no ambiente do serviço de aplicações.

## <a name="disable-tls-10-and-tls-11"></a>Desativar TLS 1.0 e TLS 1.1

Se pretende gerir as definições de TLS numa base por aplicação, em seguida, pode utilizar as diretrizes fornecidas com o [definições de impor TLS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions) documentação. 

Se pretender desativar todos os TLS 1.0 e TLS 1.1 tráfego de entrada para todas as aplicações num ASE, pode definir o seguinte **clusterSettings** entrada:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

O nome da definição diz 1.0, mas quando configurado, desativa o TLS 1.0 e TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Ordem do conjunto de cifras TLS de alteração
Outra questão de clientes é se eles podem modificar a lista de cifras negociado pelo seu servidor e isso pode ser obtido ao modificar os **clusterSettings** conforme mostrado abaixo. A lista de conjuntos de cifras disponíveis pode ser obtida a partir [este artigo MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Se valores incorretos são definidos para o conjunto de cifras que não é possível compreender o SChannel, toda a comunicação TLS ao seu servidor poderá parar de funcionar. Nesse caso, terá de remover as *FrontEndSSLCipherSuiteOrder* entrada de **clusterSettings** e submeta o modelo do Resource Manager atualizado para reverter para o conjunto de cifras predefinido definições.  Utilize esta funcionalidade com cuidado.
> 
> 

## <a name="get-started"></a>Introdução
O modelo de sites do Gestor de recursos de início rápido do Azure inclui um modelo com a definição de base para [criar um ambiente de serviço de aplicações](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
