---
title: PERGUNTAS Frequentes e questões conhecidas com identidades geridas - Azure AD
description: Problemas conhecidos com identidades geridas para recursos Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/04/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f1be2e64435cb0bcdb369a398a9a65fc3714fb2
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008541"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>PERGUNTAS Frequentes e questões conhecidas com identidades geridas para recursos da Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (Perguntas Frequentes)

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Como se pode encontrar recursos que tenham uma identidade gerida?

Pode encontrar a lista de recursos que possuem uma identidade gerida atribuída ao sistema utilizando o seguinte Comando CLI Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>As identidades geridas têm um objeto de aplicação de apoio?

N.º Identidades geridas e Registos de Aplicações AD Azure não são a mesma coisa no diretório. 

As inscrições da aplicação têm dois componentes: Um Objeto de Aplicação + Um Objeto Principal de Serviço. As identidades geridas para os recursos Azure têm apenas um desses componentes: Um Objeto Principal de Serviço. 

As identidades geridas não têm um objeto de aplicação no diretório, que é o que é normalmente usado para conceder permissões de aplicações para gráfico de MS. Em vez disso, as permissões de gráficos de MS para identidades geridas devem ser concedidas diretamente ao Diretor de Serviço.  

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>A mesma identidade gerida pode ser usada em várias regiões?

