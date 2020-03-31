---
title: Azure Media Services v3 frequentemente fez perguntas Microsoft Docs
description: Este artigo dá respostas à Azure Media Services v3 frequentemente colocadas perguntas.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: 11123ee04dd02a60dff0b88e2e6e85fcd613a7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067998"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Serviços de Media v3 frequentemente questionados

Este artigo dá respostas à Azure Media Services (AMS) v3 frequentemente colocadas perguntas.

## <a name="general"></a>Geral

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Que funções o Azure pode desempenhar ações sobre os recursos da Azure Media Services? 

Consulte [o controlo de acesso baseado em funções (RBAC) para as contas dos Serviços de Media](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Como é que transmite para dispositivos apple iOS?

Certifique-se de que tem "(formato=m3u8-aapl)" no final do seu percurso (após a parte "/manifesto" do URL) para dizer ao servidor de origem de streaming para devolver conteúdo hls para consumo em dispositivos nativos apple iOS (para mais detalhes, ver [conteúdo de entrega).](dynamic-packaging-overview.md)

### <a name="how-do-i-configure-media-reserved-units"></a>Como configurar unidades reservadas aos media?

Para as tarefas de Análise de áudio e de Análise de vídeo acionadas por Serviços de Multimédia v3 ou Video Indexer, é altamente recomendado que aprovisione a sua conta com 10 MRUs de S3. Se precisar de mais de 10 MrUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).

