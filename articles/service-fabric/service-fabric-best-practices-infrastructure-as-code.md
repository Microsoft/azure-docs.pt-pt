---
title: Infraestrutura de Service Fabric do Azure como práticas recomendadas de código | Microsoft Docs
description: Práticas recomendadas para o gerenciamento de Service Fabric como infraestrutura como código.
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
ms.openlocfilehash: 83439a913989875c5e26ee51901f10558e5debd5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955619"
---
# <a name="infrastructure-as-code"></a>Infraestrutura como código

Em um cenário de produção, crie clusters de Service Fabric do Azure usando modelos do Resource Manager. Os modelos do Resource Manager fornecem um controle maior das propriedades de recurso e garantem que você tenha um modelo de recurso consistente.

Os modelos do Resource Manager de exemplo estão disponíveis para Windows e Linux nos [exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Esses modelos podem ser usados como um ponto de partida para o modelo de cluster. Baixe `azuredeploy.json` e`azuredeploy.parameters.json` edite-os para atender aos seus requisitos personalizados.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para implantar os `azuredeploy.json` modelos `azuredeploy.parameters.json` e que você baixou acima, use os seguintes comandos de CLI do Azure:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Criando um recurso usando o PowerShell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Recursos de Service Fabric do Azure

Pode implementar serviços e aplicações no seu cluster do Service Fabric através do Azure Resource Manager. Consulte [gerenciar aplicativos e serviços como Azure Resource Manager recursos](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) para obter detalhes. Veja a seguir uma prática recomendada Service Fabric recursos específicos do aplicativo a serem incluídos em seus recursos de modelo do Resource Manager.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Para implantar seu aplicativo usando Azure Resource Manager, primeiro você deve [criar um pacote de](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) aplicativo do sfpkg Service Fabric. O script Python a seguir é um exemplo de como criar um sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configuração de atualização automática do sistema operacional da máquina virtual do Azure 
A atualização de suas máquinas virtuais é uma operação iniciada pelo usuário e é recomendável que você use a [atualização do sistema operacional automático do conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) para o gerenciamento de patches do host do Azure Service Fabric clusters; O aplicativo de orquestração de patch é uma solução alternativa destinada a quando hospedado fora do Azure, embora POA possa ser usado no Azure, com sobrecarga de Hospedagem de POA no Azure sendo um motivo comum para preferir a atualização automática do sistema operacional da máquina virtual em POA. A seguir estão as propriedades do modelo do Gerenciador de recursos do conjunto de dimensionamento de máquinas virtuais de computação para habilitar a atualização automática do sistema operacional:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ao usar atualizações automáticas do sistema operacional com o Service Fabric, a nova imagem do sistema operacional é distribuída em um domínio de atualização por vez para manter a alta disponibilidade dos serviços em execução no Service Fabric. Para utilizar atualizações automáticas do sistema operacional no Service Fabric o cluster deve ser configurado para usar a camada de durabilidade prateada ou superior.

Verifique se a seguinte chave do registro está definida como false para impedir que suas máquinas host do Windows iniciem atualizações não coordenadas: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

A seguir estão as propriedades do modelo do Gerenciador de recursos do conjunto de dimensionamento de máquinas virtuais de computação para definir a chave do registro do WindowsUpdate como false:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuração de atualização de cluster do Azure Service Fabric
A seguir está a Service Fabric propriedade de modelo do Gerenciador de recursos de cluster para habilitar a atualização automática:
```json
"upgradeMode": "Automatic",
```
Para atualizar manualmente o cluster, baixe a distribuição cab/Deb para uma máquina virtual de cluster e, em seguida, invoque o PowerShell a seguir:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Passos Seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Service Fabric a criação do cluster para Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
