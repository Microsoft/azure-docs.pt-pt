---
title: Um sistema de proteção de conteúdo de vários DRM-serviços de mídia do Azure v3
description: Este artigo fornece uma descrição detalhada de como criar um sistema de proteção de conteúdo de vários DRM com os serviços de mídia do Azure.
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
ms.openlocfilehash: efc070491ca1ea84dc8ef095a2144df9d0bf1bcb
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311908"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Criação de um sistema de proteção de conteúdo multi-DRM com controlo de acesso 

Conceber e criar um subsistema de gestão de direitos digitais (DRM) para um over-the-top (OTT) ou online de transmissão em fluxo solução é uma tarefa complexa. Operadores/online fornecedores de vídeo normalmente terceirizar essa tarefa para fornecedores de serviços DRM especializados. O objetivo deste documento é apresentar uma estrutura de referência e uma implementação de referência de um subsistema DRM ponto-a-ponto num OTT ou numa solução de transmissão em fluxo online.

Os leitores de destinados para este documento são engenheiros que trabalham em subsistemas DRM do OTT ou soluções de transmissão em fluxo/multiscreen online ou leitores que estejam interessados em subsistemas DRM. A pressuposição é de que os leitores estejam familiarizados com pelo menos uma das tecnologias de DRM no mercado, como o PlayReady, Widevine, FairPlay ou acesso Adobe.

