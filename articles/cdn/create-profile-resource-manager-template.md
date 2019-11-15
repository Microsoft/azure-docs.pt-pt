---
title: Início rápido-criar um perfil e um ponto de extremidade usando modelos do Resource Manager
titleSuffix: Azure Content Delivery Network
description: Saiba como criar um perfil de rede e um ponto de extremidade de entrega de conteúdo do Azure usando modelos do Resource Manager
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: b711a12161bc134bdcbb8c1f3e74f2e5ae06e701
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083144"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Início rápido: criar um perfil e um ponto de extremidade da CDN do Azure usando o modelo do Resource Manager

Neste guia de início rápido, você implanta um modelo de Azure Resource Manager usando a CLI. O modelo que você cria implanta um perfil CDN e um ponto de extremidade CDN para antecipar seu aplicativo Web.
Deve levar cerca de dez minutos para concluir essas etapas.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Selecionado pré-requisitos

Para os fins deste guia de início rápido, você deve ter um aplicativo Web para usar como sua origem. O aplicativo Web de exemplo usado neste guia de início rápido foi implantado para https://cdndemo.azurewebsites.net

Para obter mais informações, consulte [criar um aplicativo Web HTML estático no Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Todos os recursos devem ser implantados no mesmo grupo de recursos.

Crie o grupo de recursos no local que você selecionar. Este exemplo mostra a criação de um grupo de recursos chamado CDN no local leste dos EUA.

```bash
az group create --name cdn --location eastus
```

![Novo grupo de recursos](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

Nesta etapa, você cria um arquivo de modelo que implanta os recursos.

Embora este exemplo percorra um cenário de aceleração de site geral, há muitas outras configurações que podem ser configuradas. Essas configurações estão disponíveis na referência do modelo de Azure Resource Manager. Consulte as referências para o [perfil CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) e o [ponto de extremidade do perfil CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Observe que a CDN da Microsoft não dá suporte à modificação da lista de tipos de conteúdo.

Salve o modelo como **Resource-Manager-CDN. JSON**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>Criar os recursos

Implante o modelo usando CLI do Azure. Serão solicitadas duas entradas:

**cdnProfileSku** -o provedor de CDN que você deseja usar. As opções são:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** -o ponto de extremidade que será servido por meio da CDN, por exemplo, cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Implantar modelo do Resource Manager](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Exibir o perfil CDN

```bash
az cdn profile list --resource-group cdn -o table
```

![Exibir Perfil CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Exibir o ponto de extremidade CDN para o perfil padrão-Microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Exibir ponto de extremidade CDN](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Use o nome do host para exibir o conteúdo. Por exemplo, acesse https://cdndemo-azurewebsites-net.azureedge.net usando seu navegador.

## <a name="clean-up"></a>Limpeza

A exclusão do grupo de recursos removerá automaticamente todos os recursos que foram implantados nele.

```bash
az group delete --name cdn
```

![Excluir grupo de recursos](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referências

* [Referência de modelo de Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) de perfil CDN
* Ponto de extremidade CDN- [documentação de referência do modelo de Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como adicionar um domínio personalizado ao ponto final de CDN, veja o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um domínio personalizado ao ponto final da CDN do Azure](cdn-map-content-to-custom-domain.md)
