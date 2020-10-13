---
title: Conceitos Azure Media Services / Microsoft Docs
description: Este artigo apresenta uma breve visão geral dos conceitos e links da Microsoft Azure Media Services para mais detalhes.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 2a251ef426d41413cf81f46ca05b11640f01e87a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333788"
---
# <a name="azure-media-services-concepts"></a>Conceitos de Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Este tópico dá uma visão geral dos conceitos mais importantes dos Media Services.

## <a name="assets-and-storage"></a><a name="assets"></a>Ativos e Armazenamento
### <a name="assets"></a>Elementos
Um [Ativo](/rest/api/media/operations/asset) contém ficheiros digitais (incluindo vídeo, áudio, imagens, recolhas de miniaturas, faixas de texto e ficheiros de legendas fechados) e os metadados sobre estes ficheiros. Depois de os ficheiros digitais serem enviados para um ativo, podem ser utilizados nos fluxos de trabalho de codificação e streaming dos Serviços de Comunicação Social.

Um ativo é mapeado para um recipiente de bolhas na conta de Armazenamento Azure e os ficheiros do ativo são armazenados como bolhas de bloco nesse recipiente. As bolhas de página não são suportadas pela Azure Media Services.

Ao decidir que conteúdo sonoro e armazenado num ativo, aplicam-se as seguintes considerações:

* Um ativo deve conter apenas uma única e única instância de conteúdo de mídia. Por exemplo, uma única edição de um episódio de TV, filme ou anúncio.
* Um ativo não deve conter múltiplas representações ou edições de um ficheiro audiovisual. Um exemplo de um uso impróprio de um Ativo seria tentar armazenar mais de um episódio de TV, publicidade ou múltiplos ângulos de câmara de uma única produção dentro de um ativo. Armazenar múltiplas representações ou edições de um ficheiro audiovisual num ativo pode resultar em dificuldades na apresentação de postos de trabalho, streaming e garantia da entrega do ativo mais tarde no fluxo de trabalho.  

### <a name="asset-file"></a>Arquivo de ativos
Um [AssetFile](/rest/api/media/operations/assetfile) representa um ficheiro de vídeo ou áudio real que é armazenado num recipiente de bolhas. Um ficheiro de ativos está sempre associado a um ativo, e um ativo pode conter um ou muitos ficheiros. A tarefa Media Services Encoder falha se um objeto de ficheiro de ativo não estiver associado a um ficheiro digital num recipiente de bolhas.

A instância **do AssetFile** e o ficheiro de mídia real são dois objetos distintos. A instância Do Ficheiro Do Ativo contém metadados sobre o ficheiro de mídia, enquanto o ficheiro de mídia contém o conteúdo real dos meios de comunicação.

Não deve tentar alterar o conteúdo dos recipientes blob que foram gerados pelos Media Services sem utilizar ASP do Serviço de Comunicação Social.

### <a name="asset-encryption-options"></a>Opções de encriptação de ativos
Dependendo do tipo de conteúdo que pretende carregar, armazenar e entregar, os Media Services fornecem várias opções de encriptação que pode escolher.

>[!NOTE]
>Não é utilizada nenhuma encriptação. Este é o valor predefinido. Ao utilizar esta opção, o seu conteúdo não está protegido em trânsito ou em repouso no armazenamento.

Se planeia entregar um MP4 utilizando o download progressivo, utilize esta opção para fazer o upload do seu conteúdo.

**ArmazenamentoEncrypted** – Utilize esta opção para encriptar o seu conteúdo limpo localmente utilizando encriptação de 256 bits AES e, em seguida, carregá-lo para o Azure Storage, onde é armazenado encriptado em repouso. Os ativos protegidos com encriptação de armazenamento são automaticamente desencriptados e colocados num sistema de ficheiros encriptados antes da codificação, e opcionalmente reen encriptados antes de serem carregados como um novo ativo de saída. O caso de utilização primária para encriptação de armazenamento é quando pretende proteger os seus ficheiros de meios de entrada de alta qualidade com uma encriptação forte em repouso no disco. 

Para entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo para que os Serviços de Comunicação saibam como pretende entregar o seu conteúdo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada (por exemplo, AES, PlayReady ou nenhuma encriptação). 

