---
title: 'Azure Active Directory Domain Services: Ativar o suporte para o serviço de perfil de usuário do SharePoint | Documentos da Microsoft'
description: Configurar domínios geridos do Azure Active Directory Domain Services para suportar a sincronização de perfil para o SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
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
ms.author: ergreenl
ms.openlocfilehash: deef9b317f394213eabb5ce0ce31dd294bc0dfd1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155430"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Configurar um domínio gerido para oferecer suporte a sincronização de perfil para o SharePoint Server
SharePoint Server inclui um serviço de perfil de utilizador que é utilizada para sincronização do perfil de utilizador. Para configurar o serviço de perfil do usuário, é necessário que as permissões adequadas concedido a um domínio do Active Directory. Para obter mais informações, consulte [conceder permissões de serviços de domínio do Active Directory para a sincronização do perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Este artigo explica como configurar domínios geridos do Azure AD Domain Services para implementar o serviço de sincronização de perfil de usuário do SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>O grupo de "Contas de serviço do AAD DC"
Um grupo de segurança chamado '**contas de serviço de controlador de domínio do AAD**' está disponível na unidade organizacional "Utilizadores" no seu domínio gerido. Pode ver este grupo no **Active Directory Users and Computers** MMC snap-in no seu domínio gerido.

![Grupo de segurança de contas de serviço de controlador de domínio do AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Os membros deste grupo de segurança são delegados os seguintes privilégios:
- O privilégio "Replicar alterações do diretório" na raiz DSE do domínio gerido.
- O privilégio "Replicar alterações do diretório" no contexto de nomenclatura de configuração (cn = o contentor de configuração) do domínio gerido.

Este grupo de segurança também é um membro do grupo interno **acesso de versões anteriores ao Windows 2000**.

![Grupo de segurança de contas de serviço de controlador de domínio do AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Ativar o seu domínio gerido oferecer suporte a sincronização de perfil de usuário do SharePoint Server
Pode adicionar a conta de serviço utilizada para sincronização de perfil de utilizador de SharePoint para o **contas de serviço de controlador de domínio do AAD** grupo. Como resultado, a conta de sincronização obtém os privilégios adequados para replicar as alterações para o diretório. Este passo de configuração permite que a sincronização de perfil de usuário do SharePoint Server funcione corretamente.

![Contas de serviço do controlador de domínio do AAD - adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Contas de serviço do controlador de domínio do AAD - adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Conteúdo relacionado
* [Referência técnica - serviços de domínio do Active Directory conceder as permissões para sincronização do perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
