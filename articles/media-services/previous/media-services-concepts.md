---
title: Conceitos dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma breve visão geral de Serviços de Mídia do Microsoft Azure conceitos e links para outros artigos para obter detalhes.
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
ms.openlocfilehash: 8ff851fec7b5013b297d1796c47cc9d2911d3433
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901287"
---
# <a name="azure-media-services-concepts"></a>Conceitos dos serviços de mídia do Azure 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Este tópico fornece uma visão geral dos conceitos mais importantes dos serviços de mídia.

## <a name="a-idassetsassets-and-storage"></a>Ativos de <a id="assets"/>e armazenamento
### <a name="assets"></a>Elementos
Uma [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) contém ficheiros digitais (incluindo o vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e ficheiros de legendas) e os metadados relativos a esses ficheiros. Depois dos ficheiros digitais são carregados para um elemento, poderia ser usados nos serviços de multimédia, codificar e transmitir fluxos de trabalho.

Um ativo é mapeado para um contêiner de blob na conta de armazenamento do Azure e os arquivos no ativo são armazenados como BLOBs de blocos nesse contêiner. Não há suporte para BLOBs de páginas nos serviços de mídia do Azure.

Ao decidir qual conteúdo de mídia carregar e armazenar em um ativo, as seguintes considerações se aplicam:

* Um ativo deve conter apenas uma única instância exclusiva de conteúdo de mídia. Por exemplo, uma única edição de um episódio, filme ou anúncio de TV.
* Um ativo não deve conter várias representações ou edições de um arquivo audiovisual. Um exemplo de uso impróprio de um ativo seria tentar armazenar mais de um episódio de TV, anúncio ou vários ângulos de câmera de uma única produção dentro de um ativo. Armazenar várias representações ou edições de um arquivo audiovisual em um ativo pode resultar em dificuldades no envio de trabalhos de codificação, no streaming e na proteção da entrega do ativo posteriormente no fluxo de trabalho.  