Para mais detalhes, consulte [o processamento de meios de escala com cli](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Utilize [transformes](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transform** descreve uma receita, ou um fluxo de trabalho de tarefas para processar os seus ficheiros de vídeo ou áudio. A [Job](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real aos Serviços de Media para aplicar a **Transform** a um determinado conteúdo de vídeo ou áudio de entrada. Uma vez criada a Transform, pode submeter postos de trabalho utilizando APIs de Serviços de Media, ou qualquer um dos SDKs publicados. Para obter mais informações, veja [Transforms and Jobs](transforms-jobs-concept.md) (Transformações e Trabalhos).

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Carreguei, codificado e publiquei um vídeo. Qual seria a razão pela qual o vídeo não toca quando tento transmiti-lo?

Uma das razões mais comuns é que não tens o ponto final de streaming a partir do qual estás a tentar jogar no estado de Running.

### <a name="how-does-pagination-work"></a>Como funciona a paginação?

Ao utilizar a paginação, deve sempre utilizar o próximo link para enumerar a recolha e não depender de um determinado tamanho da página. Para mais detalhes e exemplos, consulte [Filtragem, encomenda, paging](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais as funcionalidades que ainda não estão disponíveis no Azure Media Services v3?

Para mais detalhes, consulte [lacunas de funcionalidades relativas a APIs v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de movimentação de uma conta de Media Services entre subscrições?  

Para mais detalhes, consulte [Mover uma conta de Media Services entre subscrições](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão em direto 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>Como parar a transmissão ao vivo depois da emissão estar feita?

Pode abordá-lo do lado do cliente ou do lado do servidor.

#### <a name="client-side"></a>Lado do cliente

A sua aplicação web deve solicitar ao utilizador se pretende terminar a transmissão se estiver a fechar o navegador. Este é um evento de navegador que a sua aplicação web pode lidar.

#### <a name="server-side"></a>Lado do servidor

Você pode monitorizar eventos ao vivo subscrevendo eventos da Grelha de Eventos. Para mais informações, consulte o [eventgrid event schema](media-services-event-schemas.md#live-event-types).

* Pode [subscrever](reacting-to-media-services-events.md) o nível de streaming [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) e monitorizar que não entram reconexões durante algum tempo para parar e eliminar o seu evento ao vivo.
* Ou, pode [subscrever](reacting-to-media-services-events.md) os eventos de [batimentocardíaco](media-services-event-schemas.md#liveeventingestheartbeat) ao nível da pista. Se todas as faixas tiverem bitrate de entrada caindo para 0; ou a última marca não está mais a aumentar, então também pode encerrar com segurança o evento ao vivo. Os eventos de batimentos cardíacos chegam a cada 20 segundos para cada pista para que possa ser um pouco verboso.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e ardósias de imagem durante o live stream?

A codificação ao vivo do Media Services v3 ainda não suporta a inserção de imagens de vídeo ou imagem durante o live stream. 

Pode utilizar um [codificador ao vivo no local](recommended-on-premises-live-encoders.md) para mudar o vídeo de origem. Muitas aplicações fornecem capacidade para mudar fontes, incluindo Telestream Wirecast, Switcher Studio (no iOS), OBS Studio (app gratuita) e muito mais.

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar uma encriptação de chave aES-128 ou um sistema DEDRM?

Os clientes muitas vezes perguntam-se se devem usar encriptação AES ou um sistema DEDRM. A principal diferença entre os dois sistemas é que com a encriptação AES a chave de conteúdo é transmitida ao cliente através de TLS para que a chave seja encriptada em trânsito, mas sem qualquer encriptação adicional ("na clara"). Como resultado, a chave usada para desencriptar o conteúdo é acessível ao leitor cliente e pode ser visualizada num traço de rede no cliente em texto simples. Uma encriptação clara da chave AES-128 é adequada para casos de utilização em que o espectador é uma parte de confiança (por exemplo, encriptar vídeos corporativos distribuídos dentro de uma empresa para ser visto pelos colaboradores).

Sistemas DRM como PlayReady, Widevine e FairPlay fornecem um nível adicional de encriptação na chave usada para desencriptar o conteúdo em comparação com uma chave clara AES-128. A chave de conteúdo é encriptada para uma chave protegida pelo tempo de execução da DRM em adicional a qualquer encriptação de nível de transporte fornecida pelo TLS. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador malicioso atacar. A DRM é recomendada para casos de utilização em que o espectador pode não ser uma parte de confiança e você requer o mais alto nível de segurança.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como mostrar um vídeo apenas a utilizadores que tenham uma permissão específica, sem utilizar o Azure AD?

Não é necessário utilizar nenhum fornecedor de fichas específico (como o Azure AD). Pode criar o seu próprio fornecedor [JWT](https://jwt.io/) (o chamado STS, Secure Token Service), utilizando encriptação de chave assimétrica. No seu STS personalizado, pode adicionar reclamações com base na sua lógica de negócio.

Certifique-se de que o emitente, o público e as alegações correspondem exatamente entre o que está em JWT e a Restrição de Política de Conteúdo usada no ContentKeyPolicy.

Para mais informações, consulte [Proteja o seu conteúdo utilizando encriptação dinâmica dos Media Services](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Como e onde obter o símbolo JWT antes de usá-lo para solicitar licença ou chave?

1. Para produção, é necessário ter um Serviço De Token Seguro (STS) (serviço web) que emite um símbolo JWT a quando um pedido HTTPS. Para o teste, pode utilizar o código mostrado no método **GetTokenAsync** definido em [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. O jogador terá de fazer um pedido, depois de um utilizador ser autenticado, ao STS para tal token e atribuí-lo como o valor do símbolo. Pode utilizar a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Para um exemplo de execução de STS, com chave simétrica e assimétrica, consulte [https://aka.ms/jwt](https://aka.ms/jwt). 
* Para um exemplo de um jogador baseado no Azure Media [https://aka.ms/amtest](https://aka.ms/amtest) Player usando tal token JWT, consulte (expanda a ligação "player_settings" para ver a entrada simbólica).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autoriza pedidos para transmitir vídeos com encriptação AES?

A abordagem correta é alavancar o STS (Secure Token Service):

No STS, dependendo do perfil do utilizador, adicione diferentes reclamações (como "Utilizador Premium", "Utilizador Básico", "Utilizador de EnsaioGratuito"). Com diferentes reclamações num JWT, o utilizador pode ver conteúdos diferentes. Claro que, para diferentes conteúdos/ativos, a ContentKeyPolicyRestriction terá as correspondentes Reclamações Exigidas.

Utilize apIs de Serviços de Mídia Azure para configurar a entrega de licença/chave e encriptar os seus ativos (como mostrado [nesta amostra).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Para obter mais informações, consulte:

- [Visão geral da proteção de conteúdos](content-protection-overview.md)
- [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP ou HTTPS?
A aplicação ASP.NET do leitor de MVC deve suportar o seguinte:

* Autenticação do utilizador através do Azure AD, que se encontra em HTTPS.
* Troca de JWT entre o cliente e a Azure AD, que está em HTTPS.
* Aquisição de licença DRM pelo cliente, que deve estar em HTTPS se a entrega de licença for fornecida pela Media Services. A suíte de produto PlayReady não manda HTTPS para entrega de licença. Se o seu servidor de licença PlayReady estiver fora dos Serviços de Media, pode utilizar http ou HTTPS.

A aplicação ASP.NET jogador utiliza HTTPS como uma melhor prática, pelo que o Media Player está numa página em HTTPS. No entanto, http é preferido para streaming, por isso você precisa considerar a questão dos conteúdos mistos.

* O navegador não permite conteúdo misto. Mas plug-ins como Silverlight e o OSMF plug-in para suave e DASH permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça da capacidade de injetar JavaScript malicioso, o que pode fazer com que os dados dos clientes estejam em risco. Os navegadores bloqueiam esta capacidade por padrão. A única forma de trabalhar em torno dele é do lado do servidor (origem), permitindo que todos os domínios (independentemente de HTTPS ou HTTP). Provavelmente também não é uma boa ideia.
* Evite conteúdo misto. Tanto a aplicação do jogador como o Media Player devem utilizar HTTP ou HTTPS. Ao reproduzir conteúdo misto, a tecnologia silverlightSS requer a desobstrução de um aviso de conteúdo misto. A tecnologia flashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o seu ponto final de streaming foi criado antes de agosto de 2014, não apoiará HTTPS. Neste caso, crie e use um novo ponto final de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E o streaming ao vivo?

Você pode usar exatamente o mesmo design e implementação para proteger o streaming ao vivo em Media Services, tratando o ativo associado a um programa como um ativo VOD. Para fornecer uma proteção multi-DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao Ativo como se fosse um ativo VOD antes de associar o Ativo à Saída Ao Vivo.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Media?

Muitas vezes, os clientes investiram numa exploração de servidores de licenças, quer no seu próprio centro de dados, quer numa hospedada por prestadores de serviços drm. Com a proteção de conteúdos dos Media Services, pode operar em modo híbrido. Os conteúdos podem ser hospedados e protegidos dinamicamente nos Serviços de Media, enquanto as licenças de DRM são entregues por servidores fora dos Serviços de Media. Neste caso, considere as seguintes alterações:

* O STS precisa de emitir fichas aceitáveis e podem ser verificadas pela exploração do servidor de licenças. Por exemplo, os servidores de licença Widevine fornecidos pela Axinom requerem um JWT específico que contém uma mensagem de direito. Portanto, é preciso ter um STS para emitir tal JWT. 
* Já não precisa de configurar o serviço de entrega de licenças nos Serviços de Media. Você precisa fornecer os URLs de aquisição de licença (para PlayReady, Widevine e FairPlay) quando configurar ContentKeyPolicies.

> [!NOTE]
> A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Serviços de Media v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Posso usar o portal Azure para gerir os recursos v3?

Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

* gerir os Media Services v3 [Eventos Ao Vivo,](live-events-outputs-concept.md) 
* vista (não gerir) [v3 Ativos,](assets-concept.md) 
* [obtenha informações sobre o acesso a APIs.](access-api-portal.md) 

Para todas as outras tarefas de gestão (por exemplo, [Transforms and Jobs](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API,](https://docs.microsoft.com/rest/api/media/) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito AssetFile em v3?

Os AssetFiles foram removidos da AmS API para separar os Serviços de Media da dependência do SDK de armazenamento. Agora, o Armazenamento, não os Serviços de Media, guarda a informação que pertence ao Armazenamento. 

Para mais informações, consulte [Migrate to Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Para onde foi a encriptação de armazenamento do lado do cliente?

É agora recomendado utilizar a encriptação de armazenamento do lado do servidor (que está ligado por padrão). Para mais informações, consulte a Encriptação do Serviço de [Armazenamento Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Passos seguintes

[Visão geral do Media Services v3](media-services-overview.md)
