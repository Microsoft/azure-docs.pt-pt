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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: a2619293bf3641cdca370ff528a87ae879460a3b
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086790"
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

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Como inserir quebras/vídeos e ardósias de imagem durante o live stream?

A codificação ao vivo do Media Services v3 ainda não suporta a inserção de imagens de vídeo ou imagem durante o live stream. 

Pode utilizar um [codificador ao vivo no local](recommended-on-premises-live-encoders.md) para mudar o vídeo de origem. Muitas aplicações fornecem capacidade para mudar fontes, incluindo Telestream Wirecast, Switcher Studio (no iOS), OBS Studio (app gratuita) e muito mais.

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar uma encriptação de chave aES-128 ou um sistema DEDRM?

Os clientes freqüentemente se questionam sobre se deve utilizar encriptação AES ou um sistema DRM. A principal diferença entre os dois sistemas é que com a encriptação AES a chave de conteúdo é transmitida ao cliente através de TLS para que a chave seja encriptada em trânsito, mas sem qualquer encriptação adicional ("na clara"). Como resultado, a chave usada para desencriptar o conteúdo é acessível ao leitor cliente e pode ser visualizada num traço de rede no cliente em texto simples. Uma encriptação clara da chave AES-128 é adequada para casos de utilização em que o espectador é uma parte de confiança (por exemplo, encriptar vídeos corporativos distribuídos dentro de uma empresa para ser visto pelos colaboradores).

Sistemas DRM como PlayReady, Widevine e FairPlay fornecem um nível adicional de encriptação na chave usada para desencriptar o conteúdo em comparação com uma chave clara AES-128. A chave de conteúdo é encriptada para uma chave protegida pelo tempo de execução da DRM em adicional a qualquer encriptação de nível de transporte fornecida pelo TLS. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador mal intencionado a ataques. Para casos de utilização em que o Visualizador pode não ser um confiável e requerem o nível mais elevado de segurança, recomenda-se DRM.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como mostrar um vídeo apenas a utilizadores que tenham uma permissão específica, sem utilizar o Azure AD?

