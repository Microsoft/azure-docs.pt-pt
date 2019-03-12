---
title: Início rápido - criar um perfil de rede de entrega de conteúdos do Azure e o ponto final com modelos do Resource Manager | Documentos da Microsoft
description: Saiba como criar um perfil de rede de entregar conteúdos do Azure e o ponto final com modelos do Resource Manager
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5c393916bc977f4e0bc51913bdb2dfbbd6677c97
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727553"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Início rápido: Criar um perfil de CDN do Azure e o ponto de extremidade usando o modelo do Resource Manager

Neste início rápido, vai implementar um modelo do Azure Resource Manager com CLI. O modelo criado por si implementa um perfil CDN e ponto final da CDN para a sua aplicação web de front.
Deve demorar cerca de dez minutos para concluir estes passos.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Pré-requisitos

Para efeitos deste início rápido, tem de ter uma aplicação Web para utilizar como a sua origem. O exemplo de que aplicativo Web utilizado neste início rápido foi implementado https://cdndemo.azurewebsites.net

Para obter mais informações, consulte [criar uma aplicação web HTML estática no Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Todos os recursos tem de ser implementados no mesmo grupo de recursos.

Crie o grupo de recursos na localização que selecionou. Este exemplo mostra a criação de um grupo de recursos com o nome da cdn na localização E.U.A. Leste.

```bash
az group create --name cdn --location eastus
```

![Novo Grupo de Recursos](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Criar o modelo do Resource Manager

Neste passo, vai criar um arquivo de modelo que implementa os recursos.

Embora este exemplo descreve como um cenário de aceleração de site geral, isso significa que há muitas outras configurações que podem ser configuradas. Estas definições estão disponíveis na referência de modelo do Azure Resource Manager. Consulte referências para [perfil da CDN](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles) e [ponto final do perfil de CDN](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Tenha em atenção que o Microsoft CDN não suporta a modificar a lista de tipo de conteúdo.

Guardar o modelo como **resource-manager-cdn.json**.

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

Implemente o modelo com a CLI do Azure. Será solicitado para 2 entradas:

**cdnProfileSku** -o fornecedor CDN que pretende utilizar. As opções são:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** -o ponto final que irá ser atendido por meio da CDN, por exemplo, cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Implementar o modelo do Resource Manager](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Visualizar o perfil CDN

```bash
az cdn profile list --resource-group cdn -o table
```

![Ver perfil CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Ver o ponto final da CDN para a perfil padrão da microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Ponto final da CDN do Vista](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Utilize o nome de anfitrião para ver o conteúdo. Por exemplo, acessar https://cdndemo-azurewebsites-net.azureedge.net utilizando o seu browser.

## <a name="clean-up"></a>Limpeza

A eliminar o grupo de recursos removerá automaticamente todos os recursos que foram implementados no mesmo.

```bash
az group delete --name cdn
```

![Eliminar Grupo de Recursos](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referências

* Perfil CDN - [referência de modelo do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles)
* Ponto final da CDN - [documentação de referência de modelo do Azure Resource Manager](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como adicionar um domínio personalizado ao ponto final de CDN, veja o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Add a custom domain to your Azure CDN endpoint](cdn-map-content-to-custom-domain.md) (Adicionar um domínio personalizado ao ponto final da CDN do Azure)
