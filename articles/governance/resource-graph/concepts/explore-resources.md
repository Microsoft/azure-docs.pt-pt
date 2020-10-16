---
title: Explorar os seus recursos do Azure
description: Aprenda a usar a linguagem de consulta de gráfico de recurso para explorar os seus recursos e descobrir como estão conectados.
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2b0ef3935d865618a9d4dda2825f7d4383baf772
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056247"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Explorar os seus recursos do Azure com o Resource Graph

O Azure Resource Graph fornece a capacidade de explorar e descobrir os seus recursos Azure de forma rápida e em escala. Projetado para respostas rápidas, é uma ótima maneira de aprender sobre o seu ambiente e também sobre as propriedades que existem nos seus recursos Azure.

## <a name="explore-virtual-machines"></a>Explore máquinas virtuais

Um recurso comum em Azure é uma máquina virtual. Como tipo de recurso, as máquinas virtuais têm muitas propriedades que podem ser questionadas. Cada propriedade oferece uma opção para filtrar ou encontrar exatamente o recurso que você procura.

### <a name="virtual-machine-discovery"></a>Descoberta de máquinas virtuais

Vamos começar com uma simples consulta para obter um único VM do nosso ambiente e olhar para as propriedades devolvidas.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> O cmdlet Azure PowerShell `Search-AzGraph` devolve por defeito um **PSCustomObject.** Para que a saída pareça igual à que é devolvida pelo Azure CLI, o `ConvertTo-Json` cmdlet é utilizado. O valor predefinido para **Profundidade** é _2_. Defini-lo para _100_ deve converter todos os níveis devolvidos.

Os resultados do JSON são estruturados de forma semelhante ao seguinte exemplo:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

As propriedades nos dizem informações adicionais sobre o próprio recurso da máquina virtual. Estas propriedades incluem: sistema operativo, discos, tags, e o grupo de recursos e subscrição de que é membro.

### <a name="virtual-machines-by-location"></a>Máquinas virtuais por localização

Tomando o que aprendemos sobre o recurso de máquinas virtuais, vamos usar a propriedade de **localização** para contar todas as máquinas virtuais por localização. Para atualizar a consulta, removeremos o limite e resumiremos a contagem dos valores de localização.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Os resultados do JSON são estruturados de forma semelhante ao seguinte exemplo:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Agora podemos ver quantas máquinas virtuais temos em cada região de Azure.

### <a name="virtual-machines-by-sku"></a>Máquinas virtuais por SKU

Voltando às propriedades originais da máquina virtual, vamos tentar encontrar todas as máquinas virtuais que têm um tamanho SKU de **Standard_B2s.** Olhando para o JSON devolvido, vemos que está armazenado em **properties.hardwareprofile.vmsize**. Atualizaremos a consulta para encontrar todos os VMs que correspondam a este tamanho e devolveremos apenas o nome do VM e da região.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Máquinas virtuais ligadas a discos geridos premium

Para obter os detalhes dos discos geridos premium que estão ligados a estas **Standard_B2s** máquinas virtuais, expandimos a consulta para devolver o ID de recursos desses discos geridos.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Outra forma de obter o SKU teria sido usando a propriedade de **pseudónimos** **Microsoft.Compute/virtualMachines/sku.name**. Veja os [pseudónimos show](../samples/starter.md#show-aliases) e [mostre exemplos de valores distintos.](../samples/starter.md#distinct-alias-values)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

O resultado é uma lista de identificações de discos.

### <a name="managed-disk-discovery"></a>Descoberta de disco gerido

Com o primeiro registo da consulta anterior, vamos explorar as propriedades que existem no disco gerido que foi ligado à primeira máquina virtual. A consulta atualizada utiliza o ID do disco e altera o tipo.

Exemplo de saída da consulta anterior, por exemplo:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Antes de executar a consulta, como é que sabíamos que o **tipo** deveria agora ser **Microsoft.Compute/disks?** Se olhar para o ID completo, verá **/fornecedores/Microsoft.Compute/disks/** como parte da cadeia.
Este fragmento de corda dá-lhe uma pista sobre que tipo procurar. Um método alternativo seria remover o limite por tipo e, em vez disso, procurar apenas pelo campo ID. Como o ID é único, apenas um disco seria devolvido e a propriedade **tipo** nele fornece esse detalhe.

> [!NOTE]
> Para que este exemplo funcione, deve substituir o campo de identificação por um resultado do seu próprio ambiente.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Os resultados do JSON são estruturados de forma semelhante ao seguinte exemplo:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Explore máquinas virtuais para encontrar endereços IP públicos

Este conjunto de consultas encontra e armazena todos os recursos de interfaces de rede (NIC) ligados a máquinas virtuais. Em seguida, as consultas usam a lista de NICs para encontrar cada recurso de endereço IP que é um endereço IP público e armazenar esses valores. Finalmente, as consultas fornecem uma lista dos endereços IP públicos.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Utilize o ficheiro (Azure CLI) ou variável (Azure PowerShell) na próxima consulta para obter os detalhes relacionados de recursos de interface de rede onde há um endereço IP público anexado ao NIC.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Por último, utilize a lista de recursos de endereço IP públicos armazenados no ficheiro (Azure CLI) ou variável (Azure PowerShell) para obter o endereço IP público real a partir do objeto e exibição relacionados.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Para ver como realizar estes passos numa única consulta com o `join` operador, consulte as [máquinas virtuais List com](../samples/advanced.md#join-vmpip) a sua interface de rede e amostra de IP pública.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [língua de consulta.](query-language.md)
- Consulte o idioma em uso nas [consultas de arranque](../samples/starter.md).
- Consulte utilizações avançadas em [consultas avançadas.](../samples/advanced.md)
