---
title: Configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory | Documentos da Microsoft
description: Saiba como configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4015f99e9281285c530c6ae40dbda4e97846af8
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311054"
---
# <a name="how-to-set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Como: Configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory 

Com o [acesso condicional do Azure Active Directory (Azure AD)](overview.md), pode controlar a forma como os utilizadores aceder às suas aplicações na cloud. Se pretender utilizar o acesso condicional para controlar o acesso ao SharePoint e o Exchange online, terá de:

- Verifique se o seu cenário de acesso condicional é suportado
- Impedi as aplicações de cliente ignorem a imposição de políticas de acesso condicional.   

Este artigo explica como pode solucionar ambos os casos.


## <a name="what-you-need-to-know"></a>O que precisa saber

Pode utilizar o acesso condicional do Azure AD para proteger as aplicações de cloud quando proveniente de uma tentativa de autenticação:

- Um navegador da web

- Uma aplicação de cliente que utiliza [autenticação moderna](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Algumas aplicações de cloud também suportam protocolos de autenticação. Isto aplica-se, por exemplo, ao SharePoint Online e Exchange Online. Quando uma aplicação cliente pode utilizar um protocolo de autenticação para aceder a uma aplicação da cloud, do Azure AD não pode impor uma política de acesso condicional nessa tentativa de acesso. Para impedir que uma aplicação de cliente ignorem a imposição de políticas, deve verificar se é possível permitir que apenas a autenticação moderna nas aplicações cloud afetados. 

Exemplos de cliente de acesso condicional de aplicações não é aplicável a são:

- Office 2010 e anterior

- Office 2013 quando a autenticação moderna não está ativada



 
## <a name="control-access-to-sharepoint-online"></a>Controlar o acesso ao SharePoint Online

Além de autenticação moderna, SharePoint Online também oferece suporte a protocolos de autenticação. Se os protocolos de autenticação estiverem ativados, as políticas de acesso condicional para o SharePoint não são impostas para clientes que não utilizam autenticação moderna.

Pode desativar os protocolos de autenticação para acesso do SharePoint com o **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** cmdlet: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Controlar o acesso ao Exchange Online

Quando configurar políticas de acesso condicional para o Exchange Online, terá de rever o seguinte:

- Exchange ActiveSync

- Protocolos de autenticação



### <a name="exchange-activesync"></a>Exchange ActiveSync

Embora o Exchange Active Sync suporta a autenticação moderna, existem algumas limitações sobre o suporte para cenários de acesso condicional:

- Quando seleciona **os clientes do Exchange Active Sync** na sua política, não é possível configurar outras condições.  

    ![Plataformas de dispositivos](./media/conditional-access-for-exo-and-spo/05.png)

- Definir o requisito de autenticação multifator não é suportada  

    ![Acesso condicional](./media/conditional-access-for-exo-and-spo/01.png)

Para proteger efetivamente o acesso ao Exchange Online do Exchange ActiveSync, pode:

- Configure uma política de acesso condicional suportados através dos seguintes passos:

    a. Selecione apenas **Office 365 Exchange Online** como a aplicação na cloud.  

    ![Acesso condicional](./media/conditional-access-for-exo-and-spo/04.png)

    b. Selecione **Exchange Active Sync** como **aplicação de cliente**.  

    ![Plataformas de dispositivos](./media/conditional-access-for-exo-and-spo/03.png)

- Bloquear o Exchange ActiveSync através de regras de serviços de Federação do Active Directory (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação

Além de autenticação moderna, Exchange Online também oferece suporte a protocolos de autenticação. Se os protocolos de autenticação estiverem ativados, as políticas de acesso condicional para o Exchange Online não são impostas para clientes que não utilizam autenticação moderna.

Pode desativar os protocolos de autenticação para o Exchange Online através da definição de regras do AD FS. Este bloqueia o acesso de:

- Clientes com Office antigos, como o Office 2013 que não tenham autenticação moderna ativada 

- Versões anteriores do Office


## <a name="set-up-ad-fs-rules"></a>Configurar regras de AD FS

Pode utilizar as seguintes regras de autorização de emissão para permitir ou bloquear o tráfego ao nível do AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Bloquear o tráfego herdado da extranet

Aplicando as três regras seguintes: 

- Ativar o acesso:
    - Tráfego do Exchange ActiveSync
    - Tráfego de browser
    - Tráfego de autenticação moderna
- Bloquear o acesso: 
    - Aplicações de cliente legado na extranet

**Regra 1:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regra 2:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regra 3:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Bloquear o tráfego herdado de qualquer lugar

Aplicando as três regras seguintes:

- Ativar o acesso: 
    - Tráfego do Exchange ActiveSync
    - Tráfego de browser
    - Tráfego de autenticação moderna
- Bloquear o acesso: 
    - Aplicações legadas partir de qualquer local

##### <a name="rule-1"></a>Regra 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regra 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regra 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [o que é o acesso condicional no Azure Active Directory](overview.md).

Para obter instruções sobre como configurar as regras de afirmação, consulte [configurar as regras de afirmação](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






