---
title: Configurações personalizadas para ambientes do serviço de aplicativo – Azure
description: Definições de configuração personalizadas para ambientes do serviço de aplicativo
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 768e354281aeb5e2143ddb0e190b0f27bf4f4e24
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470704"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Definições de configuração personalizadas para ambientes do serviço de aplicativo
## <a name="overview"></a>Descrição geral
Como os ambientes do serviço de aplicativo (ASEs) são isolados para um único cliente, há certas definições de configuração que podem ser aplicadas exclusivamente a ambientes do serviço de aplicativo. Este artigo documenta as várias personalizações específicas que estão disponíveis para ambientes do serviço de aplicativo.

Se você não tiver um Ambiente do Serviço de Aplicativo, consulte [como criar um ambiente do serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

Você pode armazenar Ambiente do Serviço de Aplicativo personalizações usando uma matriz no novo atributo **clusterSettings** . Esse atributo é encontrado no dicionário "Properties" da entidade de Azure Resource Manager de *hostingenvironments* .

O seguinte trecho de modelo abreviado do Resource Manager mostra o atributo **clusterSettings** :

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

O atributo **clusterSettings** pode ser incluído em um modelo do Resource Manager para atualizar o ambiente do serviço de aplicativo.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Usar Azure Resource Explorer para atualizar um Ambiente do Serviço de Aplicativo
Como alternativa, você pode atualizar o Ambiente do Serviço de Aplicativo usando [Azure Resource Explorer](https://resources.azure.com).  

1. No Gerenciador de recursos, vá para o nó da Ambiente do Serviço de Aplicativo (**assinaturas** > **provedores** de > de **resourceGroups** > **Microsoft. Web** > **hostingenvironments**). Em seguida, clique no Ambiente do Serviço de Aplicativo específico que você deseja atualizar.
2. No painel direito, clique em **leitura/gravação** na barra de ferramentas superior para permitir a edição interativa no Gerenciador de recursos.  
3. Clique no botão de **edição** azul para tornar o modelo do Resource Manager editável.
4. Role até a parte inferior do painel direito. O atributo **clusterSettings** está na parte inferior, em que você pode inserir ou atualizar seu valor.
5. Digite (ou copie e Cole) a matriz de valores de configuração que você deseja no atributo **clusterSettings** .  
6. Clique no botão verde **Put** localizado na parte superior do painel direito para confirmar a alteração no ambiente do serviço de aplicativo.

No entanto, você envia a alteração, leva aproximadamente 30 minutos multiplicado pelo número de front-ends na Ambiente do Serviço de Aplicativo para que a alteração entre em vigor.
Por exemplo, se um Ambiente do Serviço de Aplicativo tiver quatro front-ends, levará aproximadamente duas horas para que a atualização de configuração seja concluída. Enquanto a alteração de configuração está sendo distribuída, nenhuma outra operação de dimensionamento ou operação de alteração de configuração pode ocorrer na Ambiente do Serviço de Aplicativo.

## <a name="disable-tls-10-and-tls-11"></a>Desabilitar TLS 1,0 e TLS 1,1

Se você quiser gerenciar as configurações de TLS em um aplicativo pela base do aplicativo, poderá usar as diretrizes fornecidas com a documentação [impor configurações de TLS](../configure-ssl-bindings.md#enforce-tls-versions) . 

Se você quiser desabilitar todos os tráfegos TLS 1,0 e TLS 1,1 de entrada para todos os aplicativos em um ASE, poderá definir a seguinte entrada **clusterSettings** :

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

O nome da configuração diz 1,0, mas quando configurado, ele desabilita o TLS 1,0 e o TLS 1,1.

## <a name="change-tls-cipher-suite-order"></a>Alterar a ordem do conjunto de codificação TLS
Outra pergunta dos clientes é se eles podem modificar a lista de codificações negociadas pelo servidor e isso pode ser feito modificando o **clusterSettings** , conforme mostrado abaixo. A lista de conjuntos de codificação disponíveis pode ser recuperada [neste artigo do MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Se os valores incorretos estiverem definidos para o conjunto de codificação que o SChannel não pode entender, toda a comunicação TLS para o servidor poderá parar de funcionar. Nesse caso, será necessário remover a entrada *FrontEndSSLCipherSuiteOrder* de **clusterSettings** e enviar o modelo do Resource Manager atualizado para reverter para as configurações padrão do pacote de codificação.  Use essa funcionalidade com cuidado.
> 
> 

## <a name="get-started"></a>Introdução
O site do modelo de início rápido do Azure Resource Manager inclui um modelo com a definição de base para [criar um ambiente do serviço de aplicativo](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
