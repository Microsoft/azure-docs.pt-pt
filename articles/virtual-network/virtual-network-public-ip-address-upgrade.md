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
ms.custom: references_regions
ms.openlocfilehash: 74df338fd888bd7f654ddfc2fc5f9dddf10e84ab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598419"
---
# <a name="upgrade-public-ip-addresses"></a>Atualizar endereços IP públicos

Os endereços IP públicos Azure são criados com um SKU-- Básico ou Standard -- que determina aspetos da sua funcionalidade (incluindo método de atribuição, suporte de recursos e quais os recursos a que podem ser associados). 

Os seguintes cenários são revistos neste artigo:
* Como atualizar um IP público SKU básico para um IP público Standard SKU (utilizando PowerShell ou CLI)
* Como migrar um Azure Classic Reserved IP para um Azure Resource Manager Basic SKU public IP

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Atualizar o endereço IP público de Básico para Standard SKU

Para atualizar um IP público, não deve ser associado a qualquer recurso (consulte [esta página](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) para obter mais informações sobre como dissociar os IPs públicos).

>[!IMPORTANT]
>Os IPs públicos atualizados de Basic para Standard SKU continuam a não ter [zonas de disponibilidade garantidas.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Certifique-se de que isto é mantido em mente ao escolher quais os recursos para associar o endereço IP.

---
# <a name="basic-to-standard---powershell"></a>[**Básico para Standard - PowerShell**](#tab/option-upgrade-powershell)

O exemplo a seguir pressupõe a criação anterior de um IP público Básico SKU, utilizando o exemplo dado [nesta página](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) com um IP público básico **myBasicPublicIP** no **myResourceGroup**.

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

O exemplo a seguir pressupõe a criação anterior de um IP público Básico SKU, utilizando o exemplo dado [nesta página](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) com um IP público básico **myBasicPublicIP** no **myResourceGroup**.

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

O exemplo a seguir pressupõe a criação anterior de um clássico Azure Reserved IP **myReservedIP** no **myResourceGroup**. Outro pré-requisito para a migração é garantir que a assinatura do Gestor de Recursos Azure se tenha registado para migração. Isto é abordado em detalhe nos passos 3 e 4 [desta página](../virtual-machines/migration-classic-resource-manager-ps.md).

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

O exemplo a seguir pressupõe a criação anterior de um clássico Azure Reserved IP **myReservedIP** no **myResourceGroup**. Outro pré-requisito para a migração é garantir que a assinatura do Gestor de Recursos Azure se tenha registado para migração. Isto é abordado em detalhe nos passos 3 e 4 [desta página](../virtual-machines/migration-classic-resource-manager-cli.md).

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

* Para atualizar um IP público básico, não pode ser associado a qualquer recurso Azure.  Por favor, [reveja esta página](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) para mais informações sobre como desassociar os IPs públicos.  Da mesma forma, para migrar um IP Reservado, não pode ser associado a qualquer Serviço cloud.  Por favor, [reveja esta página](./remove-public-ip-address-vm.md) para obter mais informações sobre como desassociar os IPs reservados.  
* Os IPs públicos atualizados de Basic para Standard SKU continuam a não ter [zonas de disponibilidade garantidas.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  Certifique-se de que isto é mantido em mente ao escolher quais os recursos para associar o endereço IP.
* Não pode descer de Standard para Basic.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure, incluindo a diferença entre os tipos SKU, bem como [as definições de endereço IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Saiba como [atualizar os balançadores de carga pública Azure de Base para Standard](../load-balancer/upgrade-basic-standard.md).
- Compreenda [os IPs Reservados clássicos do Azure](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) e [a migração de recursos clássicos para o Gestor de Recursos Azure.](../virtual-machines/migration-classic-resource-manager-overview.md)
