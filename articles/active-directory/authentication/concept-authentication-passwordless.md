---
title: Azure Ative Directory sem palavras-passe
description: Saiba mais sobre opções de entrada sem palavras-passe no Azure Ative Directory utilizando chaves de segurança FIDO2 ou a aplicação Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d939207af195abf117645e17f4ff189b163244f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305536"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opções de autenticação sem palavras-passe para Azure Ative Directory

Funcionalidades como a autenticação de vários fatores (MFA) são uma ótima forma de proteger a sua organização, mas os utilizadores muitas vezes ficam frustrados com a camada de segurança adicional além de terem de se lembrar das suas palavras-passe. Os métodos de autenticação sem palavras-passe são mais convenientes porque a palavra-passe é removida e substituída por algo que tem, além de algo que é ou algo que sabe.

| Autenticação  | Algo que tem | Algo que és ou sabes |
| --- | --- | --- |
| Sem palavra-passe | Dispositivo, telefone ou chave de segurança do Windows 10 | Biométrico ou PIN |

Cada organização tem necessidades diferentes no que diz respeito à autenticação. A Microsoft oferece as seguintes três opções de autenticação sem palavras-passe que se integram com o Azure Ative Directory (Azure AD):

- Windows Hello para empresas
- Aplicação Microsoft Authenticator
- Chaves de segurança FIDO2

