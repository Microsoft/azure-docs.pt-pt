---
title: Servidor MFA do Azure ou o serviço, no local ou na cloud?
description: Como num administrador do Azure AD, eu precise saber qual versão do MFA, deve implementar?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: edfc26355933db549b735c4baf9744744f46734c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870478"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>Qual é a versão do MFA do Azure é adequada para a minha organização?

Antes de pode decidir onde e como implementar o multi-factor Authentication (MFA), terá de responder a perguntas de básicas três.

* [O que estou a tentar proteger](#what-am-i-trying-to-secure)
* [Onde estão localizados os utilizadores](#where-are-the-users-located)
* [Que funcionalidades preciso?](#what-features-do-i-need)

Cada uma das seções a seguir fornece detalhes para o ajudar a responder às perguntas anteriores.

## <a name="what-am-i-trying-to-secure"></a>O que estou a tentar proteger?

Para determinar a solução de verificação de dois passos correta, primeiro tem de responder à pergunta sobre o que está a tentar proteger com um fator adicional de autenticação. É uma aplicação que está no Azure? Ou um sistema de acesso remoto? Ao determinar o que está a tentar proteger, pode responder à pergunta sobre onde a multi-factor Authentication tem de ser ativada.

| O que está a tentar proteger | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Aplicações Microsoft originais |● |● |
| Aplicações SaaS na galeria de aplicações |● |  |
| Aplicações Web publicadas através do Proxy de Aplicação do Azure AD |● |  |
| Aplicações IIS não publicadas através do Proxy de Aplicação do Azure AD | |● |
| Acesso remoto como VPN, RDG usando a extensão NPS ou um servidor NPS existente | ● | ● |

## <a name="where-are-the-users-located"></a>Onde estão localizados os utilizadores

Em seguida, determine onde os utilizadores da sua organização estão localizadas ajuda a determinar a solução correta a utilizar, seja na cloud ou no local com o servidor MFA.

| Localização do Utilizador | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD e AD no local utilizando federação com o AD FS |● |● |
| O Azure AD e AD com o Azure AD Connect - sem sincronização de hash de palavra-passe ou a autenticação pass-through no local |● |● |
| O Azure AD e AD com o Azure AD Connect - com autenticação de sincronização ou pass-through de hash de palavra-passe no local |● | |
| Active Directory no local | |● |

## <a name="what-features-do-i-need"></a>Que funcionalidades preciso?

A tabela seguinte compara as funcionalidades disponíveis do Multi-Factor Authentication na nuvem e do Servidor Multi-Factor Authentication.

| Funcionalidade | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Notificação da aplicação móvel como um segundo fator | ● | ● |
| Código de verificação da aplicação móvel como um segundo fator | ● | ● |
| Chamada telefónica como segundo fator | ● | ● |
| SMS unidirecional como segundo fator | ● | ● |
| Tokens de Hardware como segundo fator | ● (pré-visualização pública) | ● |
| Palavras-passe da aplicação para os clientes do Office 365 que não suportam MFA | ● | |
| Controlo de administração sobre métodos de autenticação | ● | ● |
| Modo PIN | | ● |
| Alerta de fraudes | ● | ● |
| Relatórios do MFA | ● | ● |
| Omissão de Uso Individual | | ● |
| Saudações personalizadas para chamadas telefónicas | ● | ● |
| ID do autor da chamada personalizável para chamadas telefónicas | ● | ● |
| IPs Fidedignos | ● | ● |
| Memorizar MFA para dispositivos fidedignos | ● | |
| Acesso condicional | ● | ● |
| Cache |  | ● |

## <a name="next-steps"></a>Passos Seguintes

Agora que compreende a diferença entre o Azure Multi-factor Authentication na cloud ou o Servidor MFA no local, está na altura de configurar e utilizar o Azure Multi-factor Authentication. **Selecione o ícone que representa o seu cenário**

<center>

[![MFA na cloud](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![Servidor MFA](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
