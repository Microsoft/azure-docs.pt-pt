---
title: Privacidade do utilizador e o Azure AD totalmente integrado início de sessão único | Documentos da Microsoft
description: Este artigo trata da conformidade SSO totalmente integrado do Azure Active Directory (Azure AD) e do GDPR.
services: active-directory
keywords: o que é o Azure AD Connect, com o GDPR, necessário componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58791555"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Privacidade do utilizador e do Azure AD totalmente integrada início de sessão único

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Descrição geral


O Azure AD Seamless SSO cria o tipo de registo seguinte, que pode conter dados pessoais: 

- Ficheiros de registo de rastreio do Azure AD Connect.

Melhore a privacidade dos utilizadores para SSO totalmente integrado de duas formas:

1.  Mediante solicitação, extrair dados para uma pessoa e remover dados de que a pessoa das instalações.
2.  Certifique-se de que nenhum dado é mantido para além de 48 horas.

Recomendamos vivamente a segunda opção, pois é mais fácil de implementar e manter. Veja a seguir instruções para cada tipo de registo:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminar ficheiros de registo de rastreio do Azure AD Connect

Verifique o conteúdo do **%ProgramData%\AADConnect** conteúdo de registo de pasta e eliminar o rastreio (**rastreio -\*. log** ficheiros) dessa pasta dentro de 48 horas de instalar ou atualizar o Azure AD Connect ou modificar a configuração do SSO totalmente integrado, como esta ação pode criar dados cobertos pelo GDPR.

>[!IMPORTANT]
>Não elimine o **PersistedState.xml** ficheiros nesta pasta, como este ficheiro é utilizado para manter o estado da instalação anterior do Azure AD Connect e é utilizado quando uma instalação de atualização é feita. Este ficheiro nunca irá conter todos os dados sobre uma pessoa e nunca deve ser eliminado.

Pode rever e eliminar estes ficheiros de registo de rastreio com o Explorador do Windows ou pode utilizar o seguinte script do PowerShell para efetuar as ações necessárias:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde o script num arquivo com o ". PS1 "extensão. Execute este script conforme necessário.

Para saber mais sobre relacionados com os requisitos do Azure AD Connect com o GDPR, consulte [este artigo](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Tenha em atenção sobre os registos de controlador de domínio

Se o registo de auditoria é ativado, este produto pode gerar registos de segurança para os controladores de domínio. Para saber mais sobre como configurar políticas de auditoria, leia isto [artigo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Passos Seguintes

* [Rever a política de Privacy da Microsoft no Centro de fidedignidade](https://www.microsoft.com/trustcenter)
  - [**Resolução de problemas** ](tshoot-connect-sso.md) -Saiba como resolver problemas comuns com a funcionalidade.
  - [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para preenchimento de pedidos de novas funcionalidades.