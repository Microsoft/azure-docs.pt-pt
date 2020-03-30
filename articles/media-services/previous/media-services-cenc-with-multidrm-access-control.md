---
title: Conceção de um sistema de proteção de conteúdos com controlo de acesso utilizando o Azure Media Services Microsoft Docs
description: Saiba como licenciar o Kit de Porting De Clientes de Streaming da Microsoft Smooth.
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
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: 68f42aa13288c2416257f3ba6c0b6072c1572977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162995"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Conceção de um sistema de proteção de conteúdos com controlo de acesso utilizando o Azure Media Services 

## <a name="overview"></a>Descrição geral

Conceber e construir um subsistema de gestão de direitos digitais (DRM) para uma solução de streaming de topo (OTT) ou online é uma tarefa complexa. Operadores/fornecedores de vídeo online normalmente subcontratam esta tarefa a prestadores especializados de serviços DRM. O objetivo deste documento é apresentar um design de referência e implementação de um subsistema DEDRM de ponta a ponta numa solução de streaming OTT ou online.

Os leitores visados para este documento são engenheiros que trabalham em subsistemas DRM de OTT ou soluções de streaming/multiecrã on-line ou leitores interessados em subsistemas DRM. O pressuposto é que os leitores estão familiarizados com pelo menos uma das tecnologias DRM no mercado, tais como PlayReady, Widevine, FairPlay ou Adobe Access.

