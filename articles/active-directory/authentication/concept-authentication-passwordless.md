---
title: Entrada sem palavras-passe do Diretório Ativo Azure (pré-visualização)
description: Saiba mais sobre as opções de entrada sem palavras no Azure Ative Directory utilizando chaves de segurança FIDO2 ou a aplicação Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba579d6da8c759a4653b729f1a471efdedc2baa7
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505761"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opções de autenticação sem palavras-passe para Diretório Ativo Azure

A autenticação multi-factor (MFA) é uma ótima forma de proteger a sua organização, mas os utilizadores muitas vezes ficam frustrados com a camada de segurança adicional para além de terem de se lembrar das suas palavras-passe. Os métodos de autenticação sem palavras-passe são mais convenientes porque a palavra-passe é removida e substituída por algo que tem, além de algo que é ou algo que sabe.

|   | Algo que tenha. | Algo que éou ou sabe |
| --- | --- | --- |
| Sem palavra-passe | Dispositivo, telefone ou chave de segurança do Windows 10 | Biométrico ou PIN |

Cada organização tem necessidades diferentes quando se trata de autenticação. A Microsoft oferece as seguintes três opções de autenticação sem palavras-passe:

- Windows Hello para empresas
- Aplicação Microsoft Authenticator
- Chaves de segurança FIDO2

![Autenticação: Segurança versus conveniência](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello para empresas

O Windows Hello for Business é ideal para trabalhadores de informação que tenham o seu próprio PC windows designado. O biométrico e o PIN estão diretamente ligados ao PC do utilizador, o que impede o acesso de qualquer outra pessoa que não seja o proprietário. Com a integração de infraestruturas de chaves públicas (PKI) e suporte integrado para um único sign-on (SSO), o Windows Hello for Business fornece um método conveniente para aceder sem problemas aos recursos corporativos no local e na nuvem.

O guia de [planeamento](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello for Business pode ser usado para ajudá-lo a tomar decisões sobre o tipo de implementação do Windows Hello for Business e as opções que terá de considerar.

## <a name="microsoft-authenticator-app"></a>Aplicação autenticadora da Microsoft

Permita que o telemóvel do seu empregado se torne um método de autenticação sem palavras-passe. Pode já estar a utilizar a Aplicação Autenticadora microsoft como uma conveniente opção de autenticação de vários fatores, além de uma palavra-passe. Também pode utilizar a App Autenticador como uma opção sem palavras-passe.

![Inscreva-se no Microsoft Edge com a aplicação Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

A Aplicação Autenticadora transforma qualquer iOS ou telefone Android numa credencial forte e sem palavras-passe. Os utilizadores podem iniciar sessão em qualquer plataforma ou navegador, obtendo uma notificação para o seu telemóvel, correspondendo a um número apresentado no ecrã com o do seu telemóvel e, em seguida, usando o seu biométrico (toque ou rosto) ou PIN para confirmar.

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

As chaves de segurança FIDO2 são um método de autenticação sem palavras-passe baseado em padrões imphishable que pode vir em qualquer fator de forma. Fast Identity Online (FIDO) é um padrão aberto para autenticação sem palavras-passe. O FIDO permite que os utilizadores e organizações aproveitem a norma para iniciar sessão nos seus recursos sem um nome de utilizador ou palavra-passe utilizando uma chave de segurança externa ou uma chave de plataforma incorporada num dispositivo.

Para pré-visualização pública, os colaboradores podem usar chaves de segurança para iniciar sessão nos seus dispositivos Azure AD ou Azure Hybrid adesacadas e obter um único sinal sobre os seus recursos de nuvem e no local. Os utilizadores também podem iniciar sessão em navegadores suportados. As chaves de segurança FIDO2 são uma ótima opção para empresas que são muito sensíveis à segurança ou têm cenários ou funcionários que não estejam dispostos ou capazes de usar o seu telefone como um segundo fator.

![Inscreva-se no Microsoft Edge com uma chave de segurança](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Embora existam muitas chaves que são certificadas pelo FIDO2 certificadas pela FIDO Alliance, a Microsoft exige que algumas extensões opcionais da especificação fido2 Cliente-a-Autenticador (CTAP) sejam implementadas pelo fornecedor para garantir a máxima segurança e o melhor experiência.

Uma chave de segurança **DEVE** implementar as seguintes funcionalidades e extensões do protocolo FIDO2 CTAP para ser compatível com a Microsoft:

| # | Recurso / Confiança de extensão | Porque é que esta funcionalidade ou extensão é necessária? |
| --- | --- | --- |
| 1 | Chave residente | Esta função permite que a chave de segurança seja portátil, onde a sua credencial está armazenada na chave de segurança. |
| 2 | Pin o cliente | Esta funcionalidade permite proteger as suas credenciais com um segundo fator e aplica-se a chaves de segurança que não possuam interface de utilizador. |
| 3 | hmac-secreto | Esta extensão garante que pode iniciar sessão no seu dispositivo quando estiver fora de linha ou em modo avião. |
| 4 | Múltiplas contas por RP | Esta funcionalidade garante que pode utilizar a mesma chave de segurança em vários serviços como a Microsoft Account e o Azure Ative Directory. |

Os seguintes fornecedores oferecem chaves de segurança FIDO2 de diferentes fatores de forma que são conhecidos como compatíveis com a experiência sem palavras-passe. Encorajamo-lo a avaliar as propriedades de segurança destas chaves contactando o fornecedor, bem como a FIDO Alliance.

| Fornecedor | Contacto |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitiano | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| ESCONDIDO | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Garantia | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Se comprar e planear utilizar chaves de segurança baseadas em NFC, precisa de um leitor NFC suportado para a chave de segurança. O leitor nfc não é um requisito ou limitação Azure. Consulte o fornecedor para obter a sua chave de segurança baseada em NFC para obter uma lista de leitores de NFC suportados.

Se for um fornecedor e quiser colocar o seu dispositivo nesta lista de dispositivos suportados, contacte [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

## <a name="what-scenarios-work-with-the-preview"></a>Que cenários funcionam com a pré-visualização?

- Os administradores podem ativar métodos de autenticação sem palavras-passe para o seu inquilino
- Os administradores podem direcionar todos os utilizadores ou selecionar utilizadores/grupos dentro do seu inquilino para cada método
- Os utilizadores finais podem registar e gerir estes métodos de autenticação sem palavras-passe no seu portal de conta
- Os utilizadores finais podem iniciar sessão com estes métodos de autenticação sem palavras-passe
   - Aplicação do Autenticador Microsoft: Funciona em cenários onde é utilizada a autenticação AD Azure, incluindo em todos os navegadores, durante a configuração do Windows 10 out Of Box (OOBE) e com aplicações móveis integradas em qualquer sistema operativo.
   - Teclas de segurança: Trabalhe no ecrã de bloqueio para o Windows 10 e na web em navegadores suportados como o Microsoft Edge.

## <a name="next-steps"></a>Passos seguintes

[Ativar opções de passwords de segurança FIDO2 na sua organização](howto-authentication-passwordless-security-key.md)

[Ativar opções sem palavras-passe baseadas em telefone na sua organização](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Ligações Externas

[Aliança FIDO](https://fidoalliance.org/)

[Especificação FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
