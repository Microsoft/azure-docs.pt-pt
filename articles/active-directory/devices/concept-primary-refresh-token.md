---
title: Ficha de atualização primária (PRT) e Azure AD - Diretório Ativo Azure
description: Qual é o papel e como gerimos o Primário Refresh Token (PRT) no Diretório Ativo Azure?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672638"
---
# <a name="what-is-a-primary-refresh-token"></a>O que é um Token De Refresco Primário?

Um Token De Atualização Primário (PRT) é um artefacto chave da autenticação Azure AD nos dispositivos Windows 10, iOS e Android. Trata-se de um JSON Web Token (JWT) especialmente emitido para os corretores de fichas da primeira parte da Microsoft para permitir um único sinal (SSO) através das aplicações utilizadas nesses dispositivos. Neste artigo, forneceremos detalhes sobre como um PRT é emitido, usado e protegido em dispositivos Windows 10.

Este artigo assume que já compreende os diferentes estados do dispositivo disponíveis no Azure AD e como funciona um único sinal no Windows 10. Para obter mais informações sobre dispositivos em Azure AD, consulte o artigo [O que é a gestão de dispositivos no Diretório Ativo azure?](overview.md)

## <a name="key-terminology-and-components"></a>Terminologia e componentes chave

Os seguintes componentes do Windows desempenham um papel fundamental na solicitação e utilização de um PRT:

* **Fornecedor de Autenticação cloud** (CloudAP): O CloudAP é o fornecedor moderno de autenticação para o Windows iniciar sessão, que verifica o registo dos utilizadores num dispositivo Windows 10. A CloudAP fornece uma estrutura plugin que os fornecedores de identidade podem construir para permitir a autenticação no Windows usando as credenciais desse fornecedor de identidade.
* **Gestor de Conta Web** (WAM): WAM é o corretor de fichas padrão nos dispositivos do Windows 10. A WAM também fornece um quadro plugin que os fornecedores de identidade podem construir e permitir o SSO às suas aplicações confiando nesse fornecedor de identidade.
* **Plugin Azure AD CloudAP**: Um plugin específico da AD Azure construído sobre a estrutura CloudAP, que verifica as credenciais do utilizador com a AD Azure durante o início do windows.
* **Plugin Azure AD WAM**: Um plugin específico Azure AD construído sobre a estrutura WAM, que permite ao SSO aplicações que dependem da AD Azure para autenticação.
* **Dsreg**: Um componente específico da AD Azure no Windows 10, que trata do processo de registo do dispositivo para todos os estados do dispositivo.
* **Módulo de Plataforma Fidedigna** (TPM): Um TPM é um componente de hardware incorporado num dispositivo, que fornece funções de segurança baseadas em hardware para segredos de utilizador e dispositivo. Mais detalhes podem ser encontrados no artigo Visão geral da tecnologia do [módulo de plataforma fidedigna.](/windows/security/information-protection/tpm/trusted-platform-module-overview)

## <a name="what-does-the-prt-contain"></a>O que o PrT contém?

Um PRT contém alegações geralmente contidas em qualquer token de atualização da AD Azure. Além disso, existem algumas reclamações específicas do dispositivo incluídas no PrT. São os seguintes:

* **ID do dispositivo**: Um PRT é emitido a um utilizador num dispositivo específico. A alegação `deviceID` de ID do dispositivo determina o dispositivo em que o PRT foi emitido para o utilizador. Esta alegação é posteriormente emitida a fichas obtidas através da PrT. A alegação de ID do dispositivo é utilizada para determinar a autorização de Acesso Condicional com base no estado do dispositivo ou na conformidade.
* **Chave da sessão**: A chave da sessão é uma chave simétrica encriptada, gerada pelo serviço de autenticação Azure AD, emitida como parte do PRT. A chave da sessão funciona como prova de posse quando um PRT é usado para obter fichas para outras aplicações.

### <a name="can-i-see-whats-in-a-prt"></a>Posso ver o que há numa prt?

Um PRT é uma bolha opaca enviada da Azure AD cujo conteúdo não é do conhecimento de quaisquer componentes do cliente. Não se pode ver o que está dentro de um PrT.

## <a name="how-is-a-prt-issued"></a>Como é emitida uma PrT?

