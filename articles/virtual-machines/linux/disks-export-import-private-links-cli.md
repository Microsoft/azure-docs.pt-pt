---
title: Azure CLI - Restringir o acesso à importação/exportação a discos geridos com ligações privadas
description: Ative links privados para os seus discos geridos com Azure CLI. Permitindo-lhe exportar e importar discos de forma segura dentro apenas da sua rede virtual.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 9a005712eda1e735608e3ddf93be2bba98fbdcd3
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630044"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI - Restringir o acesso à importação/exportação de discos geridos com ligações privadas

Pode utilizar [pontos finais privados](../../private-link/private-endpoint-overview.md) para restringir a exportação e importação de discos geridos e aceder de forma segura a dados sobre uma [Ligação Privada](../../private-link/private-link-overview.md) de clientes da sua rede virtual Azure. O ponto final privado utiliza um endereço IP a partir do espaço de endereço de rede virtual para o seu serviço de discos geridos. O tráfego de rede entre clientes na sua rede virtual e discos geridos apenas atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

Para utilizar links privados para exportar/importar discos geridos, primeiro cria um recurso de acesso ao disco e liga-o a uma rede virtual na mesma subscrição, criando um ponto final privado. Em seguida, associe um disco ou uma foto com uma instância de acesso ao disco. Por fim, desloque a propriedade NetworkAccessPolicy do disco ou o instantâneo para `AllowPrivate` . Isto irá limitar o acesso à sua rede virtual. 

Pode definir a propriedade NetworkAccessPolicy `DenyAll` para evitar que qualquer pessoa exporte dados de um disco ou de um instantâneo. O valor padrão para a propriedade NetworkAccessPolicy é `AllowAll` .

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Inicie sessão na sua subscrição e desa estadi faça o seu login

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
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
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual

As políticas de rede como os grupos de segurança de rede (NSG) não são suportadas para pontos finais privados. Para implantar um Ponto Final Privado numa determinada sub-rede, é necessária uma definição explícita de desativação nessa sub-rede. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Desativar as políticas de ponto final privado da sub-rede

O Azure implementa recursos para uma sub-rede dentro de uma rede virtual, pelo que é necessário atualizar a sub-rede para desativar as políticas de rede de pontos finais privadas. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Criar um ponto final privado para o objeto de acesso ao disco

```azurecli
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

```azurecli
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

## <a name="create-a-disk-protected-with-private-links"></a>Criar um disco protegido com links privados
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Criar uma imagem instantânea de um disco protegido com links privados
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Passos seguintes

- [FAQ em links privados](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportar/copiar instantâneos geridos como VHD para uma conta de armazenamento numa região diferente com a CLI](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md)
