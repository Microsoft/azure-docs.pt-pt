---
title: 'Azure Active Directory Domain Services: Habilitar o serviço de perfil de usuário do SharePoint | Microsoft Docs'
description: Configurar Azure Active Directory Domain Services domínios gerenciados para dar suporte à sincronização de perfil para o SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4a9ee05b37a69927d70269dccef2b74a2c251722
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234109"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Configurar um domínio gerenciado para dar suporte à sincronização de perfil para o SharePoint Server
O SharePoint Server inclui um serviço de perfil de usuário que é usado para sincronização de perfil de usuário. Para configurar o serviço de perfil de usuário, as permissões apropriadas precisam ser concedidas em um domínio Active Directory. Para obter mais informações, consulte [conceder Active Directory Domain Services permissões para a sincronização de perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Este artigo explica como você pode configurar Azure AD Domain Services domínios gerenciados para implantar o serviço de sincronização de perfil de usuário do SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>O grupo ' contas de serviço de controlador de domínio do AAD '
Um grupo de segurança chamado "**contas de serviço do controlador de domínio do AAD**" está disponível na unidade organizacional "usuários" em seu domínio gerenciado. Você pode ver esse grupo no snap-in **Active Directory usuários e computadores** do MMC em seu domínio gerenciado.

![Grupo de segurança contas de serviço do AAD DC](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Os membros desse grupo de segurança delegam os seguintes privilégios:
- O privilégio "replicar alterações de diretório" no DSE raiz do domínio gerenciado.
- O privilégio "replicar alterações de diretório" no contexto de nomenclatura de configuração (CN = Contêiner de configuração) do domínio gerenciado.

Esse grupo de segurança também é membro do grupo interno **acesso compatível com anterior ao Windows 2000**.

![Grupo de segurança contas de serviço do AAD DC](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Habilitar seu domínio gerenciado para dar suporte à sincronização de perfil de usuário do SharePoint Server
Você pode adicionar a conta de serviço usada para sincronização de perfil de usuário do SharePoint ao grupo de **contas de serviço do AAD DC** . Como resultado, a conta de sincronização obtém os privilégios adequados para replicar as alterações no diretório. Esta etapa de configuração permite que a sincronização de perfil de usuário do SharePoint Server funcione corretamente.

![Contas de serviço de DC do AAD – adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Contas de serviço de DC do AAD – adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Conteúdo relacionado
* [Referência técnica-conceder permissões de Active Directory Domain Services para a sincronização de perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