Esta discussão, pelo multi-DRM Incluímos os 3 DRMs suportadas pelos serviços de multimédia do Azure: encriptação comum (CENC) para o PlayReady e Widevine, FairPlay, bem como AES-128 encriptação de chave não. Uma tendência principais na transmissão em fluxo online e o setor OTT é usar DRMs nativos em várias plataformas de cliente. Essa tendência é uma mudança do anterior que é utilizado um único DRM e o SDK de cliente para várias plataformas de cliente. Ao utilizar CENC com nativo com múltiplos DRM tanto PlayReady como Widevine são encriptados de acordo com o [encriptação comum (ISO/IEC 23001 7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) especificação.

As vantagens de utilizar o multi-DRM nativo para proteção de conteúdo são:

* Reduz o custo de encriptação como um processo de encriptação único é utilizado para plataformas diferentes com seu DRMs nativos.
* Reduz os custos de gerenciamento de ativos porque apenas uma única cópia de ativo é necessário no armazenamento.
* Elimina o custo de licenciamento, uma vez que o cliente DRM nativo é normalmente gratuito na sua plataforma nativa de clientes DRM.

### <a name="goals-of-the-article"></a>Objetivos do artigo

Os objetivos deste artigo são:

* Fornece uma estrutura de referência de um subsistema DRM que utiliza todos os 3 DRMs (CENC para DASH), o FairPlay para HLS e PlayReady para transmissão em fluxo uniforme.
* Forneça uma implementação de referência na plataforma Azure e serviços de multimédia do Azure.
* Fale sobre alguns tópicos de design e implementação.

A tabela seguinte resume o suporte nativo de DRM em diferentes plataformas e suporte EME em diferentes navegadores.

| **Plataforma de cliente** | **Nativo DRM** | **EME** |
| --- | --- | --- |
| **Smart TVs, STBs** | PlayReady, Widevine, e/ou outros | Browser/EME Embedded PlayReady e/ou Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 para PlayReady|
| **Dispositivos Android (telefone, tablet, programas de TV)** |Widevine |Chrome para Widevine |
| **iOS** | FairPlay | Safari para FairPlay (desde o iOS 11.2) |
| **macOS** | FairPlay | Safari para FairPlay (desde o Safari 9 + no Mac OS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Considerando o estado atual da implementação para cada DRM, um serviço, normalmente, pretende implementar duas ou três DRMs para se certificar de que resolver todos os tipos de pontos finais da melhor forma.

Há uma compensação entre a complexidade da lógica do serviço e a complexidade no lado do cliente para atingir um certo nível de experiência do usuário em vários clientes.

Para fazer sua escolha, tenha em mente:

* PlayReady nativamente é implementado em todos os dispositivos Windows, em alguns dispositivos Android e está disponível através de SDKs de software em praticamente qualquer plataforma.
* Widevine nativamente é implementada em todos os dispositivos Android, no Chrome e em outros dispositivos. Widevine também é suportado em navegadores Firefox e o Opera sobre DASH.
* FairPlay está disponível no iOS, macOS e tvOS.


## <a name="a-reference-design"></a>Uma estrutura de referência
Esta seção apresenta uma estrutura de referência é agnóstica para as tecnologias usadas para implementá-lo.

Um subsistema DRM pode conter os seguintes componentes:

* Gestão de chaves
* Empacotamento de encriptação de DRM
* Entrega de licença de DRM
* Controlo de acesso/verificação de elegibilidade
* Autenticação/autorização do utilizador
* Aplicação de leitor
* Rede de entrega de origem/conteúdo (CDN)

O diagrama seguinte ilustra a interação de alto nível entre os componentes num subsistema DRM:

![Subsistema DRM com CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

O design tem três camadas básicas:

* Uma camada de back office (preta) não está exposta externamente.
* Uma camada de rede de Perímetro (azul escuro) contém todos os pontos finais que enfrentam o público.
* Uma camada de internet pública (azul-clara) contém a CDN e os jogadores com o tráfego através da internet pública.

Também deve haver uma ferramenta de gerenciamento de conteúdo para o controle proteção DRM, independentemente de ser estática ou dinâmica de encriptação. As entradas para a encriptação de DRM incluem:

* Conteúdo de vídeo do MBR
* Chave de conteúdo
* URLs de aquisição de licença

Durante o tempo de reprodução, eis o fluxo de alto nível:

* O utilizador é autenticado.
* Um token de autorização é criado para o utilizador.
* Conteúdo DRM protegido (manifesto) é transferido para o jogador.
* O jogador envia uma solicitação de aquisição de licença para servidores de licença, juntamente com uma chave de ID e um token de autorização.

A secção seguinte explica o design da gestão de chaves.

| **Recurso de ContentKey** | **Cenário** |
| --- | --- |
| 1-para-1 |Caso mais simples. Ele fornece o controle melhores. Mas esta organização resulta, geralmente, o mais alto custo de entrega de licença. No mínimo, uma solicitação de licença é necessária para cada recurso protegido. |
| 1-para-muitos |Poderia usar a mesma chave de conteúdo para vários recursos. Por exemplo, para todos os recursos num grupo lógico, por exemplo, um gênero ou o subconjunto de um gênero (ou gene filmes), pode utilizar uma única chave de conteúdo. |
| Muitos-para-1 |Várias chaves de conteúdo são necessários para cada recurso. <br/><br/>Por exemplo, se precisar de aplicar proteção dinâmica de CENC com múltipla DRM para MPEG-DASH e encriptação AES-128 dinâmica para HLS, terá duas chaves de conteúdo separadas. Cada chave de conteúdo tem seu próprio ContentKeyType. (Para a chave de conteúdo utilizada para a proteção de CENC dinâmica, utilizar ContentKeyType.CommonEncryption de mensagens em fila. Para a chave de conteúdo utilizada para encriptação de AES-128 dinâmica, utilizar ContentKeyType.EnvelopeEncryption.)<br/><br/>Como outro exemplo, na proteção de CENC de conteúdo de TRAÇO, em teoria, pode utilizar uma chave de conteúdo para proteger o fluxo de vídeo e outra chave de conteúdo para proteger o transmissão de áudio. |
| Muitos-para-muitos |Combinação dos dois cenários anteriores. Um conjunto de chaves de conteúdo é utilizado para cada um dos vários recursos no mesmo grupo de recurso. |

Outro fator importante a considerar é a utilização de licenças persistentes e nonpersistent.

Por que são essas considerações importantes?

Se utilizar uma cloud pública para entrega de licenças, persistentes e nonpersistent licenças têm um impacto direto no custo da entrega de licença. Os seguintes dois casos de design diferentes servem para ilustrar:

* Subscrição mensal: utilizar uma licença persistente e o mapeamento de recurso de chave de conteúdo 1-para-muitos. Por exemplo, para filmes dos menores, utilizamos uma única chave de conteúdo para a encriptação. Neste caso:

    Número total de licenças solicitadas para filmes/dispositivo todos os filhos = 1

* Subscrição mensal: utilizar uma licença nonpersistent e mapeamento de 1 para 1 entre a chave de conteúdo e ativos. Neste caso:

    Número total de licenças solicitadas para filmes/dispositivo todos os filhos = [número de filmes assistiu] x [número de sessões]

Os dois designs diferentes resultam em padrões de pedido de licença muito diferentes. Os diferentes padrões resultam em custos se o serviço de entrega de licença é fornecido pela cloud pública, como os serviços de suporte de dados de entrega de licenças de diferentes.

## <a name="map-design-to-technology-for-implementation"></a>Design de mapa a tecnologia de implementação
Em seguida, o design genérico é mapeado para tecnologias na plataforma de serviços de multimédia do Azure com a especificação de tecnologia a ser utilizada para cada bloco de construção.

A tabela seguinte mostra o mapeamento.

| **Bloco de construção** | **Tecnologia** |
| --- | --- |
| **Leitor** |[Media Player do Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Fornecedor de identidade (IDP)** |Azure Active Directory (Azure AD) |
| **Serviço de Token seguro (STS)** |Azure AD |
| **Fluxo de trabalho de proteção de DRM** |Proteção dinâmica dos serviços de multimédia do Azure |
| **Entrega de licença DRM** |* Entrega de licenças de serviços de multimédia (PlayReady, Widevine, FairPlay) <br/>* Servidor de licenças do Axinom <br/>* Servidor de licenças do PlayReady personalizado |
| **Origem** |Ponto final de transmissão em fluxo dos serviços de multimédia do Azure |
| **Gestão de chaves** |Não é necessária para a implementação de referência |
| **Gestão de conteúdos** |Uma aplicação de consola c# |

Em outras palavras, o IDP e o STS são fornecidos pelo Azure AD. O [API de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/) é utilizado para o jogador. Os serviços de mídia do Azure e o Player de Mídia do Azure dão suporte a CENC sobre DASH, FairPlay sobre HLS, PlayReady em Smooth streaming e criptografia AES-128 para DASH, HLS e Smooth.

O diagrama seguinte mostra a estrutura geral e o fluxo com o mapeamento de tecnologia anterior:

![CENC nos serviços de multimédia](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a proteção de conteúdo de DRM, a ferramenta de gestão de conteúdos utiliza as seguintes entradas:

* Abrir conteúdo
* Chave de conteúdo de gestão de chaves
* URLs de aquisição de licença
* Uma lista de informações do Azure AD, como público-alvo, emissor e afirmações de token

Eis a saída da ferramenta de gerenciamento de conteúdo:

* ContentKeyPolicy descreve o modelo de licença DRM para cada tipo de DRM utilizado;
* ContentKeyPolicyRestriction descreve o controlo de acesso antes de uma licença DRM é emitida
* Streamingpolicy descreve as várias combinações de formato de contêiner - modo de encriptação - protocolo de transmissão em fluxo - DRM, para transmissão em fluxo
* StreamingLocator descreve o conteúdo de chave/IV utilizada para encriptação e URLs de transmissão em fluxo 

Eis o fluxo durante o tempo de execução:

* Quando a autenticação de utilizador, é gerado um JWT.
* Uma das afirmações incluídas do JWT é uma afirmação de grupos que contém o objeto de grupo EntitledUserGroup de ID. Esta afirmação é usada para passar a verificação de elegibilidade.
* O jogador transfere o manifesto de cliente de conteúdo protegido por CENC e identifica o seguinte:
   * ID de chave.
   * O conteúdo é DRM protegido.
   * URLs de aquisição de licença.
* O jogador faz um pedido de aquisição de licença com base no browser/DRM suportado. Na licença aquisição solicitação, a chave de que ID e a JWT também são enviados. O serviço de entrega de licença verifica o JWT e as declarações contidas antes que emite a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
Implementação inclui os seguintes passos:

1. Prepare recursos de teste. Codificar/pacote um vídeo de teste para o MP4 de velocidade de transmissão fragmentada nos serviços de multimédia. Este recurso está *não* DRM protegido. Proteção DRM é feita pela proteção dinâmica mais tarde.

2. Criar uma chave de ID e uma chave de conteúdo (opcionalmente, a partir de uma semente chave). Neste caso, o sistema de gestão de chaves não é necessária porque apenas uma única chave ID e chave de conteúdo são necessários para alguns dos recursos de teste.

3. Utilize a API de serviços de multimédia para configurar os serviços de entrega de licença de multi-DRM para o recurso de teste. Se utilizar servidores de licença personalizada pela sua empresa ou fornecedores da sua empresa em vez dos serviços de licenciamento nos serviços de multimédia, pode ignorar este passo. Pode especificar URLs de aquisição de licença no passo ao configurar a entrega de licenças. A API de serviços de suporte de dados é necessário especificar algumas configurações detalhadas, como restrição de política de autorização e modelos de resposta de licença para diferentes serviços de licença DRM. Neste momento, o portal do Azure não fornece a interface do Usuário necessário para esta configuração. Para informações de nível da API e código de exemplo, consulte [utilização PlayReady e/ou Widevine encriptação comum dinâmica](protect-with-drm.md).

4. Utilize a API de serviços de multimédia para configurar a política de entrega de elementos para o recurso de teste. Para informações de nível da API e código de exemplo, consulte [utilização PlayReady e/ou Widevine encriptação comum dinâmica](protect-with-drm.md).

5. Criar e configurar um inquilino do Azure AD no Azure.

6. Crie algumas contas de utilizador e grupos no seu inquilino do Azure AD. Crie, pelo menos, um grupo de "Usuário intitulado" e adicionar um utilizador a este grupo. Os utilizadores neste grupo passaram a verificação de elegibilidade de aquisição de licença. Utilizadores neste grupo não passaram a verificação de autenticação e não é possível adquirir uma licença. A associação neste grupo de "Usuário intitulado" é uma afirmação de grupos necessários no JWT emitido pelo Azure AD. Especifique este requisito de afirmação no passo quando configurar serviços de entrega de licença de multi-DRM.

7. Crie uma aplicação ASP.NET MVC para alojar o seu leitor de vídeo. Esta aplicação ASP.NET está protegida com autenticação de utilizador no inquilino do Azure AD. Afirmações adequadas estão incluídas nos tokens de acesso, obtidos após a autenticação de utilizador. Recomendamos que a API de ligação OpenID para este passo. Instale os seguintes pacotes NuGet:

   * Microsoft.Azure.ActiveDirectory.GraphClient Install-Package
   * Microsoft.Owin.Security.OpenIdConnect Install-Package
   * Microsoft.Owin.Security.Cookies Install-Package
   * Microsoft.Owin.Host.SystemWeb Install-Package
   * Install-Package ActiveDirectory

8. Criar um player com o [API de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/). Utilizar o [API do Azure Media Player ProtectionInfo](https://amp.azure.net/libs/amp/latest/docs/) para especificar a tecnologia DRM a ser utilizada em diferentes plataformas DRM.

9. A tabela seguinte mostra a matriz de teste.

    | **DRM** | **Browser** | **Resultado para o utilizador elegível** | **Resultado para o utilizador unentitled** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge ou o Internet Explorer 11 no Windows 10 |Com Êxito |Falha |
    | **Widevine** |Chrome, Firefox, Opera |Com Êxito |Falha |
    | **FairPlay** |Safari no macOS      |Com Êxito |Falha |
    | **AES-128** |Maioria dos navegadores atuais  |Com Êxito |Falha |

Para obter informações sobre como configurar o Azure AD para uma aplicação de leitor de ASP.NET MVC, veja [integrar uma aplicação com base em MVC de OWIN de serviços de multimédia do Azure com o Azure Active Directory e restringir a entrega de chave de conteúdo com base em declarações do JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Para obter mais informações, consulte [autenticação de token JWT em serviços de multimédia do Azure e a encriptação dinâmica](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Para informações sobre o Azure AD:

* Pode encontrar informações de desenvolvedor no [Guia do programador do Azure Active Directory](../../active-directory/develop/v1-overview.md).
* Pode encontrar as informações de administrador no [administrar o diretório de inquilino do Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Alguns problemas na implementação

Utilize as seguintes informações de resolução de problemas para obter ajuda com problemas de implementação.

* O emissor URL tem de terminar com "/". O público-alvo tem de ser o ID de cliente de aplicação do leitor. Além disso, adicione "/" no final do URL do emissor.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    Na [JWT Decodificador](http://jwt.calebb.net/), verá **aud** e **iss**, como mostra a JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Adicionar permissões para a aplicação no Azure AD a **configurar** separador do aplicativo. As permissões são necessárias para cada aplicativo, as versões de locais e implementados.

    ![Permissões](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Utilize o emissor correto quando configurar a proteção de CENC dinâmica.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Não funciona o seguinte:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    O GUID é o ID de inquilino do Azure AD. O GUID pode ser encontrado na **pontos de extremidade** menu pop-up no portal do Azure.

* Associação de grupo de conceder privilégios de afirmações. Certifique-se de que é o seguinte no ficheiro de manifesto de aplicação do Azure AD: 

    "groupMembershipClaims": "All" (o valor predefinido é nulo)

* Defina o TokenType adequada quando criar requisitos de restrição.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Como adicionar suporte para JWT (Azure AD), além de SWT (ACS), a predefinição TokenType é TokenType.JWT. Se usar SWT/ACS, tem de definir o token para TokenType.SWT.

## <a name="the-completed-system-and-test"></a>O sistema concluído e o teste

Esta secção explica os cenários seguintes no sistema ponto a ponto concluído para que possa ter uma imagem básica do comportamento antes de obter uma conta de início de sessão:

* Se precisar de um cenário não integrada:

    * Para recursos de vídeo alojados nos serviços de suporte de dados que são um desprotegidos ou DRM protegido, mas sem autenticação de token (uma licença de emissão para quem solicitou o seu), pode testá-lo sem iniciar sessão no. Se o seu vídeo de transmissão em fluxo é através de HTTP muda para HTTP.

* Se precisar de um cenário de integradas ponto-a-ponto:

    * Para recursos de vídeo em proteção DRM dinâmica nos serviços de multimédia, com a autenticação de token e JWT gerado pelo Azure AD, terá de iniciar sessão.

Para a aplicação de leitor de web e o início de sessão, consulte [este Web site](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Início de sessão do utilizador
Para testar o sistema DRM integrado ponto-a-ponto, tem de ter uma conta criada ou adicionado.

Que conta?

Embora o Azure originalmente permitido o acesso apenas a utilizadores com contas Microsoft, o acesso agora é permitido por usuários de ambos os sistemas. Todas as propriedades do Azure agora confiem no Azure AD para autenticação e o Azure AD autentica utilizadores organizacionais. Foi criada uma relação de Federação em que o Azure AD confia o sistema de identidade do consumidor de conta Microsoft para autenticar os utilizadores. Como resultado, o Azure AD pode autenticar contas de contas, bem como nativa do Azure AD da Microsoft de convidado.

Uma vez que o Azure AD confia o domínio da conta Microsoft, pode adicionar todas as contas de qualquer um dos seguintes domínios com o Azure AD personalizado de inquilino e utilizam a conta para iniciar sessão:

| **Nome de domínio** | **Domínio** |
| --- | --- |
| **Domínio de inquilino personalizado do Azure AD** |somename.onmicrosoft.com |
| **Domínio Corporativo.** |Microsoft.com |
| **Domínio da conta Microsoft** |Outlook.com, live.com, hotmail.com |

Pode contactar qualquer um dos autores tenham uma conta criada ou adicionado para.

As capturas de ecrã seguintes mostram as páginas de início de início de sessão diferentes utilizadas por contas de domínio diferente:

**Conta de domínio de inquilino de personalizado do Azure AD**: A página personalizada início de sessão do Azure AD personalizado de domínio de inquilino.

![Conta de domínio de inquilino personalizado do Azure AD um](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com smart card**: A página de início de sessão personalizada através do Microsoft empresarial IT com a autenticação de dois fatores.

![Conta de domínio do inquilino personalizado do Azure AD dois](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft**: A página de início de sessão da conta Microsoft para os consumidores.

![Conta de domínio de inquilino três personalizado do Azure AD](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Utilizar extensões multimédia encriptadas para PlayReady

Num navegador moderno com o Encrypted Media Extensions (EME) para o suporte do PlayReady, como o Internet Explorer 11 no Windows 8.1 ou posterior e o browser Microsoft Edge no Windows 10, o PlayReady está o DRM subjacente para EME.

![Utilizar EME para PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

A área de player escuro é porque a proteção PlayReady impede de fazer uma captura de tela de vídeo protegida.

Captura de ecrã seguinte mostra o leitor. o plug-ins e o Microsoft Security Essentials (MSE) / EME suporta:

![Leitor. o plug-ins para PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME no Microsoft Edge e Internet Explorer 11 no Windows 10 permite [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) a ser invocado em dispositivos Windows 10 que o suportam. PlayReady SL3000 desbloqueia o fluxo de conteúdo de premium aprimorada (4K, HDR) e novos modelos de entrega (por conteúdo avançado) de conteúdo.

Para se concentrar nos dispositivos Windows, o PlayReady está o DRM apenas no hardware disponível em dispositivos do Windows (PlayReady SL3000). Um serviço de transmissão em fluxo pode utilizar o PlayReady, através de EME ou aplicação plataforma Universal do Windows e oferecem uma maior qualidade de vídeo com o PlayReady SL3000 que outro DRM. Normalmente, conteúdo, cópia de segurança para fluxos de 2 mil por meio do Chrome ou Firefox e conteúdo até 4K fluxos através do Microsoft Edge/Internet Explorer 11 ou uma aplicação plataforma Universal do Windows no mesmo dispositivo. A quantidade depende de definições de serviço e a implementação.

#### <a name="use-eme-for-widevine"></a>Utilizar EME para Widevine

Num navegador moderno com o suporte EME/Widevine, por exemplo, o Chrome 41 + no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome em Android 4.4.4, o Google Widevine é DRM por trás de EME.

![Utilizar EME para Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine não impede que faça uma captura de tela de vídeo protegida.

![Leitor. o plug-ins para o Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Utilizar EME para FairPlay

Da mesma forma, pode testar conteúdo FairPlay protegido neste leitor de teste no Safari no macOS ou iOS 11.2 e posterior.

Certifique-se de colocar "FairPlay" como protectionInfo.type e colocar o URL certo para o certificado da sua aplicação no caminho de AC FPS (FairPlay Streaming certificado caminho da aplicação).

### <a name="unentitled-users"></a>Utilizadores unentitled

Se um utilizador não for um membro do grupo "Utilizadores intitulado", o utilizador não passou a verificação de elegibilidade. O serviço de licença de multi-DRM, em seguida, se recusará a emitir a pedido de licença, conforme mostrado. A descrição detalhada é "adquirir licença falha," que é conforme projetado.

![Utilizadores unentitled](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Executar um serviço de token de segurança personalizada

Se executar um STS personalizado, o JWT é emitido pelo STS personalizado com um simétrica ou uma chave assimétrica.

Captura de ecrã seguinte mostra um cenário que utiliza uma chave simétrica (utilizar o Chrome):

![STS personalizado com uma chave simétrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Captura de ecrã seguinte mostra um cenário que utiliza uma chave assimétrica através de uma X509 certificado (usando um navegador moderno da Microsoft):

![STS personalizado com uma chave assimétrica](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

Em ambos os casos anteriores, a autenticação do utilizador permanece o mesmo. Ele ocorre através do Azure AD. A única diferença é que JWTs são emitidos pelo STS personalizado em vez do Azure AD. Ao configurar a proteção de CENC dinâmica, a restrição de serviço de entrega de licença Especifica o tipo de JWT, um simétrico ou uma chave assimétrica.

## <a name="next-steps"></a>Passos seguintes

* [Perguntas mais frequentes](frequently-asked-questions.md)
* [Descrição geral da proteção de conteúdo](content-protection-overview.md)
* [Proteger os seus conteúdos com o DRM](protect-with-drm.md)
