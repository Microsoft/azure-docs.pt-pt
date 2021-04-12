---
title: Um sistema de proteção de conteúdos multi-DRM
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
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 48ae7b2356f7313acbfb298b7a492a0482e3106b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068137"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Conceber e construir um subsistema de Gestão de Direitos Digitais (DRM) para uma solução de streaming (OTT) ou de streaming on-line é uma tarefa complexa. Os operadores/fornecedores de vídeo online normalmente subcontratam esta tarefa a prestadores de serviços drm especializados. O objetivo deste documento é apresentar um design de referência e uma implementação de referência de um subsistema de DRM de ponta a ponta numa solução de streaming OTT ou online.

Os leitores-alvo deste documento são engenheiros que trabalham em subsistemas DE DRM de soluções de streaming/multiecrão online ou leitores interessados em subsistemas DRM. O pressuposto é que os leitores estão familiarizados com pelo menos uma das tecnologias de DRM no mercado, como PlayReady, Widevine, FairPlay ou Adobe Access.

Nesta discussão, por multi-DRM incluímos os 3 DRMs suportados pela Azure Media Services: Common Encryption (CENC) para PlayReady e Widevine, FairPlay, bem como encriptação de chave clara AES-128. Uma grande tendência no streaming online e na indústria OTT é usar DRMs nativos em várias plataformas de clientes. Esta tendência é uma mudança em face da anterior que utilizou um único DRM e o seu cliente SDK para várias plataformas de clientes. Quando utiliza o CENC com DRM multi-nativo, tanto o PlayReady como o Widevine são encriptados de acordo com a especificação [De encriptação Comum (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Os benefícios da utilização de multi-DRM nativo para proteção de conteúdos são que:

* Reduz o custo de encriptação porque um único processo de encriptação é usado para direcionar diferentes plataformas com os seus DRMs nativos.
* Reduz o custo de gestão de ativos porque apenas uma cópia do ativo é necessária no armazenamento.
* Elimina o custo de licenciamento do cliente DRM porque o cliente drm nativo é geralmente gratuito na sua plataforma nativa.

### <a name="goals-of-the-article"></a>Objetivos do artigo

Os objetivos deste artigo são:

* Forneça um design de referência de um subsistema DRM que utilize todos os 3 DRMs (CENC para DASH, FairPlay para HLS e PlayReady para streaming suave).
* Fornecer uma implementação de referência na plataforma Azure e Azure Media Services.
* Discuta alguns tópicos de design e implementação.

A tabela seguinte resume o suporte drm nativo em diferentes plataformas e suporte EME em diferentes navegadores.

| **Cliente plataforma** | **DRM nativo** | **EME** |
| --- | --- | --- |
| **Smart TVs, STBs** | PlayReady, Widevine e/ou outros | Navegador/EME incorporado para PlayReady e/ou Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 para PlayReady|
| **Dispositivos Android (telefone, tablet, TV)** |Widevine |Cromado para Widevine |
| **iOS** | FairPlay | Safari para FairPlay (desde o iOS 11.2) |
| **macOS** | FairPlay | Safari para FairPlay (desde Safari 9+ no macOS X 10.11+ El Capitan)|
| **tvOS** | FairPlay | |

Tendo em conta o estado atual de implantação de cada DRM, um serviço normalmente quer implementar dois ou três DRMs para se certificar de que aborda todos os tipos de pontos finais da melhor forma.

Existe uma compensação entre a complexidade da lógica de serviço e a complexidade do lado do cliente para alcançar um determinado nível de experiência do utilizador nos vários clientes.

Para fazer a sua seleção, lembre-se:

* O PlayReady é implementado de forma nativa em todos os dispositivos Windows, em alguns dispositivos Android, e disponível através de SDKs de software em praticamente qualquer plataforma.
* O Widevine é implementado de forma nativa em todos os dispositivos Android, no Chrome e em alguns outros dispositivos. Widevine também é suportado em navegadores Firefox e Opera sobre o DASH.
* FairPlay está disponível em iOS, macOS e tvOS.

## <a name="a-reference-design"></a>Um design de referência

Esta secção apresenta um design de referência que é agnóstico às tecnologias usadas para implementá-lo.

Um subsistema DRM pode conter os seguintes componentes:

* Gestão de chaves
* Embalagem de encriptação DRM
* Entrega de licença de DRM
* Controlo de controlo/acesso de direitos
* Autenticação/autorização do utilizador
* App do jogador
* Rede de origem/entrega de conteúdos (CDN)

O diagrama a seguir ilustra a interação de alto nível entre os componentes de um subsistema DRM:

![Subsistema DRM com CENC](./media/architecture-design-multi-drm-system/media-services-generic-drm-subsystem-with-cenc.png)

O design tem três camadas básicas:

* Uma camada de back-office (preto) não é exposta externamente.
* Uma camada de DMZ (azul escuro) contém todos os pontos finais que enfrentam o público.
* Uma camada de internet pública (azul claro) contém o CDN e os jogadores com tráfego através da internet pública.

Deve também existir uma ferramenta de gestão de conteúdos para controlar a proteção de DRM, independentemente de ser uma encriptação estática ou dinâmica. As entradas para encriptação DRM incluem:

* Conteúdo de vídeo MBR
* Chave de conteúdo
* URLs de aquisição de licença

Aqui está o fluxo de alto nível durante o tempo de reprodução:

* O utilizador é autenticado.
* É criado um sinal de autorização para o utilizador.
* O conteúdo protegido drm (manifesto) é descarregado para o leitor.
* O jogador submete um pedido de aquisição de licença para licenciar servidores juntamente com um ID chave e um token de autorização.

A secção seguinte discute o desenho da gestão de chaves.

| **ContentKey-to-asset** | **Cenário** |
| --- | --- |
| 1 para 1 |O caso mais simples. Fornece o melhor controlo. Mas este acordo geralmente resulta no maior custo de entrega de licença. No mínimo, é necessário um pedido de licença para cada ativo protegido. |
| 1 a muitos |Pode usar a mesma chave de conteúdo para vários ativos. Por exemplo, para todos os ativos de um grupo lógico, como um género ou o subconjunto de um género (ou gene de filme), você pode usar uma única chave de conteúdo. |
| Muitos para 1 |São necessárias várias chaves de conteúdo para cada ativo. <br/><br/>Por exemplo, se precisar de aplicar uma proteção dinâmica do CENC com multi-DRM para MPEG-DASH e encriptação dinâmica AES-128 para o HLS, precisa de duas teclas de conteúdo separadas. Cada chave de conteúdo precisa do seu próprio ContentKeyType. (Para a chave de conteúdo utilizada para uma proteção dinâmica do CENC, utilize o ContentKeyType.CommonEncrycryption. Para a chave de conteúdo utilizada para encriptação dinâmica AES-128, utilize ContentKeyType.EnvelopeEncryption.)<br/><br/>Como outro exemplo, na proteção CENC do conteúdo DOR, em teoria, pode utilizar uma chave de conteúdo para proteger o fluxo de vídeo e outra chave de conteúdo para proteger o fluxo de áudio. |
| Muitos para muitos |Combinação dos dois cenários anteriores. Um conjunto de chaves de conteúdo é usado para cada um dos múltiplos ativos do mesmo grupo de ativos. |

Outro fator importante a ter em conta é o uso de licenças persistentes e não-persistentes.

Por que estas considerações são importantes?

Se utilizar uma nuvem pública para entrega de licenças, as licenças persistentes e não-persistentes têm um impacto direto no custo de entrega da licença. As duas seguintes caixas de design diferentes servem para ilustrar:

* Subscrição mensal: Utilize uma licença persistente e um mapeamento de chave para o ativo de 1 a muitos conteúdos. Por exemplo, para todos os filmes infantis, usamos uma única chave de conteúdo para encriptação. Neste caso:

    Número total de licenças pedidas para todos os filmes/dispositivos infantis = 1

* Subscrição mensal: Utilize uma licença não-determinante e um mapeamento de 1 para 1 entre a chave de conteúdo e o ativo. Neste caso:

    Número total de licenças pedidas para todos os filmes/dispositivos infantis = [número de filmes assistidos] x [número de sessões]

Os dois designs diferentes resultam em padrões de pedido de licença muito diferentes. Os diferentes padrões resultam em diferentes custos de entrega de licenças se o serviço de entrega de licenças for fornecido por uma nuvem pública, como os Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Design de mapa para tecnologia para implementação
Em seguida, o design genérico é mapeado para tecnologias na plataforma Azure/Media Services, especificando que tecnologia usar para cada bloco de construção.

A tabela a seguir mostra o mapeamento.

| **Bloco de construção** | **Tecnologia** |
| --- | --- |
| **Jogador** |[Media Player do Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Fornecedor de Identidade (IDP)** |Azure Active Directory (Azure AD) |
| **Serviço De Token Seguro (STS)** |Azure AD |
| **Fluxo de trabalho de proteção DRM** |Proteção dinâmica dos Azure Media Services |
| **Entrega de licença DRM** |* Entrega de licença de Serviços de Mídia (PlayReady, Widevine, FairPlay) <br/>* Servidor de licença axinom <br/>* Servidor de licença personalizado PlayReady |
| **Origem** |Ponto final de streaming do Azure Media Services |
| **Gestão de chaves** |Não é necessário para a implementação de referência |
| **Content management (Gestão de conteúdos)** |Uma aplicação de consola C# |

Por outras palavras, tanto o IDP como o STS são fornecidos pela Azure AD. A [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) é utilizada para o leitor. Tanto a Azure Media Services como a Azure Media Player suportam o CENC sobre o DASH, o FairPlay sobre o HLS, o PlayReady sobre o streaming suave e a encriptação AES-128 para DASH, HLS e smooth.

O seguinte diagrama mostra a estrutura global e o fluxo com o mapeamento da tecnologia anterior:

![CENC em Serviços de Mídia](./media/architecture-design-multi-drm-system/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a proteção de conteúdos DRM, a ferramenta de gestão de conteúdos utiliza as seguintes entradas:

* Conteúdo aberto
* Chave de conteúdo da gestão de chaves
* URLs de aquisição de licença
* Uma lista de informações da Azure AD, tais como público, emitente e alegações simbólicas

Aqui está a saída da ferramenta de gestão de conteúdos:

* ContentKeyPolicy descreve o modelo de licença DRM para cada tipo de DRM utilizado;
* ContentKeyPolicyRestriction descreve o controlo de acesso antes de uma licença DRM ser emitida
* Streamingpolicy descreve as várias combinações de DRM - modo de encriptação - protocolo de streaming - formato de contentor, para streaming
* StreamingLocator descreve a chave de conteúdo/IV usada para encriptação e URLs de streaming 

Aqui está o fluxo durante o tempo de funcionação:

* Após a autenticação do utilizador, é gerado um JWT.
* Uma das alegações contidas no JWT é uma alegação de grupos que contém o iD iD do grupo ID EntitledUserGroup. Esta reclamação é usada para passar o cheque de direito.
* O jogador descarrega o manifesto do cliente de conteúdo protegido pelo CENC e identifica o seguinte:
   * Identificação de chave.
   * O conteúdo é protegido por DRM.
   * URLs de aquisição de licença.
* O jogador faz um pedido de aquisição de licença com base no suporte do browser/DRM. No pedido de aquisição de licença, o ID chave e o JWT também são submetidos. O serviço de entrega de licenças verifica o JWT e as reclamações contidas antes de emitir a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
A implementação inclui as seguintes etapas:

1. Preparar os ativos do teste. Codificar/embalar um vídeo de teste para MP4 fragmentado multi-bitrate nos Serviços de Mídia. Este ativo *não* está protegido pela DRM. A proteção por DRM é feita através de uma proteção dinâmica mais tarde.

2. Crie um ID chave e uma chave de conteúdo (opcionalmente a partir de uma semente de chave). Neste caso, o sistema de gestão chave não é necessário porque apenas uma chave de identificação e chave de conteúdo são necessárias para um par de ativos de teste.

3. Utilize a API dos Serviços de Mídia para configurar serviços de entrega de licenças multi-DRM para o ativo de teste. Se utilizar servidores de licença personalizados da sua empresa ou dos fornecedores da sua empresa em vez de serviços de licença nos Serviços de Comunicação Social, pode saltar este passo. Pode especificar urls de aquisição de licença no passo quando configurar a entrega da licença. A API dos Serviços de Mídia é necessária para especificar algumas configurações detalhadas, tais como restrição de política de autorização e modelos de resposta à licença para diferentes serviços de licenças drm. Neste momento, o portal Azure não fornece a UI necessária para esta configuração. Para obter informações de nível API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](drm-protect-with-drm-tutorial.md).

4. Utilize a API dos Serviços de Mídia para configurar a política de entrega de ativos para o ativo de teste. Para obter informações de nível API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](drm-protect-with-drm-tutorial.md).

5. Crie e configuure um inquilino AZure AD em Azure.

6. Crie algumas contas e grupos de utilizadores no seu inquilino Azure AD. Crie pelo menos um grupo "Utilizador Titular" e adicione um utilizador a este grupo. Os utilizadores deste grupo passam o controlo de direito na aquisição de licença. Os utilizadores que não estão neste grupo não passam o cheque de autenticação e não podem adquirir uma licença. A adesão a este grupo "Utilizador Titular" é uma reivindicação de grupos necessários no JWT emitido pela Azure AD. Especifica este requisito de reclamação no passo quando configurar serviços de entrega de licenças multi-DRM.

7. Crie uma aplicação MVC ASP.NET para hospedar o seu leitor de vídeo. Esta ASP.NET aplicação está protegida com autenticação do utilizador contra o inquilino AZURE AD. As reclamações adequadas estão incluídas nos tokens de acesso obtidos após a autenticação do utilizador. Recomendamos a API OpenID Connect para este passo. Instale os seguintes pacotes NuGet:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Criar um leitor utilizando a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Utilize o [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) para especificar qual a tecnologia DRM a utilizar em diferentes plataformas DE DRM.

9. A tabela seguinte mostra a matriz de teste.

    | **DRM** | **Browser** | **Resultado para o utilizador intitulado** | **Resultado para utilizador nãoentente** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge ou Internet Explorer 11 no Windows 10 |Ter sucesso |Reprovado |
    | **Widevine** |Chrome, Firefox, Ópera |Ter sucesso |Reprovado |
    | **FairPlay** |Safari em macOS      |Ter sucesso |Reprovado |
    | **AES-128** |A maioria dos navegadores modernos  |Ter sucesso |Reprovado |

Para obter informações sobre como configurar o Azure AD para uma aplicação de jogadores MVC ASP.NET, consulte [Integrar uma aplicação baseada em Azure Media Services OWIN MVC com Ozure Ative Directory e restringir a entrega de chaves de conteúdo com base em alegações de JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Para obter mais informações, consulte [a autenticação simbólica JWT nos Serviços de Media Azure e encriptação dinâmica.](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)  

Para obter informações sobre a Azure AD:

* Pode encontrar informações sobre o desenvolvedor no [guia do programador Azure Ative Directory](../../active-directory/develop/v2-overview.md).
* Você pode encontrar informações de administrador em [Administrar o seu diretório de inquilinos Azure AD](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Algumas questões em implementação

Utilize as seguintes informações de resolução de problemas para obter ajuda com problemas de implementação.

* O URL do emitente deve terminar com "/". O público deve ser a identificação do cliente de aplicação do jogador. Além disso, adicione "/" no final do URL do emitente.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    No [Decodificador JWT,](http://jwt.calebb.net/)você vê **aud** e **iss,** como mostrado no JWT:

    ![JWT](./media/architecture-design-multi-drm-system/media-services-1st-gotcha.png)

* Adicione permissões à aplicação em Azure AD no **separador Configurar** da aplicação. São necessárias permissões para cada aplicação, tanto as versões locais como implementadas.

    ![Permissões](./media/architecture-design-multi-drm-system/media-services-perms-to-other-apps.png)

* Utilize o emitente correto quando configurar uma proteção dinâmica do CENC.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    O seguinte não funciona:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    O GUID é o ID do inquilino da Azure. O GUID pode ser encontrado no menu pop-up **Endpoints** no portal Azure.

* A filiação ao grupo grant reclama privilégios. Certifique-se de que o seguinte está no ficheiro manifesto de aplicação AZure: 

    "groupMembershipClaims": "All" (o valor por defeito é nulo)

* Desacorda o TokenType adequado quando criar requisitos de restrição.

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    Como adiciona suporte para JWT (Azure AD) para além do SWT (ACS), o TokenType padrão é TokenType.JWT. Se utilizar o SWT/ACS, deve definir o símbolo para TokenType.SWT.

## <a name="the-completed-system-and-test"></a>O sistema e teste completos

Esta secção acompanha-o através dos seguintes cenários no sistema de ponta a ponta concluído para que possa ter uma imagem básica do comportamento antes de obter uma conta de inscrição:

* Se precisar de um cenário não integrado:

    * Para os ativos de vídeo alojados nos Serviços de Media que estejam desprotegidos ou protegidos por DRM, mas sem autenticação simbólica (emitindo uma licença a quem o solicitou), pode testá-lo sem iniciar sessão. Mude para HTTP se o seu streaming de vídeo tiver terminado HTTP.

* Se precisar de um cenário integrado de ponta a ponta:

    * Para os ativos de vídeo sob proteção dinâmica de DRM nos Serviços de Media, com a autenticação simbólica e JWT gerado pela Azure AD, tem de iniciar sação.

Para a aplicação web do jogador e a sua sôm-in, consulte [este site](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Início de sessão do utilizador
Para testar o sistema de DRM integrado de ponta a ponta, é necessário ter uma conta criada ou adicionada.

Que conta?

Apesar de o Azure ter sido inicialmente autorizado a aceder apenas pelos utilizadores da conta da Microsoft, o acesso é agora permitido pelos utilizadores de ambos os sistemas. Todas as propriedades Azure agora confiam em Azure AD para autenticação, e Azure AD autentica utilizadores organizacionais. Foi criada uma relação de federação onde a Azure AD confia no sistema de identidade do consumidor da conta da Microsoft para autenticar os utilizadores dos consumidores. Como resultado, o Azure AD pode autenticar as contas da Microsoft, bem como as contas AD nativas do Azure.

Uma vez que a Azure AD confia no domínio da conta da Microsoft, pode adicionar quaisquer contas de qualquer um dos seguintes domínios ao inquilino AD AD personalizado e utilizar a conta para iniciar scontabilidade:

| **Nome de domínio** | **Domínio** |
| --- | --- |
| **Domínio de inquilino azure ad personalizado** |somename.onmicrosoft.com |
| **Domínio corporativo** |microsoft.com |
| **Domínio da conta da Microsoft** |outlook.com, live.com, hotmail.com |

Pode contactar qualquer um dos autores para ter uma conta criada ou adicionada para si.

As imagens seguintes mostram diferentes páginas de insusição utilizadas por diferentes contas de domínio:

**Conta de domínio do inquilino Azure AD personalizado**: A página de inscrição personalizada do domínio de inquilino Azure AD personalizado.

![Conta de domínio de inquilino azure azure um](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com cartão inteligente**: A página de sent-in personalizada pela Microsoft corporate IT com autenticação de dois fatores.

![Conta de domínio de inquilino azure azure dois](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain2.png)

**Conta Microsoft**: A página de sindução da conta Microsoft para os consumidores.

![Conta de domínio de inquilino azure azure personalizado três](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Use extensões de mídia encriptadas para playReady

Num navegador moderno com extensões de mídia encriptadas (EME) para suporte playReady, como o Internet Explorer 11 no Windows 8.1 ou mais tarde e o navegador Microsoft Edge no Windows 10, o PlayReady é o DRM subjacente para EME.

![Use EME para PlayReady](./media/architecture-design-multi-drm-system/media-services-eme-for-playready1.png)

A área do jogador escuro é porque a proteção PlayReady impede-o de fazer uma captura de ecrã de vídeo protegido.

A imagem que se segue mostra os plug-ins do jogador e o suporte do Microsoft Security Essentials (MSE)/EME:

![Plug-ins do jogador para PlayReady](./media/architecture-design-multi-drm-system/media-services-eme-for-playready2.png)

O EME no Microsoft Edge e o Internet Explorer 11 no Windows 10 permite que [o PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) seja invocado em dispositivos Windows 10 que o suportam. O PlayReady SL3000 desbloqueia o fluxo de conteúdo premium melhorado (4K, HDR) e novos modelos de entrega de conteúdo (para conteúdo melhorado).

Para se focar nos dispositivos Windows, o PlayReady é o único DRM no hardware disponível em dispositivos Windows (PlayReady SL3000). Um serviço de streaming pode utilizar o PlayReady através do EME ou através de uma aplicação Universal Windows Platform e oferecer uma maior qualidade de vídeo utilizando o PlayReady SL3000 do que outro DRM. Normalmente, o conteúdo até 2K flui através do Chrome ou Firefox, e o conteúdo flui até 4K através do Microsoft Edge/Internet Explorer 11 ou de uma aplicação Universal Windows Platform no mesmo dispositivo. O valor depende das definições de serviço e implementação.

#### <a name="use-eme-for-widevine"></a>Use EME para a widevine

Num navegador moderno com suporte EME/Widevine, como o Chrome 41+ no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome no Android 4.4.4, o Google Widevine é o DRM por trás da EME.

![Use EME para a widevine](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine1.png)

O Widevine não te impede de fazer uma captura de ecrã de vídeo protegido.

![Plug-ins do jogador para Widevine](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Use EME para FairPlay

Da mesma forma, pode testar os conteúdos protegidos do FairPlay neste leitor de testes no Safari no macOS ou iOS 11.2 e posteriormente.

Certifique-se de que coloca "FairPlay" como protecçãoInfo.type e coloque no URL certo para o seu Certificado de Aplicação em FPS AC Path (FairPlay Streaming Application Certificate Path).

### <a name="unentitled-users"></a>Utilizadores nãoententes

Se um utilizador não for membro do grupo "Utilizadores Com Direito", o utilizador não passa o cheque de direito. O serviço de licença multi-DRM recusa-se então a emitir a licença solicitada como mostrado. A descrição detalhada é "Licença adquirir falhado", que é como projetado.

![Utilizadores nãoententes](./media/architecture-design-multi-drm-system/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Executar um serviço de ficha de segurança personalizado

Se executar uma STS personalizada, o JWT é emitido pela STS personalizada utilizando uma chave simétrica ou assimétrica.

A imagem que se segue mostra um cenário que utiliza uma chave simétrica (utilizando o Chrome):

![STS personalizado com uma chave simétrica](./media/architecture-design-multi-drm-system/media-services-running-sts1.png)

A imagem a seguir mostra um cenário que usa uma chave assimétrica através de um certificado X509 (utilizando um navegador moderno da Microsoft):

![STS personalizado com uma chave assimétrica](./media/architecture-design-multi-drm-system/media-services-running-sts2.png)

Em ambos os casos anteriores, a autenticação do utilizador mantém-se a mesma. Passa-se através do Azure AD. A única diferença é que os JWTs são emitidos pela STS personalizada em vez de Azure AD. Ao configurar uma proteção dinâmica do CENC, a restrição do serviço de entrega de licença especifica o tipo de JWT, seja uma chave simétrica ou assimétrica.