Em suma, sim, pode utilizar identidades geridas atribuídas pelo utilizador em mais de uma região de Azure. A resposta mais longa é que, embora as identidades geridas atribuídas pelo utilizador sejam criadas como recursos regionais, o [principal de serviço](../develop/app-objects-and-service-principals.md#service-principal-object) associado (SPN) criado em Azure AD está disponível globalmente. O principal do serviço pode ser utilizado a partir de qualquer região de Azure e a sua disponibilidade depende da disponibilidade do Azure AD. Por exemplo, se criou um utilizador que atribuiu identidade gerida na região South-Central e essa região se torna indisponível esta questão só afeta as atividades [do avião](../../azure-resource-manager/management/control-plane-and-data-plane.md) de controlo na própria identidade gerida.  As atividades realizadas por quaisquer recursos já configurados para utilizar as identidades geridas não seriam afetadas.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>As identidades geridas para os recursos Azure funcionam com a Azure Cloud Services?

Não, não há planos para suportar identidades geridas para recursos Azure em Azure Cloud Services.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Qual é a credencial associada a uma identidade gerida? Quanto tempo é válido e com que frequência é rodado?

> [!NOTE]
> Como as identidades geridas autenticam é um detalhe de implementação interna que está sujeito a alterações sem aviso prévio.

Identidades geridas utilizam a autenticação baseada em certificados. A credencial de cada identidade gerida tem um prazo de 90 dias e é enrolada ao fim de 45 dias.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Qual é a fronteira de segurança das identidades geridas para os recursos da Azure?

O limite de segurança da identidade é o recurso ao qual está ligado. Por exemplo, o limite de segurança para uma Máquina Virtual com identidades geridas para recursos Azure ativados, é a Máquina Virtual. Qualquer código em execução nesse VM é capaz de ligar para as identidades geridas para o ponto final dos recursos Azure e pedir fichas. É a experiência semelhante com outros recursos que suportam identidades geridas para os recursos da Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Que identidade o IMDS por defeito se não especificar a identidade no pedido?

- Se o sistema atribuído à identidade gerida estiver ativado e nenhuma identidade for especificada no pedido, o IMDS irá desreser o sistema atribuído à identidade gerida.
- Se a identidade gerida atribuída pelo sistema não estiver ativada e existir apenas um utilizador de identidade gerida atribuída, o IMDS irá por defeito com a identidade gerida atribuída por um único utilizador. 
- Se o sistema atribuído à identidade gerida não estiver ativado e existirem várias identidades geridas pelo utilizador, então é necessária especificar uma identidade gerida no pedido.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>As identidades geridas serão recriadas automaticamente se eu mudar uma subscrição para outro diretório?

N.º Se mover uma subscrição para outro diretório, terá de reuscuá-los manualmente e conceder novamente atribuições de funções ao Azure.
- Para identidades geridas atribuídas pelo sistema: desativar e reativar. 
- Para as identidades geridas atribuídas pelo utilizador: eliminar, recriar e ligá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Posso usar uma identidade gerida para aceder a um recurso num diretório/inquilino diferente?

N.º As identidades geridas não suportam atualmente cenários de diretórios cruzados. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quais as permissões do Azure RBAC necessárias para gerir a identidade num recurso? 

- Identidade gerida atribuída pelo sistema: É necessário escrever permissões sobre o recurso. Por exemplo, para máquinas virtuais, precisa de Microsoft.Compute/virtualMachines/write. Esta ação está incluída em funções específicas de recursos como [O Contribuinte De Máquina Virtual.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
- Identidade gerida atribuída pelo utilizador: É necessário escrever permissões sobre o recurso. Por exemplo, para máquinas virtuais, precisa de Microsoft.Compute/virtualMachines/write. Além da atribuição de função [de Operador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) sobre a identidade gerida.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Como posso impedir a criação de identidades geridas atribuídas pelo utilizador?

Pode impedir que os seus utilizadores criem identidades geridas atribuídas pelo utilizador através da [Política Azure](../../governance/policy/overview.md)

- Navegue até ao [portal Azure](https://portal.azure.com) e vá para a **Política.**
- Escolha **Definições**
- Selecione **+ Definição de política** e introduza as informações necessárias.
- Na pasta da secção de regras políticas

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

Depois de criar a política atribua-a ao grupo de recursos que gostaria de utilizar.

- Navegue para grupos de recursos.
- Encontre o grupo de recursos que está a usar para testar.
- Escolha **Políticas** no menu à esquerda.
- Selecione **A política de atribuir**
- Na secção **Básico,** prevê-se:
    - **Âmbito de aplicação** O grupo de recursos que estamos usando para testar
    - **Definição política**: A política que criámos anteriormente.
- Deixe todas as outras definições à sua padrão e escolha **Review + Create**

Neste momento, qualquer tentativa de criar uma identidade gerida atribuída pelo utilizador no grupo de recursos falhará.

  ![Violação de política](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar esquema para identidades geridas para extensão de recursos Azure

Quando as identidades geridas para os recursos Azure são ativadas num VM, o seguinte erro é mostrado ao tentar utilizar a função "Script de automação" para o VM, ou o seu grupo de recursos:

![Identidades geridas para erro de exportação de script de automação de recursos Azure](./media/msi-known-issues/automation-script-export-error.png)

As identidades geridas para a extensão VM dos recursos Azure (prevista para a depreciação em janeiro de 2019) não suportam atualmente a capacidade de exportar o seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não apresenta parâmetros de configuração para permitir identidades geridas para recursos Azure no recurso. Estas secções podem ser adicionadas manualmente seguindo os exemplos em [Configurar identidades geridas para recursos Azure em um VM Azure usando um modelo](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquemas se tornar disponível para as identidades geridas para a extensão VM dos recursos Azure (prevista para a depreciação em janeiro de 2019), será listada nos [Grupos de Recursos Exportadores que contenham extensões VM.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM falha no arranque depois de ser transferido do grupo de recursos ou subscrição

Se mover um VM no estado de funcionamento, ele continua a funcionar durante a mudança. No entanto, após a mudança, se o VM for parado e reiniciado, não começará. Esta questão acontece porque o VM não está a atualizar a referência às identidades geridas para a identidade dos recursos Azure e continua a apontar-lhe no antigo grupo de recursos.

**Solução** 
 
Desencadeie uma atualização sobre o VM para que possa obter valores corretos para as identidades geridas para os recursos Azure. Pode fazer uma alteração de propriedade em VM para atualizar a referência às identidades geridas para identidade de recursos Azure. Por exemplo, pode definir um novo valor de etiqueta no VM com o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova etiqueta "fixVM" com um valor de 1 no VM. 
 
Ao definir esta propriedade, o VM atualiza com as identidades geridas corretas para o recurso de recursos Azure URI, e então você deve ser capaz de iniciar o VM. 
 
Uma vez iniciado o VM, a etiqueta pode ser removida utilizando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferência de uma subscrição entre diretórios AD da Azure

As identidades geridas não são atualizadas quando uma subscrição é transferida/transferida para outro diretório. Como resultado, quaisquer identidades geridas atribuídas pelo sistema ou atribuídas pelo utilizador serão quebradas. 

Solução alternativa para identidades geridas numa subscrição que foi transferida para outro diretório:

 - Para identidades geridas atribuídas pelo sistema: desativar e reativar. 
 - Para as identidades geridas atribuídas pelo utilizador: eliminar, recriar e ligá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

Para obter mais informações, veja [Transferir uma subscrição do Azure para outro diretório do Azure AD](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Mover uma identidade gerida atribuída pelo utilizador para um grupo/subscrição de recursos diferentes

A deslocação de uma identidade gerida atribuída pelo utilizador para um grupo de recursos diferente não é suportada.