O registo do dispositivo é um pré-requisito para a autenticação baseada em dispositivos em Azure AD. É emitido um PRT para utilizadores apenas em dispositivos registados. Para mais detalhes sobre o registo do dispositivo, consulte o artigo Windows Hello para registo de [negócios e dispositivos.](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration) Durante o registo do dispositivo, o componente dsreg gera dois conjuntos de pares de chaves criptográficas:

* Chave do dispositivo (dkpub/dkpriv)
* Chave de transporte (tkpub/tkpriv)

As chaves privadas estão ligadas ao TPM do dispositivo se o dispositivo tiver um TPM válido e funcional, enquanto as chaves públicas são enviadas para a AD Azure durante o processo de registo do dispositivo. Estas teclas são utilizadas para validar o estado do dispositivo durante os pedidos de PRT.

O PRT é emitido durante a autenticação do utilizador num dispositivo Windows 10 em dois cenários:

* **Azure AD juntou-se** ou **Hybrid Azure AD juntou-se**: A PRT é emitida durante o logon do Windows quando um utilizador inicia sessão com as suas credenciais de organização. É emitida uma PRT com todas as credenciais suportadas pelo Windows 10, por exemplo, palavra-passe e Windows Hello for Business. Neste cenário, o plugin Azure AD CloudAP é a principal autoridade para o PRT.
* **Dispositivo registado em Azure AD**: Um PRT é emitido quando um utilizador adiciona uma conta de trabalho secundária ao seu dispositivo Windows 10. Os utilizadores podem adicionar uma conta ao Windows 10 de duas maneiras diferentes -  
   * Adicionar uma conta através da **utilização desta conta em todo o lado neste dispositivo** solicita-se depois de iniciar sessão numa app (por exemplo, Outlook)
   * Adicionar uma conta a partir de **Definições** > **Accounts** > Contas**Trabalho de Acesso ou** > **Ligação** Escolar

Nos cenários do dispositivo registado pela Azure AD, o plugin WAM Azure AD é a principal autoridade para o PRT, uma vez que o logon do Windows não está a acontecer com esta conta Azure AD.

> [!NOTE]
> Os fornecedores de identidade de terceiros precisam de apoiar o protocolo WS-Trust para permitir a emissão de PRT em dispositivos Windows 10. Sem a WS-Trust, o PRT não pode ser emitido aos utilizadores em Dispositivos AD Hybrid Azure ou Azure AD

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual é a vida de um PrT?

Uma vez emitido, um PRT é válido por 14 dias e é continuamente renovado desde que o utilizador utilize ativamente o dispositivo.  

## <a name="how-is-a-prt-used"></a>Como é usado um PRT?

Um PRT é utilizado por dois componentes chave no Windows:

* **Plugin Azure AD CloudAP**: Durante o início do windows, o plugin Azure AD CloudAP solicita um PRT da Azure AD utilizando as credenciais fornecidas pelo utilizador. Também coloca o PRT para permitir o registo em cache quando o utilizador não tem acesso a uma ligação à Internet.
* **Plugin WAM Azure AD**: Quando os utilizadores tentam aceder a aplicações, o plugin WAM Azure AD utiliza o PRT para ativar o SSO no Windows 10. O plugin WAM Azure AD utiliza o PRT para solicitar fichas de atualização e acesso para aplicações que dependem da WAM para pedidos simbólicos. Também permite o SSO nos navegadores injetando o PRT em pedidos de navegador. O Navegador SSO no Windows 10 é suportado no Microsoft Edge (de forma nativa) e no Chrome (através das Contas Windows 10 ou da extensão Do Office Online).

## <a name="how-is-a-prt-renewed"></a>Como é renovado um PRT?

Um PRT é renovado em dois métodos diferentes:

* **Plugin Azure AD CloudAP a cada 4 horas**: O plugin CloudAP renova o PRT a cada 4 horas durante o início do Windows. Se o utilizador não tiver ligação à Internet durante esse período, o plugin CloudAP renovará o PRT depois de o dispositivo estar ligado à internet.
* **Plugin WAM Azure AD durante pedidos**de token de aplicação : O plugin WAM permite sSO em dispositivos Windows 10, permitindo pedidos de token silenciosos para aplicações. O plugin WAM pode renovar o PRT durante estes pedidos simbólicos de duas maneiras diferentes:
   * Uma aplicação solicita à WAM um sinal de acesso silenciosamente, mas não há nenhum token de atualização disponível para essa aplicação. Neste caso, a WAM usa o PRT para solicitar um sinal para a app e recebe de volta um novo PRT na resposta.
   * Uma aplicação solicita à WAM um sinal de acesso, mas o PRT é inválido ou a AD Azure requer autorização adicional (por exemplo, autenticação de multi-factores Azure). Neste cenário, a WAM inicia um logon interativo que exige que o utilizador reatente ou forneça uma verificação adicional e um novo PRT é emitido sobre autenticação bem sucedida.

