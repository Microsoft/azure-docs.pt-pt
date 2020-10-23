---
title: Use a política de Firewall Azure para definir uma hierarquia de regras
description: Aprenda a usar a política do Azure Firewall para definir uma hierarquia de regras e impor o cumprimento.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331741"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Use a política de Firewall Azure para definir uma hierarquia de regras

Os administradores de segurança precisam de gerir firewalls e garantir o cumprimento através das implementações no local e na nuvem. Um componente-chave é a capacidade de fornecer às equipas de aplicação flexibilidade para implementar oleodutos CI/CD para criar regras de firewall de forma automatizada.

A política da Azure Firewall permite-lhe definir uma hierarquia de regras e impor o cumprimento:

- Fornece uma estrutura hierárquica para sobrepor uma política de base central em cima de uma política de equipa de aplicação de crianças. A política de base tem uma prioridade maior e corre antes da política infantil.
- Utilize uma definição de função personalizada Azure para evitar a remoção inadvertida de políticas de base e fornecer acesso seletivo a grupos de recolha de regras dentro de um grupo de subscrição ou de recursos. 

## <a name="solution-overview"></a>Descrição geral da solução

Os passos de alto nível para este exemplo são:

1. Crie uma política de firewall base no grupo de recursos da equipa de segurança. 
3. Defina regras específicas de segurança de TI na política de base. Isto adiciona um conjunto comum de regras para permitir/negar o tráfego.
4. Crie políticas de equipa de aplicação que herdem a política de base. 
5. Defina regras específicas da equipa de aplicação na apólice. Também pode migrar regras de firewalls pré-existentes.
6. Crie funções personalizadas do Azure Ative Directory para fornecer acesso fino ao grupo de recolha de regras e adicione papéis num âmbito de Política de Firewall. No exemplo seguinte, os membros da equipa de vendas podem editar grupos de recolha de regras para a Política de Firewall das equipas de vendas. O mesmo se aplica às equipas de Base de Dados e Engenharia.
7. Associe a política à firewall correspondente. Uma firewall Azure pode ter apenas uma política atribuída. Isto requer que cada equipa de aplicação tenha a sua própria firewall.



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="Equipas e requisitos" border="false":::

### <a name="create-the-firewall-policies"></a>Criar as políticas de firewall

- Uma política de firewall base.

Criar políticas para cada uma das equipas de candidatura:

- Uma política de firewall de vendas. A política de firewall de vendas herda a política de firewall base.
- Uma política de firewall de base de dados. A política de firewall database herda a política de firewall base.
- Uma política de firewall de engenharia. A política de firewall de engenharia também herda a política de firewall base.

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="Equipas e requisitos" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>Criar funções personalizadas para aceder aos grupos de recolha de regras 

As funções personalizadas são definidas para cada equipa de aplicação. O papel define operações e âmbito. As equipas de candidatura podem editar grupos de recolha de regras para as respetivas aplicações.

Utilize o seguinte procedimento de alto nível para definir funções personalizadas:

1. Obtenha a subscrição:

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. Execute o seguinte comando:

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Utilize o comando Get-AzRoleDefinition para desatar a função Reader no formato JSON. 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. Abra a ReaderSupportRole.jsnum editor.

   O seguinte mostra o resultado do JSON. Para obter informações sobre as diferentes propriedades, consulte [as funções personalizadas Azure](../role-based-access-control/custom-roles.md).

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. Editar o ficheiro JSON para adicionar o 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   operação para a propriedade **Actions.**   Não se esqueça de incluir uma vírgula após a operação de leitura. Esta ação permite ao utilizador criar e atualizar grupos de recolha de regras.
6. Em **AssignableScopes,** adicione o seu ID de subscrição com o seguinte formato: 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   Tem de adicionar os IDs de subscrição explícitos, caso contrário, não poderá importar a função para a sua subscrição.
7. Elimine a linha de propriedade **Id**   e altere a propriedade **IsCustom**   para verdadeira.
8. Alterar as propriedades **de nome**   e **descrição**   para *AZFM Rule Collection Group Autor* e *Utilizadores nesta função pode editar grupos de recolha de regras de política* de firewall

O seu ficheiro JSON deve ser semelhante ao seguinte exemplo:

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. Para criar o novo papel personalizado, utilize o comando New-AzRoleDefinition e especifique o ficheiro de definição de função JSON. 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar todas as funções personalizadas, pode utilizar o comando Get-AzRoleDefinition:

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

Também pode ver os papéis personalizados no portal Azure. Aceda à sua subscrição, selecione **Access control (IAM)**, **Roles**.

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="Equipas e requisitos":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="Equipas e requisitos":::

Para obter mais informações, consulte [Tutorial: Crie um papel personalizado Azure utilizando a Azure PowerShell](../role-based-access-control/tutorial-custom-role-powershell.md).

### <a name="add-users-to-the-custom-role"></a>Adicione os utilizadores ao papel personalizado

No portal, pode adicionar utilizadores à função de Autores do Grupo de Recolha de Regras AZFM e fornecer acesso às políticas de firewall.

1. A partir do portal, selecione a política de firewall da equipa de aplicação (por exemplo, SalesAppPolicy).
2. Selecione **Controlo de Acesso**.
3. Selecione **Adicionar atribuição de função**.
4. Adicione os utilizadores/grupos de utilizadores (por exemplo, a equipa de Vendas) ao papel.

Repita este procedimento para as outras políticas de firewall.

### <a name="summary"></a>Resumo

Firewall Policy com funções personalizadas agora fornece acesso seletivo a grupos de recolha de regras de política de firewall.

Os utilizadores não têm permissões para:
- Elimine a política de Firewall ou firewall Azure.
- Atualizar a hierarquia da política de firewall ou configurações de DNS ou inteligência de ameaça.
- Atualizar a política de firewall onde não são membros do grupo de autores do Grupo de Recolha de Regras AZFM.

Os administradores de segurança podem usar a política de base para impor os guarda-costas e bloquear certos tipos de tráfego (por exemplo, ICMP) conforme exigido pela sua empresa. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a política da Azure Firewall](policy-overview.md).