Não é necessário utilizar nenhum fornecedor de fichas específico (como o Azure AD). Pode criar o seu próprio fornecedor [JWT](https://jwt.io/) (o chamado STS, Secure Token Service), utilizando encriptação de chave assimétrica. No seu STS personalizado, pode adicionar reclamações com base na sua lógica de negócio.

Certifique-se de que o emitente, o público e as alegações correspondem exatamente entre o que está em JWT e a Restrição de Política de Conteúdo usada no ContentKeyPolicy.

Para mais informações, consulte [Proteja o seu conteúdo utilizando encriptação dinâmica dos Media Services](content-protection-overview.md).

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Como e onde obter JWT token antes de o utilizar para a licença de pedido ou a chave?

1. Para produção, é necessário ter um Serviço De Token Seguro (STS) (serviço web) que emite um símbolo JWT a quando um pedido HTTPS. Para o teste, pode utilizar o código mostrado no método **GetTokenAsync** definido em [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Leitor terá de fazer um pedido, após um utilizador é autenticado, para o STS para esse token e atribua-a como o valor do token. Pode utilizar a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Para um exemplo de execução de STS, com chave simétrica e assimétrica, consulte [https://aka.ms/jwt](https://aka.ms/jwt). 
* Para um exemplo de um jogador baseado no Azure Media Player usando tal token JWT, consulte [https://aka.ms/amtest](https://aka.ms/amtest) (expandir a ligação "player_settings" para ver a entrada simbólica).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autorizar pedidos para transmitir vídeos em fluxo com encriptação AES?

A abordagem correta é tirar partido do STS (serviço Token seguro):

No STS, dependendo do perfil do utilizador, adicione diferentes reclamações (como "Utilizador Premium", "Utilizador Básico", "Utilizador de EnsaioGratuito"). Com afirmações diferentes num JWT, o utilizador pode ver o conteúdo diferente. Obviamente, para conteúdo diferente/recurso, o ContentKeyPolicyRestriction terá o RequiredClaims correspondente.

Utilize apIs de Serviços de Mídia Azure para configurar a entrega de licença/chave e encriptar os seus ativos (como mostrado [nesta amostra).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Para obter mais informações, consulte:

- [Descrição geral da proteção de conteúdo](content-protection-overview.md)
- [Design of a multi-DRM content protection system with access control](design-multi-drm-system-with-access-control.md) (Design de um sistema de proteção de conteúdo multi-DRM com controlo de acesso)

### <a name="http-or-https"></a>HTTP ou HTTPS?
A aplicação de leitor de ASP.NET MVC tem de suportar o seguinte:

* Autenticação de utilizador através do Azure AD, o que está sob a HTTPS.
* Troca JWT entre o cliente e o Azure AD, que está abaixo do HTTPS.
* Aquisição de licença DRM pelo cliente, que tem de estar sob HTTPS se a entrega de licenças é fornecida pelos serviços de multimédia. O conjunto de produtos do PlayReady não exige o HTTPS para entrega de licenças. Se o seu servidor de licenças do PlayReady está fora dos serviços de multimédia, pode utilizar HTTP ou HTTPS.

A aplicação de leitor ASP.NET utiliza HTTPS como melhor prática, para que o leitor de multimédia é uma página em HTTPS. No entanto, o HTTP é preferido para a transmissão em fluxo, para que precisa considerar o problema de conteúdo misto.

* O browser não permite que conteúdo misto. Mas o plug-ins como Silverlight e OSMF Plug-in para suavizar e travessão permiti-lo. Conteúdo misto é uma preocupação de segurança por causa da ameaça da capacidade para injetar JavaScript malicioso, que pode fazer com que os dados do cliente estar em risco. Browsers bloquear esta capacidade, por predefinição. É a única maneira de contorná-la no lado do servidor (origem), permitindo que todos os domínios (independentemente do HTTPS ou HTTP). Isto é provavelmente não é uma boa idéia.
* Evite conteúdo misto. A aplicação de leitor e o Media Player, devem utilizar HTTP ou HTTPS. Quando a reprodução de conteúdo misto, é a tecnologia de silverlightSS necessário desmarcar um aviso de conteúdo misto. O tech flashSS processa conteúdo misto sem um aviso de conteúdo misto.
* Se o ponto final de transmissão em fluxo foi criado antes de Agosto de 2014, ele não suporta HTTPS. Neste caso, crie e utilize um novo ponto de final de transmissão em fluxo para HTTPS.

### <a name="what-about-live-streaming"></a>E quanto ao vivo de transmissão em fluxo?

Pode utilizar exatamente o mesmo design e implementação para proteger a transmissão em fluxo em direto nos serviços de multimédia ao tratar o elemento associado um programa como um ativo VOD. Para fornecer uma proteção multi-DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao Ativo como se fosse um ativo VOD antes de associar o Ativo à Saída Ao Vivo.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos serviços de multimédia?

Muitas vezes, os clientes investiram num farm de servidores de licença em seu próprio Centro de dados ou hospedado por fornecedores de serviços DRM. Com a proteção de conteúdo de serviços de multimédia, pode operar no modo híbrido. Conteúdo pode ser hospedado e dinamicamente protegido nos serviços de multimédia, enquanto as licenças DRM são fornecidas por servidores fora dos serviços de multimédia. Neste caso, considere as seguintes alterações:

* STS deve emitir tokens que são aceitáveis e podem ser verificados pelo farm de servidores de licença. Por exemplo, os servidores de licença do Widevine fornecidos pelo Axinom requerem um JWT específico que contém uma mensagem de elegibilidade. Por conseguinte, tem de ter um STS para emitir um JWT desse tipo. 
* Já não terá de configurar o serviço de entrega de licença nos serviços de multimédia. Tem de fornecer a licença de aquisição URLs (para o PlayReady, Widevine e FairPlay) ao configurar ContentKeyPolicies.

> [!NOTE]
> A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-v2-vs-v3"></a>Serviços de Media v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Posso usar o portal Azure para gerir os recursos v3?

Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

* gerir os Media Services v3 [Eventos Ao Vivo,](live-events-outputs-concept.md) 
* vista (não gerir) [v3 Ativos,](assets-concept.md) 
* [obtenha informações sobre o acesso a APIs.](access-api-portal.md) 

Para todas as outras tarefas de gestão (por exemplo, [Transforms and Jobs](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito AssetFile em v3?

Os AssetFiles foram removidos da AmS API para separar os Serviços de Media da dependência do SDK de armazenamento. Agora, o Armazenamento, não os Serviços de Media, guarda a informação que pertence ao Armazenamento. 

Para mais informações, consulte [Migrate to Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Para onde foi a encriptação de armazenamento do lado do cliente?

É agora recomendado utilizar a encriptação de armazenamento do lado do servidor (que está ligado por padrão). Para mais informações, consulte a Encriptação do Serviço de [Armazenamento Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>Passos seguintes

[Visão geral do Media Services v3](media-services-overview.md)