Nesta discussão sobre a DRM, também incluímos encriptação comum (CENC) com multi-DRM. Uma grande tendência no streaming online e na indústria OTT é usar o CENC com DRM multi-nativo em várias plataformas de clientes. Esta tendência é uma mudança em com para com a anterior que usou uma única DRM e o seu cliente SDK para várias plataformas de clientes. Quando utiliza o CENC com DRM multinativo, tanto o PlayReady como o Widevine são encriptados de acordo com a especificação de [Encriptação Comum (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Os benefícios do CENC com multi-DRM são que:

* Reduz o custo de encriptação porque um único processo de encriptação é usado para direcionar diferentes plataformas com as suas DDR nativas.
* Reduz o custo de gestão de ativos encriptados porque só é necessária uma cópia de ativos encriptados.
* Elimina o custo de licenciamento do cliente DRM porque o cliente nativo da DRM é geralmente gratuito na sua plataforma nativa.

A Microsoft é uma promotora ativa do DASH e cenc juntamente com alguns dos principais players da indústria. A Azure Media Services presta apoio ao DASH e ao CENC. Para anúncios recentes, consulte os seguintes blogs:

*  [Anunciar os serviços de entrega de licença Widevine da Google nos Serviços de Multimédia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services adiciona embalagem google Widevine para entregar um fluxo multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Objetivos do artigo

Os objetivos deste artigo são:

* Forneça um desenho de referência de um subsistema DRM que utilize o CENC com multi-DRM.
* Forneça uma implementação de referência numa plataforma Azure/Media Services.
* Discuta alguns tópicos de design e implementação.

No artigo, o termo "multi-DRM" abrange os seguintes produtos:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

A tabela seguinte resume a plataforma nativa/app nativa e navegadores suportados por cada DRM.

| **Cliente plataforma** | **Suporte nativo de DRM** | **Navegador/app** | **Formatos de streaming** |
| --- | --- | --- | --- |
| **Smart TVs, Operador STBs, OTT STBs** |PlayReady principalmente, e/ou Widevine, e/ou outros |Linux, Ópera, WebKit, outros |Vários formatos |
| **Dispositivos Windows 10 (Windows PC, tablets Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Plataforma Universal do Windows |DASH (para HLS, PlayReady não é suportado)<br/><br/>DASH, Smooth Streaming (para HLS, PlayReady não é suportado) |
| **Dispositivos Android (telefone, tablet, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **iOS (iPhone, iPad), clientes OS X e Apple TV** |FairPlay |Safari 8+/EME |HLS |

Tendo em conta o estado atual de implantação de cada DRM, um serviço normalmente quer implementar duas ou três DDR para garantir que você aborda todos os tipos de pontos finais da melhor maneira.

Existe uma compensação entre a complexidade da lógica do serviço e a complexidade do lado do cliente para atingir um determinado nível de experiência do utilizador nos vários clientes.

Para fazer a sua seleção, lembre-se:

* O PlayReady é implementado de forma nativa em todos os dispositivos Windows, em alguns dispositivos Android, e está disponível através de SDKs de software em praticamente qualquer plataforma.
* A Widevine é implementada de forma nativa em todos os dispositivos Android, no Chrome e em alguns outros dispositivos.
* FairPlay está disponível apenas em clientes iOS e Mac OS ou através do iTunes.

Existem duas opções para um multi-DRM típico:

* PlayReady e Widevine
* PlayReady, Widevine e FairPlay

## <a name="a-reference-design"></a>Um design de referência
Esta secção apresenta um design de referência agnóstico às tecnologias utilizadas para implementá-lo.

Um subsistema DRM pode conter os seguintes componentes:

* Gestão de chaves
* Embalagem DRM
* Entrega de licença de DRM
* Verificação de direitos
* Autenticação/autorização
* Leitor
* Rede de entrega de origem/conteúdo (CDN)

O diagrama que se segue ilustra a interação de alto nível entre os componentes de um subsistema DRM:

![Subsistema DRM com CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Fornecedor de identidade (IDP)** |Azure Active Directory (Azure AD) |
| **Serviço de fichas de segurança (STS)** |Azure AD |
| **Fluxo de trabalho de proteção de DRM** |Proteção dinâmica dos Serviços de Media |
| **Entrega de licença DRM** |* Entrega de licença de Serviços de Media (PlayReady, Widevine, FairPlay) <br/>* Servidor de licença axinom <br/>* Servidor de licença PlayReady personalizado |
| **Origem** |Ponto final de streaming de Serviços de Media |
| **Gestão de chaves** |Não é necessário para a implementação de referência |
| **Gestão de conteúdos** |Uma aplicação de consola C# |

Por outras palavras, tanto o IDP como o STS são utilizados com a AD Azure. O [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) é utilizado para o jogador. Tanto os Media Services como os Media Player suportam o DASH e o CENC com multi-DRM.

O diagrama seguinte mostra a estrutura e o fluxo globais com o mapeamento tecnológico anterior:

![CENC em Serviços de Media](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar encriptação dinâmica do CENC, a ferramenta de gestão de conteúdos utiliza as seguintes inputs:

* Conteúdo aberto
* Chave de conteúdo da geração/gestão chave
* URLs de aquisição de licença
* Uma lista de informações da Azure AD

Aqui está a saída da ferramenta de gestão de conteúdos:

* ContentKeyAuthorizationPolicy contém a especificação sobre como a entrega de licença verifica as especificações da licença JSON Web Token (JWT) e DRM.
* AssetDeliveryPolicy contém especificações sobre formato de streaming, proteção DRM e URLs de aquisição de licenças.

Aqui está o fluxo durante o tempo de execução:

* Após a autenticação do utilizador, é gerado um JWT.
* Uma das reivindicações contidas no JWT é uma alegação de grupos que contém o objeto de grupo ID EntitledUserGroup. Esta alegação é utilizada para passar a verificação de direitos.
* O leitor descarrega o manifesto cliente de conteúdo protegido pelo CENC e identifica o seguinte:
   * Identificação da chave.
   * O conteúdo está protegido pelo CENC.
   * URLs de aquisição de licença.
* O jogador faz um pedido de aquisição de licença com base no suporte do navegador/DRM. No pedido de aquisição da licença, o ID chave e o JWT também são apresentados. O serviço de entrega de licençaverifica o JWT e as reclamações contidas antes de emitir a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
A implementação inclui os seguintes passos:

1. Prepare os ativos de teste. Encódigo/embale um vídeo de teste para MP4 fragmentado multibitado em Serviços de Media. Este ativo *não* está protegido pela DRM. A proteção drm é feita por proteção dinâmica mais tarde.

2. Crie um ID chave e uma chave de conteúdo (opcionalmente a partir de uma semente chave). Neste caso, o sistema de gestão chave não é necessário porque apenas uma chave de identificação e chave de conteúdo são necessárias para um par de ativos de teste.

3. Utilize a API dos Serviços de Media para configurar serviços de entrega de licenças multi-DRM para o ativo de teste. Se utilizar servidores de licença personalizados pela sua empresa ou pelos fornecedores da sua empresa em vez de serviços de licença em Serviços de Media, pode saltar este passo. Pode especificar URLs de aquisição de licença no passo quando configurar a entrega da licença. A API dos Serviços de Media é necessária para especificar algumas configurações detalhadas, tais como restrição de política de autorização e modelos de resposta à licença para diferentes serviços de licença de DRM. Neste momento, o portal Azure não fornece o UI necessário para esta configuração. Para obter informações ao nível da API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

4. Utilize a API dos Serviços de Media para configurar a política de entrega de ativos para o ativo de teste. Para obter informações ao nível da API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

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

* Pode encontrar informações sobre o desenvolvedor no guia do desenvolvedor do [Azure Ative Directory](../../active-directory/azuread-dev/v1-overview.md).
* Pode encontrar informações de administrador na [Administração do seu diretório de inquilinos Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Algumas questões na implementação
Utilize as seguintes informações de resolução de problemas para ajudar nos problemas de implementação.

* O URL emitente deve terminar com "/". O público deve ser o CLIENTE do cliente de aplicação de jogador. Além disso, adicione "/" no final do URL emitente.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    No [Descodificador JWT,](http://jwt.calebb.net/) **vê-se aud** e **iss,** como mostra o JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Adicione permissões à aplicação em Azure AD no separador **Configure** da aplicação. São necessárias permissões para cada aplicação, tanto nas versões locais como implementadas.

    ![Permissões](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

## <a name="additional-topics-for-implementation"></a>Tópicos adicionais para implementação
Esta secção discute alguns tópicos adicionais no design e implementação.

### <a name="http-or-https"></a>HTTP ou HTTPS?
A aplicação ASP.NET do leitor de MVC deve suportar o seguinte:

* Autenticação do utilizador através do Azure AD, que se encontra em HTTPS.
* Troca de JWT entre o cliente e a Azure AD, que está em HTTPS.
* Aquisição de licença DRM pelo cliente, que deve estar em HTTPS se a entrega de licença for fornecida pela Media Services. A suíte de produto PlayReady não manda HTTPS para entrega de licença. Se o seu servidor de licença PlayReady estiver fora dos Serviços de Media, pode utilizar http ou HTTPS.

A aplicação ASP.NET jogador utiliza HTTPS como uma melhor prática, pelo que o Media Player está numa página em HTTPS. No entanto, http é preferido para streaming, por isso você precisa considerar a questão dos conteúdos mistos.

* O navegador não permite conteúdo misto. Mas plug-ins como Silverlight e o OSMF plug-in para suave e DASH permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça da capacidade de injetar JavaScript malicioso, o que pode fazer com que os dados dos clientes estejam em risco. Os navegadores bloqueiam esta capacidade por padrão. A única forma de trabalhar em torno dele é do lado do servidor (origem), permitindo que todos os domínios (independentemente de HTTPS ou HTTP). Provavelmente também não é uma boa ideia.
* Evite conteúdo misto. Tanto a aplicação do jogador como o Media Player devem utilizar HTTP ou HTTPS. Ao reproduzir conteúdo misto, a tecnologia silverlightSS requer a desobstrução de um aviso de conteúdo misto. A tecnologia flashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o seu ponto final de streaming foi criado antes de agosto de 2014, não apoiará HTTPS. Neste caso, crie e use um novo ponto final de streaming para HTTPS.

Na implementação de referência para conteúdos protegidos pela DRM, tanto a aplicação como o streaming estão em HTTPS. Para conteúdos abertos, o leitor não necessita de autenticação ou licença, para que possa utilizar http ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Ative Directory assinando capotamento de chave
A assinatura do capotamento da chave é um ponto importante a ter em conta na sua implementação. Se o ignorares, o sistema acabado acaba por deixar de funcionar completamente, no máximo, dentro de seis semanas.

A Azure AD utiliza padrões industriais para estabelecer confiança entre si e aplicações que utilizam a Azure AD. Especificamente, a Azure AD usa uma chave de assinatura que consiste num par de chaves público sacana e privado. Quando a Azure AD cria um símbolo de segurança que contém informações sobre o utilizador, é assinado pela Azure AD com uma chave privada antes de ser devolvido à aplicação. Para verificar se o símbolo é válido e é originário da AD Azure, a aplicação deve validar a assinatura do símbolo. A aplicação utiliza a chave pública exposta pela Azure AD que está contida no documento de metadados da federação do arrendatário. Esta chave pública, e a chave de assinatura de onde provém, é a mesma usada para todos os inquilinos em Azure AD.

Para obter mais informações sobre o capotamento da chave Azure AD, consulte [informações importantes sobre a assinatura](../../active-directory/active-directory-signing-key-rollover.md)de um capotamento de chave em Azure AD .

Entre o par de [chaves público-privado:](https://login.microsoftonline.com/common/discovery/keys/)

* A chave privada é usada pela Azure AD para gerar um JWT.
* A chave pública é utilizada por uma aplicação como serviços de entrega de licenças DRM em Serviços de Media para verificar o JWT.

Para efeitos de segurança, a Azure AD roda o certificado periodicamente (de seis em seis semanas). Em caso de falhas de segurança, o capotamento da chave pode ocorrer a qualquer momento. Por isso, os serviços de entrega de licenças nos Serviços de Media precisam de atualizar a chave pública utilizada à medida que o Azure AD gira o par chave. Caso contrário, a autenticação simbólica nos Serviços de Media falha e não é emitida nenhuma licença.

Para configurar este serviço, configura tokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando configurar os serviços de entrega de licenças DRM.

Aqui está o fluxo JWT:

* A Azure AD emite o JWT com a chave privada atual para um utilizador autenticado.
* Quando um jogador vê um CENC com conteúdo protegido multi-DRM, localiza primeiro o JWT emitido pela Azure AD.
* O jogador envia um pedido de aquisição de licença com o JWT para serviços de entrega de licença sintetizadores em Serviços de Media.
* Os serviços de entrega de licenças em Serviços de Media utilizam a chave pública atual/válida da Azure AD para verificar o JWT antes de emitir licenças.

Os serviços de entrega de licenças DRM verifiquem sempre a chave pública atual/válida da Azure AD. A chave pública apresentada pela Azure AD é a chave utilizada para verificar um JWT emitido pela Azure AD.

E se o capotamento da chave acontecer depois da Azure AD gerar um JWT mas antes do JWT ser enviado por jogadores para serviços de entrega de licenças DRM em Serviços de Media para verificação?

Como uma chave pode ser revestida a qualquer momento, mais do que uma chave pública válida está sempre disponível no documento de metadados da federação. A entrega da licença dos Serviços de Media pode utilizar qualquer uma das chaves especificadas no documento. Porque uma chave pode ser enrolada em breve, outra pode ser a sua substituição, e assim por diante.

### <a name="where-is-the-access-token"></a>Onde está o sinal de acesso?
Se olharmos para a forma como uma aplicação web chama uma aplicação API sob [identidade de Aplicação com a concessão de credenciais de cliente OAuth 2.0,](../../active-directory/azuread-dev/web-api.md)o fluxo de autenticação é o seguinte:

* Um utilizador insere-se no Azure AD na aplicação web. Para mais informações, consulte o [navegador Web para a aplicação web](../../active-directory/azuread-dev/web-app.md).
* O ponto final da autorização da Azure AD redireciona o agente utilizador de volta para a aplicação do cliente com um código de autorização. O agente utilizador devolve o código de autorização ao redirecionamento URI da aplicação cliente.
* A aplicação web precisa adquirir um sinal de acesso para que possa autenticar a Web API e recuperar o recurso pretendido. Faz um pedido ao ponto final do token Azure AD e fornece a credencial, id do cliente e id de aplicação web da API. Apresenta o código de autorização para provar que o utilizador consentiu.
* A Azure AD autentica a aplicação e devolve um sinal de acesso JWT que é usado para chamar a Web API.
* Ao longo do HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Bearer" no cabeçalho "Autorização" do pedido à Web API. A Web API valida então o JWT. Se a validação for bem sucedida, devolve o recurso desejado.

Nesta aplicação, a API da web confia que a aplicação web autenticou o utilizador. Por esta razão, este padrão é chamado de subsistema de confiança. O [diagrama de fluxo](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) de autorização descreve como funciona o fluxo de autorização-concessão de códigos.

A aquisição de licença sem restrições simbólicas segue o mesmo padrão de subsistema fidedigno. O serviço de entrega de licenças em Serviços de Media é o recurso Web API, ou o "recurso back-end" a que uma aplicação web precisa de aceder. Onde está o sinal de acesso?

O sinal de acesso é obtido a partir de Azure AD. Após a autenticação bem sucedida do utilizador, é devolvido um código de autorização. O código de autorização é então utilizado, juntamente com o ID do cliente e a chave da aplicação, para trocar pelo token de acesso. O sinal de acesso é usado para aceder a uma aplicação "pointer" que aponta ou representa o serviço de entrega de licenças dos Serviços de Media.

Para registar e configurar a aplicação de ponteiros em Azure AD, tome os seguintes passos:

1. No inquilino da AD Azure:

   * Adicione uma aplicação (recurso) com o URL de entrada de sinal https://[resource_name].azurewebsites.net/. 
   * Adicione um ID de aplicação com o URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Adicione uma nova chave para a aplicação de recursos.

3. Atualize o ficheiro manifesto da aplicação para que o grupoMembershipClaims tenha o valor "groupMembershipClaims": "All".

4. Na aplicação Azure AD que aponta para a aplicação web do jogador, na secção **Permissões a outras aplicações,** adicione a app de recursos que foi adicionada no passo 1. Sob **permissão delegada,** selecione **Access [resource_name]**. Esta opção dá permissão à aplicação web para criar fichas de acesso que acedam à aplicação de recursos. Faça isto tanto para a versão local como implementada da aplicação web se desenvolver com o Visual Studio e a aplicação web Azure.

O JWT emitido pela Azure AD é o sinal de acesso usado para aceder ao recurso ponteiro.

### <a name="what-about-live-streaming"></a>E o streaming ao vivo?
A discussão anterior incidiu sobre os ativos a pedido. E o streaming ao vivo?

Você pode usar exatamente o mesmo design e implementação para proteger o streaming ao vivo em Media Services, tratando o ativo associado a um programa como um ativo VOD.

Especificamente, para fazer streaming ao vivo nos Serviços de Media, é necessário criar um canal e depois criar um programa sob o canal. Para criar o programa, é necessário criar um ativo que contenha o arquivo ao vivo para o programa. Para fornecer ao CENC uma proteção multi-DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de iniciar o programa.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Media?
Muitas vezes, os clientes investiram numa exploração de servidores de licenças, quer no seu próprio centro de dados, quer numa hospedada por prestadores de serviços drm. Com a Media Services Content Protection, pode operar em modo híbrido. Os conteúdos podem ser hospedados e protegidos dinamicamente nos Serviços de Media, enquanto as licenças de DRM são entregues por servidores fora dos Serviços de Media. Neste caso, considere as seguintes alterações:

* O STS precisa de emitir fichas aceitáveis e podem ser verificadas pela exploração do servidor de licenças. Por exemplo, os servidores de licença Widevine fornecidos pela Axinom requerem um JWT específico que contém uma mensagem de direito. Portanto, é preciso ter um STS para emitir tal JWT. Para obter informações sobre este tipo de implementação, vá ao Centro de [Documentação Azure](https://azure.microsoft.com/documentation/) e consulte [a Use Axinom para entregar licenças widevine à Azure Media Services.](media-services-axinom-integration.md)
* Já não precisa de configurar o serviço de entrega de licenças (ContentKeyAuthorizationPolicy) nos Serviços de Media. Você precisa fornecer os URLs de aquisição de licença (para PlayReady, Widevine e FairPlay) quando configurar o AssetDeliveryPolicy para configurar o CENC com multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>E se eu quiser usar uma STS personalizada?
Um cliente pode optar por utilizar um STS personalizado para fornecer JWTs. As razões incluem:

* O IDP usado pelo cliente não suporta STS. Neste caso, um STS personalizado pode ser uma opção.
* O cliente pode precisar de um controlo mais flexível ou mais apertado para integrar o STS com o sistema de faturação de assinantes do cliente. Por exemplo, um operador MVPD pode oferecer vários pacotes de subscritores OTT, tais como premium, básico e esporte. O operador pode querer comparar as reclamações num símbolo com o pacote de um assinante, de modo a que apenas os conteúdos de um pacote específico sejam disponibilizados. Neste caso, um STS personalizado proporciona a flexibilidade e controlo necessários.

Quando utilizar um STS personalizado, devem ser feitas duas alterações:

* Quando configura o serviço de entrega de licenças para um ativo, precisa especificar a chave de segurança utilizada para a verificação pelo STS personalizado em vez da chave atual da AD Azure. (Mais detalhes se seguem.) 
* Quando um símbolo JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual em Azure AD.

Existem dois tipos de chaves de segurança:

* Chave simétrica: A mesma tecla é usada para gerar e verificar um JWT.
* Chave assimétrica: Um par de chaves público-privado num certificado X509 é usado com uma chave privada para encriptar/gerar um JWT e com a chave pública para verificar o símbolo.

> [!NOTE]
> Se utilizar a .NET Framework/C# como plataforma de desenvolvimento, o certificado X509 utilizado para uma chave de segurança assimétrica deve ter um comprimento-chave de, pelo menos, 2048. Esta é uma exigência da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey in .NET Framework. Caso contrário, é lançada a seguinte exceção:
> 
> IDX10630: O 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ser menor do que os bits '2048'.

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

![Conta de domínio de inquilino ad ida e revestido de Azure](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com cartão inteligente**: A página de inscrição personalizada pela Microsoft corporate IT com autenticação de dois fatores.

![Conta de domínio de inquilino ad ida e revestido de Azure](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft**: A página de sessão da conta microsoft para os consumidores.

![Conta de domínio de inquilino ad ida e revestido de Azure](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Utilize extensões de mídia encriptadas para playReady
Num navegador moderno com Extensões de Mídia Encriptadas (EME) para suporte PlayReady, como o Internet Explorer 11 no Windows 8.1 ou mais tarde e o navegador Microsoft Edge no Windows 10, o PlayReady é o DRM subjacente ao EME.

![Use eme para playReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A área do leitor escuro é porque a proteção PlayReady impede-o de fazer uma captura de ecrã de vídeo protegido.

A seguinte imagem mostra os plug-ins do jogador e o suporte Microsoft Security Essentials (MSE)/EME:

![Plug-ins do jogador para playReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

O EME no Microsoft Edge e no Internet Explorer 11 no Windows 10 permite que o [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) seja invocado em dispositivos Windows 10 que o suportem. O PlayReady SL3000 desbloqueia o fluxo de conteúdo premium melhorado (4K, HDR) e novos modelos de entrega de conteúdos (para conteúdo melhorado).

Para se focar nos dispositivos Windows, o PlayReady é o único DRM disponível no hardware disponível nos dispositivos Windows (PlayReady SL3000). Um serviço de streaming pode utilizar o PlayReady através do EME ou através de uma aplicação Universal Windows Platform e oferecer uma maior qualidade de vídeo utilizando o PlayReady SL3000 do que outro DRM. Normalmente, o conteúdo até 2K flui através do Chrome ou Firefox, e o conteúdo até 4K flui através do Microsoft Edge/Internet Explorer 11 ou de uma aplicação Universal Windows Platform no mesmo dispositivo. O valor depende das definições de serviço e implementação.

#### <a name="use-eme-for-widevine"></a>Utilizar eme para widevine
Num navegador moderno com suporte EME/Widevine, como o Chrome 41+ no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome no Android 4.4.4, o Google Widevine é o DRM atrás do EME.

![Utilizar eme para widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

A Widevine não o impede de fazer uma captura de ecrã de vídeo protegido.

![Plug-ins do jogador para widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Utilizadores sem direito
Se um utilizador não for membro do grupo "Utilizadores Intitulados", o utilizador não passa na verificação de direitos. O serviço de licença multi-DRM recusa-se então a emitir a licença solicitada, como mostrado. A descrição detalhada é "Licença adquirida falhou", que é como projetado.

![Utilizadores sem direito](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Executar um serviço de fichade segurança personalizado
Se executar um STS personalizado, o JWT é emitido pelo STS personalizado utilizando uma chave simétrica ou assimétrica.

A imagem que se segue mostra um cenário que utiliza uma chave simétrica (utilizando o Chrome):

![STS personalizado com uma chave simétrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

A imagem que se segue mostra um cenário que utiliza uma chave assimétrica através de um certificado X509 (utilizando um navegador moderno da Microsoft):

![STS personalizado com uma chave assimétrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Em ambos os casos anteriores, a autenticação do utilizador mantém-se na mesma. Passa-se através do Azure AD. A única diferença é que os JWTs são emitidos pelo STS personalizado em vez de Azure AD. Quando configura a proteção dinâmica do CENC, a restrição de serviço de entrega de licenças especifica o tipo de JWT, seja uma chave simétrica ou assimétrica.

## <a name="summary"></a>Resumo

Este documento discutiu o CENC com DRM multinativo e controlo de acesso através da autenticação simbólica, do seu design e da sua implementação utilizando o Azure, media services e media Player.

* Foi apresentado um desenho de referência que contém todos os componentes necessários num subsistema DRM/CENC.
* Foi apresentada uma implementação de referência no Azure, Media Services e Media Player.
* Foram também abordados alguns tópicos diretamente envolvidos no desenho e implementação.

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
