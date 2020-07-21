---
title: Azure CLI - Restringir o acesso à importação/exportação a discos geridos com links privados (pré-visualização)
description: Ativar links privados (pré-visualização) para os seus discos geridos com O Azure CLI. Permitindo-lhe exportar e importar discos de forma segura dentro apenas da sua rede virtual.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 07/15/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9184dc78f0f9f8d7997e0bb64bc4521e19364cfe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535809"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links-preview"></a>Azure CLI - Restringir o acesso à importação/exportação de discos geridos com links privados (pré-visualização)

Pode utilizar [pontos finais privados](../../private-link/private-endpoint-overview.md) (pré-visualização) para restringir a exportação e importação de discos geridos e aceder de forma segura a dados sobre uma [Ligação Privada](../../private-link/private-link-overview.md) de clientes na sua rede virtual Azure. O ponto final privado utiliza um endereço IP a partir do espaço de endereço de rede virtual para o seu serviço de discos geridos. O tráfego de rede entre os clientes da rede virtual e discos geridos atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. 

Para utilizar links privados para exportar/importar discos geridos, primeiro cria um recurso de acesso ao disco e liga-o a uma rede virtual na mesma subscrição, criando um ponto final privado. Em seguida, associe um disco ou uma foto com uma instância de acesso ao disco. Por fim, desloque a propriedade NetworkAccessPolicy do disco ou o instantâneo para `AllowPrivate` . Isto irá limitar o acesso à sua rede virtual. 

Pode definir a propriedade NetworkAccessPolicy `DenyAll` para evitar que qualquer pessoa exporte dados de um disco ou de um instantâneo. O valor padrão para a propriedade NetworkAccessPolicy é `AllowAll` .

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar pontos finais privados para exportar e importar discos geridos, tem de obter a funcionalidade ativada na sua subscrição. Envie um e-mail para mdprivatelinks@microsoft .com com os seus Ids de subscrição para obter a funcionalidade ativada para as suas subscrições.

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Inicie sessão na sua subscrição e desa estadi faça o seu login

```azurecli-interactive

subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=CentralUSEUAP
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Criar um acesso ao disco utilizando o Azure CLI
```azurecli-interactive
az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateDiskAccess.json" \
--parameters "region=$region" "diskAccessName=$diskAccessName"

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual

As políticas de rede como os grupos de segurança de rede (NSG) não são suportadas para pontos finais privados. Para implantar um Ponto Final Privado numa determinada sub-rede, é necessária uma definição explícita de desativação nessa sub-rede. 

```azurecli-interactive
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Desativar as políticas de ponto final privado da sub-rede

O Azure implementa recursos para uma sub-rede dentro de uma rede virtual, pelo que é necessário atualizar a sub-rede para desativar as políticas de rede de pontos finais privadas. 

```azurecli-interactive
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Criar um ponto final privado para o objeto de acesso ao disco

```azurecli-interactive
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada de DNS

Crie uma Zona Privada de DNS para o domínio blob de armazenamento, crie uma ligação de associação com a Rede Virtual e crie um Grupo de Zonas DNS para associar o ponto final privado à Zona Privada de DNS. 

```azurecli-interactive
az network private-dns zone create --resource-group $resourceGroupName \ 
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```
## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Criar uma imagem instantânea de um disco protegido com links privados
   ```cli
   diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)
   
   az group deployment create -g $resourceGroupName \
      --template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateSnapshotWithExportViaPrivateLink.json" \
      --parameters "snapshotNameSecuredWithPL=$snapshotNameSecuredWithPL" \
      "sourceResourceId=$diskId" \
      "diskAccessId=$diskAccessId" \
      "region=$region" \
      "networkAccessPolicy=AllowPrivate" 
```

## <a name="next-steps"></a>Próximos passos

- [FAQ em links privados](faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportação/Cópia geriu instantâneos como VHD para uma conta de armazenamento em diferentes regiões com PowerShell](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md)
