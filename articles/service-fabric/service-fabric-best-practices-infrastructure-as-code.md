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
ms.openlocfilehash: 2dfe1493c6611fb69a417895aaa1028ad5881b9c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237424"
---
# <a name="infrastructure-as-code"></a>Infraestrutura como código

Num cenário de produção, crie clusters do Azure Service Fabric com modelos do Resource Manager. Modelos do Resource Manager fornecem maior controle das propriedades de recurso e certifique-se de que tem um modelo de recursos consistentes.

Modelos do Gestor de recursos de exemplo estão disponíveis para Windows e Linux no [exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Estes modelos podem ser utilizados como ponto de partida para o modelo de cluster. Baixe `azuredeploy.json` e `azuredeploy.parameters.json` e editá-los para satisfazer os seus requisitos personalizados.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Recursos de infraestrutura de serviço do Azure

Pode implementar serviços e aplicações no seu cluster do Service Fabric através do Azure Resource Manager. Ver [gerenciar aplicativos e serviços como recursos do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) para obter detalhes. Seguem-se melhor prática Service Fabric application recursos específicos para incluir nos seus recursos de modelo do Resource Manager.

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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configuração de atualização automática da sistema operativo a Máquina Virtual do Azure 
A atualização de suas máquinas virtuais é uma operação iniciada pelo utilizador e é recomendado que utilize [Máquina Virtual de dimensionamento definido automática atualização do sistema operativo](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) para gerenciamento de patches do host; de clusters do Azure Service Fabric Patch Orchestration Application é uma solução alternativa que destina-se estiverem alojados fora do Azure, embora POA pode ser utilizado no Azure, com custos gerais de hospedagem POA no Azure que está a ser um motivo comum para dar preferência a Máquina Virtual a atualização automática do sistema operativo ao longo do POA. Seguem-se as propriedades do modelo de computação Máquina Virtual de dimensionamento definido do Resource Manager para ativar a atualização de SO automática:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ao utilizar as atualizações automáticas de SO com o Service Fabric, a nova imagem de sistema operacional é lançada um domínio de atualização em simultâneo para manter uma elevada disponibilidade dos serviços em execução no Service Fabric. Para utilizar as atualizações automáticas de SO no Service Fabric cluster tem de ser configurado para utilizar o escalão de durabilidade Silver ou superior.

Certifique-se de que a seguinte chave de registo é definido como falso para impedir que as máquinas de host windows iniciar coordenadas atualizações: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Seguem-se as propriedades do modelo de computação Máquina Virtual de dimensionamento definido do Resource Manager para definir a chave de registo WindowsUpdate como false:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuração de atualização de Cluster do Azure Service Fabric
Segue-se a propriedade de modelo do Resource Manager para ativar a atualização automática do cluster do Service Fabric:
```json
"upgradeMode": "Automatic",
```
Para atualizar manualmente o cluster, transfira a distribuição do cab/deb a uma máquina virtual de cluster e, em seguida, invoque o PowerShell seguinte:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Crie um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
