---
title: Infraestrutura de Service Fabric como código de melhores práticas do Azure | Documentos da Microsoft
description: Melhores práticas para gerir recursos de infraestrutura do serviço como infraestrutura como código.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 2a4c3adb39ebf6e58770348eccc8c78e8ff92167
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804472"
---
# <a name="infrastructure-as-code"></a>Infraestrutura como código

Num cenário de produção, crie clusters do Azure Service Fabric com modelos do Resource Manager. Modelos do Resource Manager fornecem maior controle das propriedades de recurso e certifique-se de que tem um modelo de recursos consistentes.

Modelos do Gestor de recursos de exemplo estão disponíveis para Windows e Linux no [exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Estes modelos podem ser utilizados como ponto de partida para o modelo de cluster. Baixe `azuredeploy.json` e `azuredeploy.parameters.json` e editá-los para satisfazer os seus requisitos personalizados.

Para implementar o `azuredeploy.json` e `azuredeploy.parameters.json` modelos que transferiu anteriormente, utilize os seguintes comandos do CLI do Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Criação de um recurso com o Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
New-AzureRmResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Recursos de infraestrutura de serviço do Azure

Pode implementar aplicações e serviços em cluster do Service Fabric através do Azure Resource Manager. Ver [gerenciar aplicativos e serviços como recursos do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) para obter detalhes. Seguem-se melhor prática Service Fabric application recursos específicos para incluir nos seus recursos de modelo do Resource Manager.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Para implementar a aplicação com o Azure Resource Manager, primeiro tem de [criar um sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) pacote de aplicação do Service Fabric. O script de python seguinte é um exemplo de como criar um sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Crie um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)