### <a name="asset-file"></a>Arquivo de ativo
Um [assetfile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um arquivo de áudio ou vídeo real que é armazenado em um contêiner de BLOB. Um arquivo de ativo sempre é associado a um ativo, e um ativo pode conter um ou vários arquivos. A tarefa de codificador de serviços de mídia falhará se um objeto de arquivo de ativo não estiver associado a um arquivo digital em um contêiner de BLOB.

A instância **assetfile** e o arquivo de mídia real são dois objetos distintos. A instância Assetfile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

Você não deve tentar alterar o conteúdo dos contêineres de BLOB que foram gerados pelos serviços de mídia sem usar as APIs do serviço de mídia.

### <a name="asset-encryption-options"></a>Opções de criptografia de ativo
Dependendo do tipo de conteúdo que você deseja carregar, armazenar e entregar, os serviços de mídia fornecem várias opções de criptografia das quais você pode escolher.

>[!NOTE]
>Nenhuma criptografia é usada. Este é o valor predefinido. Ao usar essa opção, seu conteúdo não será protegido em trânsito ou em repouso no armazenamento.

Se você planeja entregar um MP4 usando o download progressivo, use essa opção para carregar seu conteúdo.

**StorageEncrypted** – Use essa opção para criptografar seu conteúdo limpo localmente usando a criptografia de 256 bits do AES e, em seguida, carregá-lo no armazenamento do Azure onde ele é armazenado criptografado em repouso. Os ativos protegidos com criptografia de armazenamento são descriptografados automaticamente e colocados em um sistema de arquivos criptografados antes da codificação e, opcionalmente, criptografados novamente antes de serem carregados novamente como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco. 

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que os serviços de mídia saibam como você deseja entregar seu conteúdo. Antes que o ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite seu conteúdo usando a política de entrega especificada (por exemplo, AES, PlayReady ou sem criptografia). 

**CommonEncryptionProtected** -Use essa opção se você quiser criptografar (ou carregar conteúdo já criptografado) com criptografia comum ou PlayReady DRM (por exemplo, Smooth streaming protegido com DRM do PlayReady).

**EnvelopeEncryptionProtected** – Use essa opção se desejar proteger (ou carregar já protegido) http Live streaming (hls) criptografado com o criptografia AES (AES). Se você estiver carregando o HLS já criptografado com o AES, ele deverá ter sido criptografado pelo Gerenciador de transformação.

### <a name="access-policy"></a>Política de acesso
Um [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) define permissões (como leitura, gravação e lista) e a duração do acesso a um ativo. Normalmente, você passaria um objeto AccessPolicy para um localizador que seria usado para acessar os arquivos contidos em um ativo.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="blob-container"></a>Contentor de blobs
Um contêiner de blob fornece um agrupamento de um conjunto de BLOBs. Os contêineres de blob são usados nos serviços de mídia como ponto de limite para controle de acesso e localizadores de SAS (assinatura de acesso compartilhado) em ativos. Uma conta de armazenamento do Azure pode conter um número ilimitado de contêineres de BLOB. Um contentor pode armazenar um número ilimitado de blobs.

>[!NOTE]
> Você não deve tentar alterar o conteúdo dos contêineres de BLOB que foram gerados pelos serviços de mídia sem usar as APIs do serviço de mídia.
> 
> 

### <a name="a-idlocatorslocators"></a>Localizadores de <a id="locators"/>
Os s do [localizador](https://docs.microsoft.com/rest/api/media/operations/locator)fornecem um ponto de entrada para acessar os arquivos contidos em um ativo. Uma política de acesso é usada para definir as permissões e a duração que um cliente tem acesso a um determinado ativo. Os localizadores podem ter uma relação muitos para um com uma política de acesso, de modo que localizadores diferentes podem fornecer horários de início e tipos de conexão diferentes para clientes diferentes, enquanto todos usam as mesmas configurações de permissão e duração; no entanto, devido a uma restrição de política de acesso compartilhado definida pelos serviços de armazenamento do Azure, você não pode ter mais de cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. 

Os serviços de mídia dão suporte a dois tipos de localizadores: localizadores OnDemandOrigin, usados para transmitir mídia (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou baixar progressivamente mídia e localizadores de URL SAS, usados para carregar ou baixar arquivos de mídia to\from armazenamento do Azure. 

>[!NOTE]
>A permissão de lista (AccessPermissions. List) não deve ser usada ao criar um localizador OnDemandOrigin. 

### <a name="storage-account"></a>Conta de armazenamento
Todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Uma conta de serviço de mídia pode ser associada a uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contêineres, desde que seu tamanho total esteja abaixo de 500 TB por conta de armazenamento.  Os serviços de mídia fornecem ferramentas de nível de SDK para permitir que você gerencie várias contas de armazenamento e balancear a carga da distribuição de seus ativos durante o upload para essas contas com base em métricas ou distribuição aleatória. Para obter mais informações, consulte trabalhando com o [armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas
Normalmente, um [trabalho](https://docs.microsoft.com/rest/api/media/operations/job) é usado para processar (por exemplo, indexar ou codificar) uma apresentação de áudio/vídeo. Se você estiver processando vários vídeos, crie um trabalho para cada vídeo a ser codificado.

Um trabalho contém metadados sobre o processamento a ser executado. Cada trabalho contém uma ou mais [tarefas](https://docs.microsoft.com/rest/api/media/operations/task)que especificam uma tarefa de processamento atômico, seus ativos de entrada, ativos de saída, um processador de mídia e suas configurações associadas. As tarefas em um trabalho podem ser encadeadas em conjunto, onde o ativo de saída de uma tarefa é fornecido como o ativo de entrada para a próxima tarefa. Dessa forma, um trabalho pode conter todo o processamento necessário para uma apresentação de mídia.

## <a id="encoding"></a>Mecanismo
Os serviços de mídia do Azure fornecem várias opções para a codificação de mídia na nuvem.

Ao começar com os serviços de mídia, é importante entender a diferença entre codecs e formatos de arquivo.
Os codecs são o software que implementa os algoritmos de compactação/descompactação, enquanto os formatos de arquivo são contêineres que mantêm o vídeo compactado.

Os serviços de mídia fornecem um empacotamento dinâmico que permite que você forneça seu conteúdo codificado para MP4 de taxa de bits adaptável ou Smooth Streaming em formatos de streaming com suporte dos serviços de mídia (MPEG DASH, HLS, Smooth Streaming) sem precisar reempacotar nesses formatos de streaming.

Para aproveitar o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), você precisa codificar seu arquivo de mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos de Smooth streaming de taxa de bits adaptável e ter pelo menos um ponto de extremidade de streaming Standard ou Premium no estado iniciado.

Os serviços de mídia oferecem suporte aos seguintes codificadores sob demanda descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obter informações sobre codificadores com suporte, consulte [codificadores](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmissão em Direto
Nos serviços de mídia do Azure, um canal representa um pipeline para o processamento de conteúdo de transmissão ao vivo. Um canal recebe fluxos de entrada ao vivo de uma das duas maneiras:

* Um codificador ativo local envia RTMP de múltiplas taxas de bits ou Smooth Streaming (MP4 fragmentado) para o canal. Você pode usar os codificadores ao vivo a seguir que geram várias taxas de bits Smooth Streaming: MediaExcel, Ateme, imagine Communications, envivio, Cisco e Elemental. Os seguintes codificadores dinâmicos produzem RTMP: codificadores Adobe Flash Live Encoder, Telestream Wirecast, Teradek, HaiVision e TriCaster. Os fluxos ingeridos passam por canais sem nenhuma transcodificação e codificação adicionais. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um fluxo de taxa de bits única (em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado) é enviado para o canal que está habilitado para executar a codificação ativa com os serviços de mídia. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

### <a name="channel"></a>Canal
Nos serviços de mídia, o [canal](https://docs.microsoft.com/rest/api/media/operations/channel)s é responsável por processar conteúdo de transmissão ao vivo. Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você então fornece a um transcodificador ao vivo. O canal recebe fluxos de entrada ao vivo do transcodificador ao vivo e o torna disponível para streaming por meio de um ou mais StreamingEndpoints. Os canais também fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de ingestão e a URL de visualização ao criar o canal. Para obter essas URLs, o canal não precisa estar no estado iniciado. Quando você estiver pronto para começar a enviar dados de um transcodificador ao vivo para o canal, o canal deverá ser iniciado. Depois que o transcodificador ao vivo inicia a ingestão de dados, você pode visualizar seu fluxo.

Cada conta dos serviços de mídia pode conter vários canais, vários programas e vários StreamingEndpoints. Dependendo da largura de banda e das necessidades de segurança, os serviços StreamingEndpoints podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode efetuar pull de qualquer canal.

### <a name="program-event"></a>Programa (evento)
Um [programa (evento)](https://docs.microsoft.com/rest/api/media/operations/program) permite que você controle a publicação e o armazenamento de segmentos em uma transmissão ao vivo. Canais gerenciam programas (eventos). A relação entre canal e programa é semelhante à mídia tradicional em que um canal tem um fluxo constante de conteúdo e um programa tem como escopo um evento cronometrado nesse canal.
Você pode especificar o número de horas que deseja manter o conteúdo registrado para o programa, definindo a propriedade **ArchiveWindowLength** . Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

O ArchiveWindowLength também determina a quantidade máxima de tempo que os clientes podem buscar de volta no tempo a partir da posição atual ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa (evento) é associado a um ativo. Para publicar o programa, você deve criar um localizador para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Para obter mais informações, veja:

* [Trabalhando com canais habilitados para executar a codificação ativa com os serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md)
* [Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local (Working with Channels that Receive Multi-bitrate Live Stream from On-premises Encoders)](media-services-live-streaming-with-onprem-encoders.md)
* [Cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>A proteger conteúdo
### <a name="dynamic-encryption"></a>Encriptação dinâmica
Os serviços de mídia do Azure permitem que você proteja sua mídia desde o momento em que ela deixa o computador por meio de armazenamento, processamento e entrega. Os serviços de mídia permitem entregar o conteúdo criptografado dinamicamente com o criptografia AES (AES) (usando chaves de criptografia de 128 bits) e CENC (criptografia comum) usando PlayReady e/ou Widevine DRM. Os serviços de mídia também fornecem um serviço para a entrega de chaves AES e licenças PlayReady para clientes autorizados.

No momento, você pode criptografar os seguintes formatos de streaming: HLS, MPEG DASH e Smooth Streaming. Você não pode criptografar downloads progressivos.

Se desejar que os serviços de mídia criptografem um ativo, você precisará associar uma chave de criptografia (CommonEncryption ou EnvelopeEncryption) ao seu ativo e também configurar políticas de autorização para a chave.

Se você quiser transmitir um ativo de armazenamento criptografado, deverá configurar a política de entrega do ativo para especificar como deseja entregar seu ativo.

Quando um fluxo é solicitado por um player, os serviços de mídia usam a chave especificada para criptografar dinamicamente o conteúdo usando uma criptografia de envelope (com AES) ou criptografia comum (com PlayReady ou Widevine). Para descriptografar o fluxo, o Player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado ou não a obter a chave, o serviço avalia as políticas de autorização que você especificou para a chave.

### <a name="token-restriction"></a>Restrição de token
A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: aberta, restrição de token ou restrição de IP. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Os serviços de mídia dão suporte a tokens no formato Simple Web tokens (SWT) e no formato JSON Web token (JWT). Os serviços de mídia não fornecem serviços de token seguro. Você pode criar um STS personalizado. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de distribuição de chaves dos serviços de mídia retornará a chave (ou licença) solicitada ao cliente se o token for válido e as declarações no token corresponderem às configuradas para a chave (ou licença).

Ao configurar a política restrita de token, você deve especificar os parâmetros de chave de verificação primária, emissor e público. A chave de verificação primária contém a chave com a qual o token foi assinado, o emissor é o serviço de token seguro que emite o token. O público (às vezes chamado de escopo) descreve a intenção do token ou o recurso ao qual o token autoriza o acesso. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Para obter mais informações, veja os artigos seguintes:
- [Visão geral de proteger conteúdo](media-services-content-protection-overview.md)
- [Proteger com AES-128](media-services-protect-with-aes128.md)
- [Proteger com PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Lucrativ
### <a name="a-iddynamic_packagingdynamic-packaging"></a><a id="dynamic_packaging"/>empacotamento dinâmico
Ao trabalhar com os serviços de mídia, é recomendável codificar seus arquivos de mezanino em um conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Ponto final de transmissão em fluxo
Um StreamingEndpoint representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou a uma CDN (rede de distribuição de conteúdo) para distribuição posterior (os serviços de mídia do Azure agora fornecem a integração da CDN do Azure.) O fluxo de saída de um serviço de ponto de extremidade de streaming pode ser um fluxo ao vivo ou um ativo de vídeo sob demanda em sua conta de serviços de mídia. Os clientes dos Serviços de Multimédia escolhem um ponto final de transmissão em fluxo **Standard** ou um ou mais pontos finais de transmissão em fluxo **Premium**, de acordo com as respetivas necessidades. O ponto de extremidade de streaming padrão é adequado para a maioria das cargas de trabalho de streaming. 

O Ponto Final de Transmissão em Fluxo Standard é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Os pontos de extremidade de streaming padrão oferecem a flexibilidade para entregar seu conteúdo a praticamente todos os dispositivos por meio do empacotamento dinâmico em HLS, MPEG-DASH e Smooth Streaming, bem como criptografia dinâmica para Microsoft PlayReady, Google Widevine, Apple Fairplay e Aes128.  Eles também são dimensionados de um público muito pequeno para um grande porte com milhares de visualizadores simultâneos por meio da integração da CDN do Azure. Se você tiver uma carga de trabalho avançada ou os requisitos de capacidade de streaming não couberem em destinos de taxa de transferência de ponto de extremidade de streaming padrão ou se quiser controlar a capacidade do serviço StreamingEndpoint para lidar com as crescentes necessidades de largura de banda, é recomendável aloque unidades de escala (também conhecidas como unidades de streaming Premium).

É recomendável usar o empacotamento dinâmico e/ou a criptografia dinâmica.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Para obter mais informações, veja [este](media-services-portal-manage-streaming-endpoints.md) tópico.

Por padrão, você pode ter até 2 pontos de extremidade de streaming em sua conta de serviços de mídia. Para solicitar um limite mais alto, consulte [cotas e limitações](media-services-quotas-and-limitations.md).

Você será cobrado somente quando seu StreamingEndpoint estiver em estado de execução.

### <a name="asset-delivery-policy"></a>Política de entrega de ativos
Uma das etapas no fluxo de trabalho de distribuição de conteúdo dos serviços de mídia é configurar [políticas de entrega para ativos](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)que você deseja transmitir. A política de entrega de ativos informa aos serviços de mídia como você deseja que seu ativo seja entregue: em qual protocolo de streaming seu ativo deve ser empacotado dinamicamente (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), seja ou não você deseja criptografar dinamicamente seu ativo e como (envelope ou criptografia comum).

Se você tiver um ativo de armazenamento criptografado, antes que seu ativo possa ser transmitido, o servidor de streaming removerá a criptografia de armazenamento e transmitirá seu conteúdo usando a política de entrega especificada. Por exemplo, para entregar seu ativo criptografado com a chave de criptografia do criptografia AES (AES), defina o tipo de política como DynamicEnvelopeEncryption. Para remover a criptografia de armazenamento e transmitir o ativo em claro, defina o tipo de política como NoDynamicEncryption.

### <a name="progressive-download"></a>Download progressivo
O download progressivo permite iniciar a reprodução de mídia antes do download do arquivo inteiro. Você só pode baixar progressivamente um arquivo MP4.

>[!NOTE]
>Você deve descriptografar ativos criptografados se desejar que eles estejam disponíveis para download progressivo.

Para fornecer aos usuários URLs de download progressivo, primeiro você deve criar um localizador OnDemandOrigin. A criação do localizador fornece o caminho base para o ativo. Em seguida, você precisa acrescentar o nome do arquivo MP4. Por exemplo:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>URLs de streaming
Transmitir seu conteúdo para os clientes. Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. A criação do localizador fornece o caminho base para o ativo que contém o conteúdo que você deseja transmitir. No entanto, para poder transmitir esse conteúdo, você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto de streaming, você deve concatenar o valor do caminho do localizador e o nome do arquivo de manifesto (filename. ISM). Em seguida, acrescente/Manifest e um formato apropriado (se necessário) ao caminho do localizador.

Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, verifique se suas URLs de streaming começam com HTTPS. Atualmente, o AMS não dá suporte a SSL com domínios personalizados.  

>[!NOTE]
>Você só poderá transmitir por SSL se o ponto de extremidade de streaming do qual você entregar seu conteúdo tiver sido criado após 10 de setembro de 2014. Se suas URLs de streaming forem baseadas nos pontos de extremidade de streaming criados após 10 de setembro, a URL conterá "streaming.mediaservices.windows.net" (o novo formato). As URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não oferecem suporte a SSL. Se a URL estiver no formato antigo e você quiser ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.

A lista a seguir descreve diferentes formatos de streaming e fornece exemplos:

* Transmissão em Fluxo Uniforme

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = MPD-time-CSF)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) v4

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) v3

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