**CommonEncryptionProtected** - Utilize esta opção se pretender encriptar (ou carregar conteúdos já encriptados) com Encriptação Comum ou PlayReady DRM (por exemplo, Streaming Suave protegido com PlayReady DRM).

**EnvelopeEncryptionProtected** – Utilize esta opção se pretender proteger (ou carregar já protegido) HTTP Live Streaming (HLS) encriptado com Padrão avançado de encriptação (AES). Se está a carregar o HLS já encriptado com a AES, deve ter sido encriptado pelo Transform Manager.

### <a name="access-policy"></a>Política de acesso
Uma [AccessPolicy](/rest/api/media/operations/accesspolicy) define permissões (como ler, escrever e lista) e duração do acesso a um ativo. Normalmente, passaria um objeto AccessPolicy a um localizador que seria usado para aceder aos ficheiros contidos num ativo.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="blob-container"></a>Contentor de blobs
Um recipiente de bolhas fornece um agrupamento de um conjunto de bolhas. Os contentores blob são usados nos Serviços de Comunicação como ponto de fronteira para o controlo de acesso, e localizadores de assinatura de acesso partilhado (SAS) em ativos. Uma conta de Armazenamento Azure pode conter um número ilimitado de recipientes blob. Um contentor pode armazenar um número ilimitado de blobs.

>[!NOTE]
> Não deve tentar alterar o conteúdo dos recipientes blob que foram gerados pelos Media Services sem utilizar ASP do Serviço de Comunicação Social.
> 
> 

### <a name="locators"></a><a name="locators"></a>Localizadores
[Os localizadores](/rest/api/media/operations/locator)s fornecem um ponto de entrada para aceder aos ficheiros contidos num ativo. Uma política de acesso é usada para definir as permissões e a duração que um cliente tem acesso a um determinado ativo. Os localizadores podem ter uma relação de muitos a um com uma política de acesso, de modo a que diferentes localizadores possam fornecer diferentes tempos de início e tipos de conexão a diferentes clientes, enquanto todos usam as mesmas definições de permissão e duração; no entanto, devido a uma restrição de política de acesso partilhado definida pelos serviços de armazenamento Azure, você não pode ter mais de cinco localizadores únicos associados a um determinado ativo de uma só vez. 

