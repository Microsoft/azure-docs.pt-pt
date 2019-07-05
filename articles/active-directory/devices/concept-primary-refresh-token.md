---
title: Token de atualização principal (PRT) e o Azure AD – o Azure Active Directory
description: O que é a função do e como gerir o primário atualizar Token (PRT) no Azure Active Directory?
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
ms.openlocfilehash: 5e195a93209875b9eabfaa2ad00772281922443c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476117"
---
# <a name="what-is-a-primary-refresh-token"></a>O que é um primário atualizar Token?

Um principal atualizar Token (PRT) é um artefato de chave de autenticação do Azure AD em dispositivos Android, iOS e Windows 10. É um JSON Web Token (JWT) especialmente emitido para o Microsoft primeiro terceiros token mediadores para ativar o início de sessão único (SSO) entre os aplicativos usados nesses dispositivos. Neste artigo, forneceremos detalhes sobre como um PRT está emitido, utilizado e protegido em dispositivos Windows 10.

Este artigo pressupõe que já compreende os Estados de dispositivo diferentes disponíveis no Azure AD e como único início de sessão funciona no Windows 10. Para obter mais informações sobre os dispositivos no Azure AD, consulte o artigo [o que é a gestão de dispositivos no Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Componentes e terminologia-chave

Os seguintes componentes do Windows exercem um papel fundamental na pedir e utilização de um PRT:

* **Fornecedor de autenticação de cloud** (CloudAP): CloudAP é o fornecedor de autenticação moderna para o Windows iniciar sessão, que verifica os utilizadores que iniciem a um dispositivo Windows 10. CloudAP fornece uma estrutura de plug-in, essa identidade provedores podem se basear para ativar a autenticação do Windows com as credenciais do fornecedor de identidade.
* **Gestor de conta de Web** (WAM): WAM é o predefinição do Mediador de token em dispositivos Windows 10. WAM fornece também uma estrutura de plug-in, essa identidade provedores podem aumentar e ativar o SSO para seus aplicativos contando com esse provedor de identidade.
* **Plug-in do Azure AD CloudAP**: Plugin de específica do Azure AD criado no CloudAP framework, que verifica as credenciais de utilizador com o Azure AD durante a sessão do Windows.
* **Plug-in do Azure AD WAM**: Plugin de específica do Azure AD criado no framework WAM, que permite o SSO para aplicações que dependem do Azure AD para autenticação.
* **Dsreg**: Um componente específico do Azure AD no Windows 10, que lida com o processo de registo do dispositivo para todos os Estados de dispositivo.
* **Trusted Platform Module** (TPM): Um TPM é um componente de hardware num dispositivo, que fornece funções de segurança baseada em hardware de segredos do utilizador e dispositivo. Podem encontrar mais detalhes no artigo [Trusted Platform módulo visão geral da tecnologia](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>O que o contém PRT?

Um PRT contém declarações geralmente contidas em qualquer token de atualização do Azure AD. Além disso, existem algumas declarações de específicos de dispositivos incluídas em PRT. Estas são as seguintes:

* **ID de dispositivo**: Um PRT é emitido para um usuário num dispositivo específico. A afirmação de ID de dispositivo `deviceID` determina PRT foi emitido para o utilizador no dispositivo. Esta afirmação mais tarde é emitida para tokens de obteve por meio de PRT. A afirmação de ID de dispositivo é utilizada para determinar a autorização de acesso condicional com base no estado do dispositivo ou conformidade.
* **Chave de sessão**: A chave de sessão é uma chave simétrica criptografada, gerado pelo serviço de autenticação do Azure AD, emitido como parte de PRT. A chave de sessão funciona como prova de posse quando um PRT é usado para obter os tokens para outras aplicações.

### <a name="can-i-see-whats-in-a-prt"></a>Posso ver o que está num PRT?

Um PRT é um blob opaco enviado a partir do Azure AD, cujo conteúdo não é conhecido por quaisquer componentes de cliente. Não pode ver o que há dentro de um PRT.

## <a name="how-is-a-prt-issued"></a>Como é emitido um PRT?

Registo de dispositivos é um pré-requisito para a autenticação de dispositivo com base no Azure AD. Um PRT é emitido para os utilizadores apenas em dispositivos registados. Para obter detalhes mais aprofundados sobre o registo de dispositivos, consulte o artigo [Windows Hello para empresas e o registo de dispositivos](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante o registo do dispositivo, o componente de dsreg gera dois conjuntos de pares de chaves criptográficas:

* Chave do dispositivo (dkpub/dkpriv)
* Chave de transporte (tkpub/tkpriv)

As chaves privadas são vinculadas para TPM o dispositivo, se o dispositivo tiver um TPM válido e a funcionar, enquanto as chaves públicas são enviadas para o Azure AD durante o processo de registo do dispositivo. Estas chaves são utilizadas para validar o estado do dispositivo durante os pedidos PRT.

PRT é emitido durante a autenticação de utilizador num dispositivo Windows 10 em dois cenários:

* **Associados ao Azure AD** ou **Azure AD híbrido associou**: Um PRT é emitido durante o início de sessão do Windows, quando um utilizador inicia sessão com as credenciais da sua organização. Um PRT é emitido com todas as credenciais do Windows 10 suportados, por exemplo, palavra-passe e Windows Hello para empresas. Neste cenário, o plug-in do Azure AD CloudAP é a autoridade de principal para PRT.
* **O Azure AD registado o dispositivo**: Quando um utilizador adicionar uma conta profissional secundário para o respetivo dispositivo Windows 10, é emitido um PRT. Os usuários podem adicionar uma conta para o Windows 10 de duas formas diferentes-  
   * A adicionar uma conta através da **utilizar esta conta em qualquer lugar neste dispositivo** pedido depois de iniciar sessão a uma aplicação (por exemplo, Outlook)
   * Adicionar uma conta a partir **configurações** > **contas** > **acesso profissional ou escolar** > **Connect**

Em cenários de dispositivo do Azure AD registado, o plug-in do Azure AD WAM é a autoridade de principal para PRT, uma vez que o início de sessão do Windows não está a acontecer com esta conta do Azure AD.

> [!NOTE]
> fornecedores de identidade de terceiros 3º necessário suportar o protocolo WS-Trust para ativar a emissão de PRT em dispositivos Windows 10. Sem o WS-Trust, PRT não podem ser emitidos para os utilizadores no Azure híbridos associados ao AD ou do Azure AD dispositivos associados

## <a name="what-is-the-lifetime-of-a-prt"></a>O que é o tempo de vida de um PRT?

Depois de emitido, um PRT é válido durante 14 dias e continuamente for renovada, desde que o utilizador utiliza ativamente o dispositivo.  

## <a name="how-is-a-prt-used"></a>Como é utilizado um PRT?

Um PRT é utilizado por dois componentes-chave no Windows:

* **Plug-in do Azure AD CloudAP**: Durante o início de sessão do Windows no, o plug-in do Azure AD CloudAP solicita um PRT do Azure AD com as credenciais fornecidas pelo utilizador. Também coloca em cache PRT para ativar o início de sessão em cache quando o utilizador não tem acesso a uma ligação à internet.
* **Plug-in do Azure AD WAM**: Quando os utilizadores tentarem aceder a aplicações, o plug-in do Azure AD WAM utiliza PRT para ativar o SSO no Windows 10. Plug-in do Azure AD WAM utiliza PRT para solicitar tokens de atualização e de acesso para aplicações que dependem WAM para pedidos de token. Também permite a SSO em browsers injetando PRT em solicitações do navegador. Browser SSO no Windows 10 tem suporte no Microsoft Edge (nativo) e o Chrome (via a extensão de contas do Windows 10 ou o Office Online).

## <a name="how-is-a-prt-renewed"></a>Como é renovado um PRT?

Um PRT é renovado em dois métodos diferentes:

* **O Azure AD CloudAP Plug-in a cada 4 horas**: O plug-in de CloudAP renova PRT cada 4 horas durante a sessão do Windows. Se o utilizador não tem ligação à internet durante esse período, o plug-in do CloudAP renovará PRT depois do dispositivo está ligado à internet.
* **O Azure AD WAM Plug-in durante os pedidos de token de aplicação**: O plug-in WAM permite SSO em dispositivos Windows 10, permitindo que os pedidos de token silenciosas para aplicações. O plug-in WAM pode renovar PRT durante estes pedidos de token de duas formas diferentes:
   * Um aplicativo solicita WAM para um token de acesso silenciosamente, mas não existe nenhum token de atualização disponível para essa aplicação. Neste caso, WAM utiliza PRT para pedir um token para a aplicação e recebe de volta um novo PRT na resposta.
   * Um aplicativo solicita WAM para um token de acesso, mas PRT é inválido ou Azure AD requer autorização adicionais (por exemplo, o Azure multi-factor Authentication). Neste cenário, WAM inicia um logon interativo, exigir que o utilizador autenticar ou forneçam uma verificação adicional e um novo PRT é emitido na autenticação com êxito.

### <a name="key-considerations"></a>Considerações principais

* Um PRT apenas é emitido e renovado durante a autenticação de aplicação nativa. Um PRT não é renovado ou emitida durante uma sessão do browser.
* No Azure AD associado e híbridos associados ao Azure AD dispositivos, o plug-in de CloudAP é a autoridade primária para um PRT. Se um PRT for renovada durante um pedido de token com base em WAM, PRT é enviado para CloudAP Plug-in do, que verifica a validade da PRT com o Azure AD antes de aceitar ele.

## <a name="how-is-the-prt-protected"></a>Como é que o PRT é protegida?

Um PRT está protegido pela ligação-o para o dispositivo, o utilizador tem sessão iniciada no. O Azure AD e Windows 10 ativar a proteção de PRT através dos seguintes métodos:

* **Durante o primeiro início de sessão**: Durante o primeiro início de sessão, um PRT emitido por assinatura de pedidos com a chave de dispositivo criptograficamente gerada durante o registo do dispositivo. Num dispositivo com um TPM válido e a funcionar, a chave de dispositivo é protegida mediante o TPM, impedindo que qualquer acesso malicioso. Não é emitido um PRT se não é possível validar a assinatura de chave de dispositivo correspondente.
* **Durante os pedidos de token e a renovação**: Quando é emitido um PRT, o Azure AD também emite uma chave de sessão encriptada no dispositivo. É encriptada com a chave de transporte público (tkpub) gerados e enviados para o Azure AD como parte do registo do dispositivo. Esta chave de sessão só pode ser desencriptado pela chave privada de transporte (tkpriv) protegida pelo TPM. A chave de sessão é a chave de prova de posse (POP) para todos os pedidos enviados para o Azure AD.  A chave de sessão também está protegida por TPM e nenhum outro componente do sistema operacional pode acessá-lo. Pedidos de token ou pedidos de renovação PRT com segurança são assinados por esta chave de sessão por meio do TPM e por este motivo, não podem ser violados. O Azure AD irá invalidar as solicitações do dispositivo que não estejam assinadas pela chave de sessão correspondente.

Ao proteger estas chaves com o TPM, atores maliciosos não podem roubar as chaves nem reproduzir PRT noutro local como o TPM não está acessível, mesmo que um atacante tenha posse física do dispositivo.  Assim, usar um TPM bastante reforça a segurança do Azure AD associado, Azure AD híbrido, e do Azure AD registado contra roubo de credenciais. Para o desempenho e confiabilidade, o TPM 2.0 é a versão recomendada para todos os cenários de registo de dispositivos do Azure AD no Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Como são tokens de aplicação e os cookies do browser protegidos?

**Tokens de aplicação**: Quando um aplicativo solicita token através do WAM, o Azure AD emite um token de atualização e um token de acesso. No entanto, WAM apenas devolve o token de acesso à aplicação e protege o token de atualização em seu cache, criptografando-a com a chave do usuário data protection application programação interface (DPAPI). WAM com segurança utiliza o token de atualização por pedidos de assinatura com a chave de sessão para ainda mais emitir tokens de acesso. A chave DPAPI é protegida por uma chave simétrica do Azure AD com base no próprio Azure AD. Quando o dispositivo tiver de desencriptar o perfil de utilizador com a chave DPAPI, o Azure AD fornece a encriptados pela chave de sessão, o plug-in do CloudAP solicita o TPM para desencriptar a chave de DPAPI. Esta funcionalidade garante a consistência na proteção de tokens de atualização e evita implementar seus próprios mecanismos de proteção de aplicações.  

**Cookies do browser**: No Windows 10, o Azure AD suporta browser SSO no Internet Explorer e o Microsoft Edge nativamente ou no Google Chrome via a extensão de contas do Windows 10. A segurança baseia-se não só para proteger os cookies, mas também os pontos finais para que os cookies são enviados. Cookies do browser estão protegidos da mesma forma um PRT é, utilizando a chave de sessão para iniciar sessão e proteger os cookies.

Quando um usuário inicia uma interação de navegador, o browser (ou a extensão) invoca um anfitrião de cliente nativo do COM. O anfitrião de cliente nativo garante que a página é a partir de um dos domínios permitidos. O navegador poderia enviar que outros parâmetros para o cliente nativo do anfitrião, incluindo um valor de uso único, no entanto o anfitrião de cliente nativo garante a validação do nome do anfitrião. O anfitrião de cliente nativo solicita um cookie de PRT CloudAP Plug-in do, que cria e assina-o com a chave de sessão protegido por TPM. Como o cookie de PRT está assinado pela chave de sessão, ele não pode ser violado. Este cookie de PRT está incluído no cabeçalho do pedido para o Azure AD para o dispositivo é proveniente de validar. Se utilizar o browser Chrome, o apenas a extensão explicitamente definida no manifesto do anfitrião de cliente nativo pode invocá-lo a impedir extensões arbitrárias de fazer essas solicitações. Assim que o Azure AD valida o cookie PRT, emite um cookie de sessão para o navegador. Este cookie de sessão também contém a mesma chave de sessão emitida com um PRT. Durante os pedidos subsequentes, a chave de sessão é validada com eficiência o cookie de enlace para o dispositivo e impedir repetições de noutro local.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando um PRT obter uma afirmação de MFA?

Um PRT pode obter uma afirmação de autenticação multifator (MFA) em cenários específicos. Quando um PRT baseado em MFA é utilizado para pedir tokens para aplicativos, a afirmação MFA é transferida para os tokens de aplicação. Esta funcionalidade fornece uma experiência perfeita para os usuários, impedindo a submissão da MFA para qualquer aplicativo que precise dela. Um PRT pode obter uma afirmação de MFA das seguintes formas:

* **Inicie sessão com o Windows Hello para empresas**: Windows Hello para empresas substitui as palavras-passe e utiliza as chaves criptográficas para fornecer autenticação de dois fatores forte. Windows Hello para empresas é específico para um utilizador num dispositivo, e por si só exige o MFA para aprovisionar. Quando um utilizador inicia sessão com a Windows Hello para empresas, PRT o utilizador obtém uma afirmação de MFA. Este cenário aplica-se também aos utilizadores iniciar sessão com smart cards, se a autenticação de smart card produz uma afirmação de MFA de AD FS.
   * Como o Windows Hello para empresas é considerado autenticação multifator, a afirmação MFA é atualizada quando PRT em si é atualizada, para que a duração MFA continuamente irá expandir quando os utilizadores iniciam sessão com o WIndows Hello para empresas
* **MFA durante o início de sessão interativo do WAM no**: Durante um pedido de token através do WAM, se um utilizador é necessário para fazer a MFA a aceder à aplicação, PRT que é renovado durante esta interação é imprinted com uma afirmação de MFA.
   * Neste caso, a afirmação do MFA não é atualizada continuamente, por isso, a duração MFA baseia-se o tempo de vida definido no diretório.
* **MFA durante o registo de dispositivo**: Se um administrador tiver configurado as definições de dispositivo no Azure AD [exigir a MFA registar dispositivos](device-management-azure-portal.md#configure-device-settings), o utilizador precisa para fazer a MFA para concluir o registo. Durante este processo, PRT emitida para o utilizador tem a afirmação MFA obtida durante o registo. Esta funcionalidade aplica-se apenas para o utilizador que realizou a operação de associação, não a outros utilizadores que iniciem sessão nesse dispositivo.
   * Assim como o início de sessão interativo no WAM, a afirmação do MFA não é atualizada continuamente, para que a duração MFA baseia-se o tempo de vida definido no diretório.

Windows 10 mantém uma lista particionada de PRTs para cada credencial. Então, há um PRT para cada um dos Windows Hello para empresas, palavra-passe ou smart card. Essa divisão garante que MFA afirmações estão isoladas com base na credencial utilizada e não misture durante os pedidos de token.

## <a name="how-is-a-prt-invalidated"></a>Como é invalidado um PRT?

Um PRT é invalidado nos seguintes cenários:

* **Utilizador inválido**: Se um utilizador for eliminado ou desativado no Azure AD, o respetivo PRT é invalidado e não pode ser utilizado para obter os tokens para aplicativos. Se um utilizador eliminado ou desativado tiver iniciado sessão num dispositivo antes, o início de sessão em cache seria iniciá-los, até que CloudAP esteja ciente de seu estado inválido. Assim que CloudAP determina que o utilizador é inválido, bloqueia a inícios de sessão subsequentes. Um utilizador inválido é impedido automaticamente de início de sessão para novos dispositivos que não têm as credenciais em cache.
* **Dispositivo inválido**: Se um dispositivo é eliminado ou desativado no Azure AD, PRT obtido em que o dispositivo é invalidado e não pode ser utilizado para obter os tokens para outras aplicações. Se um utilizador já tiver sessão iniciado dispositivo inválido, eles podem continuar a fazê-lo. No entanto, todos os tokens no dispositivo são também invalidados e o utilizador não tem SSO para todos os recursos de que o dispositivo.
* **Alteração de palavra-passe**: Depois de um utilizador altera a palavra-passe, PRT obtido com a palavra-passe anterior é invalidada pelo Azure AD. Resultados de alteração de palavra-passe do utilizador a obter um novo PRT. Este invalidação pode ocorrer de duas formas diferentes:
   * Se o utilizador inicia sessão no Windows com a nova palavra-passe, CloudAP descarta PRT antigo e pede-Azure AD para emitir um novo PRT com a nova palavra-passe. Se o utilizador não tem uma ligação à internet, não é possível validar a nova palavra-passe, o Windows podem exigir que o utilizador introduzir a palavra-passe antiga.
   * Se um utilizador tiver sessão iniciada com a palavra-passe antiga ou alterado a palavra-passe depois de iniciar sessão no Windows, PRT antiga é utilizada para qualquer pedido de token com base em WAM. Neste cenário, é pedido ao utilizador para autenticar durante o pedido de token WAM e um novo PRT é emitido.
* **Problemas TPM**: Às vezes, TPM um dispositivo pode falhas no ou falhar, o que leva a inaccessibility de chaves protegidas pelo TPM. Neste caso, o dispositivo é incapaz de obter um PRT ou pedir tokens usando um PRT existente, como ele não é possível provar a posse das chaves criptográficas. Como resultado, qualquer PRT existente é invalidada pelo Azure AD. Quando o Windows 10 Deteta uma falha, ele inicia um fluxo de recuperação para voltar a registar o dispositivo com novas chaves de criptografia. Com a associação de híbrida do Azure Ad, tal como o registo inicial, a recuperação acontece silenciosamente sem intervenção do utilizador. Para dispositivos registados do Azure AD associado ou do Azure AD, a recuperação tem de ser realizadas por um utilizador que tenha privilégios de administrador no dispositivo. Neste cenário, o fluxo de recuperação é iniciado por uma linha de comandos do Windows que orienta o usuário para recuperar com êxito o dispositivo.

## <a name="detailed-flows"></a>Fluxos detalhados

Os diagramas seguintes mostram os detalhes subjacentes em emitir, renovar e usando um PRT para pedir um token de acesso para uma aplicação. Além disso, essas etapas também descrevem como os mecanismos de segurança mencionadas anteriormente são aplicados durante essas interações.

### <a name="prt-issuance-during-first-sign-in"></a>Emissão de PRT durante o primeiro início de sessão

![Emissão de PRT durante primeiro início de sessão no flow detalhada](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> No Azure AD dispositivos associados a um, este exchange acontece de forma síncrona para emitir um PRT antes do utilizador pode iniciar sessão para Windows. Híbrido do Azure AD associado dispositivos, Active Directory no local é a autoridade primária. Então, o utilizador está apenas a aguardar até que eles podem adquirir uma TGT para iniciar sessão, enquanto a emissão de PRT acontece de forma assíncrona. Este cenário não é aplicável a dispositivos do Azure AD registado como o início de sessão não utiliza credenciais do Azure AD.

| Passo | Descrição |
| :---: | --- |
| A | Utilizador introduz a palavra-passe no início de sessão na interface do Usuário. Num buffer de autenticação, o LogonUI passa as credenciais para a LSA, que passa internamente para CloudAP. CloudAP reencaminha este pedido para o plug-in de CloudAP. |
| B | Plug-in do CloudAP inicia um pedido de deteção de realm para identificar o fornecedor de identidade do utilizador. Se o inquilino do utilizador tem uma configuração do fornecedor de Federação, o Azure AD devolve troca de metadados do fornecedor de Federação ponto final de ponto final (MEX). Se não estiver, o Azure AD devolve a que o utilizador for gerido, que indica que o utilizador pode autenticar com o Azure AD. |
| C | Se o utilizador for gerido, CloudAP irá obter o valor de uso único do Azure AD. Se o utilizador está federado, o plug-in do CloudAP solicita um token SAML do fornecedor de federação com as credenciais do utilizador. Assim que for recebida, o SAML token, ele solicita um nonce do Azure AD. |
| D | Plug-in do CloudAP constrói o pedido de autenticação com as credenciais do utilizador, o valor de uso único e um âmbito de Mediador, assina o pedido com a chave do dispositivo (dkpriv) e envia-os para o Azure AD. Num ambiente federado, o plug-in do CloudAP utiliza o token SAML devolvido pelo fornecedor de Federação em vez do utilizador ' credenciais. |
| E | O Azure AD valida as credenciais de utilizador, o valor de uso único, e assinatura de dispositivo, verifica que o dispositivo é válido no inquilino e emite PRT encriptado. Juntamente com PRT, o Azure AD também emite uma chave simétrica, chamada a chave de sessão encriptada pelo Azure AD com a chave de transporte (tkpub). Além disso, a chave de sessão também é incorporada nos PRT. Esta chave de sessão funciona como a chave de prova de posse (PoP) para as solicitações subseqüentes com PRT. |
| F | Plug-in do CloudAP transmite a chave encriptada PRT e de sessão para CloudAP. CloudAP solicitar o TPM para descriptografar a chave de sessão com a chave de transporte (tkpriv) e criptografá-los com a chave do TPM novamente. CloudAP armazena a chave de sessão encriptada em seu cache, juntamente com PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Renovação PRT em inícios de sessão subsequentes

![Renovação PRT em inícios de sessão subsequentes](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Passo | Descrição |
| :---: | --- |
| A | Utilizador introduz a palavra-passe no início de sessão na interface do Usuário. Num buffer de autenticação, o LogonUI passa as credenciais para a LSA, que passa internamente para CloudAP. CloudAP reencaminha este pedido para o plug-in de CloudAP. |
| B | Se o utilizador tem sessão iniciada anteriormente no utilizador, inicia do Windows em cache inicie sessão e valida as credenciais para iniciar o utilizador a sessão. Cada 4 horas, o plug-in de CloudAP inicia a renovação PRT forma assíncrona. |
| C | Plug-in do CloudAP inicia um pedido de deteção de realm para identificar o fornecedor de identidade do utilizador. Se o inquilino do utilizador tem uma configuração do fornecedor de Federação, o Azure AD devolve troca de metadados do fornecedor de Federação ponto final de ponto final (MEX). Se não estiver, o Azure AD devolve a que o utilizador for gerido, que indica que o utilizador pode autenticar com o Azure AD. |
| D | Se o utilizador está federado, o plug-in do CloudAP solicita um token SAML do fornecedor de federação com as credenciais do utilizador. Assim que for recebida, o SAML token, ele solicita um nonce do Azure AD. Se o utilizador for gerido, CloudAP diretamente de obter o valor de uso único do Azure AD. |
| E | Plug-in do CloudAP constrói o pedido de autenticação com as credenciais do usuário, o valor de uso único e PRT existente, assina o pedido com a chave de sessão e envia-os para o Azure AD. Num ambiente federado, o plug-in do CloudAP utiliza o token SAML devolvido pelo fornecedor de Federação em vez do utilizador ' credenciais. |
| F | Azure AD valida a assinatura de chave de sessão comparando-o em relação à chave de sessão incorporada no PRT, valida o valor de uso único e verifica se o dispositivo é válido no inquilino e emite um PRT novo. Como visto anteriormente, PRT novamente é acompanhado com a chave de sessão encriptada pela chave de transporte (tkpub). |
| G | Plug-in do CloudAP transmite a chave encriptada PRT e de sessão para CloudAP. CloudAP solicita o TPM para descriptografar a chave de sessão com a chave de transporte (tkpriv) e criptografá-los com a chave do TPM novamente. CloudAP armazena a chave de sessão encriptada em seu cache, juntamente com PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Utilização PRT durante os pedidos de token de aplicação

![Utilização PRT durante os pedidos de token de aplicação](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Passo | Descrição |
| :---: | --- |
| A | Um aplicativo (por exemplo, Outlook, etc. do OneNote) inicia um pedido de token para WAM. WAM, por sua vez, solicita o plug-in do Azure AD WAM para responder ao pedido de token. |
| B | Se um token de atualização para o aplicativo já estiver disponível, o plug-in do Azure AD WAM utiliza-o para solicitar um token de acesso. Para fornecer uma prova de associação de dispositivo, o plug-in do WAM assina o pedido com a chave de sessão. O Azure AD valida a chave de sessão e emite um token de acesso e um novo token de atualização para a aplicação, criptografada pela chave de sessão. Plug-in do WAM pedidos AP de Cloud Plug-in para desencriptar tokens, que, por sua vez, solicita o TPM para descriptografar a utilizar a chave de sessão, resultando em obter os tokens de ambos os plug-in do WAM. Em seguida, o plug-in do WAM fornece apenas o token de acesso ao aplicativo, enquanto encripta novamente o token de atualização com a DPAPI e armazena-os em seu próprio cache  |
| C |  Se um token de atualização para o aplicativo não estiver disponível, o plug-in do Azure AD WAM utiliza PRT para solicitar um token de acesso. Para fornecer uma prova de posse, o plug-in do WAM assina o pedido que contém PRT com a chave de sessão. O Azure AD valida a assinatura de chave de sessão comparando-o em relação à chave de sessão incorporada no PRT, verifica se o dispositivo é válido e emite um token de acesso e um token de atualização para a aplicação. Além disso, o Azure AD pode emitir um novo PRT (com base no ciclo de atualização), todos eles encriptados pela chave de sessão. |
| D | Plug-in do WAM pedidos AP de Cloud Plug-in para desencriptar tokens, que, por sua vez, solicita o TPM para descriptografar a utilizar a chave de sessão, resultando em obter os tokens de ambos os plug-in do WAM. Em seguida, o plug-in do WAM fornece apenas o token de acesso ao aplicativo, enquanto encripta novamente o token de atualização com a DPAPI e armazena-os em seu próprio cache. Plug-in do WAM irá utilizar o token de atualização daqui em diante para esta aplicação. Plug-in do WAM também oferece volta PRT novo ao AP de Cloud Plug-in do, que valida PRT com o Azure AD antes de atualizá-la em seu próprio cache. Plug-in de AP de cloud irá utilizar o novo PRT daqui em diante. |
| E | WAM fornece o token de acesso recentemente emitidas para WAM, que por sua vez, fornece-o novamente para o aplicativo de chamada|

### <a name="browser-sso-using-prt"></a>Browser SSO com PRT

![Browser SSO com PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Passo | Descrição |
| :---: | --- |
| A | O utilizador inicia sessão Windows com as respetivas credenciais para obter um PRT. Assim que o usuário abrir o navegador, o navegador (ou de extensão) carrega os URLs do registo. |
| B | Quando um utilizador abre um URL de início de sessão do Azure AD, o browser ou a extensão valida o URL por aqueles que o obtido do registo. Se coincidirem, o navegador invoca o anfitrião de cliente nativo para obter um token. |
| C | O anfitrião de cliente nativo valida os URLs que pertencem aos fornecedores de identidade da Microsoft (conta Microsoft ou do Azure AD), extrai um nonce enviado a partir do URL e faz uma chamada para o plug-in de CloudAP para obter um cookie PRT. |
| D | O plug-in de CloudAP irá criar o cookie PRT, inicie sessão com a chave de sessão ligado a TPM e enviará de volta para o anfitrião de cliente nativo. Como o cookie é assinado pela chave de sessão, ele não pode ser violado. |
| E | O anfitrião de cliente nativo irá devolver esse cookie PRT para o navegador, que irá incluir como parte do cabeçalho de pedido chamado tokens x-ms-RefreshTokenCredential e a pedido do Azure AD. |
| F | O Azure AD valida a assinatura de chave de sessão no PRT cookie, valida o valor de uso único, verifica se o dispositivo é válido no inquilino e emite um token de ID para a página da web e um cookie de sessão encriptada para o browser. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como resolver problemas relacionados com a PRT, consulte o artigo [híbrido de resolução de problemas do Azure Active Directory associou-se os dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
