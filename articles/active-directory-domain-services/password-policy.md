---
title: Crie e utilize políticas de palavra-passe em Azure AD Domain Services | Microsoft Docs
description: Saiba como e porquê usar políticas de palavra-passe de grão fino para proteger e controlar senhas de conta num domínio gerido por Azure AD DS.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619237"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Políticas de bloqueio de passwords e de conta nos domínios geridos pelo Azure Ative Directory Domain Services

Para gerir a segurança do utilizador nos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS), pode definir políticas de palavra-passe de grãos finos que controlam as definições de bloqueio de conta ou o comprimento e complexidade mínimos da palavra-passe. Uma política de senha fina padrão é criada e aplicada a todos os utilizadores num domínio gerido por Azure AD DS. Para fornecer controlo granular e satisfazer necessidades específicas de negócio ou conformidade, políticas adicionais podem ser criadas e aplicadas a grupos específicos de utilizadores.

Este artigo mostra-lhe como criar e configurar uma política de senha de grão fino em Azure AD DS usando o Ative Directory Administrative Center.

> [!NOTE]
> As políticas de palavra-passe só estão disponíveis para domínios geridos criados utilizando o modelo de implementação do Gestor de Recursos. Para domínios geridos mais antigos criados utilizando o Classic, [migrar do modelo de rede virtual Clássico para o Gestor de Recursos.][migrate-from-classic]

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
  * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
  * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
  * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
  * O domínio gerido deve ter sido criado utilizando o modelo de implementação do Gestor de Recursos. Se necessário, [migrar do modelo de rede virtual Clássico para o Gestor de Recursos.][migrate-from-classic]
* Um VM de gestão de servidores do Windows que se junta ao domínio gerido.
  * Se necessário, complete o tutorial para [criar um VM de gestão.][tutorial-create-management-vm]
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.

## <a name="default-password-policy-settings"></a>Definições de política de palavra-passe padrão

As políticas de palavras-passe de grão fino (FGPPs) permitem aplicar restrições específicas para políticas de bloqueio de passwords e conta a diferentes utilizadores num domínio. Por exemplo, para garantir contas privilegiadas pode aplicar configurações de bloqueio de conta mais rigorosas do que contas não privilegiadas regulares. Pode criar vários FGPPs dentro de um domínio gerido e especificar a ordem de prioridade para aplicá-los aos utilizadores.

Para obter mais informações sobre as políticas de senha e utilizar o Ative Directory Administration Center, consulte os seguintes artigos:

* [Conheça as políticas de senha de grão fino](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configure políticas de senha de grão fino usando o Centro de Administração da AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

As políticas são distribuídas através de associação de grupo num domínio gerido, e quaisquer alterações que escote são aplicadas no próximo início de sôr.000. Mudar a política não desbloqueia uma conta de utilizador que já está bloqueada.

As políticas de palavra-passe comportam-se de forma um pouco diferente, dependendo da forma como a conta de utilizador a que são aplicadas foi criada. Existem duas formas de criar uma conta de utilizador em Azure AD DS:

* A conta de utilizador pode ser sincronizada a partir do Azure AD. Isto inclui contas de utilizadores criadas apenas em nuvem criadas diretamente no Azure, e contas híbridas de utilizadores sincronizadas a partir de um ambiente AD DS no local usando Azure AD Connect.
    * A maioria das contas de utilizador em Azure AD DS são criadas através do processo de sincronização a partir do Azure AD.
* A conta do utilizador pode ser criada manualmente num domínio gerido, e não existe em Azure AD.

Todos os utilizadores, independentemente da forma como são criados, têm as seguintes políticas de bloqueio de conta aplicadas pela política de senha padrão em Azure AD DS:

* **Duração do bloqueio da conta:** 30
* **Número de tentativas falhadas de início de são permitidas:** 5
* **Reset falha de tentativas de início de síltipo após:** 30 minutos
* **Idade máxima da palavra-passe (duração útil):** 90 dias

Com estas definições predefinidas, as contas do utilizador ficam bloqueadas durante 30 minutos se forem utilizadas cinco palavras-passe inválidas dentro de 2 minutos. As contas são desbloqueadas automaticamente após 30 minutos.

Os bloqueios de conta só ocorrem dentro do domínio gerido. As contas dos utilizadores só estão bloqueadas no Azure AD DS, e apenas devido a tentativas falhadas de entrada contra o domínio gerido. As contas de utilizador sincronizadas a partir da Azure AD ou no local não estão bloqueadas nos seus diretórios de origem, apenas em Azure AD DS.

Se tiver uma política de senha AD AZure que especifique uma idade máxima de senha superior a 90 dias, essa idade de senha é aplicada à política padrão em Azure AD DS. Pode configurar uma política de senha personalizada para definir uma idade máxima diferente de senha em Azure AD DS. Tenha cuidado se tiver uma idade máxima de senha mais curta configurada numa política de passwordS AD AD Azure do que no Azure AD ou num ambiente AD DS no local. Nesse cenário, a palavra-passe de um utilizador pode expirar em Azure AD DS antes de ser solicitada a alterar em Azure AD ou num ambiente AD DS no local.

Para as contas de utilizador criadas manualmente num domínio gerido, as seguintes definições de palavra-passe adicionais também são aplicadas a partir da política predefinida. Estas definições não se aplicam às contas de utilizador sincronizadas a partir do Azure AD, uma vez que um utilizador não pode atualizar a sua palavra-passe diretamente no Azure AD DS.

* **Comprimento mínimo da palavra-passe (caracteres):** 7
* **As palavras-passe devem satisfazer os requisitos de complexidade**

Não é possível modificar as definições de bloqueio de conta ou palavra-passe na política de senha padrão. Em vez disso, os membros do grupo *de administradores da AAD DC* podem criar políticas de senha personalizadas e configurá-la para anular (ter precedência sobre) a política incorporada padrão, como mostrado na secção seguinte.

## <a name="create-a-custom-password-policy"></a>Crie uma política de senha personalizada

À medida que constrói e executa aplicações no Azure, pode querer configurar uma política de senha personalizada. Por exemplo, poderia criar uma política para definir diferentes definições de política de bloqueio de contas.

As políticas de palavras-passe personalizadas são aplicadas a grupos num domínio gerido. Esta configuração substitui efetivamente a política predefinida.

Para criar uma política de palavra-passe personalizada, utilize as Ferramentas Administrativas do Diretório Ativo a partir de um VM de domínio. O Ative Directory Administrative Center permite-lhe visualizar, editar e criar recursos num domínio gerido, incluindo OUs.

> [!NOTE]
> Para criar uma política de palavra-passe personalizada num domínio gerido, tem de ser inscrito numa conta de utilizador que seja membro do grupo *de Administradores AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É mostrada uma lista de ferramentas de gestão disponíveis que foram instaladas no tutorial para [criar um VM de gestão.][tutorial-create-management-vm]
1. Para criar e gerir as OUs, selecione **Ative Directory Administrative Center** a partir da lista de ferramentas administrativas.
1. No painel esquerdo, escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Abra o recipiente **do Sistema** e, em seguida, o Recipiente **de Definições de Palavra-passe**.

    É mostrada uma política de palavra-passe incorporada para o domínio gerido. Não pode modificar esta política incorporada. Em vez disso, crie uma política de palavra-passe personalizada para anular a política por defeito.

    ![Criar uma política de senha no Ative Directory Administrative Center](./media/password-policy/create-password-policy-adac.png)

1. No painel **tarefas** à direita, selecione **Definições de Palavras-passe de Nova >**.
1. No diálogo **Configurações de Configurações de Palavras-Passe, insira** um nome para a política, como *MyCustomFGPP*.
1. Quando existem várias políticas de palavra-passe, a política com maior precedência, ou prioridade, é aplicada a um utilizador. Quanto menor for o número, maior é a prioridade. A política de senha padrão tem uma prioridade de *200*.

    Desaponha a precedência da sua política de senha personalizada para anular o padrão, tal como *1*.

1. Editar outras definições de política de palavra-passe conforme desejado. Lembre-se dos seguintes pontos-chave:

    * Configurações como complexidade de palavra-passe, idade ou tempo de validade apenas para os utilizadores criados manualmente num domínio gerido.
    * As definições de bloqueio de conta aplicam-se a todos os utilizadores, mas apenas fazem efeito dentro do domínio gerido e não no próprio AD Azure.

    ![Crie uma política de senha personalizada de grãos finos](./media/password-policy/custom-fgpp.png)

1. Desmarque **Proteger contra a eliminação acidental**. Se esta opção for selecionada, não poderá salvar o FGPP.
1. Na secção **Aplica diretamente para a** secção, selecione o botão **Adicionar.** No **diálogo Select Users or Groups,** selecione o botão **Localizações.**

    ![Selecione os utilizadores e grupos para aplicar a política de senha para](./media/password-policy/fgpp-applies-to.png)

1. As políticas de palavra-passe só podem ser aplicadas a grupos. No diálogo **Localizações,** expanda o nome de domínio, como *aaddscontoso.com,* selecione um OU, como **Utilizadores AADDC**. Se tiver um OU personalizado que contenha um grupo de utilizadores que deseja aplicar, selecione-o.

    ![Selecione o OU a que o grupo pertence](./media/password-policy/fgpp-container.png)

1. Digite o nome do grupo a que pretende aplicar a política e, em seguida, **selecione 'Verificar Nomes'** para validar a existência do grupo.

    ![Procure e selecione o grupo para aplicar O FGPP](./media/password-policy/fgpp-apply-group.png)

1. Com o nome do grupo selecionado agora apresentado na secção **Aplica-se diretamente,** selecione **OK** para guardar a sua política de palavra-passe personalizada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as políticas de senha e utilizar o Ative Directory Administration Center, consulte os seguintes artigos:

* [Conheça as políticas de senha de grão fino](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configure políticas de senha de grão fino usando o Centro de Administração da AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
