---
title: Conceitos azure Media Services [ Azure Media Services] Microsoft Docs
description: Este artigo dá uma breve visão geral dos conceitos do Microsoft Azure Media Services e liga-se a outros artigos para mais detalhes.
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
ms.openlocfilehash: 69e2c053c9fb874889bc3d5b08be6e0c7ce875a5
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162910"
---
# <a name="azure-media-services-concepts"></a>Conceitos de Serviços De Mídia Azure 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Este tema dá uma visão geral dos conceitos mais importantes dos Serviços de Media.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Ativos e Armazenamento
### <a name="assets"></a>Elementos
Um [Ativo](https://docs.microsoft.com/rest/api/media/operations/asset) contém ficheiros digitais (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legendas fechados) e os metadados sobre estes ficheiros. Depois dos ficheiros digitais são carregados para um elemento, poderia ser usados nos serviços de multimédia, codificar e transmitir fluxos de trabalho.

Um ativo é mapeado para um contentor de bolhas na conta De armazenamento Azure e os ficheiros do ativo são armazenados como bolhas de bloco nesse recipiente. As bolhas de página não são suportadas pela Azure Media Services.

Ao decidir que conteúdo de mídia deve carregar e armazenar num ativo, aplicam-se as seguintes considerações:

* Um ativo deve conter apenas um único e único exemplo de conteúdo mediático. Por exemplo, uma única edição de um episódio de TV, filme ou anúncio.
* Um ativo não deve conter múltiplas representações ou edificações de um ficheiro audiovisual. Um exemplo de um uso impróprio de um Ativo seria tentar armazenar mais do que um episódio de TV, publicidade ou múltiplos ângulos de câmara de uma única produção dentro de um ativo. Armazenar múltiplas representações ou edições de um ficheiro audiovisual num ativo pode resultar em dificuldades na apresentação de postos de trabalho de codificação, streaming e garantia da entrega do ativo posteriormente no fluxo de trabalho.  

### <a name="asset-file"></a>Ficheiro de ativos
Um [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um ficheiro de vídeo ou áudio real que é armazenado num recipiente de bolha. Um ficheiro de ativo está sempre associado a um ativo, e um ativo pode conter um ou muitos ficheiros. A tarefa Do Codificador de Serviços de Media falha se um objeto de ficheiro de ativo não estiver associado a um ficheiro digital num recipiente de blob.

A instância **AssetFile** e o ficheiro de mídia real são dois objetos distintos. A instância AssetFile contém metadados sobre o ficheiro de mídia, enquanto o ficheiro de mídia contém o conteúdo real dos meios de comunicação.

Não deve tentar alterar o conteúdo dos contentores blob que foram gerados pelos Media Services sem utilizar APIs do Serviço de Media.

### <a name="asset-encryption-options"></a>Opções de encriptação de ativos
Dependendo do tipo de conteúdo que pretende carregar, armazenar e entregar, o Media Services fornece várias opções de encriptação que pode escolher.

>[!NOTE]
>Não é utilizada encriptação. Este é o valor predefinido. Ao utilizar esta opção, o seu conteúdo não está protegido em trânsito ou em repouso no armazenamento.

Se planeia entregar um MP4 utilizando o download progressivo, utilize esta opção para fazer upload do seu conteúdo.

**ArmazenamentoEncriptado** – Utilize esta opção para encriptar o seu conteúdo limpo localmente utilizando encriptação de bitS AES 256 e, em seguida, faça o upload para o Armazenamento Azure onde é armazenado encriptado em repouso. Os ativos protegidos com encriptação de armazenamento são automaticamente desencriptados e colocados num sistema de ficheiros encriptado antes da codificação, e opcionalmente reencriptados antes de serem reencriptados como um novo ativo de saída. O principal caso de utilização para encriptação de armazenamento é quando pretende proteger os seus ficheiros de mídia de entrada de alta qualidade com encriptação forte em repouso no disco. 

Para entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo para que os Serviços de Media saibam como pretende entregar o seu conteúdo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação do armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada (por exemplo, AES, PlayReady ou sem encriptação). 

**CommonEncryptionProtected** - Utilize esta opção se pretender encriptar (ou carregar já encriptado) conteúdo com encriptação comum ou DRM PlayReady (por exemplo, Streaming Suave protegido com DRM PlayReady).

**EnvelopeEncryptionProtected** – Utilize esta opção se pretender proteger (ou carregar já protegido) HTTP Live Streaming (HLS) encriptado com Advanced Encryption Standard (AES). Se está a carregar HLS já encriptado com AES, deve ter sido encriptado pelo Transform Manager.

### <a name="access-policy"></a>Política de acesso
Uma [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) define permissões (como ler, escrever e listar) e duração de acesso a um ativo. Normalmente, passaria um objeto AccessPolicy a um localizador que seria usado para aceder aos ficheiros contidos num ativo.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="blob-container"></a>Contentor de blobs
Um recipiente de bolhas fornece um agrupamento de um conjunto de bolhas. Os contentores blob são usados nos Serviços de Media como ponto de fronteira para o controlo de acesso, e localizadores de Assinatura de Acesso Partilhado (SAS) em ativos. Uma conta de Armazenamento Azure pode conter um número ilimitado de recipientes de bolhas. Um contentor pode armazenar um número ilimitado de blobs.

>[!NOTE]
> Não deve tentar alterar o conteúdo dos contentores blob que foram gerados pelos Media Services sem utilizar APIs do Serviço de Media.
> 
> 

### <a name="a-idlocatorslocators"></a>localizadores <a id="locators"/>
[Os localizadores](https://docs.microsoft.com/rest/api/media/operations/locator)fornecem um ponto de entrada para aceder aos ficheiros contidos num ativo. Uma política de acesso é usada para definir as permissões e duração que um cliente tem acesso a um determinado ativo. Os localizadores podem ter uma relação de muitos a uma com uma política de acesso, de modo a que diferentes localizadores possam fornecer diferentes horários de início e tipos de ligação a diferentes clientes, utilizando todas as mesmas definições de permissão e duração; no entanto, devido a uma restrição da política de acesso partilhado definida pelos serviços de armazenamento Azure, não pode ter mais de cinco localizadores únicos associados a um determinado ativo de uma só vez. 

Os Media Services suportam dois tipos de localizadores: localizadores OnDemandOrigin, utilizados para transmitir meios de comunicação (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou descarregar progressivamente meios de comunicação e localizadores de URL SAS, usados para carregar ou transferir ficheiros de mídia para\a partir do armazenamento do Azure. 

>[!NOTE]
>A permissão da lista (AccessPermissions.List) não deve ser utilizada na criação de um localizador OnDemandOrigin. 

### <a name="storage-account"></a>Conta de armazenamento
Todo o acesso ao Armazenamento Azure é feito através de uma conta de armazenamento. Uma conta de Serviço de Media pode associar-se a uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contentores, desde que o seu tamanho total seja inferior a 500TB por conta de armazenamento.  A Media Services fornece ferramentas de nível SDK para permitir gerir várias contas de armazenamento e carregar o saldo da distribuição dos seus ativos durante o upload para estas contas com base em métricas ou distribuição aleatória. Para mais informações, consulte Trabalhar com [o Armazenamento Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Empregos e tarefas
Um [trabalho](https://docs.microsoft.com/rest/api/media/operations/job) é normalmente usado para processar (por exemplo, indexar ou codificar) uma apresentação áudio/vídeo. Se estiver a processar vários vídeos, crie um trabalho para que cada vídeo seja codificado.

Um trabalho contém metadados sobre o processamento a ser realizado. Cada trabalho contém uma ou mais [tarefas](https://docs.microsoft.com/rest/api/media/operations/task)que especificam uma tarefa de processamento atómico, os seus Ativos de entrada, ativos de saída, um processador de mídia e as suas configurações associadas. As tarefas dentro de um trabalho podem ser acorrentadas em conjunto, onde o ativo de saída de uma tarefa é dado como o ativo de entrada para a tarefa seguinte. Desta forma, um trabalho pode conter todo o processamento necessário para uma apresentação mediática.

## <a id="encoding"></a>Codificação
A Azure Media Services oferece múltiplas opções para a codificação de meios na nuvem.

Ao começar com os Serviços de Media, é importante entender a diferença entre códigos e formatos de ficheiros.
Os codecs são o software que implementa os algoritmos de compressão/descompressão, enquanto os formatos de ficheiros são recipientes que possuem o vídeo comprimido.

A Media Services fornece uma embalagem dinâmica que lhe permite entregar o seu conteúdo codificado bitrate adaptativo MP4 ou Smooth Streaming em formatos de streaming suportados por Media Services (MPEG DASH, HLS, Smooth Streaming) sem ter de voltar a embalar estes formatos de streaming.

Para tirar partido da [embalagem dinâmica,](media-services-dynamic-packaging-overview.md)é necessário codificar o seu ficheiro mezanino (fonte) num conjunto de ficheiros MP4 bitrate adaptáveis ou ficheiros adaptáveis de streaming suave e ter pelo menos um ponto final de streaming padrão ou premium em estado de partida.

A Media Services apoia os seguintes codificadores a pedido que são descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obter informações sobre codificadores suportados, consulte [Os Codificadores](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmissão em Fluxo em Direto
Na Azure Media Services, um Canal representa um oleoduto para o processamento de conteúdos de streaming ao vivo. Um Canal recebe streams de entrada ao vivo de uma de duas maneiras:

* Um codificador ao vivo no local envia RTMP multibitado ou Streaming Suave (MP4 fragmentado) para o Canal. Pode utilizar os seguintes codificadores ao vivo que produzem streaming liso multibitado: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores de saída RTMP: Adobe Flash Live Encoder, [Telestream Wirecast,](media-services-configure-wirecast-live-encoder.md)Teradek, Haivision e Tricaster codificadores. Os fluxos ingeridos passam pelos Canais sem mais transcodificação e codificação. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um único fluxo bitrate (num dos seguintes formatos: RTMP, ou Smooth Streaming (MP4 fragmentado)) é enviado para o Canal que está habilitado a realizar codificação ao vivo com os Media Services. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

### <a name="channel"></a>Canal
Nos Serviços de Media, [o Channel](https://docs.microsoft.com/rest/api/media/operations/channel)s é responsável pelo processamento de conteúdos de streaming ao vivo. Um Canal fornece um ponto final de entrada (URL ingerir) que fornece a um transcodificador vivo. O canal recebe streams de entrada ao vivo do transcodificador ao vivo e disponibiliza-o para streaming através de um ou mais StreamingEndpoints. Os canais também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para pré-visualizar e validar o seu fluxo antes de continuar a processar e a entregar.

Pode obter o URL ingerir e o URL de pré-visualização quando criar o canal. Para obter estes URLs, o canal não tem que estar no estado de partida. Quando estiver pronto para começar a empurrar dados de um transcodificador vivo para o canal, o canal deve ser iniciado. Assim que o transcodificador ao vivo começar a ingerir dados, pode pré-visualizar o seu fluxo.

Cada conta de Media Services pode conter vários Canais, vários Programas e vários StreamingEndpoints. Dependendo da largura de banda e das necessidades de segurança, os serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer Ponto De StreamingEnd pode puxar de qualquer Canal.

### <a name="program-event"></a>Programa (evento)
Um [Programa (evento)](https://docs.microsoft.com/rest/api/media/operations/program) permite controlar a publicação e armazenamento de segmentos em live stream. Os canais gerem programas (eventos). A relação Canal e Programa é semelhante aos meios tradicionais onde um canal tem um fluxo constante de conteúdos e um programa é traçado para algum evento cronometrado nesse canal.
Pode especificar o número de horas que pretende reter o conteúdo gravado para o programa, definindo a propriedade **ArchiveWindowLength.** Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

O ArchiveWindowLength também dita o tempo máximo que os clientes podem procurar no tempo a partir da atual posição ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa (evento) está associado a um Ativo. Para publicar o programa tem de criar um localizador para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Para obter mais informações, consulte:

* [Trabalhar com canais que estão habilitados a realizar encodificação ao vivo com os Serviços De Mídia Azure](media-services-manage-live-encoder-enabled-channels.md)
* [Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local (Working with Channels that Receive Multi-bitrate Live Stream from On-premises Encoders)](media-services-live-streaming-with-onprem-encoders.md)
* [Quotas e limitações.](media-services-quotas-and-limitations.md)

## <a name="protecting-content"></a>A proteger conteúdo
### <a name="dynamic-encryption"></a>Encriptação dinâmica
A Azure Media Services permite-lhe proteger os seus meios de comunicação a partir do momento em que deixa o computador através do armazenamento, processamento e entrega. O Media Services permite-lhe entregar o seu conteúdo encriptado de forma dinâmica com advanced Encryption Standard (AES) (utilizando chaves de encriptação de 128 bits) e encriptação comum (CENC) utilizando o Retorponamento PlayReady e/ou Widevine DRM. A Media Services também presta um serviço para a entrega de chaves AES e licenças PlayReady a clientes autorizados.

Atualmente, pode encriptar os seguintes formatos de streaming: HLS, MPEG DASH e Smooth Streaming. Não é possível encriptar downloads progressivos.

Se pretender que os Serviços de Media criptografem um ativo, precisa de associar uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) ao seu ativo e também configurar políticas de autorização para a chave.

Se pretender transmitir um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo para especificar como pretende entregar o seu ativo.

Quando um stream é solicitado por um leitor, o Media Services utiliza a chave especificada para encriptar dinamicamente o seu conteúdo utilizando uma encriptação de envelope (com AES) ou encriptação comum (com PlayReady ou Widevine). Para desencriptar o fluxo, o leitor solicitará a chave do serviço de entrega da chave. Para decidir se o utilizador está ou não autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

### <a name="token-restriction"></a>Restrição simbólica
A política de autorização chave do conteúdo poderia ter uma ou mais restrições de autorização: restrição aberta, simbólica ou restrição IP. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os Serviços de Media suportam tokens no formato Simple Web Tokens (SWT) e no formato JSON Web Token (JWT). A Media Services não fornece serviços de token seguros. Pode criar um STS personalizado. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de entrega da chave Media Services devolverá a chave (ou licença) solicitada ao cliente se o token for válido e as reclamações no símbolo corresponderem às configuradas para a chave (ou licença).

Ao configurar a política restrita do símbolo, deve especificar a chave de verificação primária, os parâmetros do emitente e do público. A chave de verificação primária contém a chave com a que o símbolo foi assinado, emitente é o serviço de fichas seguro que emite o símbolo. O público (às vezes chamado de âmbito) descreve a intenção do símbolo ou o recurso a que o símbolo autoriza o acesso. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Para obter mais informações, veja os artigos seguintes:
- [Proteger a visão geral do conteúdo](media-services-content-protection-overview.md)
- [Proteja com AES-128](media-services-protect-with-aes128.md)
- [Proteja com PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Entrega
### <a name="a-iddynamic_packagingdynamic-packaging"></a><a id="dynamic_packaging"/>embalagem dinâmica
Ao trabalhar com os Serviços de Media, recomenda-se codificar os seus ficheiros mezaninos num conjunto de MP4 bitrate adaptativo e, em seguida, converter o conjunto para o formato pretendido utilizando a [Embalagem Dinâmica](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Ponto final de transmissão em fluxo
Um StreamingEndpoint representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação de jogador de clientes, ou a uma Rede de Entrega de Conteúdos (CDN) para posterior distribuição (a Azure Media Services fornece agora a integração Azure CDN.) O fluxo de saída de um serviço de ponto final de streaming pode ser um stream ao vivo, ou um Ativo a pedido de vídeo na sua conta de Media Services. Os clientes dos Serviços de Multimédia escolhem um ponto final de transmissão em fluxo **Standard** ou um ou mais pontos finais de transmissão em fluxo **Premium**, de acordo com as respetivas necessidades. O ponto final de streaming padrão é adequado para a maioria das cargas de trabalho de streaming. 

O Ponto Final de Transmissão em Fluxo Standard é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Os Pontos Finais de Streaming Standard oferecem a flexibilidade para entregar o seu conteúdo a praticamente todos os dispositivos através de embalagens dinâmicas em HLS, MPEG-DASH e Smooth Streaming, bem como encriptação dinâmica para microsoft PlayReady, Google Widevine, Apple Fairplay e AES128.  Eles também escalam de públicos muito pequenos a muito grandes com milhares de espectadores simultâneos através da integração Azure CDN. Se tiver uma carga de trabalho avançada ou os seus requisitos de capacidade de streaming não se adequar em alvos padrão de saída de pontos finais de streaming ou se pretender controlar a capacidade do serviço StreamingEndpoint para lidar com as necessidades crescentes de largura de banda, recomenda-se que alocar unidades de escala (também conhecidas como unidades de streaming premium).

Recomenda-se a utilização de embalagens dinâmicas e/ou encriptação dinâmica.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Para obter mais informações, veja [este](media-services-portal-manage-streaming-endpoints.md) tópico.

Por predefinição pode ter até 2 pontos finais de streaming na sua conta de Media Services. Para solicitar um limite mais elevado, consulte [Quotas e limitações.](media-services-quotas-and-limitations.md)

Só é cobrado quando o seu StreamingEndpoint está em estado de funcionamento.

### <a name="asset-delivery-policy"></a>Política de entrega de ativos
Um dos passos no fluxo de trabalho de entrega de conteúdos dos Media Services é configurar políticas de [entrega de ativos](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)que pretende ser transmitido. A política de entrega de ativos diz aos Media Services como pretende que o seu ativo seja entregue: no qual o protocolo de streaming deve ser embalado dinamicamente (por exemplo, MPEG DASH, HLS, Smooth Streaming, ou todos), quer queira ou não encriptar dinamicamente seu ativo e como (envelope ou encriptação comum).

Se tiver um ativo encriptado de armazenamento, antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada. Por exemplo, para entregar o seu ativo encriptado com a chave de encriptação Advanced Encryption Standard (AES), defino o tipo de política para DynamicEnvelopeEncryption. Para remover a encriptação do armazenamento e transmitir o ativo de forma clara, delineie o tipo de política para NoDynamicEncryption.

### <a name="progressive-download"></a>Download progressivo
O download progressivo permite-lhe começar a reproduzir os meios de comunicação antes de todo o ficheiro ter sido descarregado. Só é possível descarregar progressivamente um ficheiro MP4.

>[!NOTE]
>Deve desencriptar os ativos encriptados se desejar que estejam disponíveis para download progressivo.

Para fornecer urLs de descarregamento progressivo aos utilizadores, primeiro deve criar um localizador OnDemandOrigin. Criar o localizador, dá-lhe o caminho base para o ativo. Em seguida, precisa anexar o nome do ficheiro MP4. Por exemplo:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Streaming URLs
Transmitindo o seu conteúdo aos clientes. Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. Criar o localizador, dá-lhe o Caminho base para o ativo que contém o conteúdo que pretende transmitir. No entanto, para poder transmitir este conteúdo, precisa de modificar ainda mais este caminho. Para construir um URL completo para o ficheiro manifesto de streaming, deve concatenar o valor do caminho do localizador e o nome de ficheiro manifesto (nome de ficheiro.ism). Em seguida, anexar/Manifestar e um formato apropriado (se necessário) para o caminho do localizador.

Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, verifique se suas URLs de streaming começam com HTTPS. Atualmente, a AMS não suporta a SSL com domínios personalizados.  

>[!NOTE]
>Você só poderá transmitir por SSL se o ponto de extremidade de streaming do qual você entregar seu conteúdo tiver sido criado após 10 de setembro de 2014. Se os seus URLs de streaming forem baseados nos pontos finais de streaming criados após o dia 10 de setembro, o URL contém "streaming.mediaservices.windows.net" (o novo formato). Os URLs de streaming que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam O SSL. Se a URL estiver no formato antigo e você quiser ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Utilize URLs criados com base no novo ponto final de streaming para transmitir o seu conteúdo através de SSL.

A seguinte lista descreve diferentes formatos de streaming e dá exemplos:

* Transmissão em Fluxo Uniforme

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = MPD-time-CSF)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-apl)

* Apple HTTP Live Streaming (HLS) V3

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl-v3)

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