### <a name="key-considerations"></a>Considerações principais

* Um PRT só é emitido e renovado durante a autenticação de aplicações nativas. Um PRT não é renovado ou emitido durante uma sessão de navegador.
* Em Azure AD juntou-se e híbrido Azure AD aderiu a dispositivos, o plugin CloudAP é a principal autoridade para um PRT. Se um PRT for renovado durante um pedido de ficha baseado em WAM, o PRT é enviado de volta para o plugin CloudAP, que verifica a validade do PRT com a AD Azure antes de aceitá-lo.

## <a name="how-is-the-prt-protected"></a>Como é que o PRT está protegido?

Um PRT é protegido ligando-o ao dispositivo no que o utilizador assinou. A Azure AD e o Windows 10 permitem a proteção prt através dos seguintes métodos:

* Durante o **primeiro início de sessão**: Durante o primeiro início de sessão, é emitido um PRT através da assinatura de pedidos utilizando a chave do dispositivo gerada criptograficamente durante o registo do dispositivo. Num dispositivo com um TPM válido e funcional, a chave do dispositivo é fixada pelo TPM impedindo qualquer acesso malicioso. Não é emitida uma PRT se a assinatura da chave do dispositivo correspondente não puder ser validada.
* **Durante os pedidos simbólicos e renovação**: Quando um PRT é emitido, a Azure AD também emite uma chave de sessão encriptada para o dispositivo. É encriptado com a chave de transportes públicos (tkpub) gerada e enviada para a Azure AD como parte do registo do dispositivo. Esta chave de sessão só pode ser desencriptada pela chave de transporte privado (tkpriv) fixada pelo TPM. A chave da sessão é a chave Prova de Posse (POP) para quaisquer pedidos enviados à Azure AD.  A chave da sessão também está protegida pelo TPM e nenhum outro componente de SO pode acessá-la. Os pedidos de token ou pedidos de renovação de PRT são assinados de forma segura por esta chave de sessão através do TPM e, portanto, não podem ser adulterados. A Azure AD invalidará quaisquer pedidos do dispositivo que não estejam assinados pela chave de sessão correspondente.

Ao assegurar estas chaves com o TPM, os atores maliciosos não podem roubar as chaves nem reproduzir o PRT em outro lugar, uma vez que o TPM é inacessível mesmo que um intruso tenha posse física do dispositivo.  Assim, a utilização de um TPM aumenta consideravelmente a segurança da Azure AD Joined, Hybrid Azure AD juntou-se, e dispositivos registados azure AD contra roubo de credenciais. Para desempenho e fiabilidade, o TPM 2.0 é a versão recomendada para todos os cenários de registo de dispositivos Azure AD no Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Como estão protegidos os tokens de aplicativos e os cookies de navegador?

**Tokens**de aplicativos : Quando uma aplicação pede ficha através da WAM, a Azure AD emite um token de atualização e um token de acesso. No entanto, a WAM apenas devolve o sinal de acesso à aplicação e protege o token de atualização na sua cache encriptando-o com a chave de programação de aplicação de proteção de dados do utilizador (DPAPI). A WAM usa de forma segura o token de atualização, assinando pedidos com a chave da sessão para emitir mais fichas de acesso. A chave DPAPI é protegida por uma chave simétrica baseada em Azure AD em azure ad em si. Quando o dispositivo precisa de desencriptar o perfil do utilizador com a tecla DPAPI, o Azure AD fornece a tecla DPAPI encriptada pela chave da sessão, que o plugin CloudAP solicita à TPM para desencriptar. Esta funcionalidade garante consistência na fixação de tokens de atualização e evita que as aplicações implementem os seus próprios mecanismos de proteção.  

