---
title: Entrada sem palavras-passe do Diretório Ativo Azure (pré-visualização)
description: Saiba mais sobre as opções de entrada sem palavras no Azure Ative Directory utilizando chaves de segurança FIDO2 ou a aplicação Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe602972cb16bf24b1c35b2aadfe25c499bce69f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181856"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Opções de autenticação sem palavras-passe para Diretório Ativo Azure

A autenticação multi-factor (MFA) é uma ótima forma de proteger a sua organização, mas os utilizadores muitas vezes ficam frustrados com a camada de segurança adicional para além de terem de se lembrar das suas palavras-passe. Os métodos de autenticação sem palavras-passe são mais convenientes porque a palavra-passe é removida e substituída por algo que tem, além de algo que é ou algo que sabe.

|   | Algo que tenha. | Algo que éou ou sabe |
| --- | --- | --- |
| Sem palavra-passe | Dispositivo, telefone ou chave de segurança do Windows 10 | Biométrico ou PIN |

Cada organização tem necessidades diferentes quando se trata de autenticação. A Microsoft oferece as seguintes três opções de autenticação sem palavras-passe que se integram com o Azure Ative Directory (Azure AD):

- Windows Hello para empresas
- Aplicação Microsoft Authenticator
- Chaves de segurança FIDO2

