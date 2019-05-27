---
title: Proteger zonas de DNS do Azure e registos
description: Como proteger zonas DNS e conjuntos de registos no DNS do Azure de Microsoft.
services: dns
author: WenJason
ms.service: dns
ms.topic: article
origin.date: 12/4/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 9340a43eb88b4be03c0f0ccc0d07a32f22a9001c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66121384"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Como proteger zonas e registos DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zonas e registos DNS são recursos críticos. A eliminar uma zona DNS ou até mesmo um único registo DNS pode resultar numa indisponibilidade do serviço total.  Portanto, é importante que críticos zonas e registos DNS estão protegidos contra alterações não autorizadas ou acidentais.

Este artigo explica como DNS do Azure permite-lhe proteger suas zonas e registos DNS em relação a essas alterações.  Aplicamos duas poderosas funcionalidades de segurança fornecidas pelo Azure Resource Manager: [controlo de acesso baseado em funções](../role-based-access-control/overview.md) e [bloqueios de recursos](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Do Azure com base em função de controlo de acesso (RBAC) permite a gestão de acesso detalhado para utilizadores do Azure, grupos e recursos. Utilizando o RBAC, pode conceder precisamente a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Para obter mais informações sobre como o RBAC Ajuda-o a gerir o acesso, consulte [o que é o controlo de acesso baseado em funções](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>A função de Contribuidor da zona DNS

A função de Contribuidor da zona DNS é uma função incorporada fornecida pelo Azure para gerir recursos do DNS.  A atribuição de permissões de Contribuidor da zona de DNS para um utilizador ou grupo permite esse grupo para gerir recursos DNS, mas não os recursos de qualquer outro tipo.

Por exemplo, suponha que o grupo de recursos *myzones* contém cinco zonas para Contoso Corporation. O administrador DNS a conceder permissões de Contribuidor da zona DNS a esse grupo de recursos, permite que o controle total sobre essas zonas DNS. Isso também evita a conceder permissões desnecessárias, por exemplo, o administrador DNS não é possível criar ou parar máquinas virtuais.

É a forma mais simples para atribuir as permissões RBAC [através do portal do Azure](../role-based-access-control/role-assignments-portal.md).  Open **controlo de acesso (IAM)** para o grupo de recursos, em seguida, selecione **Add**, em seguida, selecione o **Contribuidor da zona DNS** função e selecione os usuários necessários ou grupos para conceder permissões.

![Nível de grupo de recursos RBAC através do portal do Azure](./media/dns-protect-zones-recordsets/rbac1.png)

As permissões podem também ser [concedido com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Também é o comando equivalente [disponível através da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Nível de zona RBAC

Regras RBAC do Azure podem ser aplicadas a uma subscrição, um grupo de recursos ou para um recurso individual. No caso do DNS do Azure, esse recurso pode ser uma zona DNS individual ou até mesmo um conjunto de registos individual.

Por exemplo, suponha que o grupo de recursos *myzones* contém a zona *contoso.com* e um subzone *customers.contoso.com* no qual CNAME registos são criados para cada conta de cliente.  A conta utilizada para gerir estes registos CNAME deve receber permissões para criar registos na *customers.contoso.com* apenas da zona, não devem ter acesso às outras zonas.

As permissões RBAC de nível de zona podem ser concedidas através do portal do Azure.  Open **controlo de acesso (IAM)** para a zona, em seguida, selecione **Add**, em seguida, selecione o **Contribuidor da zona DNS** função e selecione os usuários necessários ou grupos para conceder permissões.

![RBAC de nível de zona DNS através do portal do Azure](./media/dns-protect-zones-recordsets/rbac2.png)

As permissões podem também ser [concedido com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Também é o comando equivalente [disponível através da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Nível de RBAC do conjunto de registos

Podemos ir um pouco mais. Considere o administrador de correio para a Contoso Corporation, o que precisa de aceder os registos MX e TXT no vértice da zona contoso.com.  Ela não precisa de acesso para quaisquer outros registos MX ou TXT ou para quaisquer registos de qualquer outro tipo.  O DNS do Azure permite-lhe atribuir permissões ao nível do conjunto de registos, a precisamente os que o administrador do correio tem acesso a registos.  O administrador de correio é concedido precisamente o controle necessários e, não conseguir efetuar outras alterações.

Permissões RBAC ao nível do conjunto de registos podem ser configuradas através do portal do Azure, utilizando o **utilizadores** botão na página do conjunto de registos:

![Conjunto de registos ao nível do RBAC através do portal do Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Também podem ser permissões RBAC ao nível do conjunto de registos [concedido com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Também é o comando equivalente [disponível através da CLI do Azure](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função incorporada de Contribuidor da zona DNS permite que o controle total sobre um recurso DNS. Também é possível criar seu próprio cliente funções do Azure, para fornecer ainda mais refinado de controle.

Considere novamente o exemplo no qual um CNAME de registos na zona *customers.contoso.com* é criada para cada conta de cliente da Contoso Corporation.  A conta utilizada para gerir estes CNAMEs deve ser concedida permissão para gerir apenas os registos CNAME.  É, em seguida, não é possível modificar os registros de outros tipos (por exemplo, alterando registros MX) ou efetuar operações de nível de zona, como a eliminação de zona.

O exemplo seguinte mostra uma definição de função personalizada para gerenciar apenas os registos CNAME:

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
        "Microsoft.Resources/subscriptions/resourceGroups/read"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade de ações define as seguintes permissões de DNS específicas:

* `Microsoft.Network/dnsZones/CNAME/*` concede o controlo total sobre os registos CNAME
* `Microsoft.Network/dnsZones/read` concede permissão para ler as zonas DNS, mas não para modificá-los, permitindo que veja a zona em que o CNAME está a ser criado.

As restantes ações são copiadas a partir da [função incorporada de Contribuidor da zona DNS](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Usando uma função RBAC personalizada para impedir a eliminar conjuntos de registos, embora ainda permitindo que eles sejam atualizados não é um controlo eficaz. Impede que os conjuntos de registo seja eliminado, mas ele não impede-los a ser modificado.  Modificações permitidas incluem adicionar e remover registros do conjunto de registos, incluindo a remoção de todos os registos para deixar um conjunto de registos vazio. Isso tem o mesmo efeito que a eliminar o conjunto de um ponto de vista de resolução DNS de registos.

Atualmente não não possível definir definições de funções personalizadas através do portal do Azure. Uma função personalizada com base nessa definição de função pode ser criada com o Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Também pode ser criado através da CLI do Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

A função pode depois ser atribuída da mesma forma como as funções incorporadas, conforme descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerir e atribuir funções personalizadas, consulte [funções personalizadas no Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recurso

Para além do RBAC, o Azure Resource Manager suporta outro tipo de controlo de segurança, ou seja, a capacidade de bloquear recursos. Em que as regras RBAC permitem controlar as ações de utilizadores e grupos específicos, bloqueios de recursos são aplicados ao recurso e entram em vigor em todos os utilizadores e funções. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

Existem dois tipos de bloqueio do recurso: **CanNotDelete** e **só de leitura**. Estes podem ser aplicadas a uma zona DNS ou para um conjunto de registos individual.  As secções seguintes descrevem os vários cenários comuns e como oferecer suporte a eles usando bloqueios de recursos.

### <a name="protecting-against-all-changes"></a>Proteção contra todas as alterações

Para impedir que quaisquer alterações que estão sendo feitas, aplica um bloqueio só de leitura para a zona.  Isto impede que novos conjuntos de registos a ser criados e existentes conjuntos de registos sejam modificados ou eliminados.

Bloqueios de recursos de nível de zona podem ser criados através do portal do Azure.  Na página da zona DNS, selecione **bloqueia**, em seguida, selecione **+ adicionar**:

![Bloqueios de recurso de nível de zona através do portal do Azure](./media/dns-protect-zones-recordsets/locks1.png)

Nível de zona de recursos bloqueios também podem ser criados através do Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Configuração de bloqueios de recursos do Azure não é atualmente suportada através da CLI do Azure.

### <a name="protecting-individual-records"></a>Proteger os registros individuais

Para impedir que um conjunto contra modificação de registos DNS existente, aplica um bloqueio só de leitura ao conjunto de registos.

> [!NOTE]
> Aplicar um bloqueio de CanNotDelete para um conjunto de registos não é um controlo eficaz. Impede que o conjunto a eliminação de registos, mas não impede-lo de ser modificado.  Modificações permitidas incluem adicionar e remover registros do conjunto de registos, incluindo a remoção de todos os registos para deixar um conjunto de registos vazio. Isso tem o mesmo efeito que a eliminar o conjunto de um ponto de vista de resolução DNS de registos.

Bloqueios de recursos de nível de conjunto de registos podem atualmente apenas ser configurado com o Azure PowerShell.  Não são suportados no portal do Azure ou da CLI do Azure.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Proteger contra a eliminação de zona

Ao eliminar uma zona no DNS do Azure, também são eliminados todos os conjuntos de registos na zona.  Esta operação não pode ser anulada.  Eliminar acidentalmente uma zona crítica tem o potencial de ter um impacto comercial significativo.  Portanto, é muito importante proteger contra a eliminação acidental de uma zona.

Aplicar um bloqueio de CanNotDelete a uma zona impede que a zona a ser eliminado.  No entanto, uma vez que os bloqueios são herdados pelos recursos subordinados, ele também impede qualquer conjuntos de registos na zona a eliminação, que talvez seja indesejável.  Além disso, conforme descrito na nota acima, também é ineficaz, já que os registos ainda podem ser removidos dos conjuntos de registos existentes.

Como alternativa, considere a aplicar um bloqueio de CanNotDelete para um conjunto de registos na zona, por exemplo, o conjunto de registos SOA.  Uma vez que a zona não pode ser eliminada sem eliminar também os conjuntos de registos, esta ação protege contra a eliminação de zona, enquanto ainda permite que os conjuntos de registos na zona deve ser modificada livremente. Se for feita uma tentativa de eliminar a zona, o Azure Resource Manager Deteta isso também seria a eliminar o conjunto de registos SOA e bloqueará a chamada porque a SOA é bloqueado.  Não existem conjuntos de registos são eliminados.

O seguinte comando do PowerShell cria um bloqueio de CanNotDelete contra o registo SOA da zona determinado:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Outra forma de impedir a eliminação acidental de uma zona é através de uma função personalizada para garantir que o operador e contas de serviço utilizadas para gerir as suas zonas não tem zona a eliminar as permissões. Quando tiver de eliminar uma zona, pode impor uma exclusão de dois passos, primeiro concederem eliminar as permissões da zona (no âmbito da zona, para impedir a eliminar a zona errada) e segundo para eliminar a zona.

Essa segunda abordagem tem a vantagem de que ele funciona para todas as zonas acessadas por essas contas, sem ter que não se esqueça de criar nenhum bloqueio. Ele tem a desvantagem que todas as contas com permissões de eliminação de zona, por exemplo, o proprietário da subscrição, ainda acidentalmente podem eliminar uma zona crítica.

É possível utilizar ambas as abordagens, bloqueios de recursos e funções personalizadas, ao mesmo tempo, como uma abordagem de defesa em profundidade para proteção de zona DNS.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre como trabalhar com o RBAC, veja [introdução à gestão de acesso no portal do Azure](../role-based-access-control/overview.md).
* Para obter mais informações sobre como trabalhar com bloqueios de recursos, consulte [bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).