**Cookies de navegador**: No Windows 10, o Azure AD suporta o navegador SSO no Internet Explorer e Microsoft Edge de forma nativa ou no Google Chrome através da extensão das contas do Windows 10. A segurança é construída não só para proteger os cookies, mas também para os pontos finais para os quais os cookies são enviados. Os cookies de navegador estão protegidos da mesma forma que um PRT, utilizando a chave da sessão para assinar e proteger os cookies.

Quando um utilizador inicia uma interação de navegador, o navegador (ou extensão) invoca um anfitrião de cliente nativo da COM. O anfitrião do cliente nativo garante que a página é de um dos domínios permitidos. O navegador poderia enviar outros parâmetros para o anfitrião do cliente nativo, incluindo um nonce, no entanto o anfitrião do cliente nativo garante a validação do nome de anfitrião. O anfitrião do cliente nativo solicita um cookie PRT do plugin CloudAP, que o cria e assina com a chave de sessão protegida por TPM. Como o cookie PRT é assinado pela chave da sessão, não pode ser adulterado. Este cookie PRT está incluído no cabeçalho de pedido para a Azure AD validar o dispositivo de que é originário. Se utilizar o navegador Chrome, apenas a extensão explicitamente definida no manifesto do anfitrião do cliente nativo pode invocá-lo impedindo que extensões arbitrárias evoquem estes pedidos. Assim que a Azure AD valida o cookie PRT, emite um cookie de sessão para o navegador. Este cookie de sessão também contém a mesma chave de sessão emitida com um PRT. Durante os pedidos subsequentes, a chave da sessão é validada ligando eficazmente o cookie ao dispositivo e impedindo repetições de outros lugares.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando é que um PRT recebe uma reclamação de MFA?

Um PRT pode obter uma reivindicação de autenticação multi-factor (MFA) em cenários específicos. Quando um PRT baseado em MFA é usado para solicitar fichas para aplicações, a alegação de MFA é transferida para esses tokens de aplicação. Esta funcionalidade proporciona uma experiência perfeita aos utilizadores, evitando o desafio mfa para cada aplicação que a exija. Um PRT pode obter uma reclamação de MFA das seguintes formas:

* **Inscreva-se no Windows Hello for Business**: O Windows Hello for Business substitui as palavras-passe e utiliza chaves criptográficas para fornecer uma autenticação forte de dois fatores. O Windows Hello for Business é específico de um utilizador num dispositivo, e por si só requer mFA para fornecer. Quando um utilizador faz login no Windows Hello for Business, o PRT do utilizador recebe uma reclamação de MFA. Este cenário aplica-se também aos utilizadores que iniciam sessão com smartcards se a autenticação do smartcard produzir uma reclamação de MFA da ADFS.
   * Como o Windows Hello for Business é considerado autenticação de vários fatores, a alegação do MFA é atualizada quando o próprio PRT é atualizado, pelo que a duração do MFA irá continuar a estender-se quando os utilizadores assinarem com a WIndows Hello for Business
* **MFA durante o signo interativo wAM em**: Durante um pedido simbólico através da WAM, se um utilizador for obrigado a fazer MFA para aceder à app, o PRT que é renovado durante esta interação é impresso com uma reivindicação de MFA.
   * Neste caso, a alegação do MFA não é atualizada continuamente, pelo que a duração do MFA baseia-se na duração vitalícia definida no diretório.
   * Quando um PRT e RT existentes anteriores forem utilizados para o acesso a uma aplicação, o PRT e o RT serão considerados como a primeira prova de autenticação. Será necessário um novo AT com uma segunda prova e uma reivindicação impressa de MFA. Isto também emitirá um novo PRT e RT.
