---
title: Proteger as Zonas e Registos do DNS - Azure DNS
description: Neste caminho de aprendizagem, começa a proteger zonas de DNS e recordes no Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 85aaf40237b6b6687c54d4b036f280805c98e7b2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618972"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Como proteger os registos e as zonas DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As zonas de DNS e os registos são recursos críticos. A eliminação de uma zona DE DNS ou de um único registo DNS pode resultar numa paragem de serviço. É importante que as zonas e registos dns estão protegidos contra alterações não autorizadas ou acidentais.

Este artigo explica como o Azure DNS permite proteger as suas zonas e registos privados de DNS contra tais alterações.  Aplicamos duas funcionalidades poderosas de títulos fornecidos pelo Azure Resource Manager: [Azure role-based access control (Azure RBAC)](../role-based-access-control/overview.md) e [bloqueios de recursos](../azure-resource-manager/management/lock-resources.md).

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

O controlo de acesso baseado em funções Azure (Azure RBAC) permite uma gestão de acessos finos para utilizadores, grupos e recursos da Azure. Com o Azure RBAC, pode conceder o nível de acesso de que os utilizadores precisam. Para obter mais informações sobre como o Azure RBAC o ajuda a gerir o acesso, consulte [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>O papel de contribuinte da zona do DNS

O papel de Contribuinte da Zona DNS é um papel integrado na gestão dos recursos privados de DNS. Esta função aplicada a um utilizador ou grupo permite-lhes gerir os recursos DNS.

O grupo de recursos *myResourceGroup* contém cinco zonas para a Contoso Corporation. A concessão de permissões de contribuinte dnss zone ao grupo de recursos, permite o controlo total sobre essas zonas dns. Evita conceder permissões desnecessárias. O administrador do DNS não pode criar ou parar máquinas virtuais.

A forma mais simples de atribuir permissões Azure RBAC é [através do portal Azure](../role-based-access-control/role-assignments-portal.md).  

Controlo **de acesso aberto (IAM)** para o grupo de recursos e, em seguida, selecione **Adicionar** e, em seguida, selecione a função contribuinte da zona **de DNS.** Selecione os utilizadores ou grupos necessários para conceder permissões.

![Nível de grupo de recursos Azure RBAC através do portal Azure](./media/dns-protect-zones-recordsets/rbac1.png)

As permissões também podem ser [concedidas através da Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

O comando equivalente também está [disponível através do Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>Nível de zona Azure RBAC

As regras do Azure RBAC podem ser aplicadas a uma subscrição, a um grupo de recursos ou a um recurso individual. Esse recurso pode ser uma zona de DNS individual, ou um conjunto de registos individuais.

Por exemplo, o grupo de recursos *myResourceGroup* contém a zona *contoso.com* e um *customers.contoso.com de subzona*. Os registos CNAME são criados para cada conta de cliente. A conta de administrador utilizada para gerir os registos da CNAME é atribuída a permissões para criar registos na zona *customers.contoso.com.* A conta só pode gerir *customers.contoso.com.*

Permissões Azure RBAC ao nível da zona podem ser concedidas através do portal Azure.  Controlo **de acesso aberto (IAM)** para a zona, selecione **Adicionar,** em seguida, selecione a função **contribuinte da zona de DNS** e selecione os utilizadores ou grupos necessários para conceder permissões.

![DNS Zone nível Azure RBAC através do portal Azure](./media/dns-protect-zones-recordsets/rbac2.png)

As permissões também podem ser [concedidas através da Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

O comando equivalente também está [disponível através do Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Recorde de nível Azure RBAC

As permissões são aplicadas ao nível recorde.  O utilizador tem o controlo das entradas de que necessita e não pode escoar quaisquer outras alterações.

As permissões de AzureC de nível recorde podem ser configuradas através do portal Azure, utilizando o botão **Access Control (IAM)** na página de conjunto de registos:

![Recorde de nível Azure RBAC através do portal Azure](./media/dns-protect-zones-recordsets/rbac3.png)

As permissões Azure RBAC de nível recorde também podem ser [concedidas utilizando a Azure PowerShell:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

O comando equivalente também está [disponível através do Azure CLI:](../role-based-access-control/role-assignments-cli.md)

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função de contribuinte de zona de DNS incorporada permite o controlo total sobre um recurso DNS. É possível construir os seus próprios papéis Azure personalizados para fornecer um controlo mais fino.

A conta utilizada para gerir cnames é autorizada apenas a gerir os registos CNAME. A conta é incapaz de modificar registos de outros tipos. A conta não é capaz de fazer operações ao nível da zona, tais como exclusão de zona.

O exemplo a seguir mostra uma definição de função personalizada apenas para a gestão dos registos CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade Actions define as seguintes permissões específicas do DNS:

* `Microsoft.Network/dnsZones/CNAME/*` concede controlo total sobre os registos CNAME
* `Microsoft.Network/dnsZones/read` concede permissão para ler zonas DNS, mas não modificá-las, permitindo-lhe ver a zona em que o CNAME está sendo criado.

As restantes Ações são copiadas do [papel integrado do DnS Zone Contributor](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> A utilização de um papel personalizado Azure para evitar a eliminação de conjuntos de registos, permitindo ainda a sua atualização, não é um controlo eficaz. Impede que os recordes sejam eliminados, mas não impede que sejam modificados.  As modificações permitidas incluem a adição e remoção de registos do conjunto de recordes, incluindo a remoção de todos os registos para deixar um recorde vazio. Isto tem o mesmo efeito que apagar o recorde estabelecido de um ponto de vista de resolução de DNS.

As definições de funções personalizadas não podem ser definidas atualmente através do portal Azure. Um papel personalizado baseado nesta definição de função pode ser criado usando Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Também pode ser criado através do Azure CLI:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

O papel pode então ser atribuído da mesma forma que as funções incorporadas, como descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerir e atribuir funções personalizadas, consulte [as funções personalizadas Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recurso

O Azure Resource Manager suporta outro tipo de controlo de segurança, a capacidade de bloquear recursos. Os bloqueios de recursos são aplicados ao recurso e são eficazes em todos os utilizadores e funções. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Existem dois tipos de bloqueio de recursos: **CanNotDelete** e **ReadOnly**. Estes tipos de bloqueio podem ser aplicados quer numa zona privada de DNS, quer para um conjunto de registos individuais. As secções seguintes descrevem vários cenários comuns e como apoiá-los usando bloqueios de recursos.

### <a name="protecting-against-all-changes"></a>Proteger contra todas as alterações

Para evitar alterações, aplique um bloqueio ReadOnly na zona. Esta fechadura impede a criação de novos conjuntos de discos e os conjuntos de registos existentes sejam modificados ou eliminados.

As fechaduras de recursos de nível de zona podem ser criadas através do portal Azure.  Na página da zona DNS, selecione **Fechaduras** e, em seguida, selecione **+Adicionar**:

![Bloqueios de recursos de nível de zona através do portal Azure](./media/dns-protect-zones-recordsets/locks1.png)

Os bloqueios de recursos ao nível da zona também podem ser criados através do [Azure PowerShell:](/powershell/module/az.resources/new-azresourcelock)

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

O comando equivalente também está [disponível através do Azure CLI:](/cli/azure/lock#az-lock-create)

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Proteger registos individuais

Para evitar que um disco DNS existente se desreserva contra a modificação, aplique um bloqueio ReadOnly no conjunto de registos.

> [!NOTE]
> Aplicar uma fechadura CanNotDelete a um conjunto de recordes não é um controlo eficaz. Impede que o recorde estabelecido seja suprimido, mas não impede que seja modificado.  As modificações permitidas incluem a adição e remoção de registos do conjunto de recordes, incluindo a remoção de todos os registos para deixar um recorde vazio. Isto tem o mesmo efeito que apagar o recorde estabelecido de um ponto de vista de resolução de DNS.

As fechaduras de nível de fixação de registo só podem ser configuradas utilizando a Azure PowerShell.  Não são apoiados no portal Azure ou no Azure CLI.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Proteção contra a eliminação de zonas

Quando uma zona é eliminada em Azure DNS, todos os conjuntos de registos na zona são eliminados.  Esta operação não pode ser desfeita. Eliminar acidentalmente uma zona crítica tem o potencial de ter um impacto significativo no negócio.  É importante proteger contra a eliminação acidental da zona.

A aplicação de um bloqueio CanNotDelete numa zona impede que a zona seja eliminada. As fechaduras são herdadas por recursos infantis. Um bloqueio evita que quaisquer conjuntos de registos na zona sejam apagados. Como descrito na nota acima, é ineficaz uma vez que os registos ainda podem ser removidos dos conjuntos de recordes existentes.

Como alternativa, aplique um bloqueio CanNotDelete a um recorde estabelecido na zona, tal como o conjunto de recordes SOA. A zona não é apagada sem apagar também os recordes. Este bloqueio protege contra a eliminação da zona, permitindo que os recordes dentro da zona sejam modificados livremente. Se for feita uma tentativa de apagar a zona, o Gestor de Recursos Azure deteta esta remoção. A remoção também eliminaria o conjunto de registos SOA, Azure Resource Manager bloqueia a chamada porque o SOA está bloqueado.  Não são apagados conjuntos de discos.

O seguinte comando PowerShell cria uma fechadura CanNotDelete contra o registo SOA da zona dada:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Outra opção para evitar a eliminação acidental da zona é utilizando uma função personalizada. Esta função garante que as contas utilizadas para gerir as suas zonas não têm permissões de exclusão de zona. 

Quando precisa de eliminar uma zona, pode impor uma eliminação em duas etapas:

 - Primeiro, zona de concessão elimina permissões
 - Segundo, conceda permissões para apagar a zona.

O papel personalizado funciona para todas as zonas acedidas por essas contas. As contas com permissões de eliminação de zonas, como o proprietário da subscrição, podem ainda eliminar acidentalmente uma zona.

É possível usar ambas as abordagens - bloqueios de recursos e funções personalizadas - ao mesmo tempo, como uma abordagem de defesa em profundidade para a proteção da zona de DNS.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o trabalho com o Azure RBAC, consulte [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).
* Para obter mais informações sobre o trabalho com bloqueios de recursos, consulte [os recursos de bloqueio com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
