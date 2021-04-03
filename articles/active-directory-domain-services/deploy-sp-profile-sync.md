---
title: Ativar o serviço de perfil de utilizador SharePoint com Azure AD DS | Microsoft Docs
description: Saiba como configurar um Azure Ative Directory Domain Services gerido domínio para suportar a sincronização de perfis para o SharePoint Server
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8fbde10ccf5a7f083f5fbddaadd6668d880a1fac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619832"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Configure Azure Ative Directory Domain Services para suportar a sincronização do perfil do utilizador para o SharePoint Server

O SharePoint Server inclui um serviço para sincronizar os perfis dos utilizadores. Esta funcionalidade permite que os perfis do utilizador sejam armazenados numa localização central e acessíveis em vários sites e quintas sharePoint. Para configurar o serviço de perfil de utilizador do SharePoint Server, as permissões apropriadas devem ser concedidas num domínio gerido por Azure Ative Directory Domain Services (Azure AD DS). Para obter mais informações, consulte [a sincronização do perfil do utilizador no SharePoint Server](/SharePoint/administration/user-profile-service-administration).

Este artigo mostra-lhe como configurar Azure AD DS para permitir o serviço de sincronização de perfil de utilizador do SharePoint Server.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Um VM de gestão de servidores do Windows que se junta ao domínio gerido Azure AD DS.
    * Se necessário, complete o tutorial para [criar um VM de gestão.][tutorial-create-management-vm]
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.
* Uma conta de serviço SharePoint para o serviço de sincronização do perfil do utilizador.
    * Se necessário, consulte [Plano para contas administrativas e de serviço no SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Visão geral das contas de serviço

Num domínio gerido, existe um grupo de segurança chamado *AAD DC Service Accounts* como parte da unidade organizacional dos *Utilizadores* (OU). Os membros deste grupo de segurança são delegados os seguintes privilégios:

- Replicar o privilégio **de Alterações** de Diretório na raiz DSE.
- Replicar o privilégio **de alterações** no contexto de nomeação *de configuração* `cn=configuration` (recipiente).

O grupo de segurança *de contas de serviço AAD DC* é também membro do grupo integrado *Pré-Windows 2000 Compatível Access*.

Quando adicionado a este grupo de segurança, a conta de serviço para o serviço de sincronização do perfil do utilizador do SharePoint Server é concedida aos privilégios necessários para funcionar corretamente.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Ativar o suporte para sincronização de perfil de utilizador do SharePoint Server

A conta de serviço do SharePoint Server necessita de privilégios adequados para replicar alterações no diretório e deixar o perfil do utilizador do SharePoint Server funcionar corretamente. Para fornecer estes privilégios, adicione a conta de serviço utilizada para a sincronização do perfil do utilizador SharePoint ao grupo *de Contas de Serviço AAD DC.*

A partir da sua VM de gestão AZure AD, complete os seguintes passos:

> [!NOTE]
> Para editar a filiação em grupo num domínio gerido, tem de ser inscrito numa conta de utilizador que seja membro do grupo *de Administradores AAD DC.*

1. A partir do ecrã Iniciar, selecione **Ferramentas Administrativas**. É mostrada uma lista de ferramentas de gestão disponíveis que foram instaladas no tutorial para [criar um VM de gestão.][tutorial-create-management-vm]
1. Para gerir a adesão ao grupo, selecione **Ative Directory Administrative Center** a partir da lista de ferramentas administrativas.
1. No painel esquerdo, escolha o seu domínio gerido, como *aaddscontoso.com*. É apresentada uma lista das OUs e dos recursos existentes.
1. Selecione o **Grupo** de Segurança das *Contas de Serviço AAD DC.*
1. Selecione **Membros,** em seguida, escolha **Adicionar...**.
1. Introduza o nome da conta de serviço SharePoint e, em seguida, selecione **OK**. No exemplo seguinte, a conta de serviço SharePoint é nomeada *spadmin:*

    ![Adicione a conta de serviço SharePoint ao grupo de segurança de contas de serviço AAD DC](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [Gerir a sincronização do perfil do utilizador no SharePoint Server](/SharePoint/administration/manage-profile-synchronization).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts