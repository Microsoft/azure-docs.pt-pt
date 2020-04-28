---
title: Explorar os seus recursos do Azure
description: Aprenda a usar a linguagem de consulta do Graph de Recursos para explorar os seus recursos e descobrir como estão conectados.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 0c191915b8c558d80ffef554ef758a35157e035c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76156986"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Explorar os seus recursos do Azure com o Resource Graph

O Azure Resource Graph fornece a capacidade de explorar e descobrir os seus recursos Azure de forma rápida e em escala. Projetado para respostas rápidas, é uma ótima maneira de aprender sobre o seu ambiente e também sobre as propriedades que compõem os seus recursos Azure.

## <a name="explore-virtual-machines"></a>Explore máquinas virtuais

Um recurso comum em Azure é uma máquina virtual. Como um tipo de recurso, as máquinas virtuais têm muitas propriedades que podem ser consultadas. Cada propriedade fornece uma opção para filtrar ou encontrar exatamente o recurso que procura.

### <a name="virtual-machine-discovery"></a>Descoberta de máquina virtual

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
> O cmdlet `Search-AzGraph` Azure PowerShell devolve um **PSCustomObject** por padrão. Para que a saída seja igual à que é `ConvertTo-Json` devolvida pelo Azure CLI, o cmdlet é utilizado. O valor predefinido para **profundidade** é _de 2_. A definição para _100_ deve converter todos os níveis devolvidos.

Os resultados da JSON são estruturados de forma semelhante ao seguinte exemplo:

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

As propriedades dizem-nos informações adicionais sobre o próprio recurso da máquina virtual, tudo desde SKU, OS, discos, tags, e o grupo de recursos e subscrição do qual é membro.

### <a name="virtual-machines-by-location"></a>Máquinas virtuais por localização

Tomando o que aprendemos sobre o recurso das máquinas virtuais, vamos usar a propriedade de **localização** para contar todas as máquinas virtuais por localização. Para atualizar a consulta, removeremos o limite e resumiremos a contagem dos valores de localização.

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

Os resultados da JSON são estruturados de forma semelhante ao seguinte exemplo:

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

Voltando às propriedades originais da máquina virtual, vamos tentar encontrar todas as máquinas virtuais que têm um tamanho SKU de **Standard_B2s**. Olhando para o regresso da JSON, vemos que está armazenado em **propriedades.hardwareprofile.vmsize**. Atualizaremos a consulta para encontrar todos os VMs que correspondam a este tamanho e devolveremos apenas o nome do VM e da região.

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

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Máquinas virtuais ligadas a discos geridos por prémios

Se quiséssemos obter os detalhes dos discos geridos por prémios que estão ligados a estas máquinas virtuais **Standard_B2s,** podemos expandir a consulta para nos dar a identificação de recursos desses discos geridos.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Outra forma de obter o SKU teria sido usando **a** propriedade pseudónimo **Microsoft.Compute/virtualMachines/sku.name**. Consulte os [pseudónimos do Show](../samples/starter.md#show-aliases) e mostre exemplos [de valores distintos.](../samples/starter.md#distinct-alias-values)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

O resultado é uma lista de identificações de discos.

### <a name="managed-disk-discovery"></a>Descoberta de disco gerido

Com o primeiro disco da consulta anterior, vamos explorar as propriedades que existem no disco gerido que foi anexado à primeira máquina virtual. A consulta atualizada utiliza o ID do disco e altera o tipo.

Por exemplo, a saída da consulta anterior, por exemplo:

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

Antes de executar a consulta, como sabíamos que o **tipo** deveria agora ser **Microsoft.Compute/discos?**
Se olhar para o ID completo, verá **/fornecedores/Microsoft.Compute/disks/** como parte da cadeia. Este fragmento de corda dá-lhe uma pista sobre que tipo procurar. Um método alternativo seria remover o limite por tipo e, em vez disso, apenas pesquisar pelo campo de identificação. Como o ID é único, apenas um disco seria devolvido e a propriedade **tipo** nele fornece esse detalhe.

> [!NOTE]
> Para que este exemplo funcione, tem de substituir o campo de identificação por um resultado do seu próprio ambiente.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Os resultados da JSON são estruturados de forma semelhante ao seguinte exemplo:

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

Este conjunto de consultas encontra e armazena todos os recursos de interfaces de rede (NIC) ligados a máquinas virtuais. Em seguida, as consultas utilizam a lista de NICs para encontrar cada recurso de endereço IP que é um endereço IP público e armazenar esses valores. Finalmente, as consultas fornecem uma lista dos endereços IP públicos.

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

Utilize o ficheiro (Azure CLI) ou variável (Azure PowerShell) na consulta seguinte para obter os detalhes de recursos de interface de rede relacionados onde há um endereço IP público anexado ao NIC.

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

Por último, utilize a lista de recursos de endereçoIP públicos armazenados no ficheiro (Azure CLI) ou variável (Azure PowerShell) para obter o endereço IP público real do objeto e do ecrã relacionados.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Para ver como realizar estes passos numa `join` única consulta com o operador, consulte as [máquinas virtuais da Lista com](../samples/advanced.md#join-vmpip) a sua interface de rede e amostra ip pública.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [linguagem de consulta.](query-language.md)
- Consulte o idioma utilizado nas [consultas de arranque](../samples/starter.md).
- Consulte [utilizações avançadas em consultas avançadas.](../samples/advanced.md)
