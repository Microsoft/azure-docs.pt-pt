---
title: Protegendo Zonas DNS e registros-DNS do Azure
description: Neste roteiro de aprendizagem, comece a proteger as zonas DNS e os conjuntos de registros em Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: rohink
ms.openlocfilehash: 549090f04f4969b00dc1c8ee8d5cc70a50523ca8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983831"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Como proteger registros e zonas DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As zonas e os registros DNS são recursos críticos. Excluir uma zona DNS ou até mesmo apenas um único registro DNS pode resultar em uma interrupção total do serviço.  Portanto, é importante que os registros e as zonas DNS críticas sejam protegidos contra alterações não autorizadas ou acidentais.

Este artigo explica como o DNS do Azure permite que você proteja suas zonas e registros DNS contra essas alterações.  Aplicamos dois recursos de segurança avançados fornecidos por Azure Resource Manager: [controle de acesso baseado em função](../role-based-access-control/overview.md) e [bloqueios de recursos](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para usuários, grupos e recursos do Azure. Usando o RBAC, você pode conceder precisamente a quantidade de acesso que os usuários precisam para executar seus trabalhos. Para obter mais informações sobre como o RBAC ajuda você a gerenciar o acesso, consulte [o que é controle de acesso baseado em função](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>A função colaborador de zona DNS

A função colaborador de zona DNS é uma função interna fornecida pelo Azure para gerenciar recursos de DNS.  Atribuir permissões de colaborador de zona DNS a um usuário ou grupo permite que esse grupo gerencie recursos DNS, mas não recursos de nenhum outro tipo.

Por exemplo, suponha que o grupo de recursos *myzones* contenha cinco zonas para a Contoso Corporation. Conceder permissões de colaborador de zona DNS do administrador de DNS a esse grupo de recursos permite o controle total sobre essas zonas DNS. Ele também evita a concessão de permissões desnecessárias, por exemplo, o administrador de DNS não pode criar ou parar máquinas virtuais.

A maneira mais simples de atribuir permissões de RBAC é [por meio do portal do Azure](../role-based-access-control/role-assignments-portal.md).  Abra o **controle de acesso (iam)** para o grupo de recursos, selecione **Adicionar**, depois selecione a função **colaborador de zona DNS** e selecione os usuários ou grupos necessários para conceder permissões.

![RBAC no nível do grupo de recursos por meio do portal do Azure](./media/dns-protect-zones-recordsets/rbac1.png)

As permissões também podem ser [concedidas usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

O comando equivalente também está [disponível por meio do CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC de nível de zona

As regras do RBAC do Azure podem ser aplicadas a uma assinatura, a um grupo de recursos ou a um recurso individual. No caso do DNS do Azure, esse recurso pode ser uma zona DNS individual ou até mesmo um conjunto de registros individual.

Por exemplo, suponha que o grupo de recursos *myzones* contém a zona *contoso.com* e uma subzona *Customers.contoso.com* na qual os registros CNAME são criados para cada conta de cliente.  A conta usada para gerenciar esses registros CNAME deve receber permissões para criar registros somente na zona *Customers.contoso.com* , ele não deve ter acesso às outras zonas.

As permissões de RBAC no nível de zona podem ser concedidas por meio do portal do Azure.  Abra o **controle de acesso (iam)** para a zona e, em seguida, selecione **Adicionar**, selecione a função **colaborador de zona DNS** e selecione os usuários ou grupos necessários para conceder permissões.

![RBAC de nível de zona DNS por meio do portal do Azure](./media/dns-protect-zones-recordsets/rbac2.png)

As permissões também podem ser [concedidas usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

O comando equivalente também está [disponível por meio do CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>RBAC do nível do conjunto de registros

Podemos avançar mais uma etapa. Considere o administrador de email da Contoso Corporation, que precisa de acesso aos registros MX e TXT no Apex da zona contoso.com.  Ela não precisa de acesso a nenhum outro registro MX ou TXT ou a qualquer registro de qualquer outro tipo.  O DNS do Azure permite que você atribua permissões no nível do conjunto de registros, exatamente aos registros aos quais o administrador de email precisa acessar.  O administrador de email recebe precisamente o controle de que precisa e não pode fazer outras alterações.

As permissões RBAC de nível de conjunto de registros podem ser configuradas por meio do portal do Azure, usando o botão **usuários** na página conjunto de registros:

![RBAC de nível de conjunto de registros por meio do portal do Azure](./media/dns-protect-zones-recordsets/rbac3.png)

As permissões RBAC de nível de conjunto de registros também podem ser [concedidas usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

O comando equivalente também está [disponível por meio do CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função de colaborador de zona DNS interna habilita o controle total sobre um recurso de DNS. Também é possível criar suas próprias funções do Azure de cliente, para fornecer um controle ainda mais refinado.

Considere novamente o exemplo no qual um registro CNAME na zona *Customers.contoso.com* é criado para cada conta de cliente da Contoso Corporation.  A conta usada para gerenciar esses CNAMEs deve receber permissão para gerenciar somente os registros CNAME.  Em seguida, ele não pode modificar os registros de outros tipos (como alterar registros MX) ou executar operações em nível de zona, como exclusão de zona.

O exemplo a seguir mostra uma definição de função personalizada para gerenciar somente os registros CNAME:

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
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade Actions define as seguintes permissões específicas de DNS:

* `Microsoft.Network/dnsZones/CNAME/*` concede controle total sobre os registros CNAME
* `Microsoft.Network/dnsZones/read` concede permissão para ler zonas DNS, mas não para modificá-las, permitindo que você veja a zona na qual o CNAME está sendo criado.

As ações restantes são copiadas da [função interna de colaborador de zona DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Usar uma função personalizada de RBAC para impedir a exclusão de conjuntos de registros enquanto ainda permite que eles sejam atualizados não é um controle efetivo. Ele impede que os conjuntos de registros sejam excluídos, mas não os impede de serem modificados.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros vazio. Isso tem o mesmo efeito que excluir o conjunto de registros de um ponto de vista de resolução DNS.

Definições de função personalizadas não podem ser definidas no momento por meio do portal do Azure. Uma função personalizada baseada nessa definição de função pode ser criada usando Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Ele também pode ser criado por meio do CLI do Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

A função pode ser atribuída da mesma maneira que as funções internas, conforme descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerenciar e atribuir funções personalizadas, consulte [funções personalizadas no RBAC do Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recursos

Além do RBAC, o Azure Resource Manager dá suporte a outro tipo de controle de segurança, ou seja, a capacidade de bloquear recursos. Onde as regras de RBAC permitem controlar as ações de usuários e grupos específicos, os bloqueios de recursos são aplicados ao recurso e são efetivos entre todos os usuários e funções. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Há dois tipos de bloqueio de recurso: **CanNotDelete** e **ReadOnly**. Eles podem ser aplicados a uma zona DNS ou a um conjunto de registros individual.  As seções a seguir descrevem vários cenários comuns e como dar suporte a eles usando bloqueios de recursos.

### <a name="protecting-against-all-changes"></a>Proteção contra todas as alterações

Para evitar que as alterações sejam feitas, aplique um bloqueio ReadOnly à zona.  Isso impede que novos conjuntos de registros sejam criados, e conjuntos de registros existentes sejam modificados ou excluídos.

Os bloqueios de recursos de nível de zona podem ser criados por meio do portal do Azure.  Na página zona DNS, selecione **bloqueios**e, em seguida, selecione **+ Adicionar**:

![Bloqueios de recursos no nível da zona por meio do portal do Azure](./media/dns-protect-zones-recordsets/locks1.png)

Bloqueios de recursos de nível de zona também podem ser criados por meio de Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

A configuração de bloqueios de recursos do Azure não tem suporte no momento por meio do CLI do Azure.

### <a name="protecting-individual-records"></a>Protegendo registros individuais

Para evitar que um conjunto de registros DNS existente seja modificado, aplique um bloqueio ReadOnly ao conjunto de registros.

> [!NOTE]
> A aplicação de um bloqueio CanNotDelete a um conjunto de registros não é um controle efetivo. Ele impede que o conjunto de registros seja excluído, mas não impede que ele seja modificado.  As modificações permitidas incluem adicionar e remover registros do conjunto de registros, incluindo a remoção de todos os registros para deixar um conjunto de registros vazio. Isso tem o mesmo efeito que excluir o conjunto de registros de um ponto de vista de resolução DNS.

Os bloqueios de recursos no nível do conjunto de registros só podem ser configurados usando Azure PowerShell.  Eles não têm suporte no portal do Azure ou CLI do Azure.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Proteção contra exclusão de zona

Quando uma zona é excluída no DNS do Azure, todos os conjuntos de registros na zona também são excluídos.  Esta operação não pode ser anulada.  Excluir acidentalmente uma zona crítica tem o potencial de ter um impacto significativo nos negócios.  Portanto, é muito importante proteger contra a exclusão acidental de zona.

Aplicar um bloqueio CanNotDelete a uma zona impede que a zona seja excluída.  No entanto, como os bloqueios são herdados por recursos filho, ele também impede que os conjuntos de registros na zona sejam excluídos, o que pode ser indesejável.  Além disso, conforme descrito na observação acima, ele também é ineficaz, pois os registros ainda podem ser removidos dos conjuntos de registros existentes.

Como alternativa, considere aplicar um bloqueio CanNotDelete a um conjunto de registros na zona, como o conjunto de registros SOA.  Como a zona não pode ser excluída sem excluir também os conjuntos de registros, isso protege contra a exclusão de zona e, ao mesmo tempo, permite que os conjuntos de registros dentro da zona sejam modificados livremente. Se for feita uma tentativa de excluir a zona, Azure Resource Manager detectará que isso também excluiria o conjunto de registros SOA e bloqueará a chamada porque a SOA está bloqueada.  Nenhum conjunto de registros foi excluído.

O comando do PowerShell a seguir cria um bloqueio CanNotDelete em relação ao registro SOA da zona determinada:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType "Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Outra maneira de evitar a exclusão acidental de zona é usar uma função personalizada para garantir que as contas de operador e de serviço usadas para gerenciar suas zonas não tenham permissões de exclusão de zona. Quando você precisa excluir uma zona, é possível impor uma exclusão em duas etapas, primeiro concedendo permissões de exclusão de zona (no escopo de zona, para evitar a exclusão da zona errada) e a segunda para excluir a zona.

Essa segunda abordagem tem a vantagem de que ela funciona para todas as zonas acessadas por essas contas, sem precisar se lembrar de criar bloqueios. Ele tem a desvantagem de que qualquer conta com permissões de exclusão de zona, como o proprietário da assinatura, ainda pode excluir acidentalmente uma zona crítica.

É possível usar as duas abordagens – bloqueios de recursos e funções personalizadas – ao mesmo tempo, como uma abordagem de defesa aprofundada para a proteção de zona DNS.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como trabalhar com o RBAC, consulte Introdução [ao gerenciamento de acesso no portal do Azure](../role-based-access-control/overview.md).
* Para obter mais informações sobre como trabalhar com bloqueios de recursos, consulte [Bloquear recursos com Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
