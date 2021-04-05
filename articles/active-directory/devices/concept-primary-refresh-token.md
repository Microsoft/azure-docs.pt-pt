---
title: Token de Atualização Primária (PRT) e Azure AD - Diretório Ativo Azure
description: Qual é o papel e como gerimos o Token Primary Refresh (PRT) em Azure Ative Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46cc8ef1158c02190f905cbe8eb1d12ea7be50a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644940"
---
# <a name="what-is-a-primary-refresh-token"></a>O que é um Token de Atualização Primário?

Um Token De Atualização Primária (PRT) é um artefacto chave da autenticação AD do Azure no Windows 10, Windows Server 2016 e versões posteriores, iOS e dispositivos Android. Trata-se de um JSON Web Token (JWT) especialmente emitido para os corretores de token da primeira parte da Microsoft para permitir um único sign-on (SSO) em todas as aplicações utilizadas nesses dispositivos. Neste artigo, iremos fornecer detalhes sobre como um PRT é emitido, usado e protegido em dispositivos Windows 10.

Este artigo assume que já compreende os diferentes estados do dispositivo disponíveis no AZure AD e como funciona uma única sação no Windows 10. Para obter mais informações sobre dispositivos em Azure AD, veja o artigo O que é a gestão de [dispositivos no Azure Ative Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Terminologia chave e componentes

Os seguintes componentes do Windows desempenham um papel fundamental na solicitação e utilização de um PRT:

* **Cloud Authentication Provider** (CloudAP): O CloudAP é o fornecedor de autenticação moderno para o Windows a iniciar sessão, que verifica o registo dos utilizadores num dispositivo Windows 10. O CloudAP fornece um quadro plugin que os fornecedores de identidade podem construir para permitir a autenticação ao Windows utilizando as credenciais desse fornecedor de identidade.
* **Gestor de Conta Web** (WAM): WAM é o corretor de fichas padrão em dispositivos Windows 10. A WAM também fornece um quadro plugin que os fornecedores de identidade podem construir e permitir que o SSO nas suas aplicações dependa desse fornecedor de identidade.
* **Azure AD CloudAP plugin**: Um plugin específico AD AZure construído na estrutura CloudAP, que verifica credenciais de utilizador com Azure AD durante o sents do Windows.
* **Plugin Azure AD WAM**: Um plugin específico AD AZure construído sobre a estrutura WAM, que permite ao SSO aplicações que dependem do Azure AD para a autenticação.
* **Dsreg**: Um componente específico AD AD no Windows 10, que trata do processo de registo do dispositivo para todos os estados do dispositivo.
* **Módulo de plataforma fidedigno** (TPM): Um TPM é um componente de hardware incorporado num dispositivo, que fornece funções de segurança baseadas em hardware para segredos de utilizador e dispositivo. Mais detalhes podem ser encontrados no artigo Visão geral da [tecnologia do módulo de plataforma fidedigna.](/windows/security/information-protection/tpm/trusted-platform-module-overview)

## <a name="what-does-the-prt-contain"></a>O que o PRT contém?

Um PRT contém alegações geralmente contidas em qualquer token de atualização AZure. Além disso, existem algumas alegações específicas do dispositivo incluídas no PrT. São os seguintes:

* **ID do dispositivo:** Um PRT é emitido a um utilizador num dispositivo específico. A alegação de ID do dispositivo `deviceID` determina o dispositivo em que o PRT foi emitido ao utilizador. Esta alegação é posteriormente emitida em fichas obtidas através do PRT. A alegação de ID do dispositivo é usada para determinar a autorização de Acesso Condicional com base no estado do dispositivo ou na conformidade.
* **Tecla de sessão**: A tecla de sessão é uma chave simétrica encriptada, gerada pelo serviço de autenticação AD AZure, emitido como parte do PRT. A chave da sessão funciona como prova de posse quando um PRT é usado para obter fichas para outras aplicações.

### <a name="can-i-see-whats-in-a-prt"></a>Posso ver o que há num prt?

Um PRT é uma bolha opaca enviada da Azure AD cujo conteúdo não é conhecido de quaisquer componentes do cliente. Não se pode ver o que há dentro de um prt.

## <a name="how-is-a-prt-issued"></a>Como é emitido um PRT?

O registo do dispositivo é um pré-requisito para a autenticação baseada no dispositivo em Azure AD. Um PRT é emitido para os utilizadores apenas em dispositivos registados. Para obter mais detalhes aprofundados sobre o registo do dispositivo, consulte o artigo [Windows Hello for Business and Device Registration](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante o registo do dispositivo, o componente dsreg gera dois conjuntos de pares de chaves criptográficas:

* Chave do dispositivo (dkpub/dkpriv)
* Chave de transporte (tkpub/tkpriv)

As chaves privadas estão ligadas ao TPM do dispositivo se o dispositivo tiver um TPM válido e funcional, enquanto as chaves públicas são enviadas para Azure AD durante o processo de registo do dispositivo. Estas teclas são utilizadas para validar o estado do dispositivo durante os pedidos de PRT.

O PRT é emitido durante a autenticação do utilizador num dispositivo Windows 10 em dois cenários:

* **A azure AD aderiu** ou **Hybrid Azure AD aderiu**: Um PRT é emitido durante a logon do Windows quando um utilizador assina com as suas credenciais de organização. Um PRT é emitido com todas as credenciais suportadas pelo Windows 10, por exemplo, palavra-passe e Windows Hello for Business. Neste cenário, o plugin Azure AD CloudAP é a principal autoridade para o PRT.
* **Dispositivo registado Azure AD**: Um PRT é emitido quando um utilizador adiciona uma conta de trabalho secundária ao seu dispositivo Windows 10. Os utilizadores podem adicionar uma conta ao Windows 10 de duas maneiras diferentes -  
   * Adicionar uma conta através da **Conta Utilizar esta conta em todo o lado neste dispositivo** pronta depois de iniciar sessão numa aplicação (por exemplo, Outlook)
   * Adicionar uma conta a partir de **Definições**  >  **Contas**  >  **Access Work ou School**  >  **Connect**

Nos cenários de dispositivo registados em Azure AD, o plugin Azure AD WAM é a principal autoridade para o PRT uma vez que o início de série do Windows não está a acontecer com esta conta Azure AD.

> [!NOTE]
> Os fornecedores de identidade de terceiros precisam de apoiar o protocolo WS-Trust para permitir a emissão de PRT em dispositivos Windows 10. Sem o WS-Trust, o PRT não pode ser emitido aos utilizadores em dispositivos híbridos Azure AD ou Azure AD. No ADFS são necessários apenas pontos finais com nome de utilizador. Tanto adfs/serviços/trust/2005/windowstransport e adfs/services/trust/13/windowstransport devem ser ativados apenas como pontos finais virados para a intranet e **NÃO devem ser expostos** como pontos finais virados para a rede através do Proxy da Aplicação Web

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual é a vida de um prt?

Uma vez emitido, um PRT é válido por 14 dias e é continuamente renovado desde que o utilizador utilize o dispositivo ativamente.  

## <a name="how-is-a-prt-used"></a>Como é usado um PRT?

Um PRT é utilizado por dois componentes-chave no Windows:

* **Plugin Azure AD CloudAP**: Durante o súpr inserção do Windows, o plugin Azure AD CloudAP solicita um PRT a partir de Azure AD utilizando as credenciais fornecidas pelo utilizador. Também cache o PRT para ativar o sinal em cache quando o utilizador não tem acesso a uma ligação à Internet.
* **Plugin Azure AD WAM**: Quando os utilizadores tentam aceder a aplicações, o plugin AZure AD WAM utiliza o PRT para permitir o SSO no Windows 10. O plugin Azure AD WAM utiliza o PRT para solicitar tokens de atualização e acesso para aplicações que dependem do WAM para pedidos simbólicos. Também permite sSO nos navegadores injetando o PRT em pedidos de navegador. O Browser SSO no Windows 10 é suportado no Microsoft Edge (nativamente) e no Chrome (através das contas windows [10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) ou extensões [Do Office Online).](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en)

## <a name="how-is-a-prt-renewed"></a>Como é que um PRT é renovado?

Um PRT é renovado em dois métodos diferentes:

* **Ficha Azure AD CloudAP a cada 4 horas**: O plugin CloudAP renova o PRT a cada 4 horas durante o iniciar sação no Windows. Se o utilizador não tiver ligação à Internet durante esse tempo, o plugin CloudAP renovará o PRT depois de o dispositivo estar ligado à internet.
* **Plugin Azure AD WAM durante os pedidos de ficha de aplicação**: O plugin WAM permite sSO em dispositivos Windows 10, permitindo pedidos de token silenciosos para aplicações. O plugin WAM pode renovar o PRT durante estes pedidos simbólicos de duas maneiras diferentes:
   * Uma aplicação solicita ao WAM um token de acesso silenciosamente, mas não há nenhum token de atualização disponível para essa aplicação. Neste caso, a WAM usa o PRT para solicitar um token para a app e recebe de volta um novo PRT na resposta.
   * Uma aplicação solicita ao WAM um token de acesso, mas o PRT é inválido ou a AZure AD requer autorização adicional (por exemplo, Autenticação Multi-Factor AD Azure). Neste cenário, a WAM inicia uma logon interativa que exige ao utilizador a reauthenticação ou a prestação de verificação adicional e é emitido um novo PRT sobre a autenticação bem sucedida.

Num ambiente ADFS, a linha de visão direta para o controlador de domínio não é necessária para renovar o PRT. A renovação do PRT requer apenas /adfs/serviços/trust/2005/usernamemixed e /adfs/services/trust/13/usernamemixed endpoints habilitados no proxy através da utilização do protocolo WS-Trust.

Os pontos finais de transporte do Windows são necessários para a autenticação de senha apenas quando uma palavra-passe é alterada, e não para renovação de PRT.

### <a name="key-considerations"></a>Considerações principais

* Um PRT só é emitido e renovado durante a autenticação de aplicações nativas. Um PRT não é renovado ou emitido durante uma sessão de navegador.
* Em Azure AD juntou-se e híbrido Azure AD adpôs, o plugin CloudAP é a principal autoridade para um PRT. Se um PRT for renovado durante um pedido simbólico baseado na WAM, o PRT é devolvido ao plugin CloudAP, que verifica a validade do PRT com Azure AD antes de aceitá-lo.

## <a name="how-is-the-prt-protected"></a>Como é que o PrT está protegido?

Um PRT está protegido ligando-o ao dispositivo ao qual o utilizador assinou. O Azure AD e o Windows 10 permitem a proteção prt através dos seguintes métodos:

* Durante o **primeiro sinal:** Durante o primeiro sinal, é emitido um PRT assinando pedidos utilizando a chave do dispositivo criptograficamente gerada durante o registo do dispositivo. Num dispositivo com um TPM válido e funcional, a tecla do dispositivo é fixada pelo TPM impedindo qualquer acesso malicioso. Não é emitida uma RPT se a assinatura da chave do dispositivo correspondente não puder ser validada.
* **Durante os pedidos simbólicos e a renovação**: Quando um PRT é emitido, a Azure AD também emite uma chave de sessão encriptada para o dispositivo. É encriptado com a chave de transporte público (tkpub) gerada e enviada para a Azure AD como parte do registo do dispositivo. Esta chave de sessão só pode ser desencriptada pela chave de transporte privado (tkpriv) assegurada pelo TPM. A chave da sessão é a chave de Prova de Posse (POP) para quaisquer pedidos enviados para a Azure AD.  A chave de sessão também está protegida pelo TPM e nenhum outro componente de SO pode aceder-lhe. Os pedidos de token ou pedidos de renovação de PRT são assinados de forma segura por esta chave de sessão através do TPM e, portanto, não podem ser adulterados. O Azure AD invalidará quaisquer pedidos do dispositivo que não sejam assinados pela tecla de sessão correspondente.

Ao assegurar estas chaves com o TPM, aumentamos a segurança do PRT de atores maliciosos que tentam roubar as chaves ou reproduzir o PrT.  Assim, a utilização de um TPM aumenta consideravelmente a segurança da Azure AD Joined, a Hybrid Azure AD juntou-se, e a Azure AD registou dispositivos contra roubo de credenciais. Para desempenho e fiabilidade, O TPM 2.0 é a versão recomendada para todos os cenários de registo de dispositivos AD Azure no Windows 10. A partir da atualização do Windows 10, 1903, o Azure AD não utiliza o TPM 1.2 para qualquer uma das teclas acima devido a problemas de fiabilidade. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Como são protegidas as fichas de aplicação e os cookies do navegador?

**Fichas de aplicação**: Quando uma aplicação solicita token através do WAM, a Azure AD emite um token de atualização e um token de acesso. No entanto, a WAM apenas devolve o token de acesso à aplicação e protege o token de atualização na sua cache, encriptando-o com a chave de programação da aplicação de proteção de dados do utilizador (DPAPI). A WAM usa de forma segura o token de atualização assinando pedidos com a chave da sessão para emitir mais tokens de acesso. A chave DPAPI é fixada por uma chave simétrica baseada em Azure em Azure AD em si. Quando o dispositivo precisa de desencriptar o perfil do utilizador com a tecla DPAPI, o Azure AD fornece a chave DPAPI encriptada pela tecla de sessão, que o plugin CloudAP solicita tPM para desencriptar. Esta funcionalidade garante a consistência na garantia de tokens de atualização e evita aplicações que implementam os seus próprios mecanismos de proteção.  

**Cookies de navegador**: No Windows 10, o Azure AD suporta o navegador SSO no Internet Explorer e Microsoft Edge de forma nativa ou no Google Chrome através da extensão de contas do Windows 10. A segurança é construída não só para proteger os cookies, mas também para os pontos finais para os quais os cookies são enviados. Os cookies do navegador são protegidos da mesma forma que um PRT, utilizando a chave de sessão para assinar e proteger os cookies.

Quando um utilizador inicia uma interação de navegador, o navegador (ou extensão) invoca um anfitrião de cliente nativo COM. O anfitrião do cliente nativo garante que a página é de um dos domínios permitidos. O navegador poderia enviar outros parâmetros para o anfitrião do cliente nativo, incluindo um nonce, no entanto o anfitrião do cliente nativo garante validação do nome anfitrião. O anfitrião do cliente nativo solicita um cookie PRT do plugin CloudAP, que cria e assina com a chave de sessão protegida por TPM. Como o cookie PRT é assinado pela chave da sessão, é muito difícil de adulterar. Este cookie PRT está incluído no cabeçalho de pedido da Azure AD para validar o dispositivo de onde provém. Se utilizar o navegador Chrome, apenas a extensão explicitamente definida no manifesto do anfitrião do cliente nativo pode invocar o mesmo impedindo que extensões arbitrárias não escamem estes pedidos. Assim que o Azure AD validar o cookie PRT, emite um cookie de sessão para o navegador. Este cookie de sessão também contém a mesma chave de sessão emitida com um PRT. Durante os pedidos subsequentes, a tecla de sessão é validada efetivamente ligando o cookie ao dispositivo e evitando repetições de outros lugares.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando é que um PRT recebe uma reclamação de MFA?

Um PRT pode obter uma reivindicação de autenticação multi-factor (MFA) em cenários específicos. Quando um PRT baseado em MFA é usado para solicitar fichas para aplicações, a alegação do MFA é transferida para esses tokens de aplicações. Esta funcionalidade proporciona uma experiência perfeita aos utilizadores, evitando o desafio MFA para cada app que a necessite. Um PRT pode obter uma reclamação de MFA das seguintes formas:

* **Iniciar sposição com o Windows Hello for Business**: Windows Hello for Business substitui palavras-passe e utiliza chaves criptográficas para fornecer uma autenticação forte de dois fatores. O Windows Hello for Business é específico de um utilizador num dispositivo, e por si só requer que o MFA seja prospeito. Quando um utilizador faz login com o Windows Hello for Business, o PRT do utilizador recebe uma reclamação de MFA. Este cenário aplica-se também aos utilizadores que iniciam sessão com smartcards se a autenticação smartcard produzir uma reclamação de MFA da ADFS.
   * Como o Windows Hello for Business é considerado autenticação multi-factor, a alegação do MFA é atualizada quando o prt em si é atualizado, de modo que a duração do MFA irá prolongar-se continuamente quando os utilizadores iniciarem sação com a WIndows Hello for Business
* **MFA durante o sinal interativo WAM em**: Durante um pedido simbólico através do WAM, se um utilizador for obrigado a fazer MFA para aceder à app, o PRT que é renovado durante esta interação é impresso com uma reivindicação de MFA.
   * Neste caso, a reclamação do MFA não é atualizada continuamente, pelo que a duração do MFA baseia-se na duração do tempo de vida definido no diretório.
   * Quando um PRT e RT existentes anteriores forem utilizados para o acesso a uma app, o PRT e o RT serão considerados como a primeira prova de autenticação. Será necessária uma nova AT com uma segunda prova e uma reclamação de MFA impressa. Isto também emitirá um novo PRT e RT.
* **MFA durante o registo do dispositivo**: Se um administrador tiver configurado as definições do seu dispositivo em Azure AD para exigir que o [MFA registe dispositivos,](device-management-azure-portal.md#configure-device-settings)o utilizador precisa de fazer MFA para completar o registo. Durante este processo, o PRT que é emitido ao utilizador tem a reclamação do MFA obtida durante o registo. Esta capacidade aplica-se apenas ao utilizador que fez a operação de junção, e não a outros utilizadores que se inscrevam nesse dispositivo.
   * Semelhante ao sinal interativo WAM, a alegação do MFA não é atualizada continuamente, pelo que a duração do MFA baseia-se no conjunto de vida útil definido no diretório.

O Windows 10 mantém uma lista de PrTs divididos para cada credencial. Então, há um PRT para cada um do Windows Hello para Negócios, senha ou smartcard. Esta partição garante que as alegações de MFA são isoladas com base na credencial utilizada e não misturadas durante os pedidos simbólicos.

## <a name="how-is-a-prt-invalidated"></a>Como é que um PRT é invalidado?

Um PRT é invalidado nos seguintes cenários:

* **Utilizador inválido**: Se um utilizador for eliminado ou desativado em Azure AD, o seu PRT é invalidado e não pode ser utilizado para obter fichas para aplicações. Se um utilizador eliminado ou desativado já ter assinado um dispositivo antes, o login em cache iniciar-se-ia até que o CloudAP tenha conhecimento do seu estado inválido. Uma vez que o CloudAP determina que o utilizador é inválido, bloqueia os logons subsequentes. Um utilizador inválido é automaticamente bloqueado de iniciar sôms para novos dispositivos que não têm as suas credenciais em cache.
* **Dispositivo inválido**: Se um dispositivo for eliminado ou desativado em Azure AD, o PRT obtido nesse dispositivo é invalidado e não pode ser utilizado para obter fichas para outras aplicações. Se um utilizador já estiver inscrito num dispositivo inválido, pode continuar a fazê-lo. Mas todos os tokens do dispositivo são invalidados e o utilizador não tem SSO para quaisquer recursos a partir desse dispositivo.
* **Alteração da palavra-passe**: Depois de um utilizador alterar a sua palavra-passe, o PRT obtido com a senha anterior é invalidado pela Azure AD. A alteração da palavra-passe resulta na obtenção de um novo PRT. Esta invalidação pode acontecer de duas maneiras diferentes:
   * Se o utilizador entrar no Windows com a sua nova senha, o CloudAP descarta o antigo PRT e pede ao Azure AD que emita um novo PRT com a sua nova senha. Se o utilizador não tiver uma ligação à Internet, a nova palavra-passe não pode ser validada, o Windows poderá exigir que o utilizador introduza a sua antiga senha.
   * Se um utilizador tiver iniciado sessão com a sua senha antiga ou tiver alterado a sua palavra-passe após a sua assinatura no Windows, o antigo PRT é utilizado para quaisquer pedidos de fichas baseados no WAM. Neste cenário, o utilizador é solicitado a reautenticar durante o pedido de token WAM e é emitido um novo PRT.
* **Problemas de TPM**: Por vezes, o TPM de um dispositivo pode vacilar ou falhar, levando à inacessibilidade das teclas asseguradas pelo TPM. Neste caso, o dispositivo é incapaz de obter um PRT ou de solicitar fichas usando um PRT existente, uma vez que não pode provar a posse das chaves criptográficas. Como resultado, qualquer PRT existente é invalidado pela Azure AD. Quando o Windows 10 deteta uma falha, inicia um fluxo de recuperação para voltar a registar o dispositivo com novas teclas criptográficas. Com a participação do Hybrid Azure Ad, tal como o registo inicial, a recuperação acontece silenciosamente sem a entrada do utilizador. Para os dispositivos registados Azure AD ou Azure AD, a recuperação tem de ser realizada por um utilizador que tenha privilégios de administrador no dispositivo. Neste cenário, o fluxo de recuperação é iniciado por um pedido do Windows que orienta o utilizador a recuperar com sucesso o dispositivo.

## <a name="detailed-flows"></a>Fluxos detalhados

Os seguintes diagramas ilustram os detalhes subjacentes na emissão, renovação e utilização de um PRT para solicitar um sinal de acesso para uma aplicação. Além disso, estes passos também descrevem como os mecanismos de segurança acima referidos são aplicados durante estas interações.

### <a name="prt-issuance-during-first-sign-in"></a>Emissão prt durante o primeiro sinal em

![Emissão prt durante o primeiro sinal em fluxo detalhado](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Nos dispositivos aderidos a Azure AD, esta troca ocorre sincronizadamente para emitir um PRT antes que o utilizador possa apresentar-se ao Windows. Nos dispositivos híbridos Azure AD, o Ative Directory no local é a principal autoridade. Assim, o utilizador só está à espera até que possa adquirir um TGT para iniciar sessão, enquanto a emissão de PRT acontece de forma assíncronea. Este cenário não se aplica aos dispositivos registados Azure AD, uma vez que o início de são não utiliza credenciais AZURE AD.

| Passo | Description |
| :---: | --- |
| A | O utilizador introduz a sua palavra-passe no sinal na UI. LogonUI passa as credenciais num tampão de auth para LSA, que por sua vez passa internamente para cloudAP. O CloudAP remete este pedido para o plugin CloudAP. |
| B | O plugin CloudAP inicia um pedido de descoberta de reinos para identificar o fornecedor de identidade para o utilizador. Se o inquilino do utilizador tiver uma configuração de fornecedor de federação, a Azure AD devolve o ponto final de câmbio do fornecedor de metadados (MEX). Caso contrário, o Azure AD devolve que o utilizador é gerido indicando que o utilizador pode autenticar com Azure AD. |
| C | Se o utilizador for gerido, o CloudAP receberá o nonce do Azure AD. Se o utilizador for federado, o plugin CloudAP solicita um token SAML ao provedor da federação com as credenciais do utilizador. Uma vez que recebe, o token SAML, solicita um nó da Azure AD. |
| D | O plugin CloudAP constrói o pedido de autenticação com as credenciais do utilizador, nonce, e um âmbito de corretagem, assina o pedido com a tecla Dispositivo (dkpriv) e envia-o para Azure AD. Num ambiente federado, o plugin CloudAP utiliza o token SAML devolvido pelo provedor da federação em vez das credenciais do utilizador. |
| E | A Azure AD valida as credenciais do utilizador, o nonce e a assinatura do dispositivo, verifica que o dispositivo é válido no arrendatário e emite o PRT encriptado. Juntamente com o PRT, a Azure AD também emite uma chave simétrica, chamada tecla session encriptada pela Azure AD usando a chave de transporte (tkpub). Além disso, a tecla Session também está incorporada no PRT. Esta sessão funciona como a chave de prova de posse (PoP) para pedidos subsequentes com o PRT. |
| F | O plugin CloudAP passa a tecla prt e session encriptada para o CloudAP. O CloudAP solicita ao TPM que desencripte a tecla 'Sessão' utilizando a tecla De Transporte (tkpriv) e a reencrimeira usando a chave própria do TPM. O CloudAP armazena a chave de Sessão encriptada na sua cache juntamente com o PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renovação do PRT em logons subsequentes

![Renovação do PRT em logons subsequentes](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Passo | Description |
| :---: | --- |
| A | O utilizador introduz a sua palavra-passe no sinal na UI. LogonUI passa as credenciais num tampão de auth para LSA, que por sua vez passa internamente para cloudAP. O CloudAP remete este pedido para o plugin CloudAP. |
| B | Se o utilizador tiver iniciado o login anteriormente no utilizador, o Windows inicia o login em cache e valida as credenciais para iniciar sessão no utilizador. A cada 4 horas, o plugin CloudAP inicia a renovação prt assíncrona. |
| C | O plugin CloudAP inicia um pedido de descoberta de reinos para identificar o fornecedor de identidade para o utilizador. Se o inquilino do utilizador tiver uma configuração de fornecedor de federação, a Azure AD devolve o ponto final de câmbio do fornecedor de metadados (MEX). Caso contrário, o Azure AD devolve que o utilizador é gerido indicando que o utilizador pode autenticar com Azure AD. |
| D | Se o utilizador for federado, o plugin CloudAP solicita um token SAML ao provedor da federação com as credenciais do utilizador. Uma vez que recebe, o token SAML, solicita um nó da Azure AD. Se o utilizador for gerido, o CloudAP obterá diretamente o nonce do Azure AD. |
| E | O plugin CloudAP constrói o pedido de autenticação com as credenciais do utilizador, nonce, e o PRT existente, assina o pedido com a tecla Session e envia-o para Azure AD. Num ambiente federado, o plugin CloudAP utiliza o token SAML devolvido pelo provedor da federação em vez das credenciais do utilizador. |
| F | O Azure AD valida a assinatura da chave Sessão comparando-a com a chave Sessão incorporada no PRT, valida o nonce e verifica se o dispositivo é válido no arrendatário e emite um novo PRT. Como visto anteriormente, o PRT é novamente acompanhado com a tecla Session encriptada pela chave de transporte (tkpub). |
| G | O plugin CloudAP passa a tecla prt e session encriptada para o CloudAP. O CloudAP solicita ao TPM que desencripte a tecla 'Sessão' utilizando a tecla De Transporte (tkpriv) e a reencrimar utilizando a própria chave do TPM. O CloudAP armazena a chave de Sessão encriptada na sua cache juntamente com o PRT. |

> [!NOTE]
> Um PRT pode ser renovado externamente sem a necessidade de uma ligação VPN quando os pontos finais do utilizador são ativados externamente.

### <a name="prt-usage-during-app-token-requests"></a>Utilização prt durante pedidos de ficha de aplicação

![Utilização prt durante pedidos de ficha de aplicação](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Passo | Description |
| :---: | --- |
| A | Uma aplicação (por exemplo, Outlook, OneNote, etc.) inicia um pedido simbólico à WAM. A WAM, por sua vez, pede ao plugin Azure AD WAM para servir o pedido simbólico. |
| B | Se já estiver disponível um token Refresh para a aplicação, o plugin Azure AD WAM utiliza-o para solicitar um token de acesso. Para fornecer provas de ligação do dispositivo, o plugin WAM assina o pedido com a tecla Session. O Azure AD valida a tecla Session e emite um token de acesso e um novo token de atualização para a aplicação, encriptado pela tecla Session. O plugin WAM solicita plugin Cloud AP para desencriptar os tokens, que, por sua vez, solicita ao TPM para desencriptar usando a tecla Session, resultando em plugin WAM recebendo ambos os tokens. Em seguida, o plugin WAM fornece apenas o token de acesso à aplicação, enquanto reencripta o token de atualização com DPAPI e armazena-o na sua própria cache  |
| C |  Se não estiver disponível um token Refresh para a aplicação, o plugin Azure AD WAM utiliza o PRT para solicitar um token de acesso. Para fornecer provas de posse, o plugin WAM assina o pedido que contém o PRT com a tecla Session. O Azure AD valida a assinatura da chave Sessão comparando-a com a chave Sessão incorporada no PRT, verifica que o dispositivo é válido e emite um token de acesso e um token de atualização para a aplicação. além disso, o Azure AD pode emitir um novo PRT (baseado no ciclo de atualização), todos eles encriptados pela tecla Session. |
| D | O plugin WAM solicita plugin Cloud AP para desencriptar os tokens, que, por sua vez, solicita ao TPM para desencriptar usando a tecla Session, resultando em plugin WAM recebendo ambos os tokens. Em seguida, o plugin WAM fornece apenas o token de acesso à aplicação, enquanto ele reencriptou o token de atualização com DPAPI e armazena-o na sua própria cache. O plugin WAM utilizará o token de atualização que vai para a frente para esta aplicação. O plugin WAM também devolve o novo plugin PRT à Cloud AP, que valida o PRT com Azure AD antes de atualizá-lo na sua própria cache. O plugin Cloud AP utilizará o novo PRT para a frente. |
| E | A WAM fornece o token de acesso recém-emitido à WAM, que por sua vez, fornece-o de volta ao pedido de chamada|

### <a name="browser-sso-using-prt"></a>SSO do navegador usando PRT

![SSO do navegador usando PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Passo | Description |
| :---: | --- |
| A | O utilizador entra no Windows com as suas credenciais para obter um PRT. Assim que o utilizador abrir o navegador, o navegador (ou extensão) carrega os URLs a partir do registo. |
| B | Quando um utilizador abre um URL de login Azure AD, o navegador ou extensão valida o URL com os obtidos a partir do registo. Se corresponderem, o navegador invoca o anfitrião do cliente nativo para obter um token. |
| C | O anfitrião do cliente nativo valida que os URLs pertencem aos fornecedores de identidade da Microsoft (conta Microsoft ou AD AZure), extrai um nonce enviado do URL e faz uma chamada para o plugin CloudAP para obter um cookie PRT. |
| D | O plugin CloudAP criará o cookie PRT, iniciará sessão com a tecla de sessão ligada ao TPM e enviará-o de volta para o anfitrião do cliente nativo. |
| E | O anfitrião do cliente nativo devolverá este cookie PRT ao navegador, que o incluirá como parte do cabeçalho de pedido chamado x-ms-RefreshTokenCredential e solicitará fichas da Azure AD. |
| F | O Azure AD valida a assinatura da chave Sessão no cookie PRT, valida o nonce, verifica se o dispositivo é válido no inquilino, e emite um token de ID para a página web e um cookie de sessão encriptado para o navegador. |

> [!NOTE]
> O fluxo SSO do navegador descrito nos passos acima não se aplica a sessões em modos privados como o InPrivate no Microsoft Edge ou Incógnito no Google Chrome (quando se utiliza a extensão das Contas Microsoft).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre problemas relacionados com o PRT, consulte o artigo [O Diretório híbrido Azure Ative de resolução de problemas juntou-se aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
