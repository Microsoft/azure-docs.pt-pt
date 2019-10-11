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
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: 3876c6f80e9f18059ab4abac67732cdbf2ca24fa
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248314"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Políticas de bloqueio de senha e conta em domínios gerenciados

Para gerenciar a segurança de usuário no Azure Active Directory Domain Services (AD DS do Azure), você pode definir políticas de senha refinadas que controlam as configurações de bloqueio de conta ou a complexidade e o comprimento mínimo da senha. Uma política de senha refinada padrão é criada e aplicada a todos os usuários em um domínio gerenciado AD DS do Azure. Para fornecer controle granular e atender às necessidades específicas de negócios ou de conformidade, políticas adicionais podem ser criadas e aplicadas a grupos de usuários específicos.

Este artigo mostra como criar e configurar uma política de senha refinada no Azure AD DS usando o Centro Administrativo do Active Directory.

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

## <a name="default-password-policy-settings"></a>Configurações de política de senha padrão

As FGPPs (políticas de senha refinadas) permitem que você aplique restrições específicas para políticas de bloqueio de senha e de conta a usuários diferentes em um domínio. Por exemplo, para proteger contas com privilégios, você pode aplicar configurações de bloqueio de conta mais rigorosas do que as contas regulares sem privilégios. Você pode criar vários FGPPs em um domínio gerenciado AD DS do Azure e especificar a ordem de prioridade para aplicá-los aos usuários.

As políticas são distribuídas por meio de associação de grupo em um domínio gerenciado AD DS do Azure, e todas as alterações feitas são aplicadas na próxima entrada do usuário. A alteração da política não desbloqueia uma conta de usuário que já está bloqueada.

As políticas de senha se comportam de maneira um pouco diferente dependendo de como a conta de usuário à qual elas são aplicadas foi criada. Há duas maneiras pelas quais uma conta de usuário pode ser criada no Azure AD DS:

* A conta de usuário pode ser sincronizada no Azure AD. Isso inclui contas de usuário somente em nuvem criadas diretamente no Azure e contas de usuário híbridos sincronizadas de um ambiente de AD DS local usando Azure AD Connect.
    * A maioria das contas de usuário no Azure AD DS são criadas por meio do processo de sincronização do Azure AD.
* A conta de usuário pode ser criada manualmente em um domínio gerenciado AD DS do Azure e não existe no Azure AD.

Todos os usuários, independentemente de como são criados, têm as seguintes políticas de bloqueio de conta aplicadas pela política de senha padrão no Azure AD DS:

* **Duração do bloqueio de conta:** 30
* **Número de tentativas de logon com falha permitidas:** 5
* **Redefinir a contagem de tentativas de logon com falha após:** 30 minutos
* **Duração máxima da senha (tempo de vida):** 90 dias

Com essas configurações padrão, as contas de usuário são bloqueadas por 30 minutos se cinco senhas inválidas forem usadas em 2 minutos. As contas são desbloqueadas automaticamente após 30 minutos.

Os bloqueios de conta só ocorrem no domínio gerenciado. As contas de usuário são bloqueadas somente no Azure AD DS e somente devido a tentativas de entrada com falha no domínio gerenciado. As contas de usuário que foram sincronizadas no do Azure AD ou locais não são bloqueadas em seus diretórios de origem, somente no Azure AD DS.

Se você tiver uma política de senha do Azure AD que especifique uma duração máxima de senha superior a 90 dias, essa duração de senha será aplicada à política padrão no Azure AD DS. Você pode configurar uma política de senha personalizada para definir uma duração de senha máxima diferente no Azure AD DS. Tome cuidado se você tiver uma idade de senha máxima mais curta configurada em uma política de senha de AD DS do Azure do que no Azure AD ou em um ambiente de AD DS local. Nesse cenário, a senha de um usuário pode expirar no Azure AD DS antes que seja solicitada a alteração no Azure AD em um ambiente de AD DS local.

Para contas de usuário criadas manualmente em um domínio gerenciado AD DS do Azure, as configurações de senha adicionais a seguir também são aplicadas a partir da política padrão. Essas configurações não se aplicam a contas de usuário sincronizadas no Azure AD, pois um usuário não pode atualizar sua senha diretamente no Azure AD DS.

* **Comprimento mínimo da senha (caracteres):** 7
* **As senhas devem atender aos requisitos de complexidade**