![Autenticação: Segurança versus conveniência](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello para empresas

O Windows Hello for Business é ideal para trabalhadores de informação que tenham o seu próprio PC windows designado. O biométrico e o PIN estão diretamente ligados ao PC do utilizador, o que impede o acesso de qualquer outra pessoa que não seja o proprietário. Com a integração de infraestruturas de chaves públicas (PKI) e suporte integrado para um único sign-on (SSO), o Windows Hello for Business fornece um método conveniente para aceder sem problemas aos recursos corporativos no local e na nuvem.

![Exemplo de um utilizador de sessão com o Windows Hello for Business](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Os seguintes passos mostram como o processo de entrada funciona com o Azure Ative Directory.

![Diagrama que descreve os passos envolvidos para o sessão do utilizador com o Windows Hello for Business](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Um utilizador assina no Windows usando gesto biométrico ou PIN. O gesto desbloqueia a chave privada Windows Hello for Business e é enviado para o fornecedor de suporte de segurança cloud Authentication, referido como o *fornecedor Cloud AP*.
1. O provedor da Cloud AP solicita um nonce da Azure AD.
1. A Azure AD devolve um nonce válido por 5 minutos.
1. O fornecedor Cloud AP assina o nonce usando a chave privada do utilizador e devolve o nonce assinado ao Azure AD.
1. A Azure AD valida o nonce assinado utilizando a chave pública registada de forma segura do utilizador contra a assinatura nonce. Depois de validar a assinatura, a Azure AD valida o nonce assinado devolvido. Quando o nonce é validado, a Azure AD cria um token de atualização primária (PRT) com chave de sessão que é encriptada à chave de transporte do dispositivo e devolve-a ao fornecedor Cloud AP.
1. O fornecedor Cloud AP recebe o PRT encriptado com chave de sessão. Utilizando a chave de transporte privado do dispositivo, o fornecedor Cloud AP desencripta a chave da sessão e protege a chave da sessão utilizando o Módulo de Plataforma Fidedigna (TPM) do dispositivo.
1. O fornecedor Cloud AP devolve uma resposta de autenticação bem sucedida ao Windows. O utilizador poderá então aceder ao Windows, bem como às aplicações cloud e no local sem a necessidade de voltar a autenticar (SSO).

O guia de [planeamento](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) do Windows Hello for Business pode ser usado para ajudá-lo a tomar decisões sobre o tipo de implementação do Windows Hello for Business e as opções que terá de considerar.

## <a name="microsoft-authenticator-app"></a>Aplicação autenticadora da Microsoft

Permita que o telemóvel do seu empregado se torne um método de autenticação sem palavras-passe. Pode já estar a utilizar a Aplicação Autenticadora microsoft como uma conveniente opção de autenticação de vários fatores, além de uma palavra-passe. Também pode utilizar a App Autenticador como uma opção sem palavras-passe.

![Inscreva-se no Microsoft Edge com a aplicação Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

A Aplicação Autenticadora transforma qualquer iOS ou telefone Android numa credencial forte e sem palavras-passe. Os utilizadores podem iniciar sessão em qualquer plataforma ou navegador, obtendo uma notificação para o seu telemóvel, correspondendo a um número apresentado no ecrã com o do seu telemóvel e, em seguida, usando o seu biométrico (toque ou rosto) ou PIN para confirmar. Consulte o [Download e instale a aplicação Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) para obter detalhes de instalação.

A autenticação sem palavras-passe utilizando a App Autenticador segue o mesmo padrão básico que o Windows Hello for Business. É um pouco mais complicado, uma vez que o utilizador precisa de ser identificado para que o Azure AD possa encontrar a versão microsoft Authenticator App a ser utilizada:

![Diagrama que descreve os passos envolvidos para o sessão do utilizador com a Aplicação Autenticador a Microsoft](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. O utilizador introduz o seu nome de utilizador.
1. A Azure AD deteta que o utilizador tem uma credencial forte e inicia o fluxo credencial forte.
1. Uma notificação é enviada para a aplicação através do Apple Push Notification Service (APNS) em dispositivos iOS, ou através de Firebase Cloud Messaging (FCM) em dispositivos Android.
1. O utilizador recebe a notificação push e abre a aplicação.
1. A aplicação chama Azure AD e recebe um desafio de prova de presença e nonce.
1. O utilizador completa o desafio introduzindo a sua chave biométrica ou PIN para desbloquear a chave privada.
1. O nonce é assinado com a chave privada e enviado de volta para a Azure AD.
1. A Azure AD realiza validação de chaves públicas/privadas e devolve um símbolo.

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

As chaves de segurança FIDO2 são um método de autenticação sem palavras-passe baseado em padrões imphishable que pode vir em qualquer fator de forma. Fast Identity Online (FIDO) é um padrão aberto para autenticação sem palavras-passe. O FIDO permite que os utilizadores e organizações aproveitem a norma para iniciar sessão nos seus recursos sem um nome de utilizador ou palavra-passe utilizando uma chave de segurança externa ou uma chave de plataforma incorporada num dispositivo.

Para pré-visualização pública, os colaboradores podem usar chaves de segurança para iniciar sessão nos seus dispositivos Azure AD ou Azure Hybrid adesacadas e obter um único sinal sobre os seus recursos de nuvem e no local. Os utilizadores também podem iniciar sessão em navegadores suportados. As chaves de segurança FIDO2 são uma ótima opção para empresas que são muito sensíveis à segurança ou têm cenários ou funcionários que não estejam dispostos ou capazes de usar o seu telefone como um segundo fator.

![Inscreva-se no Microsoft Edge com uma chave de segurança](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

O seguinte processo é utilizado quando um utilizador faz o sinal de segurança com uma chave de segurança FIDO2:

![Diagrama que descreve os passos envolvidos para o acesso ao utilizador com uma chave de segurança FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. O utilizador liga a chave de segurança FIDO2 ao computador.
2. O Windows deteta a chave de segurança FIDO2.
3. O Windows envia um pedido de autenticação.
4. A AD Azure envia um nonce.
5. O utilizador completa o seu gesto para desbloquear a chave privada armazenada no enclave seguro da chave de segurança FIDO2.
6. A chave de segurança FIDO2 assina o nonce com a chave privada.
7. O pedido de ficha de atualização primária (PRT) com nonce assinado é enviado para a Azure AD.
8. A Azure AD verifica o nonce assinado utilizando a chave pública FIDO2.
9. A Azure AD devolve prt para permitir o acesso aos recursos no local.

Apesar de existirem muitas chaves certificadas pelo FIDO2 pela Fido Alliance, a Microsoft exige que algumas extensões opcionais da especificação fido2 Cliente-a-Autenticador (CTAP) sejam implementadas pelo fornecedor para garantir a máxima segurança e a melhor experiência.

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
| Gemalto (Grupo Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/sites/default/files/2019-01/OneSpan-FIDO-Authentication.pdf](https://www.onespan.com/sites/default/files/2019-01/OneSpan-FIDO-Authentication.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Se comprar e planear utilizar chaves de segurança baseadas em NFC, precisa de um leitor NFC suportado para a chave de segurança. O leitor nfc não é um requisito ou limitação Azure. Consulte o fornecedor para obter a sua chave de segurança baseada em NFC para obter uma lista de leitores de NFC suportados.

Se for um fornecedor e quiser colocar o seu dispositivo nesta [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)lista de dispositivos suportados, contacte .

## <a name="what-scenarios-work-with-the-preview"></a>Que cenários funcionam com a pré-visualização?

- Os administradores podem ativar métodos de autenticação sem palavras-passe para o seu inquilino
- Os administradores podem direcionar todos os utilizadores ou selecionar utilizadores/grupos dentro do seu inquilino para cada método
- Os utilizadores finais podem registar e gerir estes métodos de autenticação sem palavras-passe no seu portal de conta
- Os utilizadores finais podem iniciar sessão com estes métodos de autenticação sem palavras-passe
   - Aplicação do Autenticador Microsoft: Funciona em cenários onde é utilizada a autenticação AD Azure, incluindo em todos os navegadores, durante a configuração do Windows 10 out Of Box (OOBE) e com aplicações móveis integradas em qualquer sistema operativo.
   - Teclas de segurança: Trabalhe no ecrã de bloqueio para o Windows 10 e na web em navegadores suportados como o Microsoft Edge (legado e novo Edge).

## <a name="choose-a-passwordless-method"></a>Escolha um método sem palavras-passe

A escolha entre estas três opções sem palavras-passe depende dos requisitos de segurança, plataforma e aplicação da sua empresa.

Aqui estão alguns fatores a ter em conta na escolha da tecnologia sem palavras-passe da Microsoft:

||**Windows Hello para empresas**|**Sem palavras-passe com a aplicação Microsoft Authenticator**|**Chaves de segurança FIDO2**|
|:-|:-|:-|:-|
|**Pré-requisito**| Windows 10, versão 1809 ou mais tarde<br>Azure Active Directory| Aplicação Microsoft Authenticator<br>Telefone (iOS e dispositivos Android com android 6.0 ou superior.)|Windows 10, versão 1809 ou mais tarde<br>Azure Active Directory|
|**Modo**|Plataforma|Software|Hardware|
|**Sistemas e dispositivos**|PC com módulo de plataforma fidedigno incorporado (TPM)<br>Reconhecimento de PIN e biometria |PIN e reconhecimento biométrico no telefone|Dispositivos de segurança FIDO2 compatíveis com a Microsoft|
|**Experiência de utilizador**|Inscreva-se utilizando um PIN ou reconhecimento biométrico (facial, íris ou impressão digital) com dispositivos Windows.<br>A autenticação do Windows Hello está ligada ao dispositivo; o utilizador precisa tanto do dispositivo como de um componente de inscrição, como um PIN ou um fator biométrico para aceder aos recursos corporativos.|Inscreva-se utilizando um telemóvel com digitalização de impressões digitais, reconhecimento facial ou de íris, ou PIN.<br>Os utilizadores fazem sessão para trabalhar ou conta pessoal a partir do seu PC ou telemóvel.|Inscreva-se na utilização de dispositivo de segurança FIDO2 (biometria, PIN e NFC)<br>O utilizador pode aceder a dispositivos com base em controlos de organização e autenticar com base em PIN, biometria utilizando dispositivos como chaves de segurança USB e smartcards, chaves ou wearables ativados por NFC.|
|**Cenários habilitados**| Experiência sem palavra-passe com dispositivo Windows.<br>Aplicável para PC de trabalho dedicado com capacidade para um único sinal para dispositivo e aplicações.|Solução sem palavra-passe em qualquer lugar utilizando o telemóvel.<br>Aplicável para aceder a trabalho ou aplicações pessoais na web a partir de qualquer dispositivo.|Experiência sem palavra-passe para trabalhadores que usam biometria, PIN e NFC.<br>Aplicável para computadores partilhados e onde um telemóvel não é uma opção viável (como pessoal de mesa de ajuda, quiosque público ou equipa hospitalar)|

Utilize a tabela seguinte para escolher qual o método que irá suportar os seus requisitos e utilizadores.

|Persona|Cenário|Ambiente|Tecnologia sem palavras-passe|
|:-|:-|:-|:-|
|**Administração**|Acesso seguro a um dispositivo para tarefas de gestão|Dispositivo do Windows 10 atribuído|Windows Olá para chave de segurança De negócios e/ou FIDO2|
|**Administração**|Tarefas de gestão em dispositivos não Windows| Dispositivo móvel ou não windows|Sem palavras-passe com a aplicação Microsoft Authenticator|
|**Técnico de informação**|Trabalho de produtividade|Dispositivo do Windows 10 atribuído|Windows Olá para chave de segurança De negócios e/ou FIDO2|
|**Técnico de informação**|Trabalho de produtividade| Dispositivo móvel ou não windows|Sem palavras-passe com a aplicação Microsoft Authenticator|
|**Trabalhador da linha da frente**|Quiosques numa fábrica, fábrica, retalho ou entrada de dados|Dispositivos Windows 10 partilhados|Chaves de segurança FIDO2|

## <a name="next-steps"></a>Passos seguintes

[Ativar opções sem palavras-passe de chave de segurança FIDO2 na sua organização](howto-authentication-passwordless-security-key.md)

[Ativar opções sem palavras-passe baseadas em telefone na sua organização](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Ligações Externas

[Aliança FIDO](https://fidoalliance.org/)

[Especificação FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
