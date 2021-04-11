---
title: Identidades geridas para recursos Azure frequentemente feitas perguntas - Azure AD"
description: Perguntas frequentes sobre identidades geridas
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 3d3ab9859eb9f85d5ca7d0573fa79443ae9fe964
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251011"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Identidades geridas para recursos Azure frequentemente feitas perguntas - Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

## <a name="administration"></a>Administração

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Como se pode encontrar recursos que tenham uma identidade gerida?

Pode encontrar a lista de recursos que possuem uma identidade gerida atribuída ao sistema utilizando o seguinte Comando CLI Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quais as permissões do Azure RBAC necessárias para gerir a identidade num recurso? 

- Identidade gerida atribuída pelo sistema: É necessário escrever permissões sobre o recurso. Por exemplo, para máquinas virtuais, precisa de Microsoft.Compute/virtualMachines/write. Esta ação está incluída em funções específicas de recursos como [O Contribuinte De Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
- Identidade gerida atribuída pelo utilizador: É necessário escrever permissões sobre o recurso. Por exemplo, para máquinas virtuais, precisa de Microsoft.Compute/virtualMachines/write. Além da atribuição de função [de Operador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) sobre a identidade gerida.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Como posso impedir a criação de identidades geridas atribuídas pelo utilizador?

Pode impedir que os seus utilizadores criem identidades geridas atribuídas pelo utilizador através da [Política Azure](../../governance/policy/overview.md)

1. Navegue até ao [portal Azure](https://portal.azure.com) e vá para a **Política.**
2. Escolha **Definições**
3. Selecione **+ Definição de política** e introduza as informações necessárias.
4. Na pasta da secção de regras políticas
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

Depois de criar a política, atribua-a ao grupo de recursos que pretende utilizar.

1. Navegue para grupos de recursos.
2. Encontre o grupo de recursos que está a usar para testar.
3. Escolha **Políticas** no menu à esquerda.
4. Selecione **A política de atribuir**
5. Na secção **Básico,** forneça:
    1. **Âmbito de aplicação** O grupo de recursos que estamos usando para testar
    1. **Definição política**: A política que criámos anteriormente.
6. Deixe todas as outras definições à sua padrão e escolha **Review + Create**

Neste momento, qualquer tentativa de criar uma identidade gerida atribuída pelo utilizador no grupo de recursos falhará.

  ![Violação de política](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Conceitos

### <a name="do-managed-identities-have-a-backing-app-object"></a>As identidades geridas têm um objeto de aplicação de apoio?

N.º Identidades geridas e Registos de Aplicações AD Azure não são a mesma coisa no diretório.

As inscrições da aplicação têm dois componentes: Um Objeto de Aplicação + Um Objeto Principal de Serviço.
As identidades geridas para os recursos Azure têm apenas um desses componentes: Um Objeto Principal de Serviço.

As identidades geridas não têm um objeto de aplicação no diretório, que é o que é normalmente usado para conceder permissões de aplicações para gráfico de MS. Em vez disso, as permissões de gráficos de MS para identidades geridas devem ser concedidas diretamente ao Diretor de Serviço.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Qual é a credencial associada a uma identidade gerida? Quanto tempo é válido e com que frequência é rodado?

> [!NOTE]
> Como as identidades geridas autenticam é um detalhe de implementação interna que está sujeito a alterações sem aviso prévio.

Identidades geridas utilizam a autenticação baseada em certificados. A credencial de cada identidade gerida tem um prazo de 90 dias e é enrolada ao fim de 45 dias.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Que identidade o IMDS por defeito se não especificar a identidade no pedido?

- Se o sistema atribuído à identidade gerida estiver ativado e nenhuma identidade for especificada no pedido, o IMDS desrespecifique a identidade gerida atribuída ao sistema atribuído.
- Se a identidade gerida atribuída pelo sistema não estiver ativada e existir apenas um utilizador de identidade gerida atribuída, o IMDS desresídeo desse único utilizador atribuído à identidade gerida.
- Se a identidade gerida atribuída pelo sistema não estiver ativada e existirem várias identidades geridas pelo utilizador, então é-lhe exigido que especifique uma identidade gerida no pedido.

## <a name="limitations"></a>Limitações

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>A mesma identidade gerida pode ser usada em várias regiões?

Em suma, sim, pode utilizar identidades geridas atribuídas pelo utilizador em mais de uma região de Azure. A resposta mais longa é que, embora as identidades geridas atribuídas pelo utilizador sejam criadas como recursos regionais, o [principal de serviço](../develop/app-objects-and-service-principals.md#service-principal-object) associado (SP) criado em Azure AD está disponível globalmente. O principal do serviço pode ser utilizado a partir de qualquer região de Azure e a sua disponibilidade depende da disponibilidade do Azure AD. Por exemplo, se criou um utilizador que atribuiu identidade gerida na região South-Central e essa região se torna indisponível esta questão só afeta as atividades [do avião](../../azure-resource-manager/management/control-plane-and-data-plane.md) de controlo na própria identidade gerida.  As atividades realizadas por quaisquer recursos já configurados para utilizar as identidades geridas não seriam afetadas.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>As identidades geridas para os recursos Azure funcionam com a Azure Cloud Services?

Não, não há planos para suportar identidades geridas para recursos Azure em Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Qual é a fronteira de segurança das identidades geridas para os recursos da Azure?

O limite de segurança da identidade é o recurso ao qual está ligado. Por exemplo, o limite de segurança para uma Máquina Virtual com identidades geridas para recursos Azure ativados, é a Máquina Virtual. Qualquer código em execução nesse VM é capaz de ligar para as identidades geridas para o ponto final dos recursos Azure e pedir fichas. É a experiência semelhante com outros recursos que suportam identidades geridas para os recursos da Azure.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>As identidades geridas serão recriadas automaticamente se eu mudar uma subscrição para outro diretório?

N.º Se mover uma subscrição para outro diretório, terá de reuscuá-los manualmente e conceder novamente atribuições de funções ao Azure.
- Para identidades geridas atribuídas pelo sistema: desativar e reativar. 
- Para as identidades geridas atribuídas pelo utilizador: eliminar, recriar e ligá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Posso usar uma identidade gerida para aceder a um recurso num diretório/inquilino diferente?

N.º As identidades geridas não suportam atualmente cenários de diretórios cruzados. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Existem limites de taxas que se aplicam às identidades geridas?

Os limites de identidades geridas têm dependências dos limites de serviço Azure, limites do Serviço de Metadados de Instância Azure (IMDS) e limites de serviço do Azure Ative Directory.

- **Os limites de serviço da Azure** definem o número de operações de criação que podem ser realizadas nos níveis de arrendatário e subscrição. As identidades geridas pelo utilizador também têm [limitações](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) em torno da forma como podem ser nomeadas.
- **IMDS** Em geral, os pedidos ao IMDS limitam-se a cinco pedidos por segundo. Os pedidos que excedam este limiar serão rejeitados com 429 respostas. Os pedidos à categoria Identidade Gerida estão limitados a 20 pedidos por segundo e 5 pedidos simultâneos. Pode ler mais no artigo [Azure Instance Medata Serice (Windows).](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity)
- **Serviço de Diretório Ativo Azure** Cada identidade gerida conta para o limite de quota de objeto num inquilino AD Azure, conforme descrito nos [limites e restrições de serviço Azure AD](../enterprise-users/directory-service-limits-restrictions.md).


## <a name="is-it-ok-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>É correto mover uma identidade gerida atribuída pelo utilizador para um grupo/subscrição de recursos diferentes?

A deslocação de uma identidade gerida atribuída pelo utilizador para um grupo de recursos diferente não é suportada.

## <a name="next-steps"></a>Passos seguintes

- Saiba [como as identidades geridas funcionam com máquinas virtuais](how-managed-identities-work-vm.md)