Você não pode modificar as configurações de bloqueio de conta ou senha na política de senha padrão. Em vez disso, os membros do grupo de *Administradores do AAD DC* podem criar políticas de senha personalizadas e configurá-las para substituir (prevalecem sobre) a política interna padrão, conforme mostrado na próxima seção.

## <a name="create-a-custom-password-policy"></a>Criar uma política de senha personalizada

À medida que você cria e executa aplicativos no Azure, talvez queira configurar uma política de senha personalizada. Por exemplo, você pode criar uma política para definir configurações de política de bloqueio de conta diferentes.

As políticas de senha personalizadas são aplicadas a grupos em um domínio gerenciado AD DS do Azure. Essa configuração substitui efetivamente a política padrão.

Para criar uma política de senha personalizada, use as ferramentas administrativas Active Directory de uma VM ingressada no domínio. O Centro Administrativo do Active Directory permite exibir, editar e criar recursos em um domínio gerenciado do Azure AD DS, incluindo UOs.

> [!NOTE]
> Para criar uma política de senha personalizada em um domínio gerenciado AD DS do Azure, você deve estar conectado a uma conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

1. Na tela iniciar, selecione **Ferramentas administrativas**. É mostrada uma lista de ferramentas de gerenciamento disponíveis que foram instaladas no tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].
1. Para criar e gerenciar UOs, selecione **centro administrativo do Active Directory** na lista de ferramentas administrativas.
1. No painel esquerdo, escolha seu domínio gerenciado AD DS do Azure, como *contoso.com*.
1. Abra o contêiner do **sistema** e, em seguida, o **contêiner de configuração de senha**.

    Uma política de senha interna para o domínio gerenciado do Azure AD DS é mostrada. Você não pode modificar essa política interna. Em vez disso, crie uma política de senha personalizada para substituir a política padrão.

    ![Criar uma política de senha no Centro Administrativo do Active Directory](./media/password-policy/create-password-policy-adac.png)

1. No painel **tarefas** à direita, selecione **novo > configurações de senha**.
1. Na caixa de diálogo **criar configurações de senha** , insira um nome para a política, como *MyCustomFGPP*.
1. Quando existem várias diretivas de senha, a política com a maior precedência, ou prioridade, é aplicada a um usuário. Quanto menor o número, maior a prioridade. A política de senha padrão tem uma prioridade de *200*.

    Defina a precedência para sua política de senha personalizada para substituir o padrão, como *1*.

1. Edite outras configurações de política de senha conforme desejado. Lembre-se dos seguintes pontos principais:

    * Configurações como complexidade de senha, idade ou tempo de expiração somente para usuários criados manualmente em um domínio gerenciado AD DS do Azure.
    * As configurações de bloqueio de conta se aplicam a todos os usuários, mas só entram em vigor no domínio gerenciado.

    ![Criar uma política de senha refinada personalizada](./media/how-to/custom-fgpp.png)

1. Desmarque **proteger contra exclusão acidental**. Se essa opção estiver selecionada, você não poderá salvar o FGPP.
1. Na seção **aplica-se diretamente a** , selecione o botão **Adicionar** . Na caixa de diálogo **Selecionar usuários ou grupos** , clique no botão **locais** .

    ![Selecione os usuários e grupos aos quais aplicar a política de senha](./media/how-to/fgpp-applies-to.png)

1. As políticas de senha só podem ser aplicadas a grupos. Na caixa de diálogo **locais** , expanda o nome de domínio, como *contoso.com*, e selecione uma UO, como **usuários do AADDC**. Se você tiver uma UO personalizada que contenha um grupo de usuários que deseja aplicar, selecione essa UO.

    ![Selecione a UO à qual o grupo pertence](./media/how-to/fgpp-container.png)

1. Digite o nome do grupo ao qual você deseja aplicar a política e, em seguida, selecione **verificar nomes** para validar se o grupo existe.

    ![Pesquise e selecione o grupo para aplicar FGPP](./media/how-to/fgpp-apply-group.png)

1. Com o nome do grupo selecionado agora exibido na seção **aplica-se diretamente a** , selecione **OK** para salvar sua política de senha personalizada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre políticas de senha e usar o centro de administração do Active Directory, consulte os seguintes artigos:

* [Saiba mais sobre políticas de senha refinadas](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurar políticas de senha refinadas usando o centro de administração do AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
