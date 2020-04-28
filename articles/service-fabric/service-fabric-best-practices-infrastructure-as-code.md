---
title: Infraestrutura de tecido de serviço Azure como Código Boas Práticas
description: Boas práticas e considerações de design para a gestão do Tecido de Serviço Azure como infraestrutura como código.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551833"
---
# <a name="infrastructure-as-code"></a>Infraestrutura como código

Num cenário de produção, crie clusters de tecido de serviço Azure utilizando modelos de Gestor de Recursos. Os modelos do Gestor de Recursos proporcionam um maior controlo das propriedades dos recursos e asseguram-se de que tem um modelo de recursos consistente.

Os modelos do Sample Resource Manager estão disponíveis para Windows e Linux nas [amostras Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Estes modelos podem ser usados como ponto de partida para o seu modelo de cluster. Descarregue `azuredeploy.json` e `azuredeploy.parameters.json` edite-os para satisfazer os seus requisitos personalizados.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para implementar `azuredeploy.json` `azuredeploy.parameters.json` os modelos e modelos que descarregou acima, utilize os seguintes comandos Azure CLI:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Criar um recurso usando powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Recursos tecidos de serviço Azure

Pode implementar aplicações e serviços no seu cluster Service Fabric através do Azure Resource Manager. Consulte [a Manage aplicações e serviços como recursos do Gestor de Recursos Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource) para mais detalhes. Seguem-se os recursos específicos de aplicação de aplicação service fabric de boas práticas para incluir nos recursos do seu modelo de Gestor de Recursos.

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

Para implementar a sua aplicação utilizando o Gestor de Recursos Azure, primeiro deve criar um pacote de aplicação de tecido de serviço [sfpkg.](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) O seguinte guião python é um exemplo de como criar um sfpkg:

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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configuração automática de upgrade automática do sistema operativo de máquinas virtuais Azure 
A atualização das suas máquinas virtuais é uma operação iniciada pelo utilizador, e recomenda-se que utilize a [atualização automática](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) do sistema operativo automático para os clusters de tecido de serviço Azure; Patch Orchestration Application é uma solução alternativa que se destina quando hospedada fora de Azure, embora poA possa ser usado em Azure, com a sobrecarga de hospedagem poa em Azure sendo uma razão comum para preferir o Sistema Operativo Virtual Máquina Automática upgrade em vez de POA. Seguem-se as propriedades do modelo de gestor de recursos de conjunto de escala de máquina virtual computacional para permitir a atualização do Auto OS:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ao utilizar atualizações automáticas de OS com tecido de serviço, a nova imagem de OS é lançada um Domínio de Atualização de cada vez para manter uma elevada disponibilidade dos serviços em funcionamento no Tecido de Serviço. Para utilizar atualizações automáticas de OS em Tecido de Serviço, o seu cluster deve ser configurado para utilizar o Nível de Durabilidade prateada ou superior.

Certifique-se de que a seguinte tecla de registo está definida como falsa para evitar que as máquinas de hospedar do windows iniciem atualizações descoordenadas: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\WindowsUpdate\AU.

Seguem-se as propriedades do modelo de gestor de recursos de conjunto de escala de máquina virtual Compute para definir a chave de registo do WindowsUpdate para falsas:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuração de upgrade de cluster de tecido de serviço azure
Segue-se a propriedade de modelo de gestor de recursos de cluster de tecido de serviço para permitir a atualização automática:
```json
"upgradeMode": "Automatic",
```
Para atualizar manualmente o seu cluster, descarregue a distribuição de táxi/deb para uma máquina virtual de cluster e, em seguida, invoque o seguinte PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação](service-fabric-tutorial-create-vnet-and-windows-cluster.md) de cluster de tecido de serviço para Windows Server
* Criar um cluster em VMs ou computadores em execução linux: [Criar um cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
