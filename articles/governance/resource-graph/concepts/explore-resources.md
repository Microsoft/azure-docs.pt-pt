---
title: Explore os recursos do Azure
description: Saiba como utilizar a linguagem de consulta do gráfico de recursos para explorar os recursos e descobrir como eles estão conectados.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0b4a75558f5e82b707ae5d012acef4d2c5c4b7a0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723805"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Explorar os seus recursos do Azure com o Resource Graph

Gráfico de recursos do Azure fornece a capacidade de explorar e descobrir os recursos do Azure rapidamente e em escala. Concebidos para tempos de resposta rápidos, é uma ótima maneira de aprender sobre o seu ambiente e também sobre as propriedades que compõem os recursos do Azure.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>Explorar máquinas virtuais

Um recurso comum no Azure é uma máquina virtual. Como um tipo de recurso, as máquinas virtuais têm muitas propriedades que podem ser consultadas. Cada propriedade fornece uma opção para encontrar exatamente o recurso que está procurando ou filtragem.

### <a name="virtual-machine-discovery"></a>Deteção de máquinas virtuais

Vamos começar com uma consulta simples para obter uma única VM do nosso ambiente e ver as propriedades devolvidas.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> O Azure PowerShell `Search-AzGraph` cmdlet devolve uma **PSCustomObject** por predefinição. A saída parecem iguais como o que é retornado pela CLI do Azure, o `ConvertTo-Json` cmdlet é utilizado. O valor predefinido para **profundidade** é _2_. Defini-la como _100_ deve converter devolvidos todos os níveis.

Os resultados JSON são estruturados semelhante ao seguinte exemplo:

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

As propriedades, diga-nos informações adicionais sobre o recurso de máquina virtual em si, SKU, sistema operacional, discos, etiquetas, e o grupo de recursos e subscrição é um membro de.

### <a name="virtual-machines-by-location"></a>Máquinas virtuais por localização

Levando o que aprendemos sobre o recurso de máquinas virtuais, vamos utilizar o **localização** propriedade para a contagem de todas as máquinas virtuais com base na localização. Para atualizar a consulta, vamos remover o limite e resumir a contagem de valores de localização.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Os resultados JSON são estruturados semelhante ao seguinte exemplo:

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

Agora podemos ver quantas máquinas de virtuais temos em cada região do Azure.

### <a name="virtual-machines-by-sku"></a>Máquinas virtuais por SKU

Voltando ao propriedades da máquina virtual original, vamos tentar encontrar todas as máquinas virtuais que têm um tamanho SKU de **Standard_B2s**. Olhando para o JSON devolvido, podemos ver que é armazenado no **properties.hardwareprofile.vmsize**. Iremos atualizar a consulta para encontrar todas as VMs que correspondem este tamanho e retornam apenas o nome da VM e região.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>As máquinas virtuais ligadas para discos geridos premium

Se quiséssemos obter os detalhes de discos geridos premium que estejam anexados a eles **Standard_B2s** máquinas virtuais, podemos expandir a consulta para nos dar o ID de recurso desses discos geridos.

```kusto
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Outra forma de obter o SKU teria sido utilizando o **aliases** propriedade **Microsoft.Compute/virtualMachines/sku.name**. Consulte a [Mostrar aliases](../samples/starter.md#show-aliases) e [Mostrar valores distintos de alias](../samples/starter.md#distinct-alias-values) exemplos.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

O resultado é uma lista de IDs de disco.

### <a name="managed-disk-discovery"></a>Deteção de disco gerido

Com o primeiro registo da consulta anterior, vamos explorar as propriedades que existem no disco gerido que foi ligado ao primeira máquina virtual. A consulta atualizada utiliza o ID de disco e altera o tipo.

Exemplo de saída da consulta anterior, por exemplo:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Antes de executar a consulta, como sabemos o **tipo** deve agora ser **Microsoft.Compute/disks**?
Se examinar o ID completo, verá **/providers/Microsoft.Compute/disks/** como parte da cadeia de caracteres. Este fragmento de cadeia de caracteres dá-lhe uma dica sobre o que escreva para procurar. Um método alternativo seria remover o limite por tipo e em vez disso, apenas procurar pelo campo de ID. Como o ID é exclusivo, seria retornado apenas um registo e o **tipo** propriedade nele fornece esse detalhe.

> [!NOTE]
> Para este exemplo funcione, tem de substituir o campo ID com um resultado do seu próprio ambiente.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Os resultados JSON são estruturados semelhante ao seguinte exemplo:

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

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Explorar máquinas virtuais, para localizar endereços IP públicos

Este conjunto de CLI do Azure de consultas primeiro localiza e armazena todos os recursos (NIC) ligados a máquinas virtuais de interfaces de rede. Em seguida, ele usa a lista de NICs para encontrar cada recurso de endereço IP que é um endereço IP público e armazenar esses valores. Por fim, ele fornece uma lista de endereços IP públicos.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

Utilize o `nics.txt` ficheiro na seguinte consulta para obter a interface de rede relacionados detalhes de recursos onde existe um endereço IP público anexado para o NIC.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

Último, utilize a lista de recursos de endereço IP público armazenada no `ips.txt` para obter o endereço IP público real dos mesmos e exibir.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>Passos Seguintes

- Saber mais sobre a [linguagem de consulta](query-language.md)
- Consulte o idioma utilizado na [consultas Starter](../samples/starter.md)
- Consulte avançada usa no [avançadas consultas](../samples/advanced.md)