---
title: Um sistema multi-DRM de proteção de conteúdos - Azure Media Services v3
description: Estes artigos dão uma descrição detalhada de como conceber um sistema de proteção de conteúdos multi-DRM com a Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161788"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso 

Conceber e construir um subsistema de Gestão de Direitos Digitais (DRM) para uma solução de streaming de topo (OTT) ou online é uma tarefa complexa. Operadores/fornecedores de vídeo online normalmente subcontratam esta tarefa a prestadores especializados de serviços DRM. O objetivo deste documento é apresentar um design de referência e uma implementação de referência de um subsistema DEDRDe de ponta a ponta numa solução de streaming OTT ou online.

Os leitores visados para este documento são engenheiros que trabalham em subsistemas DRM de OTT ou soluções de streaming/multiecrã on-line ou leitores interessados em subsistemas DRM. O pressuposto é que os leitores estão familiarizados com pelo menos uma das tecnologias DRM no mercado, tais como PlayReady, Widevine, FairPlay ou Adobe Access.

Nesta discussão, por multi-DRM incluímos os 3 DRMs suportados pela Azure Media Services: Common Encryption (CENC) para PlayReady e Widevine, FairPlay, bem como encriptação de chave clara AES-128. Uma grande tendência no streaming online e na indústria OTT é usar DRMs nativos em várias plataformas de clientes. Esta tendência é uma mudança em com para com a anterior que usou uma única DRM e o seu cliente SDK para várias plataformas de clientes. Quando utiliza o CENC com DRM multinativo, tanto o PlayReady como o Widevine são encriptados de acordo com a especificação de [Encriptação Comum (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Os benefícios da utilização de multi-DRM nativo para proteção de conteúdos são que:

* Reduz o custo de encriptação porque um único processo de encriptação é usado para direcionar diferentes plataformas com as suas DDR nativas.
* Reduz o custo de gestão dos ativos porque só é necessária uma única cópia do ativo no armazenamento.
* Elimina o custo de licenciamento do cliente DRM porque o cliente nativo da DRM é geralmente gratuito na sua plataforma nativa.

### <a name="goals-of-the-article"></a>Objetivos do artigo

Os objetivos deste artigo são:

* Forneça um design de referência de um subsistema DRM que utiliza todos os 3 DRMs (CENC para DASH, FairPlay para HLS e PlayReady para streaming suave).
* Forneça uma implementação de referência na plataforma Azure e Azure Media Services.
* Discuta alguns tópicos de design e implementação.

A tabela seguinte resume o suporte de DRM nativo em diferentes plataformas e suporte em EME em diferentes navegadores.

| **Cliente plataforma** | **DRM nativo** | **EME** |
| --- | --- | --- |
| **Smart TVs, STBs** | PlayReady, Widevine e/ou outros | Navegador/EME incorporado para PlayReady e/ou Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 para PlayReady|
| **Dispositivos Android (telefone, tablet, TV)** |Widevine |Cromo para Widevine |
| **iOS** | FairPlay | Safari para FairPlay (desde iOS 11.2) |
| **macOS** | FairPlay | Safari para FairPlay (desde Safari 9+ em Mac OS X 10.11+ El Capitan)|
| **tvOS** | FairPlay | |

Tendo em conta o estado atual de implantação de cada DRM, um serviço normalmente quer implementar duas ou três DDR para garantir que você aborda todos os tipos de pontos finais da melhor maneira.

Existe uma compensação entre a complexidade da lógica do serviço e a complexidade do lado do cliente para atingir um determinado nível de experiência do utilizador nos vários clientes.

Para fazer a sua seleção, lembre-se:

* O PlayReady é implementado de forma nativa em todos os dispositivos Windows, em alguns dispositivos Android, e está disponível através de SDKs de software em praticamente qualquer plataforma.
* A Widevine é implementada de forma nativa em todos os dispositivos Android, no Chrome e em alguns outros dispositivos. A Widevine também é suportada nos navegadores Firefox e Opera sobre o DASH.
* FairPlay está disponível em iOS, macOS e tvOS.


## <a name="a-reference-design"></a>Um design de referência
Esta secção apresenta um design de referência agnóstico às tecnologias utilizadas para implementá-lo.

Um subsistema DRM pode conter os seguintes componentes:

* Gestão de chaves
* Embalagem de encriptação DRM
* Entrega de licença de DRM
* Controlo de direitos/controlo de acesso
* Autenticação/autorização do utilizador
* Aplicativo de jogador
* Rede de entrega de origem/conteúdo (CDN)

O diagrama que se segue ilustra a interação de alto nível entre os componentes de um subsistema DRM:

![Subsistema DRM com CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

O design tem três camadas básicas:

* Uma camada de back-office (preto) não é exposta externamente.
* Uma camada de DMZ (azul escuro) contém todos os pontos finais que enfrentam o público.
* Uma camada pública de internet (azul claro) contém o CDN e jogadores com tráfego através da internet pública.

Deve também existir uma ferramenta de gestão de conteúdos para controlar a proteção de DRM, independentemente de ser encriptação estática ou dinâmica. As inputs para encriptação DRM incluem:

* Conteúdo de vídeo MBR
* Chave de conteúdo
* URLs de aquisição de licença

Aqui está o fluxo de alto nível durante o tempo de reprodução:

* O utilizador é autenticado.
* É criado um símbolo de autorização para o utilizador.
* O conteúdo protegido drm (manifesto) é descarregado para o leitor.
* O jogador submete um pedido de aquisição de licença aos servidores de licença juntamente com um ID chave e um símbolo de autorização.

A secção seguinte discute o desenho da gestão chave.

| **ContentKey-to-asset** | **Cenário** |
| --- | --- |
| 1 a 1 |O caso mais simples. Fornece o melhor controlo. Mas este acordo geralmente resulta no maior custo de entrega da licença. No mínimo, é necessário um pedido de licença para cada ativo protegido. |
| 1 a muitos |Pode usar a mesma chave de conteúdo para vários ativos. Por exemplo, para todos os ativos de um grupo lógico, como um género ou o subconjunto de um género (ou gene de filme), pode usar uma única chave de conteúdo. |
| Muitos para 1 |São necessárias múltiplas teclas de conteúdo para cada ativo. <br/><br/>Por exemplo, se precisar de aplicar uma proteção dinâmica do CENC com multi-DRM para MPEG-DASH e encriptação dinâmica AES-128 para HLS, precisa de duas teclas de conteúdo separadas. Cada chave de conteúdo precisa do seu próprio ContentKeyType. (Para a chave de conteúdo utilizada para uma proteção dinâmica do CENC, utilize contentKeyType.CommonEncryption. Para a chave de conteúdo utilizada para encriptação dinâmica AES-128, utilize ContentKeyType.EnvelopeEncryption.)<br/><br/>Como outro exemplo, na proteção do CENC do conteúdo dash, em teoria, pode utilizar uma chave de conteúdo para proteger o fluxo de vídeo e outra chave de conteúdo para proteger o fluxo de áudio. |
| Muitos para muitos |Combinação dos dois cenários anteriores. Um conjunto de chaves de conteúdo é usado para cada um dos múltiplos ativos do mesmo grupo de ativos. |

Outro fator importante a ter em conta é o uso de licenças persistentes e não persistentes.

Por que estas considerações são importantes?

Se utilizar uma nuvem pública para entrega de licença, as licenças persistentes e não persistentes têm um impacto direto no custo de entrega da licença. Os dois casos de design diferentes servem para ilustrar:

* Subscrição mensal: Utilize uma licença persistente e um mapeamento chave-a-activo de 1 a muitos conteúdos. Por exemplo, para todos os filmes infantis, usamos uma única chave de conteúdo para encriptação. Neste caso:

    Número total de licenças solicitadas para todos os filmes/dispositivos infantis = 1

* Subscrição mensal: Utilize uma licença não persistente e um mapeamento de 1 para 1 entre a chave de conteúdo e o ativo. Neste caso:

    Número total de licenças solicitadas para todos os filmes/dispositivos infantis = [número de filmes assistidos] x [número de sessões]

Os dois desenhos diferentes resultam em padrões de pedido de licença muito diferentes. Os diferentes padrões resultam em diferentes custos de entrega de licençase o serviço de entrega de licenças for fornecido por uma nuvem pública como os Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Design de mapa para tecnologia para implementação
Em seguida, o design genérico é mapeado para tecnologias na plataforma Azure/Media Services, especificando qual a tecnologia a utilizar para cada bloco de construção.

A tabela seguinte mostra o mapeamento.

| **Bloco de construção** | **Tecnologia** |
| --- | --- |
| **Leitor** |[Media Player do Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Fornecedor de Identidade (IDP)** |Azure Active Directory (Azure AD) |
| **Serviço de Token Seguro (STS)** |Azure AD |
| **Fluxo de trabalho de proteção de DRM** |Proteção dinâmica dos Serviços De Mídia Azure |
| **Entrega de licença DRM** |* Entrega de licença de Serviços de Media (PlayReady, Widevine, FairPlay) <br/>* Servidor de licença axinom <br/>* Servidor de licença PlayReady personalizado |
| **Origem** |Ponta final de streaming dos Serviços De Mídia Azure |
| **Gestão de chaves** |Não é necessário para a implementação de referência |
| **Gestão de conteúdos** |Uma aplicação de consola C# |

Por outras palavras, tanto o IDP como o STS são fornecidos pela Azure AD. O [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) é utilizado para o jogador. Tanto o Azure Media Services como o Azure Media Player suportam o CENC sobre o DASH, o FairPlay sobre o HLS, o PlayReady sobre o streaming suave e a encriptação AES-128 para DASH, HLS e smooth.

O diagrama seguinte mostra a estrutura e o fluxo globais com o mapeamento tecnológico anterior:

![CENC em Serviços de Media](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a proteção de conteúdos DRM, a ferramenta de gestão de conteúdos utiliza as seguintes inputs:

* Conteúdo aberto
* Chave de conteúdo da gestão chave
* URLs de aquisição de licença
* Uma lista de informações da Azure AD, tais como audiência, emitente e alegações simbólicas

Aqui está a saída da ferramenta de gestão de conteúdos:

* ContentKeyPolicy descreve modelo de licença DRM para cada tipo de DRM utilizado;
* ContentKeyPolicyRestriction descreve o controlo de acesso antes de uma licença de DRM ser emitida
* A política de streaming descreve as várias combinações de DRM - modo de encriptação - protocolo de streaming - formato de contentor, para streaming
* StreamingLocator descreve chave de conteúdo/IV usada para encriptação e URLs de streaming 

Aqui está o fluxo durante o tempo de execução:

* Após a autenticação do utilizador, é gerado um JWT.
* Uma das reivindicações contidas no JWT é uma alegação de grupos que contém o objeto de grupo ID EntitledUserGroup. Esta alegação é utilizada para passar a verificação de direitos.
* O leitor descarrega o manifesto cliente de conteúdo protegido pelo CENC e identifica o seguinte:
   * Identificação da chave.
   * O conteúdo está protegido pela DRM.
   * URLs de aquisição de licença.
* O jogador faz um pedido de aquisição de licença com base no suporte do navegador/DRM. No pedido de aquisição da licença, o ID chave e o JWT também são apresentados. O serviço de entrega de licençaverifica o JWT e as reclamações contidas antes de emitir a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
A implementação inclui os seguintes passos:

1. Prepare os ativos de teste. Encódigo/embale um vídeo de teste para MP4 fragmentado multibitado em Serviços de Media. Este ativo *não* está protegido pela DRM. A proteção drm é feita por proteção dinâmica mais tarde.

2. Crie um ID chave e uma chave de conteúdo (opcionalmente a partir de uma semente chave). Neste caso, o sistema de gestão chave não é necessário porque apenas uma chave de identificação e chave de conteúdo são necessárias para um par de ativos de teste.

3. Utilize a API dos Serviços de Media para configurar serviços de entrega de licenças multi-DRM para o ativo de teste. Se utilizar servidores de licença personalizados pela sua empresa ou pelos fornecedores da sua empresa em vez de serviços de licença em Serviços de Media, pode saltar este passo. Pode especificar URLs de aquisição de licença no passo quando configurar a entrega da licença. A API dos Serviços de Media é necessária para especificar algumas configurações detalhadas, tais como restrição de política de autorização e modelos de resposta à licença para diferentes serviços de licença de DRM. Neste momento, o portal Azure não fornece o UI necessário para esta configuração. Para obter informações ao nível da API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](protect-with-drm.md).

4. Utilize a API dos Serviços de Media para configurar a política de entrega de ativos para o ativo de teste. Para obter informações ao nível da API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](protect-with-drm.md).

5. Crie e configure um inquilino da Azure AD em Azure.

6. Crie algumas contas e grupos de utilizadores no seu inquilino Azure AD. Crie pelo menos um grupo "Utilizador Intitulado" e adicione um utilizador a este grupo. Os utilizadores deste grupo passam o cheque de direito na aquisição da licença. Os utilizadores que não se desloquem a passar a verificação de autenticação e não podem adquirir uma licença. A adesão a este grupo "Utilizador Intitulado" é uma reivindicação de grupos exigidos no JWT emitido pela Azure AD. Especifica este requisito de reclamação na etapa quando configura os serviços de entrega de licenças multi-DRM.

7. Crie uma aplicação ASP.NET MVC para hospedar o seu leitor de vídeo. Esta aplicação ASP.NET está protegida com autenticação do utilizador contra o inquilino da AD Azure. As reclamações adequadas estão incluídas nas fichas de acesso obtidas após a autenticação do utilizador. Recomendamos a OpenID Connect API para este passo. Instale os seguintes pacotes NuGet:

   * Pacote de instalação Microsoft.Azure.ActiveDirectory.GraphClient
   * Pacote de instalação Microsoft.Owin.Security.OpenIdConnect
   * Pacote de instalação Microsoft.Owin.Security.Cookies
   * Instalar-pacote Microsoft.Owin.Host.SystemWeb
   * Pacote de instalação Microsoft.IdentityModel.Clients.ActiveDirectory

8. Crie um jogador utilizando a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Utilize a [API Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual a tecnologia DRM a utilizar em diferentes plataformas de DRM.

9. A tabela seguinte mostra a matriz de teste.

    | **DRM** | **Browser** | **Resultado para utilizador intitulado** | **Resultado para utilizador sem direito** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge ou Internet Explorer 11 no Windows 10 |Sucesso |Reprovado |
    | **Widevine** |Chrome, Firefox, Ópera |Sucesso |Reprovado |
    | **FairPlay** |Safari no macOS      |Sucesso |Reprovado |
    | **AES-128** |Navegadores mais modernos  |Sucesso |Reprovado |

Para obter informações sobre como configurar o Azure AD para uma aplicação de jogador ASP.NET MVC, consulte [Integrar uma aplicação baseada em Azure Media Services OWIN MVC com o Azure Ative Directory e restringir](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)a entrega da chave de conteúdo com base em alegações da JWT.

Para mais informações, consulte a autenticação simbólica [jWT nos Serviços De Mídia Azure e encriptação dinâmica](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Para obter informações sobre o Azure AD:

* Pode encontrar informações sobre o desenvolvedor no guia do desenvolvedor do [Azure Ative Directory](../../active-directory/develop/v2-overview.md).
* Pode encontrar informações de administrador na [Administração do seu diretório de inquilinos Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Algumas questões na implementação

Utilize as seguintes informações de resolução de problemas para ajudar nos problemas de implementação.

* O URL emitente deve terminar com "/". O público deve ser o CLIENTE do cliente de aplicação de jogador. Além disso, adicione "/" no final do URL emitente.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    No [Descodificador JWT,](http://jwt.calebb.net/) **vê-se aud** e **iss,** como mostra o JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Adicione permissões à aplicação em Azure AD no separador **Configure** da aplicação. São necessárias permissões para cada aplicação, tanto nas versões locais como implementadas.

    ![Permissões](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Utilize o emitente correto quando configurar uma proteção dinâmica do CENC.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    O seguinte não funciona:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    O GUID é o ID de inquilino da AD Azure. O GUID pode ser encontrado no menu pop-up **Endpoints** no portal Azure.

* Grant associação de membros de grupo reclama privilégios. Certifique-se de que o seguinte está no ficheiro manifesto de aplicação Azure AD: 

    "GroupMembershipClaims": "All" (o valor predefinido é nulo)

* Detete o TokenType adequado quando criar requisitos de restrição.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Uma vez que adiciona suporte para JWT (Azure AD) para além do SWT (ACS), o TokenType predefinido é TokenType.JWT. Se utilizar o SWT/ACS, tem de definir o símbolo para TokenType.SWT.

## <a name="the-completed-system-and-test"></a>O sistema e teste completos

Esta secção acompanha-o através dos seguintes cenários no sistema final completo para que possa ter uma imagem básica do comportamento antes de obter uma conta de inscrição:

* Se precisar de um cenário não integrado:

    * Para os ativos de vídeo hospedados nos Serviços de Media que estejam protegidos ou protegidos por DRM, mas sem autenticação simbólica (emitindo uma licença a quem o solicitou), pode testá-lo sem assinar. Mude para HTTP se o seu streaming de vídeo estiver terminado http.

* Se precisar de um cenário integrado de ponta a ponta:

    * Para os ativos em vídeo sob a proteção dinâmica de DRM em Media Services, com a autenticação simbólica e JWT gerado pela Azure AD, você precisa iniciar o contrato.

Para a aplicação web do jogador e o seu início de sessão, consulte [este site](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Início de sessão do utilizador
Para testar o sistema de DRM integrado de ponta a ponta, é necessário ter uma conta criada ou adicionada.

Que conta?

Apesar de o Azure ter inicialmente permitido o acesso apenas pelos utilizadores da conta da Microsoft, o acesso é agora permitido pelos utilizadores de ambos os sistemas. Todas as propriedades azure agora confiam na Azure AD para autenticação, e a Azure AD autentica os utilizadores organizacionais. Foi criada uma relação com a federação onde a Azure AD confia no sistema de identidade de consumo da conta microsoft para autenticar os utilizadores de consumo. Como resultado, a Azure AD pode autenticar contas da Microsoft, bem como contas ad autóctones do Azure.

Uma vez que a Azure AD confia no domínio da conta da Microsoft, pode adicionar quaisquer contas de qualquer um dos seguintes domínios ao inquilino ad' personalizado do Azure e utilizar a conta para iniciar sessão:

| **Nome de domínio** | **Domínio** |
| --- | --- |
| **Domínio de inquilino ad ida e revestido de Azure** |somename.onmicrosoft.com |
| **Domínio corporativo** |microsoft.com |
| **Domínio da conta microsoft** |outlook.com, live.com, hotmail.com |

Pode contactar qualquer um dos autores para ter uma conta criada ou adicionada para si.

As seguintes imagens mostram diferentes páginas de inscrição usadas por diferentes contas de domínio:

Conta de domínio de inquilino ad ida **e reinado**por IA personalizado : A página de entrada personalizada do domínio personalizado do inquilino Azure AD.

![Conta de domínio de inquilino ad ida e da AD Personalizada](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com cartão inteligente**: A página de inscrição personalizada pela Microsoft corporate IT com autenticação de dois fatores.

![Conta de domínio de inquilino ad ida e reinado da AD Custom Azure dois](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft**: A página de sessão da conta microsoft para os consumidores.

![Conta de domínio de inquilino ad ida e revestido de Azure Personalizado três](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Utilize extensões de mídia encriptadas para playReady

Num navegador moderno com Extensões de Mídia Encriptadas (EME) para suporte PlayReady, como o Internet Explorer 11 no Windows 8.1 ou mais tarde e o navegador Microsoft Edge no Windows 10, o PlayReady é o DRM subjacente ao EME.

![Use eme para playReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

A área do leitor escuro é porque a proteção PlayReady impede-o de fazer uma captura de ecrã de vídeo protegido.

A seguinte imagem mostra os plug-ins do jogador e o suporte Microsoft Security Essentials (MSE)/EME:

![Plug-ins do jogador para playReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

O EME no Microsoft Edge e no Internet Explorer 11 no Windows 10 permite que o [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) seja invocado em dispositivos Windows 10 que o suportem. O PlayReady SL3000 desbloqueia o fluxo de conteúdo premium melhorado (4K, HDR) e novos modelos de entrega de conteúdos (para conteúdo melhorado).

Para se focar nos dispositivos Windows, o PlayReady é o único DRM disponível no hardware disponível nos dispositivos Windows (PlayReady SL3000). Um serviço de streaming pode utilizar o PlayReady através do EME ou através de uma aplicação Universal Windows Platform e oferecer uma maior qualidade de vídeo utilizando o PlayReady SL3000 do que outro DRM. Normalmente, o conteúdo até 2K flui através do Chrome ou Firefox, e o conteúdo até 4K flui através do Microsoft Edge/Internet Explorer 11 ou de uma aplicação Universal Windows Platform no mesmo dispositivo. O valor depende das definições de serviço e implementação.

#### <a name="use-eme-for-widevine"></a>Utilizar eme para widevine

Num navegador moderno com suporte EME/Widevine, como o Chrome 41+ no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome no Android 4.4.4, o Google Widevine é o DRM atrás do EME.

![Utilizar eme para widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

A Widevine não o impede de fazer uma captura de ecrã de vídeo protegido.

![Plug-ins do jogador para widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Use EME para FairPlay

Da mesma forma, pode testar conteúdo protegido fairPlay neste leitor de teste no Safari no macOS ou iOS 11.2 e mais tarde.

Certifique-se de que coloca "FairPlay" como protecçãoInfo.type e coloque no URL certo para o seu Certificado de Aplicação no FPS AC Path (FairPlay Streaming Application Certificate Path).

### <a name="unentitled-users"></a>Utilizadores sem direito

Se um utilizador não for membro do grupo "Utilizadores Intitulados", o utilizador não passa na verificação de direitos. O serviço de licença multi-DRM recusa-se então a emitir a licença solicitada, como mostrado. A descrição detalhada é "Licença adquirida falhou", que é como projetado.

![Utilizadores sem direito](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Executar um serviço de fichade segurança personalizado

Se executar um STS personalizado, o JWT é emitido pelo STS personalizado utilizando uma chave simétrica ou assimétrica.

A imagem que se segue mostra um cenário que utiliza uma chave simétrica (utilizando o Chrome):

![STS personalizado com uma chave simétrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

A imagem que se segue mostra um cenário que utiliza uma chave assimétrica através de um certificado X509 (utilizando um navegador moderno da Microsoft):

![STS personalizado com uma chave assimétrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

Em ambos os casos anteriores, a autenticação do utilizador mantém-se na mesma. Passa-se através do Azure AD. A única diferença é que os JWTs são emitidos pelo STS personalizado em vez de Azure AD. Quando configura a proteção dinâmica do CENC, a restrição de serviço de entrega de licenças especifica o tipo de JWT, seja uma chave simétrica ou assimétrica.

## <a name="next-steps"></a>Passos seguintes

* [Perguntas frequentes](frequently-asked-questions.md)
* [Visão geral da proteção de conteúdos](content-protection-overview.md)
* [Proteja o seu conteúdo com a DRM](protect-with-drm.md)
