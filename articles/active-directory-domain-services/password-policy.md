---
title: Criar e usar políticas de senha no Azure AD Domain Services | Microsoft Docs
description: Saiba como e por que usar políticas de senha refinadas para proteger e controlar senhas de contas em um domínio gerenciado do Azure AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617153"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Políticas de bloqueio de senha e conta em domínios gerenciados

Para gerenciar a segurança da conta no Azure Active Directory Domain Services (AD DS do Azure), você pode definir políticas de senha refinadas que controlam as configurações, como o comprimento mínimo da senha, o tempo de expiração da senha ou a complexidade da senha. Uma política de senha padrão é aplicada a todos os usuários em um domínio gerenciado AD DS do Azure. Para fornecer controle granular e atender às necessidades específicas de negócios ou de conformidade, políticas adicionais podem ser criadas e aplicadas a grupos de usuários específicos.

Este artigo mostra como criar e configurar uma política de senha refinada usando o Centro Administrativo do Active Directory.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
  * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
  * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
  * Se necessário, conclua o tutorial para [criar e configurar uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
  * Se necessário, conclua o tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Visão geral das políticas de senha refinadas (FGPP)

As FGPPs (políticas de senha refinadas) permitem que você aplique restrições específicas para políticas de bloqueio de senha e de conta a usuários diferentes em um domínio. Por exemplo, para proteger contas com privilégios, você pode aplicar configurações de senha mais estritas do que as contas regulares sem privilégios. Você pode criar vários FGPPs para especificar políticas de senha em um domínio gerenciado do Azure AD DS.

As seguintes configurações de senha podem ser configuradas usando FGPP:

* Comprimento mínimo da senha
* Histórico de senha
* As senhas devem atender aos requisitos de complexidade
* Duração mínima da senha
* Duração máxima da senha
* Política de bloqueio de conta
  * Duração do bloqueio de conta
  * Número de tentativas de logon com falha permitidas
  * Redefinir a contagem de tentativas de logon com falha após

FGPP afeta somente os usuários criados no Azure AD DS. Usuários de nuvem e usuários de domínio sincronizados no Azure AD DS domínio gerenciado do Azure AD não são afetados pelas políticas de senha.

As políticas são distribuídas por meio de associação de grupo no domínio gerenciado AD DS do Azure, e todas as alterações feitas são aplicadas na próxima entrada do usuário. A alteração da política não desbloqueia uma conta de usuário que já está bloqueada.

## <a name="default-fine-grained-password-policy-settings"></a>Configurações de política de senha refinadas padrão

Em um domínio gerenciado do Azure AD DS, as seguintes políticas de senha são configuradas por padrão e aplicadas a todos os usuários:

* **Comprimento mínimo da senha (caracteres):** 7
* **Duração máxima da senha (tempo de vida):** 90 dias
* **As senhas devem atender aos requisitos de complexidade**

As políticas de bloqueio de conta a seguir são então configuradas por padrão:

* **Duração do bloqueio de conta:** 30
* **Número de tentativas de logon com falha permitidas:** 5
* **Falha ao redefinir a contagem de tentativas de logon após:** 30 minutos

Com essas configurações padrão, as contas de usuário são bloqueadas por 30 minutos se cinco senhas inválidas forem usadas em 2 minutos. As contas são desbloqueadas automaticamente após 30 minutos.

Você não pode modificar ou excluir a política de senha refinada padrão interna. Em vez disso, os membros do grupo de *Administradores do AAD DC* podem criar um FGPP personalizado e configurá-lo para substituir (ter precedência sobre) o FGPP interno padrão, conforme mostrado na próxima seção.

## <a name="create-a-custom-fine-grained-password-policy"></a>Criar uma política de senha refinada personalizada

À medida que você cria aplicativos e no Azure, talvez queira configurar um FGPP personalizado. Alguns exemplos da necessidade de criar um FGPP personalizado incluem para definir uma política de bloqueio de conta diferente ou para definir uma configuração de tempo de vida de senha padrão para o domínio gerenciado.

Você pode criar um FGPP personalizado e aplicá-lo a grupos específicos em seu domínio gerenciado AD DS do Azure. Essa configuração substitui efetivamente o FGPP padrão. Você também pode criar políticas de senha refinadas personalizadas e aplicá-las a quaisquer UOs personalizadas criadas no domínio gerenciado AD DS do Azure.

Para criar uma política de senha refinada, use as ferramentas administrativas Active Directory de uma VM ingressada no domínio. O Centro Administrativo do Active Directory permite exibir, editar e criar recursos em um domínio gerenciado do Azure AD DS, incluindo UOs.

> [!NOTE]
> Para criar uma política de senha refinada em um domínio gerenciado AD DS do Azure, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Na tela iniciar, selecione **Ferramentas administrativas**. É mostrada uma lista de ferramentas de gerenciamento disponíveis que foram instaladas no tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
1. Para criar e gerenciar UOs, selecione **centro administrativo do Active Directory** na lista de ferramentas administrativas.
1. No painel esquerdo, escolha seu domínio gerenciado AD DS do Azure, como *contoso.com*.
1. No painel **tarefas** à direita, selecione **novo > configurações de senha**.
1. Na caixa de diálogo **criar configurações de senha** , insira um nome para a política, como *MyCustomFGPP*. Defina a precedência como adequadamente para substituir o FGPP padrão (que é *200*), como *1*.

    Edite outras configurações de política de senha conforme desejado, como **impor o histórico de senha** para exigir que o usuário crie uma senha diferente das *24* senhas anteriores.

    ![Criar uma política de senha refinada personalizada](./media/how-to/custom-fgpp.png)

1. Desmarque **proteger contra exclusão acidental**. Se essa opção estiver selecionada, você não poderá salvar o FGPP.
1. Na seção **aplica-se diretamente a** , selecione o botão **Adicionar** . Na caixa de diálogo **Selecionar usuários ou grupos** , clique no botão **locais** .

    ![Selecione os usuários e grupos aos quais aplicar a política de senha](./media/how-to/fgpp-applies-to.png)

1. Políticas de senha refinadas só podem ser aplicadas a grupos. Na caixa de diálogo **locais** , expanda o nome de domínio, como *contoso.com*, e selecione uma UO, como **usuários do AADDC**. Se você tiver uma UO personalizada que contenha um grupo de usuários que deseja aplicar, selecione essa UO.

    ![Selecione a UO à qual o grupo pertence](./media/how-to/fgpp-container.png)

1. Digite o nome do grupo ao qual você deseja aplicar a política e, em seguida, selecione **verificar nomes** para validar se o grupo existe.

    ![Pesquise e selecione o grupo para aplicar FGPP](./media/how-to/fgpp-apply-group.png)

1. Com o nome do grupo selecionado agora exibido na seção **aplica-se diretamente a** , selecione **OK** para salvar sua política de senha personalizada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre políticas de senha refinadas e como usar o centro de administração do Active Directory, consulte os seguintes artigos:

* [Saiba mais sobre políticas de senha refinadas](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurar políticas de senha refinadas usando o centro de administração do AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
