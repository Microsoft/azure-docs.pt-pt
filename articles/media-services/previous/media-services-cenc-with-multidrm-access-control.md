---
title: Criação de um sistema de proteção de conteúdo com o controlo de acesso através dos serviços de multimédia do Azure | Documentos da Microsoft
description: Saiba mais sobre como licenciar o Microsoft Smooth Streaming cliente portar Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: 336552c142e504ae7296314512f00688e30d032e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894364"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Criação de um sistema de proteção de conteúdo com o controlo de acesso através dos serviços de multimédia do Azure 

## <a name="overview"></a>Descrição geral

Conceber e criar um subsistema de direitos digitais (DRM) de gestão para um over-the-top (OTT) ou online de transmissão em fluxo solução é uma tarefa complexa. Operadores/online fornecedores de vídeo normalmente terceirizar essa tarefa para fornecedores de serviços DRM especializados. O objetivo deste documento é apresentar uma estrutura de referência e implementação de um subsistema DRM ponto-a-ponto num OTT ou numa solução de transmissão em fluxo online.

Os leitores de destinados para este documento são engenheiros que trabalham em subsistemas DRM do OTT ou soluções de transmissão em fluxo/multiscreen online ou leitores que estejam interessados em subsistemas DRM. A pressuposição é de que os leitores estejam familiarizados com pelo menos uma das tecnologias de DRM no mercado, como o PlayReady, Widevine, FairPlay ou acesso Adobe.

