---
title: Atualizar endereços IP públicos
titleSuffix: Azure Virtual Network
description: Atualizar endereços IP públicos de Básico para Standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 9ea29c47349fd7ccee469188f8929a864cf7bbef
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905796"
---
# <a name="upgrade-public-ip-addresses"></a>Atualizar endereços IP públicos

Os endereços IP públicos Azure são criados com um SKU-- Básico ou Standard -- que determina aspetos da sua funcionalidade (incluindo método de atribuição, utilização através de zonas de disponibilidade e quais os recursos a que podem ser associados). 

Os seguintes cenários são revistos neste artigo:
* Como atualizar um IP público SKU básico para um IP público Standard SKU (usando Portal, PowerShell ou CLI)
* Como migrar um Azure Classic Reserved IP para um Azure Resource Manager Basic SKU public IP

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Atualizar o endereço IP público de Básico para Standard SKU

>[!NOTE]
>A capacidade de atualizar os IPs públicos de Base para Standard não está disponível em todas as regiões.  Consulte [**as Limitações**](#limitations) para mais detalhes.

Para atualizar um IP público, não deve ser associado a qualquer recurso (consulte [esta página](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) para obter mais informações sobre como dissociar os IPs públicos).

>[!IMPORTANT]
>Os IPs públicos atualizados de Basic para Standard SKU continuam a não ter [zonas de disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)  Isto significa que não podem ser associados a um recurso Azure que seja redundante ou ligado a uma zona pré-especificada em regiões onde tal é oferecido.

---
# <a name="basic-to-standard---powershell"></a>[**Básico para Standard - PowerShell**](#tab/option-upgrade-powershell)

O exemplo a seguir pressupõe a criação anterior de um IP público Básico SKU, utilizando o exemplo dado [nesta página](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic) com um IP público básico **myBasicPublicIP** no **myResourceGroup**.

Para atualizar o IP, basta executar os comandos abaixo utilizando o PowerShell.  Note se o endereço IP já está estático, essa secção pode ser ignorada.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Básico para Standard - CLI**](#tab/option-upgrade-cli)

O exemplo a seguir pressupõe a criação anterior de um IP público Básico SKU, utilizando o exemplo dado [nesta página](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic) com um IP público básico **myBasicPublicIP** no **myResourceGroup**.

Para atualizar o IP, basta executar os comandos abaixo utilizando o Azure CLI.  Note se o endereço IP já está estático, essa secção pode ser ignorada.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Upgrade (migrar) um IP reservado clássico para um IP público estático

Para beneficiar das novas capacidades no Azure Resource Manager, pode migrar o endereço IP estático existente existente -- chamado IPs Reservado -- do modelo Clássico para o modelo moderno Azure Resource Manager.  O IP público migrado será um tipo SKU básico.


---

# <a name="reserved-to-basic---powershell"></a>[**Reservado ao Básico - PowerShell**](#tab/option-migrate-powershell)

O exemplo a seguir pressupõe a criação anterior de um clássico Azure Reserved IP **myReservedIP** no **myResourceGroup**. Outro pré-requisito para a migração é garantir que a assinatura do Gestor de Recursos Azure se tenha registado para migração. Isto é abordado em detalhe nos passos 3 e 4 [desta página](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-ps).

Para migrar o IP Reservado, execute os comandos abaixo utilizando o PowerShell.  Note se o endereço IP não estiver associado a nenhum serviço (abaixo existe um serviço chamado **myService),** esse passo pode ser ignorado.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
O comando anterior apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode proceder com os seguintes passos para preparar e comprometer a migração:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Um novo grupo de recursos em Azure Resource Manager é criado usando o nome do IP reservado migrado (no exemplo acima, seria o grupo de recursos **myReservedIP-Migrated).**

# <a name="reserved-to-basic---cli"></a>[**Reservado ao Básico - CLI**](#tab/option-migrate-cli)

O exemplo a seguir pressupõe a criação anterior de um clássico Azure Reserved IP **myReservedIP** no **myResourceGroup**. Outro pré-requisito para a migração é garantir que a assinatura do Gestor de Recursos Azure se tenha registado para migração. Isto é abordado em detalhe nos passos 3 e 4 [desta página](https://docs.microsoft.com/azure/virtual-machines/linux/migration-classic-resource-manager-cli).

Para migrar o IP Reservado, execute os comandos abaixo utilizando o CLI Azure.  Note se o endereço IP não estiver associado a nenhum serviço (abaixo existe um serviço chamado **myService** e a implementação **myDeployment),** esse passo pode ser ignorado.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
O comando anterior apresenta quaisquer avisos e erros que bloqueiem a migração. Se a validação for bem sucedida, pode proceder com os seguintes passos para preparar e comprometer a migração:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Um novo grupo de recursos em Azure Resource Manager é criado usando o nome do IP reservado migrado (no exemplo acima, seria o grupo de recursos **myReservedIP-Migrated).**

---

## <a name="limitations"></a>Limitações

* Esta capacidade não está atualmente disponível nas seguintes regiões:<br>
US Gov - Virginia<br>
US DoD - Leste<br>
US DoD Centro<br>
Leste da China<br>
China Leste 2<br>
Norte da China<br>
China Norte 2

* Para atualizar um IP público básico, não pode ser associado a qualquer recurso Azure.  Por favor, [reveja esta página](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) para mais informações sobre como desassociar os IPs públicos.  Da mesma forma, para migrar um IP Reservado, não pode ser associado a qualquer Serviço cloud.  Por favor, [reveja esta página](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm) para obter mais informações sobre como desassociar os IPs reservados.  
* Os IPs públicos atualizados de Basic para Standard SKU continuarão a não ter [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) e, portanto, não podem ser associados a um recurso Azure que seja redundante ou zonal.  Note que isto se aplica apenas às regiões que oferecem zonas de disponibilidade.
* Não pode descer de Standard para Basic.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure, incluindo a diferença entre os tipos SKU, bem como [as definições de endereço IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Saiba como [atualizar os balançadores de carga pública Azure de Base para Standard](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard).
- Compreenda [os IPs Reservados clássicos do Azure](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) e [a migração de recursos clássicos para o Gestor de Recursos Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)