* **MFA durante o registo**do dispositivo : Se um administrador tiver configurado as definições do seu dispositivo em Azure AD para exigir que o [MFA registe dispositivos,](device-management-azure-portal.md#configure-device-settings)o utilizador precisa de fazer MFA para completar o registo. Durante este processo, o PRT que é emitido ao utilizador tem a reclamação de MFA obtida durante o registo. Esta capacidade aplica-se apenas ao utilizador que fez a operação de adesão, e não a outros utilizadores que iniciaram o contrato.
   * À semelhança do sinal interativo da WAM, a alegação de MFA não é atualizada continuamente, pelo que a duração do MFA baseia-se na duração vitalícia definida no diretório.

O Windows 10 mantém uma lista dividida de PrTs para cada credencial. Portanto, há um PRT para cada um dos Windows Hello for Business, password ou smartcard. Esta divisão garante que as alegações de MFA são isoladas com base na credencial utilizada e não misturadas durante pedidos simbólicos.

## <a name="how-is-a-prt-invalidated"></a>Como é que um PRT é invalidado?

Um PRT é invalidado nos seguintes cenários:

* **Utilizador inválido**: Se um utilizador for eliminado ou desativado em AD Azure, o seu PRT é invalidado e não pode ser utilizado para obter fichas para aplicações. Se um utilizador eliminado ou desativado já tivesse assinado um dispositivo antes, o registo em cache os iniciaria, até que o CloudAP tenha conhecimento do seu estado inválido. Uma vez que o CloudAP determina que o utilizador é inválido, bloqueia logons subsequentes. Um utilizador inválido é automaticamente bloqueado do sessão para novos dispositivos que não têm as suas credenciais em cache.
* **Dispositivo inválido**: Se um dispositivo for eliminado ou desativado em AD Azure, o PRT obtido nesse dispositivo é invalidado e não pode ser utilizado para obter fichas para outras aplicações. Se um utilizador já estiver inscrito num dispositivo inválido, pode continuar a fazê-lo. Mas todas as fichas do dispositivo são invalidadas e o utilizador não tem SSO a quaisquer recursos desse dispositivo.
* **Alteração da palavra-passe**: Depois de um utilizador alterar a sua palavra-passe, o PRT obtido com a palavra-passe anterior é invalidado pela AD Azure. A alteração da palavra-passe resulta na obtenção de um novo PRT. Esta invalidação pode acontecer de duas maneiras diferentes:
   * Se o utilizador fizer o insessão no Windows com a sua nova palavra-passe, o CloudAP descarta o antigo PRT e solicita ao Azure AD que emita um novo PRT com a sua nova senha. Se o utilizador não tiver uma ligação à Internet, a nova palavra-passe não poderá ser validada, o Windows poderá exigir que o utilizador introduza a sua antiga palavra-passe.
   * Se um utilizador tiver iniciado sessão com a sua antiga palavra-passe ou tiver alterado a sua palavra-passe depois de iniciar sessão no Windows, o antigo PRT é utilizado para quaisquer pedidos de fichas baseados em WAM. Neste cenário, o utilizador é solicitado a reautenticar durante o pedido de token WAM e é emitido um novo PRT.
* Problemas de TPM : Por **vezes,** o TPM de um dispositivo pode vacilar ou falhar, levando à inacessibilidade das chaves fixadas pelo TPM. Neste caso, o dispositivo é incapaz de obter um PRT ou solicitar fichas usando um PRT existente, uma vez que não pode provar a posse das teclas criptográficas. Como resultado, qualquer PRT existente é invalidado pela Azure AD. Quando o Windows 10 deteta uma falha, inicia um fluxo de recuperação para voltar a registar o dispositivo com novas teclas criptográficas. Com a adesão do Hybrid Azure Ad, tal como o registo inicial, a recuperação acontece silenciosamente sem a entrada do utilizador. Para a Azure AD aderiu ou dispositivos registados em Azure AD, a recuperação tem de ser realizada por um utilizador que tenha privilégios de administrador no dispositivo. Neste cenário, o fluxo de recuperação é iniciado por um pedido do Windows que guia o utilizador a recuperar com sucesso o dispositivo.

## <a name="detailed-flows"></a>Fluxos detalhados

Os seguintes diagramas ilustram os detalhes subjacentes na emissão, renovação e utilização de um PRT para solicitar um sinal de acesso para uma aplicação. Além disso, estes passos também descrevem como os mecanismos de segurança acima referidos são aplicados durante estas interações.

### <a name="prt-issuance-during-first-sign-in"></a>Emissão de PRT durante o primeiro sinal

![Emissão de PRT durante o primeiro sinal em fluxo detalhado](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Em Dispositivos aderes a AD Azure, esta troca acontece sincronizadamente para emitir um PRT antes que o utilizador possa iniciar sessão no Windows. Em dispositivos híbridos Azure AD, no local o Ative Directory é a principal autoridade. Assim, o utilizador só está à espera até que possa adquirir um TGT para iniciar sessão, enquanto a emissão de PRT acontece de forma assíncrona. Este cenário não se aplica aos dispositivos registados pela Azure AD, uma vez que o logon não utiliza credenciais Azure AD.

| Passo | Descrição |
| :---: | --- |
| A | O utilizador introduz a sua palavra-passe no sinal em UI. O LogonUI passa as credenciais num tampão auth para lSA, que por sua vez passa internamente para cloudAP. CloudAP reencaminha este pedido para o plugin CloudAP. |
| B | O plugin CloudAP inicia um pedido de descoberta do reino para identificar o fornecedor de identidade para o utilizador. Se o inquilino do utilizador tiver uma configuração de fornecedor da federação, a Azure AD devolve o ponto final da Bolsa de Metadados (MEX) do fornecedor da federação. Caso contrário, a Azure AD declara que o utilizador é gerido indicando que o utilizador pode autenticar com AD Azure. |
| C | Se o utilizador for gerido, o CloudAP receberá o nonce da Azure AD. Se o utilizador for federado, o plugin CloudAP solicita um token SAML ao fornecedor da federação com as credenciais do utilizador. Uma vez que recebe, o símbolo SAML, solicita um nonce da Azure AD. |
| D | O plugin CloudAP constrói o pedido de autenticação com as credenciais do utilizador, nonce, e um âmbito de corretor, assina o pedido com a chave dispositivo (dkpriv) e envia-o para a AD Azure. Num ambiente federado, o plugin CloudAP utiliza o token SAML devolvido pelo provedor da federação em vez das credenciais do utilizador. |
| E | A Azure AD valida as credenciais de utilizador, a assinatura nonce e do dispositivo, verificando que o dispositivo é válido no inquilino e emite o PRT encriptado. Juntamente com o PRT, a Azure AD também emite uma chave simétrica, chamada chave Session encriptada pela Azure AD utilizando a chave de transporte (tkpub). Além disso, a chave Sessão também está incorporada no PRT. Esta chave da Sessão funciona como a chave prova de posse (PoP) para pedidos subsequentes com o PRT. |
| F | CloudAP plugin passa a chave PRT e Session encriptada para CloudAP. O CloudAP solicita ao TPM que desencripta a tecla Sessão utilizando a chave de transporte (tkpriv) e a criptografe novamente utilizando a própria chave do TPM. CloudAP armazena a chave sessão encriptada na sua cache juntamente com o PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renovação de PRT em logons subsequentes

![Renovação de PRT em logons subsequentes](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Passo | Descrição |
| :---: | --- |
| A | O utilizador introduz a sua palavra-passe no sinal em UI. O LogonUI passa as credenciais num tampão auth para lSA, que por sua vez passa internamente para cloudAP. CloudAP reencaminha este pedido para o plugin CloudAP. |
| B | Se o utilizador já tiver iniciado previamente o início do registo no utilizador, o Windows inicia o registo em cache e valida credenciais para iniciar o login do utilizador. A cada 4 horas, o plugin CloudAP inicia a renovação de PRT sincronicamente. |
| C | O plugin CloudAP inicia um pedido de descoberta do reino para identificar o fornecedor de identidade para o utilizador. Se o inquilino do utilizador tiver uma configuração de fornecedor da federação, a Azure AD devolve o ponto final da Bolsa de Metadados (MEX) do fornecedor da federação. Caso contrário, a Azure AD declara que o utilizador é gerido indicando que o utilizador pode autenticar com AD Azure. |
| D | Se o utilizador for federado, o plugin CloudAP solicita um token SAML ao fornecedor da federação com as credenciais do utilizador. Uma vez que recebe, o símbolo SAML, solicita um nonce da Azure AD. Se o utilizador for gerido, o CloudAP receberá diretamente o nonce da Azure AD. |
| E | O plugin CloudAP constrói o pedido de autenticação com as credenciais do utilizador, nonce, e o PRT existente, assina o pedido com a chave Sessão e envia-o para a AD Azure. Num ambiente federado, o plugin CloudAP utiliza o token SAML devolvido pelo provedor da federação em vez das credenciais do utilizador. |
| F | A Azure AD valida a assinatura da chave sessão comparando-a com a chave sessão incorporada no PRT, valida o nonce e verifica que o dispositivo é válido no inquilino e emite um novo PRT. Como visto anteriormente, o PRT é novamente acompanhado com a chave Sessão encriptada pela chave transporte (tkpub). |
| G | CloudAP plugin passa a chave PRT e Session encriptada para CloudAP. O CloudAP solicita ao TPM que desencripta a tecla Sessão utilizando a chave de transporte (tkpriv) e a criptografe novamente utilizando a própria chave do TPM. CloudAP armazena a chave sessão encriptada na sua cache juntamente com o PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Utilização de PRT durante pedidos de token de aplicação

![Utilização de PRT durante pedidos de token de aplicação](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Passo | Descrição |
| :---: | --- |
| A | Uma aplicação (por exemplo, Outlook, OneNote etc.) inicia um pedido simbólico à WAM. A WAM, por sua vez, pede ao plugin WAM Da AD Azure para servir o pedido simbólico. |
| B | Se já estiver disponível um token Refresh para a aplicação, o plugin WAM Da AD Azure usa-o para solicitar um token de acesso. Para fornecer um comprovativo de encadernação do dispositivo, o plugin WAM assina o pedido com a tecla Sessão. A Azure AD valida a chave Session e emite um token de acesso e um novo token de atualização para a app, encriptado pela chave Session. O plugin WAM solicita que o plugin Cloud AP desencriptar os tokens, que, por sua vez, solicita ao TPM que desencriptasse usando a tecla Session, resultando em plugin WAM recebendo ambos os tokens. Em seguida, o plugin WAM fornece apenas o sinal de acesso à aplicação, enquanto ele reencripta o token de atualização com DPAPI e armazena-o na sua própria cache  |
| C |  Se não estiver disponível um token Refresh para a aplicação, o plugin WAM Azure AD utiliza o PRT para solicitar um sinal de acesso. Para apresentar provas de posse, o plugin WAM assina o pedido que contém o PRT com a tecla Session. A Azure AD valida a assinatura da chave session comparando-a com a chave Session incorporada no PRT, verifica que o dispositivo é válido e emite um token de acesso e um token de atualização para a aplicação. além disso, a Azure AD pode emitir um novo PRT (baseado no ciclo de atualização), todos encriptados pela chave Sessão. |
| D | O plugin WAM solicita que o plugin Cloud AP desencriptar os tokens, que, por sua vez, solicita ao TPM que desencriptasse usando a tecla Session, resultando em plugin WAM recebendo ambos os tokens. Em seguida, o plugin WAM fornece apenas o sinal de acesso à aplicação, enquanto ele reencripta o token de atualização com DPAPI e armazena-o na sua própria cache. O plugin WAM utilizará o token de atualização para esta aplicação. O plugin WAM também devolve o novo PLUGR à Cloud AP plugin, que valida o PRT com a AD Azure antes de o atualizar na sua própria cache. Cloud AP plugin usará o novo PRT em frente. |
| E | WAM fornece o recém-emitido sinal de acesso à WAM, que por sua vez, fornece-o de volta ao pedido de chamada|

### <a name="browser-sso-using-prt"></a>Navegador SSO usando PRT

![Navegador SSO usando PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Passo | Descrição |
| :---: | --- |
| A | O utilizador inicia sessão no Windows com as suas credenciais para obter um PRT. Assim que o utilizador abrir o navegador, o navegador (ou extensão) carrega os URLs do registo. |
| B | Quando um utilizador abre um URL de login Azure AD, o navegador ou extensão valida o URL com os obtidos a partir do registo. Se corresponderem, o navegador invoca o anfitrião do cliente nativo por obter um símbolo. |
| C | O anfitrião do cliente nativo valida que os URLs pertencem aos fornecedores de identidade da Microsoft (conta Microsoft ou Azure AD), extrai uma nonce enviada do URL e faz uma chamada para o plugin CloudAP para obter um cookie PRT. |
| D | O plugin CloudAP criará o cookie PRT, iniciará sessão com a chave de sessão tpm-bound e enviá-lo-á de volta para o anfitrião do cliente nativo. Como o cookie é assinado pela chave da sessão, não pode ser adulterado. |
| E | O anfitrião do cliente nativo devolverá este cookie PRT ao navegador, que irá incluí-lo como parte do cabeçalho de pedido chamado x-ms-RefreshTokenCredential e solicitar fichas da Azure AD. |
| F | A Azure AD valida a assinatura da chave session no cookie PRT, valida o nonce, verifica que o dispositivo é válido no inquilino, e emite um token de ID para a página web e um cookie de sessão encriptado para o navegador. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre problemas relacionados com o PRT, consulte o artigo [Troubleshooting hybrid Azure Ative Directory juntou-se aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