Nesta discussão de DRM, incluímos também a encriptação comum (CENC) com múltipla DRM. Uma tendência principais na transmissão em fluxo online e o setor OTT é usar CENC com múltipla nativo DRM em várias plataformas de cliente. Essa tendência é uma mudança do anterior que é utilizado um único DRM e o SDK de cliente para várias plataformas de cliente. Ao utilizar CENC com nativo com múltiplos DRM tanto PlayReady como Widevine são encriptados de acordo com o [encriptação comum (ISO/IEC 23001 7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) especificação.

Os benefícios da CENC com múltipla DRM são:

* Reduz o custo de encriptação como um processo de encriptação único é utilizado para plataformas diferentes com seu DRMs nativos.
* Reduz os custos de gerenciamento de ativos encriptados porque é necessário apenas uma única cópia de ativos encriptados.
* Elimina o custo de licenciamento, uma vez que o cliente DRM nativo é normalmente gratuito na sua plataforma nativa de clientes DRM.

A Microsoft é um Active Directory promoter de TRAÇO e CENC juntamente com alguns jogadores principais setores. Serviços de multimédia do Azure fornece suporte para DASH e CENC. Para anúncios recentes, consulte os seguintes blogues:

*  [Anunciar os serviços de entrega de licença Widevine da Google nos Serviços de Multimédia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Serviços de multimédia do Azure adiciona o empacotamento do Widevine da Google para o fornecimento de um fluxo de multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Objetivos do artigo

Os objetivos deste artigo são:

* Fornece uma estrutura de referência de um subsistema DRM que utiliza CENC com múltipla DRM.
* Fornece uma implementação de referência sobre uma plataforma de serviços de multimédia do Azure.
* Fale sobre alguns tópicos de design e implementação.

Neste artigo, o termo "multi-DRM" abrange os seguintes produtos:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

A tabela seguinte resume a aplicação de plataforma nativa/nativa e browsers suportados por cada DRM.

| **Plataforma de cliente** | **Suporte nativo de DRM** | **Aplicação/browser** | **Formatos de transmissão em fluxo** |
| --- | --- | --- | --- |
| **Smart TVs, operador STBs, OTT STBs** |PlayReady principalmente, e/ou Widevine, e/ou outros |Linux, Opera, WebKit, outros |Vários formatos |
| **Dispositivos Windows 10 (Windows PC, tablets Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Plataforma Universal do Windows |TRAÇO (para HLS, PlayReady não é suportado)<br/><br/>DASH, transmissão em fluxo uniforme (para HLS, PlayReady não é suportado) |
| **Dispositivos Android (telefone, tablet, programas de TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), os clientes dos X e Apple TV** |FairPlay |Safari 8 c++ /CLI EME |HLS |

Considerando o estado atual da implementação para cada DRM, um serviço, normalmente, pretende implementar duas ou três DRMs para se certificar de que resolver todos os tipos de pontos finais da melhor forma.

Há uma compensação entre a complexidade da lógica do serviço e a complexidade no lado do cliente para atingir um certo nível de experiência do usuário em vários clientes.

Para fazer sua escolha, tenha em mente:

* PlayReady nativamente é implementado em todos os dispositivos Windows, em alguns dispositivos Android e está disponível através de SDKs de software em praticamente qualquer plataforma.
* Widevine nativamente é implementada em todos os dispositivos Android, no Chrome e em outros dispositivos.
* FairPlay está disponível apenas em iOS e os clientes de Mac OS, ou por meio do iTunes.

Existem duas opções para um típico multi-DRM:

* PlayReady e Widevine
* PlayReady, Widevine e FairPlay

## <a name="a-reference-design"></a>Uma estrutura de referência
Esta seção apresenta uma estrutura de referência é agnóstica para as tecnologias usadas para implementá-lo.

Um subsistema DRM pode conter os seguintes componentes:

* Gestão de chaves
* Empacotamento de DRM
* Entrega de licença de DRM
* Verificação de elegibilidade
* Autenticação/autorização
* Leitor
* Rede de entrega de origem/conteúdo (CDN)

O diagrama seguinte ilustra a interação de alto nível entre os componentes num subsistema DRM:

![Subsistema DRM com CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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

* Subscrição mensal: Utilize uma licença persistente e o mapeamento de recurso de chave de conteúdo 1-para-muitos. Por exemplo, para filmes dos menores, utilizamos uma única chave de conteúdo para a encriptação. Neste caso:

    Número total de licenças solicitadas para filmes/dispositivo todos os filhos = 1

* Subscrição mensal: Utilize uma licença nonpersistent e mapeamento de 1 para 1 entre a chave de conteúdo e ativos. Neste caso:

    Número total de licenças solicitadas para filmes/dispositivo todos os filhos = [número de filmes assistiu] x [número de sessões]

Os dois designs diferentes resultam em padrões de pedido de licença muito diferentes. Os diferentes padrões resultam em custos se o serviço de entrega de licença é fornecido pela cloud pública, como os serviços de suporte de dados de entrega de licenças de diferentes.

## <a name="map-design-to-technology-for-implementation"></a>Design de mapa a tecnologia de implementação
Em seguida, o design genérico é mapeado para tecnologias na plataforma de serviços de multimédia do Azure com a especificação de tecnologia a ser utilizada para cada bloco de construção.

A tabela seguinte mostra o mapeamento.

| **Bloco de construção** | **Tecnologia** |
| --- | --- |
| **Leitor** |[Media Player do Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Fornecedor de identidade (IDP)** |Azure Active Directory (Azure AD) |
| **Serviço de token de segurança (STS)** |Azure AD |
| **Fluxo de trabalho de proteção de DRM** |Proteção de dinâmica de serviços de multimédia |
| **Entrega de licença DRM** |* Entrega de licenças de serviços de multimédia (PlayReady, Widevine, FairPlay) <br/>* Servidor de licenças do Axinom <br/>* Servidor de licenças do PlayReady personalizado |
| **Origem** |Ponto final de transmissão em fluxo dos serviços de multimédia |
| **Gestão de chaves** |Não é necessária para a implementação de referência |
| **Gestão de conteúdos** |Uma aplicação de consola c# |

Em outras palavras, o IDP e o STS são utilizados com o Azure AD. O [API de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/) é utilizado para o jogador. Serviços de multimédia e o leitor de multimédia suportam DASH e CENC com múltipla DRM.

O diagrama seguinte mostra a estrutura geral e o fluxo com o mapeamento de tecnologia anterior:

![CENC nos serviços de multimédia](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a encriptação de CENC dinâmica, a ferramenta de gestão de conteúdos utiliza as seguintes entradas:

* Abrir conteúdo
* Chave de conteúdo da chave geração/gestão
* URLs de aquisição de licença
* Uma lista de informações do Azure AD

Eis a saída da ferramenta de gerenciamento de conteúdo:

* ContentKeyAuthorizationPolicy contém as especificações no como entrega de licenças verifica um JSON Web Token (JWT) e especificações de licença DRM.
* AssetDeliveryPolicy contém especificações na transmissão em fluxo de formato, proteção DRM e URLs de aquisição de licença.

Eis o fluxo durante o tempo de execução:

* Quando a autenticação de utilizador, é gerado um JWT.
* Uma das afirmações incluídas do JWT é uma afirmação de grupos que contém o objeto de grupo EntitledUserGroup de ID. Esta afirmação é usada para passar a verificação de elegibilidade.
* O jogador transfere o manifesto de cliente de conteúdo protegido por CENC e identifica o seguinte:
   * ID de chave.
   * O conteúdo é CENC protegido.
   * URLs de aquisição de licença.
* O jogador faz um pedido de aquisição de licença com base no browser/DRM suportado. Na licença aquisição solicitação, a chave de que ID e a JWT também são enviados. O serviço de entrega de licença verifica o JWT e as declarações contidas antes que emite a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
Implementação inclui os seguintes passos:

1. Prepare recursos de teste. Codificar/pacote um vídeo de teste para o MP4 de velocidade de transmissão fragmentada nos serviços de multimédia. Este recurso está *não* DRM protegido. Proteção DRM é feita pela proteção dinâmica mais tarde.

2. Criar uma chave de ID e uma chave de conteúdo (opcionalmente, a partir de uma semente chave). Neste caso, o sistema de gestão de chaves não é necessária porque apenas uma única chave ID e chave de conteúdo são necessários para alguns dos recursos de teste.

3. Utilize a API de serviços de multimédia para configurar os serviços de entrega de licença de multi-DRM para o recurso de teste. Se utilizar servidores de licença personalizada pela sua empresa ou fornecedores da sua empresa em vez dos serviços de licenciamento nos serviços de multimédia, pode ignorar este passo. Pode especificar URLs de aquisição de licença no passo ao configurar a entrega de licenças. A API de serviços de suporte de dados é necessário especificar algumas configurações detalhadas, como restrição de política de autorização e modelos de resposta de licença para diferentes serviços de licença DRM. Neste momento, o portal do Azure não fornece a interface do Usuário necessário para esta configuração. Para informações de nível da API e código de exemplo, consulte [utilização PlayReady e/ou Widevine encriptação comum dinâmica](media-services-protect-with-playready-widevine.md).

4. Utilize a API de serviços de multimédia para configurar a política de entrega de elementos para o recurso de teste. Para informações de nível da API e código de exemplo, consulte [utilização PlayReady e/ou Widevine encriptação comum dinâmica](media-services-protect-with-playready-widevine.md).

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

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Adicionar permissões para a aplicação no Azure AD a **configurar** separador do aplicativo. As permissões são necessárias para cada aplicativo, as versões de locais e implementados.

    ![Permissões](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

## <a name="additional-topics-for-implementation"></a>Tópicos adicionais para a implementação
Esta secção descreve alguns tópicos adicionais no design e implementação.

### <a name="http-or-https"></a>HTTP ou HTTPS?
A aplicação de leitor de ASP.NET MVC tem de suportar o seguinte:

* Autenticação de utilizador através do Azure AD, o que está sob a HTTPS.
* Troca JWT entre o cliente e o Azure AD, que está abaixo do HTTPS.
* Aquisição de licença DRM pelo cliente, que tem de estar sob HTTPS se a entrega de licenças é fornecida pelos serviços de multimédia. O conjunto de produtos do PlayReady não exige o HTTPS para entrega de licenças. Se o seu servidor de licenças do PlayReady está fora dos serviços de multimédia, pode utilizar HTTP ou HTTPS.

A aplicação de leitor ASP.NET utiliza HTTPS como melhor prática, para que o leitor de multimédia é uma página em HTTPS. No entanto, o HTTP é preferido para a transmissão em fluxo, para que precisa considerar o problema de conteúdo misto.

* O browser não permite que conteúdo misto. Mas o plug-ins como Silverlight e OSMF Plug-in para suavizar e travessão permiti-lo. Conteúdo misto é uma preocupação de segurança por causa da ameaça da capacidade para injetar JavaScript malicioso, que pode fazer com que os dados do cliente estar em risco. Browsers bloquear esta capacidade, por predefinição. É a única maneira de contorná-la no lado do servidor (origem), permitindo que todos os domínios (independentemente do HTTPS ou HTTP). Isto é provavelmente não é uma boa idéia.
* Evite conteúdo misto. A aplicação de leitor e o Media Player, devem utilizar HTTP ou HTTPS. Quando a reprodução de conteúdo misto, é a tecnologia de silverlightSS necessário desmarcar um aviso de conteúdo misto. O tech flashSS processa conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto final de transmissão em fluxo foi criado antes de Agosto de 2014, ele não suporta HTTPS. Neste caso, crie e utilize um novo ponto de final de transmissão em fluxo para HTTPS.

Na implementação de referência para conteúdo protegido por DRM, a aplicação e a transmissão em fluxo são em HTTPS. Para abrir conteúdos, o jogador não tem de autenticação ou uma licença, para que possa utilizar HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>O Azure Active Directory rollover da chave de assinatura
Rollover de chave de assinatura é um ponto importante levar em consideração em sua implementação. Se ignorá-lo, o sistema terminado deixa eventualmente de funcionar completamente, dentro de seis semanas até.

Azure AD utiliza padrões da indústria para estabelecer confiança entre ele e aplicações que utilizam o Azure AD. Especificamente, o Azure AD utiliza uma chave de assinatura que consiste num par de chaves público e privado. Quando o AD do Azure cria um token de segurança que contém informações sobre o utilizador, ele é assinado pelo Azure AD com uma chave privada antes de ser enviada para a aplicação. Para verificar que o token é válido e originados do Azure AD, a aplicação tem de validar a assinatura do token. O aplicativo usa a chave pública exposta pelo Azure AD que está contido no documento de metadados de Federação do inquilino. Esta chave pública e a chave de assinatura da qual ela deriva, é o mesmo usado para todos os inquilinos no Azure AD.

Para obter mais informações sobre o rollover da chave do Azure AD, consulte [informações importantes sobre o rollover da chave de assinatura no Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Entre as [par de chaves públicas-privadas](https://login.microsoftonline.com/common/discovery/keys/):

* A chave privada é utilizada pelo Azure AD para gerar um JWT.
* A chave pública é utilizada por uma aplicação, como serviços de entrega de licença DRM nos serviços de multimédia para verificar o JWT.

Por motivos de segurança do Azure AD gira o certificado periodicamente (a cada seis semanas). No caso de falhas de segurança, o rollover da chave pode ocorrer a qualquer momento. Por conseguinte, os serviços de entrega de licença nos serviços de multimédia tem de atualizar a chave pública utilizada como o Azure AD gira o par de chaves. Caso contrário, ocorre uma falha de autenticação de token nos serviços de multimédia e nenhuma licença é emitida.

Para configurar este serviço, defina TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando configurar serviços de entrega de licença DRM.

Eis o fluxo do JWT:

* O Azure AD emite o JWT com a chave privada para um utilizador autenticado atual.
* Quando um jogador vê um CENC com conteúdo de multi-DRM protegido, primeiro localiza JWT emitido pelo Azure AD.
* O jogador envia um pedido de aquisição de licença com o JWT para serviços de entrega de licença nos serviços de multimédia.
* Os serviços de entrega de licença nos serviços de multimédia utilizam a chave pública atual válido do Azure AD para verificar o JWT antes de emitir licenças.

Serviços de entrega de licença DRM sempre Verifique a chave pública atual válido do Azure AD. A chave pública apresentada pelo Azure AD é a chave utilizada para verificar um JWT emitido pelo Azure AD.

E se o rollover da chave acontece depois do Azure AD gera um JWT, mas antes do JWT é enviada por jogadores para serviços de entrega de licença DRM nos serviços de multimédia para verificação?

Uma vez que uma chave pode ser transferida a qualquer momento, a mais do que uma chave pública válida está sempre disponível no documento de metadados de Federação. Entrega de licenças de serviços de multimédia, pode utilizar qualquer uma das chaves especificadas no documento. Porque uma chave pode ser revertida em breve, outro pode ser sua substituição e assim por diante.

### <a name="where-is-the-access-token"></a>Onde posso encontrar o token de acesso?
Se examinar como uma aplicação web chama uma aplicação de API sob [identidade da aplicação com a concessão de credenciais de cliente OAuth 2.0](../../active-directory/develop/web-api.md), o fluxo de autenticação é o seguinte:

* Um utilizador inicia sessão com o Azure AD na aplicação web. Para obter mais informações, consulte [navegador da Web para o aplicativo web](../../active-directory/develop/web-app.md).
* O ponto de final de autorização do Azure AD redireciona o agente de utilizador para a aplicação de cliente com um código de autorização. O agente do utilizador devolve o código de autorização para o URI de redirecionamento da aplicação cliente.
* A aplicação web tem de adquirir um token de acesso, para que possa autenticar para a API web e recuperar o recurso pretendido. Ele faz um pedido para o ponto de final de token do Azure AD e fornece a credencial, ID de cliente e URI de ID da aplicação da API web. Ela apresenta o código de autorização para provar que deu consentimento ao utilizador.
* O Azure AD autentica o aplicativo e devolve um token de acesso do JWT que é utilizado para chamar a API web.
* Através de HTTPS, a aplicação web utiliza o token de acesso devolvido do JWT para adicionar a cadeia de caracteres do JWT com uma designação de "Bearer" no cabeçalho do pedido "Autorização" para a API web. A API web, em seguida, valida o JWT. Se a validação for bem-sucedida, ela retorna o recurso pretendido.

Neste fluxo de identidade da aplicação, a API web confia que o aplicativo web autenticar o utilizador. Por esse motivo, esse padrão é chamado de um subsistema confiável. O [diagrama de fluxo de autorização](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) descreve como autorização--concessão do código de fluxo funciona.

Aquisição de licença com restrição de token segue o mesmo padrão de subsistema confiável. O serviço de entrega de licença nos serviços de multimédia é o recurso de API web ou o "recurso de back-end" que um aplicativo web precisa de acesso. Então, onde está o token de acesso?

O token de acesso é obtido a partir do Azure AD. Após a autenticação de utilizador com êxito, é devolvido um código de autorização. O código de autorização é usado, juntamente com o ID de cliente e a chave da aplicação, para o exchange para o token de acesso. O token de acesso é utilizado para aceder a uma aplicação de "ponteiro" que aponta para ou representa o serviço de entrega de licença de serviços de multimédia.

Para registar e configurar a aplicação de ponteiro no Azure AD, siga os passos seguintes:

1. No inquilino do Azure AD:

   * Adicione uma aplicação (recurso) com o início de sessão no URL https://[resource_name].azurewebsites.net/. 
   * Adicione um ID de aplicação com o URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Adicione uma nova chave para a aplicação de recurso.

3. Atualize o ficheiro de manifesto de aplicação para que a propriedade groupMembershipClaims tem o valor "groupMembershipClaims": "Tudo".

4. Na aplicação do Azure AD que aponta para a aplicação web player, na secção **permissões para outras aplicações**, adicionar a aplicação de recurso que foi adicionada no passo 1. Sob **delegado permissão**, selecione **acesso [resource_name]**. Esta opção dá a permissão da aplicação web para criar tokens de acesso que acederem à aplicação de recurso. Fazer isso para a versão local e implantada da aplicação web se desenvolver com o Visual Studio e a aplicação web do Azure.

JWT emitido pelo Azure AD é o token de acesso utilizado para aceder ao recurso de ponteiro.

### <a name="what-about-live-streaming"></a>E quanto ao vivo de transmissão em fluxo?
A discussão anterior se concentrou em recursos a pedido. E quanto ao vivo de transmissão em fluxo?

Pode utilizar exatamente o mesmo design e implementação para proteger a transmissão em fluxo em direto nos serviços de multimédia ao tratar o elemento associado um programa como um ativo VOD.

Especificamente, para transmissão em direto nos serviços de multimédia, terá de criar um canal e, em seguida, criar um programa sob o canal. Para criar o programa, terá de criar um elemento que contém o arquivo em direto para o programa. Para fornecer CENC com proteção de multi-DRM do conteúdo em direto, aplicam-se a mesmo configuração/processamento para o elemento como se fosse um ativo VOD antes de iniciar o programa.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos serviços de multimédia?
Muitas vezes, os clientes investiram num farm de servidores de licença em seu próprio Centro de dados ou hospedado por fornecedores de serviços DRM. Com a proteção de conteúdo de serviços de multimédia, pode operar no modo híbrido. Conteúdo pode ser hospedado e dinamicamente protegido nos serviços de multimédia, enquanto as licenças DRM são fornecidas por servidores fora dos serviços de multimédia. Neste caso, considere as seguintes alterações:

* STS deve emitir tokens que são aceitáveis e podem ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pelo Axinom requerem um JWT específico que contém uma mensagem de elegibilidade. Por conseguinte, tem de ter um STS para emitir um JWT desse tipo. Para informações sobre este tipo de implementação, vá para o [Centro de documentação do Azure](https://azure.microsoft.com/documentation/) e ver [utilização Axinom para entregar licenças de Widevine para serviços de multimédia do Azure](media-services-axinom-integration.md).
* Já não terá de configurar o serviço de entrega de licença (ContentKeyAuthorizationPolicy) nos serviços de multimédia. Tem de fornecer a licença de aquisição URLs (para o PlayReady, Widevine e FairPlay) ao configurar AssetDeliveryPolicy configurar CENC com múltipla DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>E se eu quiser utilizar um STS personalizado?
Um cliente pode optar por utilizar um STS personalizado para fornecer JWTs. Os motivos incluem:

* O IDP utilizado pelo cliente não suporta o STS. Neste caso, um STS personalizado pode ser uma opção.
* O cliente poderá ter um controlo para integrar o STS com o subscritor do cliente, sistema de faturação mais flexível ou maior. Por exemplo, um operador MVPD pode oferecer vários pacotes de subscritor OTT, como premium, básico e desporto. O operador deve-se de acordo com as afirmações num token com o pacote de um subscritor para que apenas o conteúdo num pacote específico é disponibilizado. Neste caso, um STS personalizado fornece a flexibilidade necessária e o controle.

Quando utilizar um STS personalizado, duas alterações devem ser feitas:

* Quando configurar o serviço de entrega de licença para um recurso, tem de especificar a chave de segurança utilizada para verificação pelo STS personalizado em vez da chave atual do Azure AD. (Seguem mais detalhes.) 
* Quando é gerado um token JTW, é especificada uma chave de segurança em vez da chave privada do atual X509 certificado no Azure AD.

Existem dois tipos de chaves de segurança:

* Chave simétrica: A mesma chave é utilizada para gerar e verificar um JWT.
* Chave assimétrica: Um par de chaves públicas-privadas uma X509 certificado é utilizado com uma chave privada para encriptar/gerar um JWT e com a chave pública para verificar o token.

> [!NOTE]
> Se usar o .NET Framework / c# como sua plataforma de desenvolvimento, X509 certificado utilizado para uma chave assimétrica segurança tem de ter um comprimento de chave de, pelo menos, 2048. Este é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework. Caso contrário, é gerada a seguinte exceção:
> 
> IDX10630: O 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ser menor que "2048" bits.

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

**Conta de domínio de inquilino personalizado do Azure AD**: A página personalizada início de sessão do Azure AD personalizado o domínio de inquilino.

![Conta de domínio de inquilino personalizado do Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com smart card**: A página de início de sessão personalizada através do Microsoft empresarial IT com a autenticação de dois fatores.

![Conta de domínio de inquilino personalizado do Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft**: A página de início de sessão da conta Microsoft para os consumidores.

![Conta de domínio de inquilino personalizado do Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Utilizar extensões multimédia encriptadas para PlayReady
Num navegador moderno com o Encrypted Media Extensions (EME) para o suporte do PlayReady, como o Internet Explorer 11 no Windows 8.1 ou posterior e o browser Microsoft Edge no Windows 10, o PlayReady está o DRM subjacente para EME.

![Utilizar EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A área de player escuro é porque a proteção PlayReady impede de fazer uma captura de tela de vídeo protegida.

Captura de ecrã seguinte mostra o leitor. o plug-ins e o Microsoft Security Essentials (MSE) / EME suporta:

![Leitor. o plug-ins para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME no Microsoft Edge e Internet Explorer 11 no Windows 10 permite [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) a ser invocado em dispositivos Windows 10 que o suportam. PlayReady SL3000 desbloqueia o fluxo de conteúdo de premium aprimorada (4K, HDR) e novos modelos de entrega (por conteúdo avançado) de conteúdo.

Para se concentrar nos dispositivos Windows, o PlayReady está o DRM apenas no hardware disponível em dispositivos do Windows (PlayReady SL3000). Um serviço de transmissão em fluxo pode utilizar o PlayReady, através de EME ou aplicação plataforma Universal do Windows e oferecem uma maior qualidade de vídeo com o PlayReady SL3000 que outro DRM. Normalmente, conteúdo, cópia de segurança para fluxos de 2 mil por meio do Chrome ou Firefox e conteúdo até 4K fluxos através do Microsoft Edge/Internet Explorer 11 ou uma aplicação plataforma Universal do Windows no mesmo dispositivo. A quantidade depende de definições de serviço e a implementação.

#### <a name="use-eme-for-widevine"></a>Utilizar EME para Widevine
Num navegador moderno com o suporte EME/Widevine, por exemplo, o Chrome 41 + no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome em Android 4.4.4, o Google Widevine é DRM por trás de EME.

![Utilizar EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine não impede que faça uma captura de tela de vídeo protegida.

![Leitor. o plug-ins para o Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Utilizadores unentitled
Se um utilizador não for um membro do grupo "Utilizadores intitulado", o utilizador não passou a verificação de elegibilidade. O serviço de licença de multi-DRM, em seguida, se recusará a emitir a pedido de licença, conforme mostrado. A descrição detalhada é "adquirir licença falha," que é conforme projetado.

![Utilizadores unentitled](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Executar um serviço de token de segurança personalizada
Se executar um STS personalizado, o JWT é emitido pelo STS personalizado com um simétrica ou uma chave assimétrica.

Captura de ecrã seguinte mostra um cenário que utiliza uma chave simétrica (utilizar o Chrome):

![STS personalizado com uma chave simétrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Captura de ecrã seguinte mostra um cenário que utiliza uma chave assimétrica através de uma X509 certificado (usando um navegador moderno da Microsoft):

![STS personalizado com uma chave assimétrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Em ambos os casos anteriores, a autenticação do utilizador permanece o mesmo. Ele ocorre através do Azure AD. A única diferença é que JWTs são emitidos pelo STS personalizado em vez do Azure AD. Ao configurar a proteção de CENC dinâmica, a restrição de serviço de entrega de licença Especifica o tipo de JWT, um simétrico ou uma chave assimétrica.

## <a name="summary"></a>Resumo
Este documento discutidos CENC com nativo com múltiplos DRM e controlo de acesso através de autenticação de token, seu design e sua implementação com o Azure, serviços de multimédia e o Media Player.

* Uma estrutura de referência foi apresentada que contém todos os componentes necessários num subsistema DRM/CENC.
* Uma implementação de referência foi apresentada no Azure, serviços de multimédia e o leitor de multimédia.
* Alguns tópicos diretamente envolvido no design e da implementação também foram abordados.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
