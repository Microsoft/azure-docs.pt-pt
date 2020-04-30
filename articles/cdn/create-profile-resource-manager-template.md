---
title: Quickstart - Criar um perfil e ponto final usando modelos de Gestor de Recursos
titleSuffix: Azure Content Delivery Network
description: Saiba como criar um perfil de rede de entrega de conteúdo azure e ponto final usando modelos de Gestor de Recursos
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
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: dfd7b933502b96c0952a24dbee563e9b537dcdd8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683465"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Quickstart: Criar um perfil E ponto final do Azure CDN utilizando o modelo de Gestor de Recursos

Neste arranque rápido, você implementa um modelo de Gestor de Recursos Azure usando CLI. O modelo que cria implementa um perfil CDN e um ponto final cdN para fazer frente à sua aplicação web.
Deve levar cerca de dez minutos para completar estes passos.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Prequisitos

Para efeitos deste arranque rápido, deve ter uma Aplicação Web para usar como origem. O exemplo Aplicação Web usado neste quickstart foi implementado parahttps://cdndemo.azurewebsites.net

Para mais informações, consulte [Criar uma aplicação web HTML estática em Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Todos os recursos devem ser mobilizados no mesmo grupo de recursos.

Crie o grupo de recursos no local que seleciona. Este exemplo mostra a criação de um grupo de recursos chamado CDN na localização leste dos EUA.

```azurecli-interactive
az group create --name cdn --location eastus
```

![Novo Grupo de Recursos](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

Neste passo, cria-se um ficheiro de modelo que implementa os recursos.

Enquanto este exemplo passa por um cenário de aceleração geral do site, existem muitas outras configurações que podem ser configuradas. Estas definições estão disponíveis na referência do modelo do Gestor de Recursos do Azure. Consulte referências para [o perfil cdn](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) e o ponto final do [perfil CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Note que o Microsoft CDN não suporta modificar a lista do tipo de conteúdo.

Guarde o modelo como **gestor de recursos-cdn.json**.

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

Despolete o modelo utilizando o Azure CLI. Ser-lhe-á solicitada seleção para 2 inputs:

**cdnProfileSku** - o fornecedor cdN que pretende utilizar. As opções são:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** - o ponto final que será servido através do CDN, por exemplo, cdndemo.azurewebsites.net.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Modelo de gestor de recursos de implementação](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Ver o perfil da CDN

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![Ver perfil CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Ver o Ponto Final do CDN para o perfil standard-microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Ver ponto final do CDN](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Utilize o Nome do Anfitrião para visualizar o conteúdo. Por exemplo, aceda a https:\//cdndemo-azurewebsites-net.azureedge.net usando o seu navegador.

## <a name="clean-up"></a>Limpeza

A eliminação do grupo de recursos removerá automaticamente todos os recursos que nele foram utilizados.

```azurecli-interactive
az group delete --name cdn
```

![Eliminar grupo de recursos](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referências

* Perfil CDN - Referência do modelo do gestor de [recursos azure](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN Endpoint - Documentação de referência de [modelo de gestor de recursos azure](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como adicionar um domínio personalizado ao ponto final de CDN, veja o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um domínio personalizado ao ponto final da CDN do Azure](cdn-map-content-to-custom-domain.md)