Os Media Services suportam dois tipos de localizadores: localizadores OnDemandOrigin, utilizados para transmitir meios de comunicação (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou descarregar progressivamente meios de comunicação e localizadores DE URL SAS, usados para carregar ou descarregar ficheiros de mídia para\a partir do armazenamento da Azure. 

>[!NOTE]
>A permissão de lista (AccessPermissions.List) não deve ser utilizada ao criar um localizador OnDemandOrigin. 

### <a name="storage-account"></a>Conta de armazenamento
Todo o acesso ao Azure Storage é feito através de uma conta de armazenamento. Uma conta Media Service pode associar-se a uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contentores, desde que o seu tamanho total seja inferior a 500TB por conta de armazenamento.  Os Serviços de Media fornecem ferramentas de nível SDK para que possa gerir várias contas de armazenamento e equilibrar a distribuição dos seus ativos durante o upload para estas contas com base em métricas ou distribuição aleatória. Para mais informações, consulte Working with [Azure Storage](/previous-versions/azure/dn767951(v=azure.100)). 

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas
Um [trabalho](/rest/api/media/operations/job) é normalmente usado para processar (por exemplo, índice ou codificação) uma apresentação áudio/vídeo. Se estiver a processar vários vídeos, crie um trabalho para que cada vídeo seja codificado.

Um trabalho contém metadados sobre o processamento a ser realizado. Cada trabalho contém uma ou mais [tarefas](/rest/api/media/operations/task)que especificam uma tarefa de processamento atómico, os seus Ativos de entrada, ativos de saída, um processador de mídia e as suas definições associadas. As tarefas dentro de um trabalho podem ser acorrentadas em conjunto, onde o ativo de saída de uma tarefa é dado como o ativo de entrada para a tarefa seguinte. Desta forma, um trabalho pode conter todo o processamento necessário para uma apresentação mediática.

## <a name="encoding"></a><a id="encoding"></a>Encoding
A Azure Media Services oferece múltiplas opções para a codificação de meios na nuvem.

Ao começar com os Media Services, é importante entender a diferença entre codecs e formatos de ficheiros.
Os codecs são o software que implementa os algoritmos de compressão/descompressão, enquanto os formatos de ficheiros são contentores que seguram o vídeo comprimido.

Os Media Services fornecem embalagens dinâmicas que lhe permitem fornecer o seu conteúdo codificado de bitrate adaptável MP4 ou Smooth Streaming em formatos de streaming suportados por Media Services (MPEG DASH, HLS, Smooth Streaming) sem ter de voltar a embalar estes formatos de streaming.

Para tirar partido da [embalagem dinâmica,](media-services-dynamic-packaging-overview.md)é necessário codificar o seu ficheiro mezanino (fonte) num conjunto de ficheiros mp4 de bitrate adaptativo ou ficheiros de streaming suave de bitrate adaptativo e ter pelo menos um ponto final de streaming standard ou premium no estado iniciado.

Os Serviços de Comunicação Social apoiam o seguinte codificadora a pedido que é descrito neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Para obter informações sobre este codificado suportado, consulte [o Encoder](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmissão em Fluxo em Direto
No Azure Media Services, um Canal representa um oleoduto para o processamento de conteúdos de streaming ao vivo. Um Canal recebe transmissões de entrada ao vivo de uma de duas maneiras:

* Um codificadora ao vivo no local envia para o Canal um RTMP ou Streaming Liso (MP4 fragmentado) no local. Pode utilizar os seguintes codificadores ao vivo que produzem streaming suave multi-bitrate: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores ao vivo produção RTMP: Adobe Flash Live Encoder, [Telestream Wirecast,](media-services-configure-wirecast-live-encoder.md)Teradek, Haivision codificadores. Os fluxos ingeridos passam por canais sem mais transcoding e codificação. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um único fluxo bitrate (num dos seguintes formatos: RTMP, ou Smooth Streaming (MP4 fragmentado)) é enviado para o Canal que está habilitado a realizar codificação ao vivo com serviços de mídia. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

### <a name="channel"></a>Channel
Nos Serviços de Comunicação Social, [os Channel](/rest/api/media/operations/channel)s são responsáveis pelo processamento de conteúdos de streaming ao vivo. Um Canal fornece um ponto final de entrada (ingest URL) que fornece a um transcodificador vivo. O canal recebe streams de entrada ao vivo a partir do transcodificador ao vivo e disponibiliza-o para streaming através de um ou mais StreamingEndpoints. Os canais também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para visualizar e validar o seu fluxo antes de ser processado e entregue.

Pode obter o URL de ingestão e o URL de pré-visualização quando criar o canal. Para obter estes URLs, o canal não tem que estar no estado iniciado. Quando estiver pronto para começar a empurrar dados de um transcodificador vivo para o canal, o canal tem de ser iniciado. Assim que o transcodificador vivo começar a ingerir dados, pode visualizar o seu fluxo.

Cada conta de Serviços de Mídia pode conter vários canais, vários programas e vários streamingEndpoints. Dependendo da largura de banda e das necessidades de segurança, os serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode puxar de qualquer Canal.

### <a name="program-event"></a>Programa (evento)
Um [Programa (evento)](/rest/api/media/operations/program) permite-lhe controlar a publicação e armazenamento de segmentos num stream ao vivo. Os canais gerem programas (eventos). A relação channel and Program é semelhante aos meios tradicionais onde um canal tem um fluxo constante de conteúdo e um programa é telescópio para algum evento cronometrado nesse canal.
Pode especificar o número de horas que pretende reter o conteúdo gravado para o programa, definindo a propriedade **ArchiveWindowLength.** Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

O ArchiveWindowLength também dita o tempo máximo que os clientes podem procurar no tempo a partir da atual posição ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa (evento) está associado a um Ativo. Para publicar o programa tem de criar um localizador para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Para obter mais informações, consulte:

* [Trabalhar com canais que estão habilitados a executar a codificação ao vivo com os Serviços de Media Azure](media-services-manage-live-encoder-enabled-channels.md)
* [Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md)
* [Quotas e limitações.](media-services-quotas-and-limitations.md)

## <a name="protecting-content"></a>A proteger conteúdo
### <a name="dynamic-encryption"></a>Encriptação dinâmica
A Azure Media Services permite-lhe proteger os seus meios de comunicação a partir do momento em que deixa o computador através do armazenamento, processamento e entrega. Os Serviços de Comunicação Social permitem-lhe entregar o seu conteúdo encriptado dinamicamente com o Advanced Encryption Standard (AES) (utilizando chaves de encriptação de 128 bits) e encriptação comum (CENC) utilizando o PlayReady e/ou Widevine DRM. Os Serviços de Comunicação Social também fornecem um serviço para a entrega de chaves AES e licenças PlayReady a clientes autorizados.

Atualmente, pode encriptar os seguintes formatos de streaming: HLS, MPEG DASH e Smooth Streaming. Não é possível encriptar transferências progressivas.

Se deseja que os Serviços de Comunicação Social encriptem um ativo, precisa de associar uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) ao seu ativo e também configurar políticas de autorização para a chave.

Se pretender transmitir um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo para especificar como pretende entregar o seu ativo.

Quando um stream é solicitado por um jogador, os Media Services utilizam a chave especificada para encriptar dinamicamente o seu conteúdo utilizando uma encriptação de envelope (com AES) ou encriptação comum (com PlayReady ou Widevine). Para desencriptar o fluxo, o leitor solicitará a chave do serviço de entrega da chave. Para decidir se o utilizador está ou não autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

### <a name="token-restriction"></a>Restrição simbólica
A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: restrição aberta, token ou restrição de IP. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os Media Services suportam tokens no formato Web Tokens Simples (SWT) e Web Token JSON (JWT). Os Serviços de Comunicação Social não fornecem Serviços DeKen Seguro. Pode criar uma STS personalizada. O STS deve ser configurado para criar um símbolo assinado com a chave especificada e emitir alegações que especificou na configuração da restrição simbólica. O serviço de entrega chave dos Serviços de Comunicação social devolverá a chave solicitada (ou licença) ao cliente se o token for válido e as reclamações no token corresponderem às configuradas para a chave (ou licença).

Ao configurar a política restrita simbólica, deve especificar a chave de verificação primária, o emitente e os parâmetros do público. A chave de verificação primária contém a chave com a qual o símbolo foi assinado, o emitente é o serviço de ficha segura que emite o token. O público (às vezes chamado de âmbito) descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega chave dos Serviços de Comunicação social valida que estes valores no símbolo correspondem aos valores do modelo.

Para obter mais informações, veja os artigos seguintes:
- [Proteger a visão geral do conteúdo](media-services-content-protection-overview.md)
- [Proteger com AES-128](media-services-protect-with-aes128.md)
- [Proteger com PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Entrega
### <a name="dynamic-packaging"></a><a name="dynamic_packaging"></a>Empacotamento dinâmico
Ao trabalhar com os Media Services, recomenda-se codificar os seus ficheiros mezaninos num conjunto de MP4 de bitragem adaptativa e, em seguida, converter o conjunto para o formato pretendido utilizando a [Embalagem Dinâmica](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Ponto final de transmissão em fluxo
Um StreamingEndpoint representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação do leitor de clientes, ou a uma Rede de Entrega de Conteúdos (CDN) para posterior distribuição (a Azure Media Services agora fornece a integração Azure CDN.) O fluxo de saída de um serviço de ponto final de streaming pode ser um stream ao vivo, ou um Ativo a pedido de vídeo na sua conta de Media Services. Os clientes dos Serviços de Multimédia escolhem um ponto final de transmissão em fluxo **Standard** ou um ou mais pontos finais de transmissão em fluxo **Premium**, de acordo com as respetivas necessidades. O ponto final de streaming padrão é adequado para a maioria das cargas de trabalho de streaming. 

O Ponto Final de Transmissão em Fluxo Standard é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Os Standard Streaming Endpoints oferecem a flexibilidade para entregar o seu conteúdo a praticamente todos os dispositivos através de embalagens dinâmicas em HLS, MPEG-DASH e Smooth Streaming, bem como encriptação dinâmica para o Microsoft PlayReady, Google Widevine, Apple Fairplay e AES128.  Eles também escalam de audiências muito pequenas a muito grandes com milhares de espectadores simultâneos através da integração Azure CDN. Se tiver uma carga de trabalho avançada ou os seus requisitos de capacidade de streaming não se adequarem aos objetivos de produção de fluxo padrão ou se pretende controlar a capacidade do serviço StreamingEndpoint para lidar com as crescentes necessidades de largura de banda, recomenda-se alocar unidades de escala (também conhecidas como unidades de streaming premium).

Recomenda-se a utilização de embalagens dinâmicas e/ou encriptação dinâmica.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Para obter mais informações, veja [este](media-services-portal-manage-streaming-endpoints.md) tópico.

Por predefinição, pode ter até 2 pontos finais de streaming na sua conta de Media Services. Para solicitar um limite mais elevado, consulte [quotas e limitações.](media-services-quotas-and-limitations.md)

Só é cobrado quando o seu StreamingEndpoint está em funcionamento.

### <a name="asset-delivery-policy"></a>Política de entrega de ativos
Um dos passos no fluxo de trabalho de entrega de conteúdos dos Media Services é configurar políticas de [entrega de ativos](/rest/api/media/operations/assetdeliverypolicy)que pretende ser transmitido. A política de entrega de ativos diz aos Media Services como pretende que o seu ativo seja entregue: em que protocolo de streaming o seu ativo será embalado dinamicamente (por exemplo, MPEG DASH, HLS, Smooth Streaming, ou tudo), quer queira ou não encriptar dinamicamente o seu ativo e como (envelope ou encriptação comum).

Se tiver um ativo encriptado de armazenamento, antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada. Por exemplo, para entregar o seu ativo encriptado com a chave de encriptação Advanced Encryption Standard (AES), desafie o tipo de política para DynamicEnvelopeEncrycrytion. Para remover a encriptação de armazenamento e transmitir o ativo de forma clara, desafie o tipo de política para NoDynamicEncrycryption.

### <a name="progressive-download"></a>Download progressivo
O download progressivo permite-lhe começar a reproduzir meios de comunicação antes de todo o ficheiro ser descarregado. Só é possível descarregar progressivamente um ficheiro MP4.

>[!NOTE]
>Tem de desencriptar os ativos encriptados se desejar que estejam disponíveis para download progressivo.

Para fornecer aos utilizadores URLs de descarregamento progressivo, primeiro tem de criar um localizador OnDemandOrigin. Criar o localizador, dá-lhe o Caminho base para o ativo. Em seguida, tem de anexar o nome do ficheiro MP4. Por exemplo:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

### <a name="streaming-urls"></a>Streaming URLs
Streaming do seu conteúdo para os clientes. Para fornecer urls de streaming aos utilizadores, primeiro tem de criar um localizador OnDemandOrigin. Criar o localizador, dá-lhe o Caminho base para o ativo que contém o conteúdo que pretende transmitir. No entanto, para poder transmitir este conteúdo, é necessário modificar ainda mais este caminho. Para construir um URL completo no ficheiro manifesto de streaming, tem de concatenar o valor do localizador Path e o nome de ficheiro manifesto (filename.ism). Em seguida, o apêndice /Manifesto e um formato apropriado (se necessário) para o caminho do localizador.

Também pode transmitir o seu conteúdo através de uma ligação TLS. Para isso, certifique-se de que os seus URLs de streaming começam com HTTPS. Atualmente, a AMS não suporta TLS com domínios personalizados.  

>[!NOTE]
>Só é possível transmitir através do TLS se o ponto final de streaming a partir do qual entrega o seu conteúdo foi criado após o dia 10 de setembro de 2014. Se os urLs de streaming forem baseados nos pontos finais de streaming criados após o dia 10 de setembro, o URL contém "streaming.mediaservices.windows.net" (o novo formato). Os URLs de streaming que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam TLS. Se o seu URL estiver no formato antigo e pretender ser capaz de transmitir através do TLS, crie um novo ponto final de streaming. Utilize URLs criados com base no novo ponto final de streaming para transmitir o seu conteúdo através de TLS.

A seguinte lista descreve diferentes formatos de streaming e dá exemplos:

* Transmissão em Fluxo Uniforme

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{localizador ID}/{filename}.ism/Manifest (formato=m3u8-aapl-v3)

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl-v3)

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
