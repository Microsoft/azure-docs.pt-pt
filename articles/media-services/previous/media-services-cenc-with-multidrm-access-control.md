---
title: Design de um sistema de proteção de conteúdos com controlo de acessos utilizando a Azure Media Services Microsoft Docs
description: Saiba como licenciar o Kit de Porting do Cliente de Streaming Microsoft.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: b98b66d8f0350c32e89d62d776ee1288d9271712
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841157"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Conceção de um sistema de proteção de conteúdos com controlo de acesso usando a Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Descrição geral

Conceber e construir um subsistema de gestão de direitos digitais (DRM) para uma solução de streaming (OTT) ou de streaming online é uma tarefa complexa. Os operadores/fornecedores de vídeo online normalmente subcontratam esta tarefa a prestadores de serviços drm especializados. O objetivo deste documento é apresentar um design de referência e implementação de um subsistema de DRM de ponta a ponta numa solução de streaming OTT ou online.

Os leitores-alvo deste documento são engenheiros que trabalham em subsistemas DE DRM de soluções de streaming/multiecrão online ou leitores interessados em subsistemas DRM. O pressuposto é que os leitores estão familiarizados com pelo menos uma das tecnologias de DRM no mercado, como PlayReady, Widevine, FairPlay ou Adobe Access.

Nesta discussão de DRM, também incluímos encriptação comum (CENC) com multi-DRM. Uma grande tendência no streaming online e na indústria OTT é usar o CENC com DRM multi-nativo em várias plataformas de clientes. Esta tendência é uma mudança em face da anterior que utilizou um único DRM e o seu cliente SDK para várias plataformas de clientes. Quando utiliza o CENC com DRM multi-nativo, tanto o PlayReady como o Widevine são encriptados de acordo com a especificação [De encriptação Comum (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Os benefícios do CENC com multi-DRM são que:

* Reduz o custo de encriptação porque um único processo de encriptação é usado para direcionar diferentes plataformas com os seus DRMs nativos.
* Reduz o custo de gestão de ativos encriptados porque apenas uma cópia de ativos encriptados é necessária.
* Elimina o custo de licenciamento do cliente DRM porque o cliente drm nativo é geralmente gratuito na sua plataforma nativa.

A Microsoft é um promotor ativo do DASH e CENC juntamente com alguns dos principais players da indústria. A Azure Media Services presta apoio ao DASH e AO CENC. Para anúncios recentes, consulte os seguintes blogs:

*  [Anunciar os serviços de entrega de licença Widevine da Google nos Serviços de Multimédia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services adiciona embalagem do Google Widevine para entregar um stream multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Objetivos do artigo

Os objetivos deste artigo são:

* Fornecer um design de referência de um subsistema DRM que utiliza CENC com multi-DRM.
* Fornecer uma implementação de referência numa plataforma Azure/Media Services.
* Discuta alguns tópicos de design e implementação.

No artigo, o termo "multi-DRM" abrange os seguintes produtos:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

A tabela seguinte resume a plataforma nativa/aplicação nativa e os navegadores suportados por cada DRM.

| **Cliente plataforma** | **Suporte drm nativo** | **Navegador/app** | **Formatos de streaming** |
| --- | --- | --- | --- |
| **Smart TVs, operador STBs, OTT STBs** |PlayReady principalmente, e/ou Widevine, e/ou outros |Linux, Ópera, WebKit, outros |Vários formatos |
| **Dispositivos Windows 10 (Pc Windows, tablets Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Plataforma Universal do Windows |DASH (para HLS, PlayReady não é suportado)<br/><br/>DASH, Smooth Streaming (para HLS, PlayReady não é suportado) |
| **Dispositivos Android (telefone, tablet, TV)** |Widevine |Cromado/EME |DASH |
| **iOS (iPhone, iPad), clientes OS X e Apple TV** |FairPlay |Safari 8+/EME |HLS |

Tendo em conta o estado atual de implantação de cada DRM, um serviço normalmente quer implementar dois ou três DRMs para se certificar de que aborda todos os tipos de pontos finais da melhor forma.

Existe uma compensação entre a complexidade da lógica de serviço e a complexidade do lado do cliente para alcançar um determinado nível de experiência do utilizador nos vários clientes.

Para fazer a sua seleção, lembre-se:

* O PlayReady é implementado de forma nativa em todos os dispositivos Windows, em alguns dispositivos Android, e disponível através de SDKs de software em praticamente qualquer plataforma.
* O Widevine é implementado de forma nativa em todos os dispositivos Android, no Chrome e em alguns outros dispositivos.
* O FairPlay está disponível apenas em clientes iOS e Mac OS ou através do iTunes.

Existem duas opções para um multi-DRM típico:

* PlayReady e Widevine
* PlayReady, Widevine e FairPlay

## <a name="a-reference-design"></a>Um design de referência
Esta secção apresenta um design de referência que é agnóstico às tecnologias usadas para implementá-lo.

Um subsistema DRM pode conter os seguintes componentes:

* Gestão de chaves
* Embalagem DRM
* Entrega de licença de DRM
* Verificação de direitos
* Autenticação/autorização
* Leitor
* Rede de origem/entrega de conteúdos (CDN)

O diagrama a seguir ilustra a interação de alto nível entre os componentes de um subsistema DRM:

![Subsistema DRM com CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Fornecedor de identidade (IDP)** |Azure Active Directory (Azure AD) |
| **Serviço de ficha de segurança (STS)** |Azure AD |
| **Fluxo de trabalho de proteção DRM** |Proteção dinâmica dos Serviços de Mídia |
| **Entrega de licença DRM** |* Entrega de licença de Serviços de Mídia (PlayReady, Widevine, FairPlay) <br/>* Servidor de licença axinom <br/>* Servidor de licença personalizado PlayReady |
| **Origem** |Ponto final de streaming de serviços de mídia |
| **Gestão de chaves** |Não é necessário para a implementação de referência |
| **Content management (Gestão de conteúdos)** |Uma aplicação de consola C# |

Por outras palavras, tanto o IDP como o STS são utilizados com Azure AD. A [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) é utilizada para o leitor. Tanto os Media Services como o Media Player apoiam o DASH e o CENC com multi-DRM.

O seguinte diagrama mostra a estrutura global e o fluxo com o mapeamento da tecnologia anterior:

![CENC em Serviços de Mídia](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar encriptação dinâmica do CENC, a ferramenta de gestão de conteúdos utiliza as seguintes entradas:

* Conteúdo aberto
* Chave de conteúdo da geração/gestão chave
* URLs de aquisição de licença
* Uma lista de informações da Azure AD

Aqui está a saída da ferramenta de gestão de conteúdos:

* ContentKeyAuthorizationPolicy contém a especificação de como a entrega da licença verifica as especificações da licença JSON Web Token (JWT) e DRM.
* A AssetDeliveryPolicy contém especificações sobre formato de streaming, proteção de DRM e URLs de aquisição de licenças.

Aqui está o fluxo durante o tempo de funcionação:

* Após a autenticação do utilizador, é gerado um JWT.
* Uma das alegações contidas no JWT é uma alegação de grupos que contém o iD iD do grupo ID EntitledUserGroup. Esta reclamação é usada para passar o cheque de direito.
* O jogador descarrega o manifesto do cliente de conteúdo protegido pelo CENC e identifica o seguinte:
   * Identificação de chave.
   * O conteúdo está protegido pelo CENC.
   * URLs de aquisição de licença.
* O jogador faz um pedido de aquisição de licença com base no suporte do browser/DRM. No pedido de aquisição de licença, o ID chave e o JWT também são submetidos. O serviço de entrega de licenças verifica o JWT e as reclamações contidas antes de emitir a licença necessária.

## <a name="implementation"></a>Implementação
### <a name="implementation-procedures"></a>Procedimentos de implementação
A implementação inclui as seguintes etapas:

1. Preparar os ativos do teste. Codificar/embalar um vídeo de teste para MP4 fragmentado multi-bitrate nos Serviços de Mídia. Este ativo *não* está protegido pela DRM. A proteção por DRM é feita através de uma proteção dinâmica mais tarde.

2. Crie um ID chave e uma chave de conteúdo (opcionalmente a partir de uma semente de chave). Neste caso, o sistema de gestão chave não é necessário porque apenas uma chave de identificação e chave de conteúdo são necessárias para um par de ativos de teste.

3. Utilize a API dos Serviços de Mídia para configurar serviços de entrega de licenças multi-DRM para o ativo de teste. Se utilizar servidores de licença personalizados da sua empresa ou dos fornecedores da sua empresa em vez de serviços de licença nos Serviços de Comunicação Social, pode saltar este passo. Pode especificar urls de aquisição de licença no passo quando configurar a entrega da licença. A API dos Serviços de Mídia é necessária para especificar algumas configurações detalhadas, tais como restrição de política de autorização e modelos de resposta à licença para diferentes serviços de licenças drm. Neste momento, o portal Azure não fornece a UI necessária para esta configuração. Para obter informações de nível API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

4. Utilize a API dos Serviços de Mídia para configurar a política de entrega de ativos para o ativo de teste. Para obter informações de nível API e código de amostra, consulte [Use PlayReady e/ou Widevine dynamic common encryption](media-services-protect-with-playready-widevine.md).

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

* Pode encontrar informações sobre o desenvolvedor no [guia do programador Azure Ative Directory](../../active-directory/azuread-dev/v1-overview.md).
* Você pode encontrar informações de administrador em [Administrar o seu diretório de inquilinos Azure AD](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Algumas questões em implementação
Utilize as seguintes informações de resolução de problemas para obter ajuda com problemas de implementação.

* O URL do emitente deve terminar com "/". O público deve ser a identificação do cliente de aplicação do jogador. Além disso, adicione "/" no final do URL do emitente.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    No [Decodificador JWT,](http://jwt.calebb.net/)você vê **aud** e **iss,** como mostrado no JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Adicione permissões à aplicação em Azure AD no **separador Configurar** da aplicação. São necessárias permissões para cada aplicação, tanto as versões locais como implementadas.

    ![Permissões](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Como adiciona suporte para JWT (Azure AD) para além do SWT (ACS), o TokenType padrão é TokenType.JWT. Se utilizar o SWT/ACS, deve definir o símbolo para TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Tópicos adicionais para implementação
Esta secção discute alguns tópicos adicionais na conceção e implementação.

### <a name="http-or-https"></a>HTTP OU HTTPS?
A aplicação do leitor de MVC ASP.NET deve suportar o seguinte:

* Autenticação do utilizador através do Azure AD, que se encontra em HTTPS.
* Troca de JWT entre o cliente e a Azure AD, que está em HTTPS.
* Aquisição de licença drm pelo cliente, que deve estar sob HTTPS se a entrega da licença for fornecida pela Media Services. A suíte de produto PlayReady não manda HTTPS para entrega de licença. Se o seu servidor de licença PlayReady estiver fora dos Serviços de Mídia, pode utilizar HTTP ou HTTPS.

A aplicação ASP.NET do jogador utiliza HTTPS como uma boa prática, pelo que o Media Player está numa página em HTTPS. No entanto, HTTP é preferido para streaming, pelo que é necessário considerar a questão dos conteúdos mistos.

* O navegador não permite conteúdo misto. Mas plug-ins como Silverlight e o plug-in OSMF para smooth e DASH permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça da capacidade de injetar JavaScript malicioso, o que pode fazer com que os dados dos clientes estejam em risco. Os navegadores bloqueiam esta capacidade por padrão. A única forma de contornar isto é no lado do servidor (origem) permitindo que todos os domínios (independentemente de HTTPS ou HTTP). Isto também provavelmente não é uma boa ideia.
* Evite o conteúdo misto. Tanto a aplicação do jogador como o Media Player devem utilizar HTTP ou HTTPS. Ao reproduzir conteúdo misto, a tecnologia SilverlightS requer uma limpeza de um aviso de conteúdo misto. A tecnologia flashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o seu ponto final de streaming foi criado antes de agosto de 2014, não apoiará HTTPS. Neste caso, crie e utilize um novo ponto final de streaming para HTTPS.

Na implementação de referência para conteúdos protegidos por DRM, tanto a aplicação como o streaming estão em HTTPS. Para conteúdos abertos, o leitor não necessita de autenticação ou licença, pelo que pode utilizar HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Ative Directory assinando a chave de capotamento
A assinatura da chave de capotamento é um ponto importante a ter em conta na sua implementação. Se o ignorares, o sistema acabado acaba por deixar de funcionar completamente, no prazo máximo de seis semanas.

A Azure AD usa padrões da indústria para estabelecer confiança entre si e aplicações que usam Azure AD. Especificamente, a Azure AD usa uma chave de assinatura que consiste num par de chaves público e privado. Quando o Azure AD cria um símbolo de segurança que contém informações sobre o utilizador, é assinado pela Azure AD com uma chave privada antes de ser devolvido à aplicação. Para verificar se o token é válido e originário da Azure AD, o pedido deve validar a assinatura do token. A aplicação utiliza a chave pública exposta pela Azure AD que está contida no documento de metadados da federação do arrendatário. Esta chave pública, e a chave de assinatura de onde provém, é a mesma usada para todos os inquilinos em Azure AD.

Para obter mais informações sobre a capotagem da chave Azure, consulte [informações importantes sobre a assinatura da chave de capotamento em Azure AD](../../active-directory/develop/active-directory-signing-key-rollover.md).

Entre o [par de chaves público-privado:](https://login.microsoftonline.com/common/discovery/keys/)

* A chave privada é usada pela Azure AD para gerar um JWT.
* A chave pública é utilizada por uma aplicação como os serviços de entrega de licenças DRM nos Serviços de Comunicação Social para verificar o JWT.

Por razões de segurança, a Azure AD roda o certificado periodicamente (de seis em seis semanas). Em caso de falhas de segurança, o capotamento da chave pode ocorrer a qualquer momento. Por isso, os serviços de entrega de licenças nos Serviços de Media precisam de atualizar a chave pública utilizada à medida que o Azure AD roda o par de chaves. Caso contrário, a autenticação simbólica nos Serviços de Comunicação Social falha e não é emitida nenhuma licença.

Para configurar este serviço, você configura TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando configurar serviços de entrega de licenças DRM.

Aqui está o fluxo JWT:

* A Azure AD emite o JWT com a chave privada atual para um utilizador autenticado.
* Quando um leitor vê um CENC com conteúdo protegido multi-DRM, primeiro localiza o JWT emitido pela Azure AD.
* O jogador envia um pedido de aquisição de licença com o JWT para licenciar serviços de entrega em Media Services.
* Os serviços de entrega de licenças nos Serviços de Media utilizam a chave pública atual/válida da Azure AD para verificar o JWT antes de emitir licenças.

Os serviços de entrega de licenças DRM verifiquem sempre a chave pública atual/válida da Azure AD. A chave pública apresentada pela Azure AD é a chave utilizada para verificar um JWT emitido pela Azure AD.

E se a capotagem da chave acontecer depois de o Azure AD gerar um JWT mas antes do JWT ser enviado pelos jogadores para os serviços de entrega de licenças drm nos Serviços de Media para verificação?

Como uma chave pode ser revirada a qualquer momento, mais do que uma chave pública válida está sempre disponível no documento de metadados da federação. A entrega da licença dos Serviços de Comunicação Social pode utilizar qualquer uma das chaves especificadas no documento. Porque uma chave pode ser enrolada em breve, outra pode ser a sua substituição, e assim por diante.

### <a name="where-is-the-access-token"></a>Onde está o símbolo de acesso?
Se olhar como uma aplicação web chama uma aplicação API sob identidade de aplicação com o subsídio de credenciais de [cliente OAuth 2.0,](../../active-directory/azuread-dev/web-api.md)o fluxo de autenticação é o seguinte:

* Um utilizador assina no Azure AD na aplicação web. Para obter mais informações, consulte [o navegador Web para a aplicação web.](../../active-directory/azuread-dev/web-app.md)
* O ponto final de autorização Azure AD redireciona o agente do utilizador de volta para a aplicação do cliente com um código de autorização. O agente utilizador devolve o código de autorização ao URI de redirecionamento da aplicação do cliente.
* A aplicação web precisa adquirir um token de acesso para que possa autenticar a API web e recuperar o recurso pretendido. Faz um pedido ao ponto final da Azure AD e fornece a credencial, identificação do cliente e iD de aplicação da Web API. Apresenta o código de autorização para provar que o utilizador consentiu.
* O Azure AD autentica a aplicação e devolve um token de acesso JWT que é usado para chamar a API web.
* Em HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Portador" no cabeçalho "Autorização" do pedido à API web. A API web valida então o JWT. Se a validação for bem sucedida, devolve o recurso pretendido.

Neste fluxo de identidade de aplicação, a API web confia que a aplicação web autenticou o utilizador. Por esta razão, este padrão é chamado de subsistema de confiança. O [diagrama de fluxo](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) de autorização descreve como funciona o fluxo de concessão de código de autorização.

A aquisição de licença com restrição simbólica segue o mesmo padrão de subsistema fidedignos. O serviço de entrega de licenças nos Media Services é o recurso API web, ou o "recurso back-end" a que uma aplicação web precisa de aceder. Então, onde está o símbolo de acesso?

O token de acesso é obtido a partir de Azure AD. Após a autenticação bem sucedida do utilizador, é devolvido um código de autorização. O código de autorização é então utilizado, juntamente com o ID do cliente e a chave da aplicação, para trocar o token de acesso. O token de acesso é utilizado para aceder a uma aplicação de "pointer" que aponta para ou representa o serviço de entrega de licenças dos Media Services.

Para registar e configurar a app pointer em Azure AD, tome os seguintes passos:

1. No inquilino da AD AZure:

   * Adicione uma aplicação (recurso) com o URL de inscrição https://[resource_name].azurewebsites.net/. 
   * Adicione um ID de aplicação com o URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Adicione uma nova chave para a aplicação de recursos.

3. Atualizar o ficheiro manifesto da aplicação para que a propriedade groupMembershipClaims tenha o valor "groupMembershipClaims": "All".

4. Na aplicação AD AZure que aponta para a aplicação web do jogador, na secção **Permissões a outras aplicações**, adicione a app de recursos que foi adicionada no passo 1. Sob **permissão delegada,** selecione **Access [resource_name]**. Esta opção dá permissão à aplicação web para criar fichas de acesso que acedam à aplicação de recursos. Faça isto tanto para a versão local como implementada da aplicação web se desenvolver com o Visual Studio e a aplicação web Azure.

O JWT emitido pela Azure AD é o símbolo de acesso utilizado para aceder ao recurso pointer.

### <a name="what-about-live-streaming"></a>E o streaming ao vivo?
A discussão anterior centrou-se nos ativos a pedido. E o streaming ao vivo?

Você pode usar exatamente o mesmo design e implementação para proteger o streaming ao vivo em Media Services, tratando o ativo associado a um programa como um ativo VOD.

Especificamente, para fazer streaming ao vivo nos Serviços de Media, você precisa criar um canal e, em seguida, criar um programa sob o canal. Para criar o programa, é necessário criar um ativo que contenha o arquivo ao vivo para o programa. Para fornecer ao CENC a proteção multi-DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de iniciar o programa.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licenças fora dos Serviços de Media?
Muitas vezes, os clientes investiram numa fazenda de servidores de licenças, quer no seu próprio centro de dados, quer num hospedado por prestadores de serviços DRM. Com a Proteção de Conteúdos dos Serviços de Mídia, pode operar em modo híbrido. Os conteúdos podem ser hospedados e dinamicamente protegidos nos Serviços de Mídia, enquanto as licenças de DRM são entregues por servidores fora dos Serviços de Mídia. Neste caso, considere as seguintes alterações:

* O STS precisa de emitir fichas que sejam aceitáveis e que possam ser verificadas pela fazenda do servidor de licenças. Por exemplo, os servidores de licença widevine fornecidos pela Axinom requerem um JWT específico que contém uma mensagem de direito. Portanto, você precisa ter uma STS para emitir tal JWT. Para obter informações sobre este tipo de implementação, vá ao Centro de [Documentação Azure](https://azure.microsoft.com/documentation/) e consulte [a Use Axinom para entregar licenças widevine à Azure Media Services](media-services-axinom-integration.md).
* Já não precisa de configurar o serviço de entrega de licenças (ContentKeyAuthorizationPolicy) nos Serviços de Comunicação Social. Tem de fornecer urLs de aquisição de licença (para PlayReady, Widevine e FairPlay) quando configurar a AssetDeliveryPolicy para configurar o CENC com multi-DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>E se eu quiser usar uma STS personalizada?
Um cliente pode optar por utilizar uma STS personalizada para fornecer JWTs. As razões incluem:

* O IDP utilizado pelo cliente não suporta STS. Neste caso, uma STS personalizada pode ser uma opção.
* O cliente poderá necessitar de um controlo mais flexível ou mais apertado para integrar o STS com o sistema de faturação de assinantes do cliente. Por exemplo, um operador MVPD pode oferecer vários pacotes de assinantes OTT, tais como premium, básico e desportivo. O operador pode querer comparar as reclamações num token com o pacote de um assinante para que apenas o conteúdo de um pacote específico seja disponibilizado. Neste caso, uma STS personalizada proporciona a flexibilidade e controlo necessários.

Quando utilizar uma STS personalizada, devem ser feitas duas alterações:

* Ao configurar o serviço de entrega de licenças para um ativo, precisa de especificar a chave de segurança utilizada para a verificação pelo STS personalizado em vez da chave atual do Azure AD. (Mais detalhes seguem.) 
* Quando um token JTW é gerado, uma chave de segurança é especificada em vez da chave privada do certificado X509 atual em Azure AD.

Existem dois tipos de chaves de segurança:

* Chave simétrica: A mesma chave é utilizada para gerar e verificar um JWT.
* Chave assimétrica: Um par de chaves público-privado num certificado X509 é usado com uma chave privada para encriptar/gerar um JWT e com a chave pública para verificar o token.

> [!NOTE]
> Se utilizar o .NET Framework/C# como plataforma de desenvolvimento, o certificado X509 utilizado para uma chave de segurança assimétrica deve ter um comprimento de chave de pelo menos 2048. Esta é uma exigência do sistema de classe.IdentityModel.Tokens.X509AsymmetricSecurityKey em .NET Framework. Caso contrário, é lançada a seguinte exceção:
> 
> IDX10630: O 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para a assinatura não pode ser menor do que as bits de '2048'.

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

![Screenshot que mostra a página de inscrição personalizada do domínio do inquilino Azure A D personalizado.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com cartão inteligente**: A página de sent-in personalizada pela Microsoft corporate IT com autenticação de dois fatores.

![Screenshot que mostra a página de sent-in personalizada pela Microsoft corporate I T com autenticação de dois fatores.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft**: A página de sindução da conta Microsoft para os consumidores.

![Conta de domínio do inquilino da Azure AD personalizada](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Use extensões de mídia encriptadas para playReady
Num navegador moderno com extensões de mídia encriptadas (EME) para suporte playReady, como o Internet Explorer 11 no Windows 8.1 ou mais tarde e o navegador Microsoft Edge no Windows 10, o PlayReady é o DRM subjacente para EME.

![Use EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A área do jogador escuro é porque a proteção PlayReady impede-o de fazer uma captura de ecrã de vídeo protegido.

A imagem que se segue mostra os plug-ins do jogador e o suporte do Microsoft Security Essentials (MSE)/EME:

![Plug-ins do jogador para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

O EME no Microsoft Edge e o Internet Explorer 11 no Windows 10 permite que [o PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) seja invocado em dispositivos Windows 10 que o suportam. O PlayReady SL3000 desbloqueia o fluxo de conteúdo premium melhorado (4K, HDR) e novos modelos de entrega de conteúdo (para conteúdo melhorado).

Para se focar nos dispositivos Windows, o PlayReady é o único DRM no hardware disponível em dispositivos Windows (PlayReady SL3000). Um serviço de streaming pode utilizar o PlayReady através do EME ou através de uma aplicação Universal Windows Platform e oferecer uma maior qualidade de vídeo utilizando o PlayReady SL3000 do que outro DRM. Normalmente, o conteúdo até 2K flui através do Chrome ou Firefox, e o conteúdo flui até 4K através do Microsoft Edge/Internet Explorer 11 ou de uma aplicação Universal Windows Platform no mesmo dispositivo. O valor depende das definições de serviço e implementação.

#### <a name="use-eme-for-widevine"></a>Use EME para a widevine
Num navegador moderno com suporte EME/Widevine, como o Chrome 41+ no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome no Android 4.4.4, o Google Widevine é o DRM por trás da EME.

![Use EME para a widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

O Widevine não te impede de fazer uma captura de ecrã de vídeo protegido.

![Plug-ins do jogador para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Utilizadores nãoententes
Se um utilizador não for membro do grupo "Utilizadores Com Direito", o utilizador não passa o cheque de direito. O serviço de licença multi-DRM recusa-se então a emitir a licença solicitada como mostrado. A descrição detalhada é "Licença adquirir falhado", que é como projetado.

![Utilizadores nãoententes](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Executar um serviço de ficha de segurança personalizado
Se executar uma STS personalizada, o JWT é emitido pela STS personalizada utilizando uma chave simétrica ou assimétrica.

A imagem que se segue mostra um cenário que utiliza uma chave simétrica (utilizando o Chrome):

![STS personalizado com uma chave simétrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

A imagem a seguir mostra um cenário que usa uma chave assimétrica através de um certificado X509 (utilizando um navegador moderno da Microsoft):

![STS personalizado com uma chave assimétrica](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Em ambos os casos anteriores, a autenticação do utilizador mantém-se a mesma. Passa-se através do Azure AD. A única diferença é que os JWTs são emitidos pela STS personalizada em vez de Azure AD. Ao configurar uma proteção dinâmica do CENC, a restrição do serviço de entrega de licença especifica o tipo de JWT, seja uma chave simétrica ou assimétrica.

## <a name="summary"></a>Resumo

Este documento discutiu o CENC com DRM multi-nativo e controlo de acesso através da autenticação simbólica, do seu design e da sua implementação através da utilização do Azure, Media Services e Media Player.

* Foi apresentado um desenho de referência que contém todos os componentes necessários num subsistema DRM/CENC.
* Foi apresentada uma implementação de referência no Azure, Media Services e Media Player.
* Foram também discutidos alguns tópicos diretamente envolvidos na conceção e implementação.

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
