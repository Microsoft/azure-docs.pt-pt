---
title: Infraestrutura de tecido de serviço Azure como código das melhores práticas
description: Boas práticas e considerações de design para gerir o Azure Service Fabric como infraestrutura como código.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b765d92778df40caec0864dc6f547324216fdb07
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611985"
---
# <a name="infrastructure-as-code"></a>Infraestrutura como código

Num cenário de produção, crie clusters de tecido de serviço Azure usando modelos de Gestor de Recursos. Os modelos do Gestor de Recursos proporcionam um maior controlo das propriedades dos recursos e asseguram que tem um modelo de recursos consistente.

Os modelos do Gestor de Recursos da Amostra estão disponíveis para Windows e Linux nas [amostras Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Estes modelos podem ser usados como ponto de partida para o seu modelo de cluster. Faça o download `azuredeploy.json` e `azuredeploy.parameters.json` edite-os para satisfazer os seus requisitos personalizados.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para implementar os `azuredeploy.json` modelos e `azuredeploy.parameters.json` modelos que descarregou acima, utilize os seguintes comandos Azure CLI:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Criar um recurso usando Powershell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Recursos de tecido de serviço Azure

Pode implementar aplicações e serviços no seu cluster de Tecidos de Serviço através do Azure Resource Manager. Consulte [as aplicações e serviços de Gestão como Recursos Azure Resource Manager](./service-fabric-application-arm-resource.md) para obter mais detalhes. Seguem-se recursos específicos de aplicação de tecido de serviço de boas práticas para incluir nos recursos do modelo do Gestor de Recursos.

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

Para implementar a sua aplicação utilizando o Azure Resource Manager, primeiro tem de criar um pacote de aplicação de tecido de serviço [sfpkg.](./service-fabric-package-apps.md#create-an-sfpkg) O seguinte script python é um exemplo de como criar um sfpkg:

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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Configuração automática de upgrade do sistema operativo da máquina virtual Azure 
A atualização das suas máquinas virtuais é uma operação iniciada pelo utilizador, e recomenda-se que utilize [a atualização do Sistema Operativo Automático conjunto de máquinas virtuais](service-fabric-patch-orchestration-application.md) para a gestão de patchs de anfitrião de clusters de tecidos de serviço Azure; Patch Orchestration Application é uma solução alternativa que se destina a quando hospedado fora de Azure, embora o POA possa ser usado em Azure, com a sobrecarga de hospedagem de POA em Azure sendo uma razão comum para preferir o Sistema Operativo Virtual Funcionamento Automático do Sistema Operativo em vez de POA. Seguem-se as propriedades do modelo do Gestor de Recursos do Conjunto de Máquinas Virtuais compute para permitir a atualização do Auto OS:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ao utilizar atualizações automáticas de OS com Tecido de Serviço, a nova imagem de SO é lançada um Domínio de Atualização de cada vez para manter uma elevada disponibilidade dos serviços em execução no Tecido de Serviço. Para utilizar atualizações automáticas de OS no Tecido de Serviço, o seu cluster deve ser configurado para utilizar o Nível de Durabilidade prateada ou superior.

Certifique-se de que a seguinte chave de registo está definida como falsa para evitar que as máquinas de anfitrião do windows iniciem atualizações descoordenadas: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Seguem-se as propriedades do modelo do Gestor de Recursos do Conjunto de Máquinas Virtuais compute para definir a chave de registo do WindowsUpdate para falso:
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Configuração de upgrade de cluster de tecido de serviço Azure
Segue-se a propriedade do modelo do Gestor de Recursos do Cluster de Tecido de Serviço para permitir a atualização automática:
```json
"upgradeMode": "Automatic",
```
Para atualizar manualmente o seu cluster, descarregue a distribuição da cabina/deb para uma máquina virtual de cluster e, em seguida, invoque o seguinte PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação de cluster de tecido de serviço para windows server](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)