![Autenticação: Segurança versus conveniência](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello para empresas

O Windows Hello for Business é ideal para os trabalhadores da informação que têm o seu próprio PC Windows designado. As credenciais biométricas e PIN estão diretamente ligadas ao PC do utilizador, o que impede o acesso de qualquer pessoa que não seja o proprietário. Com a integração de infraestruturas de chaves públicas (PKI) e suporte integrado para um único sign-on (SSO), o Windows Hello for Business fornece um método conveniente para aceder perfeitamente aos recursos corporativos no local e na nuvem.

![Exemplo de um utilizador que inscreva com o Windows Hello for Business](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Os seguintes passos mostram como o processo de inscrição funciona com a Azure AD:

![Diagrama que descreve os passos envolvidos para o s-in do utilizador com o Windows Hello for Business](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Um utilizador assina no Windows utilizando gesto biométrico ou PIN. O gesto desbloqueia a chave privada Windows Hello for Business e é enviado para o fornecedor de suporte de segurança Cloud Authentication, referido como o *fornecedor Cloud AP*.
1. O fornecedor Cloud AP solicita um nó (um número arbitrário aleatório que pode ser usado apenas uma vez) a partir de Azure AD.
1. A AZure AD devolve um nó válido por 5 minutos.
1. O fornecedor Cloud AP assina o nonce usando a chave privada do utilizador e devolve o nó assinado ao Azure AD.
1. O Azure AD valida o nó assinado utilizando a chave pública registada de forma segura do utilizador contra a assinatura nonce. Após validar a assinatura, a Azure AD valida então o nonce assinado devolvido. Quando o nonce é validado, o Azure AD cria um token de atualização primária (PRT) com chave de sessão que é encriptada para a chave de transporte do dispositivo e devolve-a ao fornecedor Cloud AP.
1. O fornecedor Cloud AP recebe o PRT encriptado com a chave de sessão. Utilizando a chave de transporte privado do dispositivo, o fornecedor Cloud AP desencripta a tecla de sessão e protege a chave de sessão utilizando o Módulo de Plataforma Fidedigna do dispositivo (TPM).
1. O fornecedor Cloud AP devolve uma resposta de autenticação bem sucedida ao Windows. O utilizador pode então aceder ao Windows, bem como aplicações em nuvem e no local, sem necessidade de autenticar novamente (SSO).

O guia de [planeamento](/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello para negócios pode ser utilizado para o ajudar a tomar decisões sobre o tipo de implementação do Windows Hello para o Negócio e as opções que terá de considerar.

## <a name="microsoft-authenticator-app"></a>App autenticador microsoft

Também pode permitir que o telefone do seu colaborador se torne um método de autenticação sem palavras-passe. Pode já estar a utilizar a App autenticador microsoft como uma opção de autenticação multi-factor conveniente, além de uma palavra-passe. Também pode utilizar a App Authenticator como opção sem palavras-passe.

![Inscreva-se no Microsoft Edge com a aplicação Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

A App Authenticator transforma qualquer telefone iOS ou Android numa credencial forte e sem palavras-passe. Os utilizadores podem iniciar sposição em qualquer plataforma ou navegador através da notificação do seu telemóvel, correspondendo um número exibido no ecrã ao do telemóvel e, em seguida, utilizando o seu biométrico (toque ou rosto) ou PIN para confirmar. Consulte o [Download e instale a aplicação Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) para obter detalhes de instalação.

A autenticação sem palavras-passe utilizando a aplicação Authenticator segue o mesmo padrão básico que o Windows Hello for Business. É um pouco mais complicado, uma vez que o utilizador precisa de ser identificado para que o Azure AD possa encontrar a versão da Microsoft Authenticator App a ser utilizada:

![Diagrama que descreve os passos envolvidos para o sôm-in do utilizador com a App autenticador da Microsoft](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. O utilizador introduz o seu nome de utilizador.
1. A Azure AD deteta que o utilizador tem uma credencial forte e inicia o fluxo de Credencial Forte.
1. Uma notificação é enviada para a aplicação através do Apple Push Notification Service (APNS) em dispositivos iOS, ou através de Mensagens Cloud Firebase (FCM) em dispositivos Android.
1. O utilizador recebe a notificação push e abre a aplicação.
1. A aplicação chama Azure AD e recebe um desafio de prova de presença e nonce.
1. O utilizador completa o desafio introduzindo o seu biométrico ou PIN para desbloquear a chave privada.
1. O nonce é assinado com a chave privada e enviado de volta para a Azure AD.
1. A Azure AD realiza a validação de chaves públicas/privadas e devolve um token.

Para começar com o início do sing-in sem palavra-passe, complete o seguinte como:

> [!div class="nextstepaction"]
> [Ativar o sinal sem palavra-passe utilizando a aplicação Authenticator](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

A Fido (Fast IDentity Online) Alliance ajuda a promover normas de autenticação aberta e a reduzir o uso de palavras-passe como forma de autenticação. FIDO2 é o mais recente padrão que incorpora o padrão de autenticação web (WebAuthn).

As chaves de segurança FIDO2 são um método de autenticação sem palavras-passe sem padrões que pode vir em qualquer fator de forma. Fast Identity Online (FIDO) é um padrão aberto para a autenticação sem palavras-passe. O FIDO permite que os utilizadores e organizações aproveitem a norma para iniciar sedus nos seus recursos sem um nome de utilizador ou palavra-passe utilizando uma chave de segurança externa ou uma chave de plataforma incorporada num dispositivo.

Os utilizadores podem registar-se e, em seguida, selecionar uma chave de segurança FIDO2 na interface de entrada como principal meio de autenticação. Estas teclas de segurança FIDO2 são normalmente dispositivos USB, mas também podem utilizar Bluetooth ou NFC. Com um dispositivo de hardware que trata da autenticação, a segurança de uma conta é aumentada, uma vez que não há nenhuma senha que possa ser exposta ou adivinhada.

As chaves de segurança FIDO2 podem ser usadas para iniciar súbs no seu AD Azure ou no híbrido Azure AD que se uniu aos dispositivos Windows 10 e obter um único sinal sobre os seus recursos na nuvem e no local. Os utilizadores também podem iniciar sedução para navegadores suportados. As chaves de segurança FIDO2 são uma ótima opção para empresas que são muito sensíveis à segurança ou têm cenários ou funcionários que não estão dispostos ou capazes de usar o seu telefone como um segundo fator.

Dispomos de um documento de referência para o qual [os navegadores suportam a autenticação FIDO2 com AD AZure,](fido2-compatibility.md)bem como as melhores práticas para programadores que [pretendam apoiar fido2 auth nas aplicações que desenvolvem.](../develop/support-fido2-authentication.md)

![Inscreva-se no Microsoft Edge com uma chave de segurança](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

O processo é utilizado quando um utilizador assina com uma chave de segurança FIDO2:

![Diagrama que descreve os passos envolvidos para o sôm-in do utilizador com uma chave de segurança FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. O utilizador liga a chave de segurança FIDO2 ao computador.
2. O Windows deteta a chave de segurança FIDO2.
3. O Windows envia um pedido de autenticação.
4. A Azure AD envia um nó.
5. O utilizador completa o seu gesto para desbloquear a chave privada armazenada no enclave seguro da chave de segurança FIDO2.
6. A chave de segurança FIDO2 assina o nonce com a chave privada.
7. O pedido de token de atualização primária (PRT) com nonce assinado é enviado para Azure AD.
8. A Azure AD verifica o nó assinado usando a chave pública FIDO2.
9. A Azure AD devolve prt para permitir o acesso aos recursos no local.

Embora existam muitas chaves certificadas pelo FIDO2 pela FiDO Alliance, a Microsoft exige que algumas extensões opcionais da especificação fido2 Client-to-Authenticator Protocol (CTAP) sejam implementadas pelo fornecedor para garantir a máxima segurança e a melhor experiência.

Uma chave de segurança **DEVE** implementar as seguintes funcionalidades e extensões do protocolo FIDO2 CTAP para ser compatível com a Microsoft:

| # | Recurso / Confiança de extensão | Por que esta funcionalidade ou extensão é necessária? |
| --- | --- | --- |
| 1 | Chave residente | Esta funcionalidade permite que a chave de segurança seja portátil, onde a sua credencial está armazenada na chave de segurança. |
| 2 | Pino do cliente | Esta funcionalidade permite-lhe proteger as suas credenciais com um segundo fator e aplica-se a chaves de segurança que não possuem interface de utilizador. |
| 3 | hmac-secreto | Esta extensão garante que pode iniciar sôm no seu dispositivo quando estiver desligado ou em modo avião. |
| 4 | Várias contas por RP | Esta funcionalidade garante que pode utilizar a mesma chave de segurança em vários serviços, como a Microsoft Account e o Azure Ative Directory. |

### <a name="fido2-security-key-providers"></a>Principais fornecedores de segurança FIDO2

Os seguintes fornecedores oferecem chaves de segurança FIDO2 de diferentes fatores de forma que são conhecidos por serem compatíveis com a experiência sem palavras-passe. Encorajamo-lo a avaliar as propriedades de segurança destas chaves contactando o fornecedor, bem como a FIDO Alliance.

| Fornecedor | Contacto |
| --- | --- |
| Rio Yubico | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |
| Feitiano | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Garantia | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Soluções TrustKey | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Grupo Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Hipersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| Vincss | [https://passwordless.vincss.net](https://passwordless.vincss.net) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Token2 Suíça | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| GoTrustID Inc. | [https://www.gotrustid.com/idem-key](https://www.gotrustid.com/idem-key) |
| Estação Kensington | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |

> [!NOTE]
> Se comprar e planear usar chaves de segurança baseadas em NFC, precisa de um leitor NFC suportado para a chave de segurança. O leitor NFC não é um requisito ou limitação do Azure. Consulte o fornecedor para obter a sua chave de segurança baseada no NFC para obter uma lista de leitores NFC suportados.

Se é um fornecedor e quer colocar o seu dispositivo nesta lista de dispositivos suportados, [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) contacte.

Para começar com as chaves de segurança FIDO2, complete o seguinte como:

> [!div class="nextstepaction"]
> [Ativar sinal sem palavra-passe usando chaves de segurança FIDO2](howto-authentication-passwordless-security-key.md)

## <a name="supported-scenarios"></a>Cenários suportados

As seguintes considerações são aplicáveis:

- Os administradores podem ativar métodos de autenticação sem palavras-passe para o seu inquilino
- Os administradores podem direcionar todos os utilizadores ou selecionar utilizadores/grupos dentro do seu inquilino para cada método
- Os utilizadores finais podem registar-se e gerir estes métodos de autenticação sem palavras-passe no portal da conta
- Os utilizadores finais podem iniciar sação com estes métodos de autenticação sem palavras-passe:
   - Microsoft Authenticator App: Funciona em cenários onde a autenticação Azure AD é utilizada, incluindo em todos os navegadores, durante a configuração do Windows 10 e com aplicações móveis integradas em qualquer sistema operativo.
   - Chaves de segurança: Trabalhe no ecrã de bloqueio para o Windows 10 e na web em navegadores suportados como o Microsoft Edge (tanto o legado como o novo Edge).

## <a name="choose-a-passwordless-method"></a>Escolha um método sem palavra-passe

A escolha entre estas três opções sem palavras-passe depende da segurança, plataforma e requisitos de aplicação da sua empresa.

Aqui ficam alguns fatores a ter em conta na escolha da tecnologia sem palavras-passe da Microsoft:

||**Windows Hello para empresas**|**Iniciar sing-in sem palavra-passe com a aplicação Microsoft Authenticator**|**Chaves de segurança FIDO2**|
|:-|:-|:-|:-|
|**Pré-requisito**| Windows 10, versão 1809 ou mais tarde<br>Azure Active Directory| Aplicação Microsoft Authenticator<br>Telefone (dispositivos iOS e Android com Android 6.0 ou superior.)|Windows 10, versão 1903 ou mais tarde<br>Azure Active Directory|
|**Modo**|Plataforma|Software|Hardware|
|**Sistemas e dispositivos**|PC com um módulo de plataforma fidedigna incorporado (TPM)<br>Reconhecimento pin e biométrico |Reconhecimento pin e biométrico no telefone|Dispositivos de segurança FIDO2 compatíveis com a Microsoft|
|**Experiência do utilizador**|Inscreva-se utilizando um PIN ou reconhecimento biométrico (facial, íris ou impressão digital) com dispositivos Windows.<br>A autenticação do Windows Hello está ligada ao dispositivo; o utilizador necessita tanto do dispositivo como de um componente de inscrição, como um PIN ou um fator biométrico para aceder aos recursos corporativos.|Inscreva-se usando um telemóvel com digitalização de impressões digitais, reconhecimento facial ou íris, ou PIN.<br>Os utilizadores insinuam-se no trabalho ou na conta pessoal a partir do seu computador ou telemóvel.|Inscreva-se utilizando o dispositivo de segurança FIDO2 (biometria, PIN e NFC)<br>O utilizador pode aceder ao dispositivo com base em controlos de organização e autenticar com base em PIN, biometria utilizando dispositivos como chaves de segurança USB e smartcards, chaves ou wearables ativados pelo NFC.|
|**Cenários habilitados**| Experiência sem palavra-passe com dispositivo Windows.<br>Aplicável para pc de trabalho dedicado com capacidade para um único sign-on para dispositivos e aplicações.|Solução sem palavra-passe em qualquer lugar usando o telemóvel.<br>Aplicável para aceder a trabalhos ou aplicações pessoais na web a partir de qualquer dispositivo.|Experiência sem palavra-passe para trabalhadores que usam biometria, PIN e NFC.<br>Aplicável para computadores partilhados e onde um telemóvel não é uma opção viável (como pessoal de secretária de ajuda, quiosque público ou equipa hospitalar)|

Utilize a seguinte tabela para escolher qual o método que suporta os seus requisitos e utilizadores.

|Persona|Scenario|Ambiente|Tecnologia sem palavras-passe|
|:-|:-|:-|:-|
|**Administração**|Acesso seguro a um dispositivo para tarefas de gestão|Dispositivo atribuído ao Windows 10|Windows Hello para chave de segurança Business e/ou FIDO2|
|**Administração**|Tarefas de gestão em dispositivos não Windows| Dispositivo móvel ou não-janelas|Iniciar sing-in sem palavra-passe com a aplicação Microsoft Authenticator|
|**Técnico de informação**|Trabalho de produtividade|Dispositivo atribuído ao Windows 10|Windows Hello para chave de segurança Business e/ou FIDO2|
|**Técnico de informação**|Trabalho de produtividade| Dispositivo móvel ou não-janelas|Iniciar sing-in sem palavra-passe com a aplicação Microsoft Authenticator|
|**Trabalhador da linha da frente**|Quiosques numa fábrica, instalações, retalhistas ou entrada de dados|Dispositivos compartilhados do Windows 10|Chaves de segurança FIDO2|

## <a name="next-steps"></a>Passos seguintes

Para começar com sem palavras-passe em Azure AD, complete um dos seguintes como-tos:

* [Ativar o sinal de acesso sem palavras-passe da chave de segurança FIDO2](howto-authentication-passwordless-security-key.md)
* [Ativar o acesso sem palavras-passe com base no telefone com a aplicação Authenticator](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Links Externos

* [Aliança FIDO](https://fidoalliance.org/)
* [Especificação DO FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
