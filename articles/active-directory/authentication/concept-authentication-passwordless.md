---
title: Azure Active Directory conexão sem senha (versão prévia)
description: Entrada sem senha no Azure AD usando as chaves de segurança do FIDO2 ou o aplicativo Microsoft Authenticator (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: e073b4ff366c05cdf429f81d46647cd330604057
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081584"
---
# <a name="passwordless-authentication-options"></a>Opções de autenticação com senha

A autenticação multifator (MFA) é uma ótima maneira de proteger sua organização, mas os usuários ficam frustrados com a camada adicional sobre a existência de se lembrar de suas senhas. Os métodos de autenticação sem senha são mais convenientes, pois a senha é removida e substituída por algo que você tem algo ou algo que você sabe.

|   | Algo que você tem | Algo que você está ou conhece |
| --- | --- | --- |
| Sem palavra-passe | Dispositivo, telefone ou chave de segurança do Windows 10 | Biometria ou PIN |

Cada organização tem necessidades diferentes quando se trata de autenticação. A Microsoft oferece três opções de autenticação com senha:

- Windows Hello para empresas
- Aplicação Microsoft Authenticator
- Chaves de segurança do FIDO2

![Autenticação: segurança versus conveniência](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello para empresas

O Windows Hello para empresas é ideal para os operadores de informações que têm seu próprio computador Windows designado. A biométrica e o PIN estão diretamente ligados ao computador do usuário, o que impede o acesso de qualquer pessoa que não seja o proprietário. Com a integração PKI e o suporte interno para SSO (logon único), o Windows Hello para empresas fornece um método simples e conveniente para acessar diretamente os recursos corporativos locais e na nuvem.

O [Guia de planejamento](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello para empresas pode ser usado para ajudá-lo a tomar decisões sobre o tipo de implantação do Windows Hello para empresas e as opções que você precisará considerar.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator aplicativo

Permitir que o telefone do funcionário se torne um método de autenticação com senha. Talvez você já esteja usando o aplicativo Microsoft Authenticator como uma opção de autenticação multifator conveniente, além de uma senha. Mas agora ele está disponível como uma opção sem senha.

![Entrar no Microsoft Edge com o aplicativo Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Ele transforma qualquer telefone iOS ou Android em uma credencial forte e sem senha, permitindo que os usuários entrem em qualquer plataforma ou navegador, obtendo uma notificação para seu telefone, correspondendo a um número exibido na tela para aquele em seu telefone e usando suas biométricas ( toque ou face) ou PIN para confirmar.

## <a name="fido2-security-keys"></a>Chaves de segurança do FIDO2

As chaves de segurança FIDO2 são um método de autenticação de senha com base em padrões não Phish que pode ser fornecido em qualquer fator forma. A FIDO (Fast Identity online) é um padrão aberto para autenticação com senha. Ele permite que usuários e organizações aproveitem o padrão para entrar em seus recursos sem nome de usuário ou senha usando uma chave de segurança externa ou uma chave de plataforma incorporada a um dispositivo.

Para a visualização pública, os funcionários podem usar as chaves de segurança para entrar em seus dispositivos Windows 10 ingressados no Azure AD e obter logon único em seus recursos locais e de nuvem. Eles também podem entrar em navegadores com suporte.

![Entrar no Microsoft Edge com uma chave de segurança](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Embora existam muitas chaves que são FIDO2 certificadas pela Aliança de FIDO, a Microsoft exige que algumas extensões opcionais da especificação CTAP (FIDO2 Client-to-Authenticator Protocol) sejam implementadas pelo fornecedor para garantir a segurança máxima e a melhor ocorrer.

Uma chave de segurança **deve** implementar os seguintes recursos e extensões do protocolo FIDO2 CTAP para ser compatível com a Microsoft:

| # | Confiança de recurso/extensão | Por que esse recurso ou extensão é necessário? |
| --- | --- | --- |
| 1 | Chave residente | Esse recurso permite que a chave de segurança seja portátil, em que sua credencial é armazenada na chave de segurança. |
| 2 | PIN do cliente | Esse recurso permite que você proteja suas credenciais com um segundo fator e se aplica a chaves de segurança que não têm uma interface do usuário. |
| 3 | HMAC-segredo | Essa extensão garante que você possa entrar em seu dispositivo quando ele estiver offline ou no modo avião. |
| 4 | Várias contas por RP | Esse recurso garante que você possa usar a mesma chave de segurança em vários serviços, como conta da Microsoft e Azure Active Directory. |

Os provedores a seguir oferecem chaves de segurança FIDO2 de fatores forma diferentes que são conhecidos como compatíveis com a experiência sem senha. A Microsoft incentiva os clientes a avaliar as propriedades de segurança dessas chaves contatando o fornecedor, bem como a FIDO Alliance.

| Fornecedor | Contacto |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Se você comprar e planejar usar as chaves de segurança com base em NFC, precisará de um leitor NFC com suporte.

Se você for um fornecedor e quiser obter seu dispositivo nesta lista, entre em contato com [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

As chaves de segurança do FIDO2 são uma ótima opção para empresas que são muito sensíveis à segurança ou que têm cenários ou funcionários que não estão dispostos ou podem usar seus telefones como um segundo fator.

## <a name="what-scenarios-work-with-the-preview"></a>Quais cenários funcionam com a versão prévia?

- Os administradores podem habilitar métodos de autenticação com senha para seus locatários
- Os administradores podem direcionar todos os usuários ou Selecionar usuários/grupos dentro de seu locatário para cada método
- Os usuários finais podem registrar e gerenciar esses métodos de autenticação com senha em seu portal de conta
- Os usuários finais podem entrar com esses métodos de autenticação sem senha
   - Microsoft Authenticator aplicativo: funcionará em cenários em que a autenticação do Azure AD é usada, inclusive em todos os navegadores, durante a instalação do OOBE (Windows 10) e com aplicativos móveis integrados em qualquer sistema operacional.
   - Chaves de segurança: funcionarão na tela de bloqueio para o Windows 10 e a Web em navegadores com suporte, como o Microsoft Edge.

## <a name="next-steps"></a>Passos seguintes

[Habilitar as opções de passwordlesss de chave de segurança do FIDO2 em sua organização](howto-authentication-passwordless-security-key.md)

[Habilitar opções com senha baseada em telefone em sua organização](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Links externos

[FIDO Alliance](https://fidoalliance.org/)

[Especificação CTAP FIDO2](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
