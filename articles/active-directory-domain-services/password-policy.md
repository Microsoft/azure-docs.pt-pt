---
title: Criar e utilizar políticas de password sintetizar em Serviços de Domínio Azure AD [ Microsoft Docs
description: Saiba como e porquê usar políticas de senha de grão fino para proteger e controlar as palavras-passe da conta num domínio gerido pelo Azure AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4402c1ce2f051c8d1911e7c0332d4cac787ce1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613192"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Políticas de bloqueio de passwords e contas em domínios geridos

Para gerir a segurança dos utilizadores nos Serviços de Domínio do Diretório Ativo do Azure (Azure AD DS), pode definir políticas de senha de grãos finos que controlam as definições de bloqueio da conta ou o comprimento mínimo e complexidade da palavra-passe. Uma política de senha de grãos finos padrão é criada e aplicada a todos os utilizadores num domínio gerido por Azure AD DS. Para fornecer controlo granular e satisfazer necessidades específicas de negócioou de conformidade, políticas adicionais podem ser criadas e aplicadas a grupos específicos de utilizadores.

Este artigo mostra-lhe como criar e configurar uma política de senha sinuosa em Azure AD DS utilizando o Ative Directory Administrative Center.

> [!NOTE]
> As políticas de password só estão disponíveis para domínios geridos pelo Azure AD DS criados utilizando o modelo de implementação do Gestor de Recursos. Para domínios geridos mais antigos criados usando classic, [migrar do modelo clássico de rede virtual para O Gestor][migrate-from-classic]de Recursos .

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
  * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
  * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
  * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
  * A instância Azure AD DS deve ter sido criada utilizando o modelo de implementação do Gestor de Recursos. Se necessário, [migrar do modelo clássico de rede virtual para O Gestor][migrate-from-classic]de Recursos .
* Um VM de gestão do Servidor Windows que se junta ao domínio gerido pelo Azure AD DS.
  * Se necessário, complete o tutorial para criar um VM de [gestão.][tutorial-create-management-vm]
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.

## <a name="default-password-policy-settings"></a>Definições de política de palavra-passe padrão

As políticas de senha de grão fino (FGPPs) permitem aplicar restrições específicas para as políticas de password e bloqueio de conta a diferentes utilizadores num domínio. Por exemplo, para garantir contas privilegiadas, pode aplicar configurações mais rigorosas de bloqueio de conta do que contas regulares não privilegiadas. Pode criar vários FGPPs dentro de um domínio gerido pelo Azure AD DS e especificar a ordem de prioridade para os aplicar aos utilizadores.

As políticas são distribuídas através da associação de grupos num domínio gerido pelo Azure AD DS, e quaisquer alterações que faça são aplicadas no próximo início de sessão do utilizador. Mudar a apólice não desbloqueia uma conta de utilizador que já está bloqueada.

As políticas de password comportam-se de forma um pouco diferente, dependendo da forma como a conta de utilizador a que são aplicadas foi criada. Existem duas formas de criar uma conta de utilizador em DS AD Azure:

* A conta de utilizador pode ser sincronizada a partir de Azure AD. Isto inclui contas de utilizadores exclusivamente em nuvem criadas diretamente no Azure, e contas híbridas de utilizador sincronizadas a partir de um ambiente AD DS no local usando Azure AD Connect.
    * A maioria das contas de utilizadores em Azure AD DS são criadas através do processo de sincronização a partir de Azure AD.
* A conta de utilizador pode ser criada manualmente num domínio gerido por Azure AD DS, e não existe em Azure AD.

Todos os utilizadores, independentemente da forma como são criados, têm as seguintes políticas de bloqueio de conta aplicadas pela política de senha padrão no Azure ADDS:

* **Duração do bloqueio da conta:** 30
* **Número de tentativas falhadas de logon permitidas:** 5
* **Reset failed logon tentativas contam após:** 30 minutos
* **Idade máxima da senha (vida útil):** 90 dias

Com estas definições predefinidas, as contas de utilizador ficam bloqueadas durante 30 minutos se cinco palavras-passe inválidas forem usadas dentro de 2 minutos. As contas são automaticamente desbloqueadas após 30 minutos.

Os bloqueios de conta só ocorrem dentro do domínio gerido. As contas dos utilizadores só estão bloqueadas em DS AD Azure, e apenas devido a tentativas falhadas de entrada contra o domínio gerido. As contas de utilizadores que foram sincronizadas a partir de Azure AD ou no local não estão bloqueadas nos seus diretórios de origem, apenas em Azure AD DS.

Se tiver uma política de senha seletiva Azure que especifique uma idade máxima de senha superior a 90 dias, essa idade da palavra-passe é aplicada à política padrão no Azure AD DS. Pode configurar uma política de senha personalizada para definir uma idade máxima de senha diferente no Azure AD DS. Tenha cuidado se tiver uma idade máxima de senha mais curta configurada numa política de senha saqueada do Azure AD do que em Azure AD ou num ambiente AD DS no local. Nesse cenário, a palavra-passe de um utilizador pode expirar em DS AD Azure antes de ser solicitada a mudança em Azure AD ou num ambiente AD DS no local.

Para as contas de utilizador criadas manualmente num domínio gerido pelo Azure AD DS, as seguintes definições adicionais de senha também são aplicadas a partir da política predefinida. Estas definições não se aplicam às contas de utilizador sincronizadas a partir do Azure AD, uma vez que um utilizador não pode atualizar a sua palavra-passe diretamente no Azure ADDS.

* **Comprimento mínimo da palavra-passe (caracteres):** 7
* **As palavras-passe devem satisfazer os requisitos de complexidade**

Não é possível modificar o bloqueio da conta ou as definições de palavra-passe na política de palavra-passe predefinida. Em vez disso, os membros do grupo de administradores da *AAD DC* podem criar políticas de senha personalizadas e configurá-la para anular (ter precedência sobre) a política incorporada padrão, como mostrado na secção seguinte.

## <a name="create-a-custom-password-policy"></a>Criar uma política de senha personalizada

À medida que constrói e executa aplicações no Azure, pode querer configurar uma política de senha personalizada. Por exemplo, poderia criar uma política para definir diferentes definições de política de bloqueio de conta.

As políticas de senha personalizadas são aplicadas a grupos num domínio gerido pelo Azure AD DS. Esta configuração substitui efetivamente a política padrão.

Para criar uma política de senha personalizada, utilize as Ferramentas Administrativas de Diretório Ativo a partir de um VM filiado em domínio. O Ative Directory Administrative Center permite-lhe visualizar, editar e criar recursos num domínio gerido pela Azure AD DS, incluindo OUs.

> [!NOTE]
> Para criar uma política de senha personalizada num domínio gerido pelo Azure AD DS, deve ser inscrito numa conta de utilizador que seja membro do grupo de *administradores da AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É mostrada uma lista de ferramentas de gestão disponíveis que foram instaladas no tutorial para criar um VM de [gestão.][tutorial-create-management-vm]
1. Para criar e gerir as OUs, selecione **Ative Directory Administrative Center** a partir da lista de ferramentas administrativas.
1. No painel esquerdo, escolha o seu domínio gerido azure AD DS, como *aaddscontoso.com*.
1. Abra o recipiente **do sistema** e, em seguida, o recipiente de definições de **palavra-passe**.

    É apresentada uma política de senha incorporada para o domínio gerido pelo Azure AD DS. Não pode modificar esta política incorporada. Em vez disso, crie uma política de senha personalizada para anular a política de incumprimento.

    ![Criar uma política de senha no Centro Administrativo de Diretório Ativo](./media/password-policy/create-password-policy-adac.png)

1. No painel **'Tarefas'** à direita, selecione **Definições de palavra-passe de nova >**.
1. No diálogo **'Definições de Definições de Escolha',** introduza um nome para a apólice, como *MyCustomFGPP*.
1. Quando existem várias políticas de senha, a política com a maior precedência, ou prioridade, é aplicada a um utilizador. Quanto menor o número, maior a prioridade. A política de senha padrão tem uma prioridade de *200*.

    Detete a precedência para que a sua política de senha personalizada anule o padrão, como *1*.

1. Editar outras definições de política de palavra-passe conforme desejado. Lembre-se dos seguintes pontos-chave:

    * Definições como complexidade de palavra-passe, idade ou tempo de validade apenas para utilizadores criados manualmente num domínio gerido pelo Azure AD DS.
    * As definições de bloqueio da conta aplicam-se a todos os utilizadores, mas apenas entram em vigor dentro do domínio gerido e não no próprio Azure AD.

    ![Criar uma política personalizada de senha de grãos finos](./media/how-to/custom-fgpp.png)

1. Descontrolar **Proteja contra a eliminação acidental**. Se esta opção for selecionada, não pode salvar o FGPP.
1. Na secção **Aplica-se diretamente,** selecione o botão **Adicionar.** No diálogo **Select Users ou Groups,** selecione o botão **Locais.**

    ![Selecione os utilizadores e grupos para aplicar a política de palavra-passe para](./media/how-to/fgpp-applies-to.png)

1. As políticas de senha só podem ser aplicadas a grupos. No diálogo **Local,** expanda o nome de domínio, como *aaddscontoso.com,* em seguida, selecione um OU, como **utilizadores AADDC**. Se tiver um OU personalizado que contenha um grupo de utilizadores que deseja candidatar, selecione esse OU.

    ![Selecione a Ou a que o grupo pertence](./media/how-to/fgpp-container.png)

1. Digite o nome do grupo ao ao que pretende aplicar a apólice e, em seguida, selecione **'Ver Nomes'** para validar que o grupo existe.

    ![Procure e selecione o grupo para aplicar FGPP](./media/how-to/fgpp-apply-group.png)

1. Com o nome do grupo selecionado agora exibido na secção **Aplica-se diretamente à** secção, selecione **OK** para guardar a sua política de senha personalizada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas de passworde utilizando o Ative Directory Administration Center, consulte os seguintes artigos:

* [Conheça as políticas de senha de grãos finos](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configure políticas de senha de grãos finos usando o Centro de Administração aD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
