---
title: O Azure Active Directory passe início de sessão (pré-visualização)
description: Início de sessão passe no Azure AD com chaves de segurança de FIDO2 ou a aplicação Microsoft Authenticator (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712088"
---
# <a name="what-is-passwordless"></a>O que é passe?

Autenticação multifator (MFA) é uma excelente forma de proteger a sua organização, mas os utilizadores obtêm frustrados com a camada adicional sobre ter de memorizar as palavras-passe. Métodos de autenticação de início são mais convenientes, porque a palavra-passe foi removida e substituída com algo que tem mais alguma coisa que está ou algo que conhece.

|   | Algo que tem | Algo que é ou o que saber |
| --- | --- | --- |
| Sem palavra-passe | Chave de telefone ou de segurança | Biometria ou PIN |

Reconhecemos que cada organização tem necessidades diferentes quando se trata de autenticação. Microsoft oferece atualmente Windows Hello, nossa experiência de início premier para o PC do Windows. Estamos a adicionar novas credenciais para a família de início: Aplicação Microsoft Authenticator e FIDO2 chaves de segurança.

## <a name="microsoft-authenticator-app"></a>Aplicação Microsoft Authenticator

Permitir que o telefone dos seus funcionários para se tornar um método de autenticação de início. Pode já estar a utilizar a aplicação Microsoft Authenticator como uma opção conveniente multi-factor authentication, além de uma palavra-passe. Mas agora, está disponível como uma opção de início.

Ela se transformará qualquer iOS ou Android telefone numa credencial forte, passe ao permitir que os utilizadores iniciem sessão para qualquer plataforma ou browser ao obter uma notificação para o telemóvel, que correspondem um número apresentado no ecrã para um no seu telefone e, em seguida, usando seus biométrica ( Touch ou enfrentam) ou um PIN para confirmar.

## <a name="fido2-security-keys"></a>Chaves de segurança de FIDO2

Chaves de segurança de FIDO2 são um método de autenticação de início e baseada em padrões de unphishable que pode ser qualquer fator forma. Rápido identidade Online (FIDO) é um padrão aberto para a autenticação de início. Ele permite que os utilizadores e organizações tirem partido o padrão iniciar sessão nos seus recursos sem um nome de utilizador ou palavra-passe com uma chave de segurança externa ou uma chave de plataforma num dispositivo.

Para a pré-visualização pública, os funcionários podem utilizar chaves de segurança externa para iniciar sessão em suas máquinas do Azure Active Directory associado ao Windows 10 (com a versão 1809 ou superior) e obter início de sessão único para os recursos na nuvem. Também pode iniciar sessão para browsers suportados.

Embora haja várias chaves que são FIDO2 certificada da Alliance FIDO, a Microsoft requer algumas extensões opcionais da especificação FIDO2 CTAP para ser implementada pelo fornecedor para garantir o máximo de segurança e a melhor experiência.

Uma chave de segurança **tem** implementar as seguintes funcionalidades e extensões do protocolo de FIDO2 CTAP para ser compatível com a Microsoft:

| # | Funcionalidade / extensão de confiança | Por que motivo é isso necessário? |
| --- | --- | --- |
| 1 | Chave residente | Esta funcionalidade permite que a chave de segurança seja portátil, onde a sua credencial é armazenado na chave de segurança. |
| 2 | Pin de cliente | Esta funcionalidade permite-lhe proteger as suas credenciais com um segundo fator e aplica a chaves de segurança que não têm uma interface do usuário. |
| 3 | HMAC-secret | Esta extensão garante que pode iniciar sessão no seu dispositivo quando está offline ou no modo de avião. |
| 4 | Várias contas por RP | Esta funcionalidade garante que pode usar a mesma chave de segurança em vários serviços, como Microsoft Account e Azure Active Directory. |

Os seguintes fornecedores oferecem FIDO2 chaves de segurança de diferentes fatores de forma que são conhecidos por serem compatíveis com a experiência de paswordless. A Microsoft incentiva os clientes a avaliar as propriedades de segurança dessas chaves ao contactar o fornecedor, bem como FIDO Alliance.

| Fornecedor | Contacto |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Se for um fornecedor e pretender obter o seu dispositivo nesta lista, contacte [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com).

Chaves de segurança de FIDO2 são uma ótima opção para empresas que são de segurança muita confidencial ou ter cenários ou funcionários que não são dispostos ou pode utilizar o seu telefone como um segundo fator.

## <a name="what-scenarios-work-with-the-preview"></a>O que cenários trabalham com a pré-visualização?

1. Os administradores podem ativar métodos de autenticação de início para o respetivo inquilino
1. Os administradores podem todos os utilizadores de destino ou selecione os utilizadores/grupos no respetivo inquilino para cada método
1. Os utilizadores finais podem se registrar e gerir estes métodos de autenticação de início no seu portal de contas
1. Os utilizadores finais podem iniciar a sessão com estes métodos de autenticação de início
   1. Aplicação de autenticador da Microsoft: Será trabalho em todos os cenários em que é utilizada a autenticação do Azure AD, incluindo todos os browsers, durante o Windows 10 Out do programa de configuração de caixa de configuração inicial (OOBE) e com integrado aplicações móveis em qualquer sistema operativo.
   1. Chaves de segurança: Funcionará no ecrã de bloqueio para Windows 10 versão 1809 ou superior e a web em browsers suportados, como o Microsoft Edge.

## <a name="next-steps"></a>Passos Seguintes

[Ativar opções de início da sua organização](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Ligações externas

[FIDO Alliance](https://fidoalliance.org/)

[Especificação de FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
