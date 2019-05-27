---
title: Resolver problemas relacionados com o RBAC para recursos do Azure | Documentos da Microsoft
description: Resolva problemas com o controlo de acesso baseado em funções (RBAC) para recursos do Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 5dda2eafe86d037faab6284c2af0d8026c194d11
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921154"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Resolver problemas relacionados com o RBAC para recursos do Azure

Este artigo responde a perguntas comuns sobre o controlo de acesso baseado em funções (RBAC) para recursos do Azure, para que saiba o que esperar ao utilizar as funções no portal do Azure e pode resolver problemas de acesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemas com atribuições de funções do RBAC

- Se não for possível adicionar uma atribuição de função no portal do Azure no **controlo de acesso (IAM)** porque o **Add** > **adicionar atribuição de função** opção está desativada ou uma vez que obterá o erro de permissões "o cliente com o id de objeto não tem autorização para realizar a ação", verifique que o tem atualmente sessão iniciada com um utilizador que está atribuído uma função que tenha as `Microsoft.Authorization/roleAssignments/write` permissão como [proprietário](built-in-roles.md#owner) ou [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator) no âmbito para atribuir a função que está a tentar.
- Se receber a mensagem de erro "podem ser criadas mais atribuições de função (código: RoleAssignmentLimitExceeded)" quando tenta atribuir uma função, experimente atribuir funções a grupos, de modo a reduzir o número de atribuições. O Azure suporta até **duas mil** atribuições de funções por subscrição.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se precisar de passos para saber como criar uma função personalizada, veja os tutoriais de função personalizada usando [do Azure PowerShell](tutorial-custom-role-powershell.md) ou [CLI do Azure](tutorial-custom-role-cli.md).
- Se não é possível atualizar uma função personalizada existente, verifique se o é atualmente sessão iniciada com um utilizador que está atribuído uma função que tenha as `Microsoft.Authorization/roleDefinition/write` permissão, tais como [proprietário](built-in-roles.md#owner) ou [o administrador de acesso de utilizador](built-in-roles.md#user-access-administrator).
- Se não conseguir eliminar uma função personalizada e obtiver a mensagem de erro “Não existem atribuições de funções existentes que façam referência à função (código: RoleDefinitionHasAssignments)", significa que ainda há atribuições de funções a utilizar a função personalizada. Remova essas atribuições e experimente eliminar a função personalizada novamente.
- Se receber a mensagem de erro “Limite de definição de função excedido. Podem ser criadas mais definições de função (código: RoleDefinitionLimitExceeded) "ao tentar criar uma nova função personalizada, eliminar quaisquer funções personalizadas que não estão a ser utilizadas. O Azure suporta até **5000** funções personalizadas num inquilino. (Para nuvens especializadas, como o Azure Government, Azure Alemanha e Azure China 21Vianet, o limite é de funções personalizadas de 2000.)
- Se obtiver um erro semelhante a "o cliente tem permissão para executar a ação 'Microsoft.Authorization/roleDefinitions/write' no âmbito '/ subscrições / {subscriptionid}', no entanto, a subscrição associada não foi encontrada" ao tentar atualizar uma função personalizada, verifique Se um ou mais [âmbitos atribuíveis](role-definitions.md#assignablescopes) ter sido eliminada no inquilino. Se o âmbito tiver sido eliminado, crie um pedido de suporte, pois não existe nenhuma solução self-service atualmente.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Recuperar o RBAC quando as subscrições são movidas entre inquilinos

- Se precisar de passos para saber como transferir uma subscrição para um Azure AD diferente do inquilino, consulte [transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md).
- Se transferir uma subscrição para outro inquilino do Azure AD, todas as atribuições de funções são eliminadas permanentemente do inquilino do Azure AD de origem e não são migradas para o inquilino do Azure AD de destino. Tem de recriar as atribuições de funções no inquilino de destino. Também terá de recriar manualmente as identidades geridas para recursos do Azure. Para obter mais informações, consulte [FAQ e problemas conhecidos com geridos identidades](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se for um Azure AD Administrador Global e não tiver acesso a uma subscrição depois que ele foi movido entre inquilinos, utilize o **Access management para recursos do Azure** alternância para temporariamente [elevar o acesso do utilizador](elevate-access-global-admin.md) para obter acesso à subscrição.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas relacionados com administradores ou coadministradores de serviços

- Se estiver a ter problemas com o administrador de serviços ou coadministradores, veja [adicionar ou alterar os administradores de subscrição do Azure](../billing/billing-add-change-azure-subscription-administrator.md) e [funções de administrador de subscrição clássico, funções RBAC do Azure e o Azure AD funções de administrador](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Acesso negado ou erros de permissão

- Se receber o erro de permissões “O cliente com o id de objeto não tem autorização para realizar a ação acima do âmbito (código: AuthorizationFailed)" quando tenta criar um recurso, confirme que tem sessão iniciada com um utilizador que tenha atribuída uma função com a permissão de escrita no recurso no âmbito selecionado. Por exemplo, para gerir máquinas virtuais num grupo de recursos, deve ter a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou no âmbito principal). Para obter uma lista das permissões de cada função incorporada, veja [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas dos recursos do Azure)
- Se obtiver o erro de permissões "Não tem permissão para criar um pedido de suporte" quando tenta criar ou atualizar um pedido de suporte, verifique se o é atualmente sessão iniciada com um utilizador que está atribuído uma função que tenha o `Microsoft.Support/supportTickets/write` permissão, como [Contribuidor de pedido de suporte](built-in-roles.md#support-request-contributor).

## <a name="rbac-changes-are-not-being-detected"></a>Alterações RBAC não estão a ser detetadas

O Gestor de recursos do Azure armazena em cache, às vezes, configurações e dados para melhorar o desempenho. Quando criar ou eliminar as atribuições de funções, pode demorar até 30 minutos para que as alterações entrem em vigor. Se estiver a utilizar o portal do Azure, o Azure PowerShell ou a CLI do Azure, pode forçar uma atualização das suas alterações de atribuição de função ao terminar a sessão e iniciar sessão. Se estiver a efetuar alterações à atribuição de função com chamadas de REST API, pode forçar uma atualização ao atualizar o token de acesso.

## <a name="web-app-features-that-require-write-access"></a>Funcionalidades da aplicação Web que necessitam de acesso de escrita

Se conceder um acesso de só de leitura do utilizador para uma aplicação web individual, algumas funcionalidades estão desativadas que não pode estar esperando. As seguintes capacidades de gestão requerem **escrever** aceder a uma aplicação web (Contribuidor ou proprietário) e não estão disponíveis em qualquer cenário de só de leitura.

* Comandos (como iniciar, parar, etc.)
* Como a alteração das definições de configuração geral, as definições de dimensionamento, as definições de cópia de segurança e as definições de monitorização
* Aceder a credenciais de publicação e outros segredos, como as definições da aplicação e as cadeias de ligação
* Registos de transmissão em fluxo
* Configuração de registos de diagnóstico
* Console (linha de comandos)
* Implementações de Active Directory e recentes (para implementação contínua do local git)
* Gasto estimado
* Testes Web
* Rede virtual (visível apenas para um leitor se anteriormente tiver sido configurada uma rede virtual por um utilizador com acesso de escrita).

Se não é possível aceder a qualquer um destes mosaicos, terá de solicitar o administrador de acesso de contribuinte para a aplicação web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos da aplicação Web que necessitam de acesso de escrita

Aplicações Web são complicadas pela presença de alguns recursos diferentes que interação. Este é um grupo de recursos típico com alguns dos Web sites:

![Grupo de recursos de aplicação Web](./media/troubleshooting/website-resource-model.png)

Como resultado, se conceder alguém o acesso apenas na aplicação web, grande parte da funcionalidade no painel do Web site no portal do Azure está desativado.

Esses itens requerem **escrever** aceder para o **plano do App Service** que corresponde ao seu Web site:  

* Ver a aplicação web do escalão de preço (gratuita ou Standard)  
* Configuração de dimensionamento (número de instâncias, tamanho da máquina virtual, definições de dimensionamento automático)  
* Quotas (armazenamento, CPU, largura de banda)  

Esses itens requerem **escrever** acesso a todo **grupo de recursos** que contém o seu Web site:  

* Certificados SSL e enlaces (certificados SSL podem ser partilhados entre sites no mesmo grupo de recursos e localização geográfica)  
* Regras de alerta  
* Definições de dimensionamento automático  
* Componentes de informações de aplicação  
* Testes Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funcionalidades das máquinas virtuais que necessitam de acesso de escrita

Assim como aplicações web, algumas funcionalidades no painel da máquina virtual requerem acesso de escrita para a máquina virtual ou para outros recursos no grupo de recursos.

Máquinas virtuais estão relacionadas com nomes de domínio, redes virtuais, contas de armazenamento e regras de alerta.

Esses itens requerem **escrever** aceder para o **Máquina Virtual**:

* Pontos Finais  
* Endereços IP  
* Discos  
* Extensões  

Eles exigem **escrever** acesso a ambos os **Máquina Virtual**e o **grupo de recursos** (juntamente com o nome de domínio) que estejam na:  

* Conjunto de Disponibilidade  
* Conjunto com balanceamento de carga  
* Regras de alerta  

Se não é possível aceder a qualquer um destes mosaicos, peça ao administrador de acesso de Contribuidor ao grupo de recursos.

## <a name="azure-functions-and-write-access"></a>As funções do Azure e o acesso de escrita

Algumas funcionalidades do [as funções do Azure](../azure-functions/functions-overview.md) necessitam de acesso de escrita. Por exemplo, se um utilizador tem atribuído a função de leitor, não poderão ver as funções dentro de uma aplicação de funções. O portal apresentará **(sem acesso)**.

![Aplicações de funções sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar a **funcionalidades de plataforma** separador e, em seguida, clique em **todas as definições** ver algumas definições relacionadas com uma aplicação de funções (semelhante a uma aplicação web), mas não podem modificar qualquer uma destas definições.

## <a name="next-steps"></a>Passos Seguintes
* [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
* [Ver registos de atividade para alterações RBAC para recursos do Azure](change-history-report.md)

