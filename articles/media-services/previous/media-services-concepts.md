---
title: Conceitos de serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico fornece uma visão geral dos conceitos de serviços de multimédia do Azure
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
ms.openlocfilehash: 3b7b821227478d8c6b5859f24c50407f9eb213fe
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312347"
---
# <a name="azure-media-services-concepts"></a>Conceitos de serviços de multimédia do Azure 

Este tópico apresenta uma visão geral dos conceitos mais importantes dos serviços de multimédia.

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>Ativos e de armazenamento
### <a name="assets"></a>Elementos
Uma [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) contém ficheiros digitais (incluindo o vídeo, áudio, imagens, coleções de miniaturas, pistas de texto e ficheiros de legendas) e os metadados relativos a esses ficheiros. Depois dos ficheiros digitais são carregados para um elemento, poderia ser usados nos serviços de multimédia, codificar e transmitir fluxos de trabalho.

Um elemento é mapeado para um contentor de BLOBs na conta de armazenamento do Azure e os ficheiros no elemento são armazenados como blobs de blocos nesse contentor. Blobs de páginas não são suportados pelos serviços de multimédia do Azure.

Ao decidir que conteúdo de multimédia para carregar e armazenar num elemento, aplicam-se as seguintes considerações:

* Um elemento deve conter apenas uma única e exclusiva instância do conteúdo de mídia. Por exemplo, uma edição de única de um episódio TV, filmes ou anúncio.
* Um recurso não deve conter várias representações ou as edições de um arquivo audiovisual. Um exemplo de uma utilização incorreta de um recurso poderia tentar armazenar mais de um episódio da TV, anúncio ou vários ângulos de câmera de um único dentro de um ativo de produção. Armazenar várias representações ou as edições de um arquivo audiovisual num elemento pode resultar em dificuldades de submeter as tarefas de codificação, transmissão em fluxo e proteger a entrega de ativo mais tarde no fluxo de trabalho.  

### <a name="asset-file"></a>Ficheiro de elemento
Uma [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um ficheiro de vídeo ou áudio real que é armazenado num contentor de Blobs. Um ficheiro de elemento é sempre associado a um elemento e um elemento pode conter um ou vários ficheiros. A tarefa de codificador de serviços de multimédia falha se um objeto de ficheiro de recurso não está associado um ficheiro digital num contentor de Blobs.

O **AssetFile** instância e o ficheiro de multimédia real são dois objetos distintos. A instância de AssetFile contém metadados sobre o ficheiro de multimédia, ao passo que o ficheiro de suporte de dados contém o conteúdo de mídia real.

Não deve tentar alterar os conteúdos de contentores de BLOBs que foram gerados pelos serviços de multimédia sem utilizar as APIs de serviço de suporte de dados.

### <a name="asset-encryption-options"></a>Opções de encriptação do elemento
Dependendo do tipo de conteúdo que pretende carregar, armazenar e entregar, os Media Services fornecem várias opções de encriptação que pode escolher.

>[!NOTE]
>Sem encriptação é utilizada. Este é o valor predefinido. Ao utilizar esta opção não está protegido seu conteúdo em trânsito ou inativos no armazenamento.

Se planear distribuir um MP4 utilizando transferência progressiva, utilize esta opção para carregar o conteúdo.

**StorageEncrypted** – Utilize esta opção para encriptar o seu conteúdo transparente localmente utilizando encriptação AES de 256 bits e, em seguida, carregue-o ao armazenamento do Azure onde estão armazenados encriptados em inatividade. Os elementos protegidos com encriptação de armazenamento são desencriptados automaticamente e colocados no sistema de ficheiros encriptados antes da codificação e opcionalmente encriptados novamente antes de carregar novamente como um novo elemento de saída. O principal motivo para a encriptação de armazenamento é quando deseja proteger os ficheiros de suporte de dados de entrada de alta qualidade com uma encriptação forte Inativos no disco. 

Para fornecer um recurso de criptografado de armazenamento, é necessário configurar a política de entrega de elementos, para que os serviços de multimédia, saiba como deseja distribuir os seus conteúdos. Antes do seu elemento pode ser transmitido em fluxo, o servidor de transmissão em fluxo remove a encriptação de armazenamento e transmite os seus conteúdos através da política de entrega especificado (por exemplo, AES, PlayReady ou sem encriptação). 

**CommonEncryptionProtected** -Utilize esta opção se pretende encriptar (ou carregar já encriptou) conteúdo com encriptação comum ou PlayReady DRM (por exemplo, transmissão em fluxo uniforme protegida com PlayReady DRM).

**EnvelopeEncryptionProtected** – Utilize esta opção se pretender proteger (ou carregar já protegido) HTTP Live Streaming (HLS) encriptado com encriptação AES (Advanced Standard). Se estiver a carregar HLS já encriptado com AES, tem de ter sido criptografado pelo Gestor de transformação.

### <a name="access-policy"></a>Política de acesso
Uma [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) define permissões (como leitura, escrita e lista) e a duração de acesso para um elemento. Passa um objeto de AccessPolicy normalmente para um localizador de que, em seguida, seria usado para acessar os arquivos contidos num elemento.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="blob-container"></a>Contentor de BLOBs
Um contentor de blob fornece um agrupamento de um conjunto de blobs. Contentores de BLOBs são utilizados nos serviços de multimédia como ponto de limites para o controlo de acesso e localizadores de assinatura de acesso partilhado (SAS) ativos. Uma conta de armazenamento do Azure pode conter um número ilimitado de contentores de Blobs. Um contentor pode armazenar um número ilimitado de blobs.

>[!NOTE]
> Não deve tentar alterar os conteúdos de contentores de BLOBs que foram gerados pelos serviços de multimédia sem utilizar as APIs de serviço de suporte de dados.
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>Localizadores
[Localizador](https://docs.microsoft.com/rest/api/media/operations/locator)s fornecer um ponto de entrada para aceder aos ficheiros contidos num elemento. Uma política de acesso é utilizada para definir as permissões e a duração do que um cliente tem acesso a um determinado recurso. Os localizadores podem ter um muitos para uma relação com uma política de acesso, de modo a que os localizadores diferentes podem fornecer tempos de início diferentes e tipos de ligação para diferentes clientes durante a utilização de todas as definições de duração; e a mesma permissão No entanto, devido a uma restrição de política de acesso partilhado definida pelos serviços de armazenamento do Azure, não pode ter mais de cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. 

Os Media Services suportam dois tipos de localizadores: Ondemandorigin, utilizados para transmitir multimédia (por exemplo, MPEG DASH, HLS ou Smooth Streaming) ou transferir progressivamente os suportes de dados e localizadores de URL de SAS, utilizados para carregar ou transferir ficheiros de multimédia to\from armazenamento do Azure. 

>[!NOTE]
>A permissão de lista (AccessPermissions.List) não deve ser usada ao criar um localizador de OnDemandOrigin. 

### <a name="storage-account"></a>Conta de armazenamento
Todos os acessos ao armazenamento do Azure é feito através de uma conta de armazenamento. Uma conta de serviço de suporte de dados pode associar uma ou mais contas de armazenamento. Uma conta pode conter um número ilimitado de contentores, desde que seu tamanho total em 500TB por conta de armazenamento.  Os Media Services fornecem ferramentas de nível de SDK para permitir que a gerenciar várias contas de armazenamento e a distribuição dos seus ativos durante o carregamento para estas contas, com base nas métricas ou distribuição aleatória de balanceamento de carga. Para obter mais informações, consulte Working with trabalhar [armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Trabalhos e tarefas
R [tarefa](https://docs.microsoft.com/rest/api/media/operations/job) é normalmente utilizado para o processo (por exemplo, indexar ou codificar) uma apresentação de áudio/vídeo. Se estiver a processar vários vídeos, crie uma tarefa para cada vídeo estar codificado.

Uma tarefa contém metadados sobre o processamento a ser executado. Cada tarefa contém um ou mais [tarefa](https://docs.microsoft.com/rest/api/media/operations/task)s que especificar uma tarefa de processamento atômica, seus ativos de entrada, saída ativos, um processador de multimédia e as respetivas definições associadas. Tarefas dentro de uma tarefa podem ser encadeadas alterado, em que o elemento de saída de uma tarefa é fornecido como o elemento de entrada para a próxima tarefa. Desta forma, uma tarefa pode conter todo o processamento necessário para uma apresentação do suporte de dados.

## <a id="encoding"></a>Encoding
Serviços de multimédia do Azure fornece várias opções para a codificação de multimédia na cloud.

Quando a começar a utilizar os serviços de multimédia, é importante compreender a diferença entre codecs e formatos de arquivo.
Codecs são o software que implementa os algoritmos de compactação/descompactação enquanto os formatos de arquivo são contentores que contêm o vídeo compactado.

Os Media Services fornecem um empacotamento dinâmico permite-lhe fornecer o conteúdo de velocidade de transmissão adaptável MP4 ou Smooth Streaming com codificação de transmissão em fluxo em formatos suportados pelos Media Services (MPEG DASH, HLS, Smooth Streaming) sem ter de voltar o pacote para estes formatos de transmissão em fluxo.

Para tirar partido das [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), terá de codificar o (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável e ter, pelo menos, um endpoint de transmissão em fluxo standard ou premium Estado a utilizar.

Os Media Services suportam os seguintes codificadores a pedido que são descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obter informações sobre codificadores suportados, consulte [codificadores](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmissão em Direto
Nos serviços de multimédia do Azure, um canal representa um pipeline de processamento de conteúdo de transmissão em fluxo em direto. Um canal recebe transmissões em direto de entrada em uma das seguintes formas:

* Um codificador em direto no local envia múltipla RTMP ou transmissão em fluxo uniforme (MP4 fragmentado) para o canal. Pode utilizar os seguintes codificadores em direto que múltipla transmissão em fluxo uniforme de saída: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores em direto transmitem RTMP: Codificadores Adobe Flash Live Encoder, Telestream Wirecast, Teradek, Haivision e Tricaster. Passam as transmissões em fluxo através de canais, sem qualquer outra transcodificação e codificação. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um fluxo de velocidade de transmissão única (em um dos seguintes formatos: RTMP ou transmissão em fluxo uniforme (MP4 fragmentado)) é enviada para o canal ativado para realizar live encoding com Media Services. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

### <a name="channel"></a>Canal
Nos serviços de multimédia [canal](https://docs.microsoft.com/rest/api/media/operations/channel)s são responsáveis por processar o conteúdo de transmissão em fluxo em direto. Um canal fornece um ponto de final de entrada (URL de ingestão) que, em seguida, forneça a um transcodificador ao vivo. O canal recebe transmissões em direto de entrada do transcodificador em direto e disponibiliza-o para transmissão em fluxo através de pontos finais de um ou mais. Canais também fornecem um ponto de extremidade pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão antes de mais processamentos e entregas.

Pode obter o URL de inserção e o URL de pré-visualização ao criar o canal. Para obter estes URLs, o canal não tem de estar no estado iniciado. Quando estiver pronto para começar a enviar dados a partir de um transcodificador ao vivo dentro do canal, o canal tem de ser iniciado. Assim que for iniciada a transcodificador ao vivo a ingestão de dados, pode visualizar a sua transmissão em fluxo.

Cada conta de Media Services pode conter vários canais, vários programas e pontos finais vários. Consoante as necessidades de largura de banda e de segurança, StreamingEndpoint serviços podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode extrair a partir de qualquer canal.

### <a name="program-event"></a>Programa (evento)
R [programa (evento)](https://docs.microsoft.com/rest/api/media/operations/program) permite-lhe controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Os canais gerem programas (eventos). A relação de canal e o programa é semelhante à multimédia tradicional onde um canal tem um fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.
Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração do **ArchiveWindowLength** propriedade. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

ArchiveWindowLength também determina que a quantidade máxima de clientes de tempo pode recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa (evento) está associado um recurso. Para publicar o programa tem de criar um localizador para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Para obter mais informações, consulte:

* [Trabalhar com canais ativados para realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md)
* [Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local (Working with Channels that Receive Multi-bitrate Live Stream from On-premises Encoders)](media-services-live-streaming-with-onprem-encoders.md)
* [Quotas e limitações](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>A proteger conteúdo
### <a name="dynamic-encryption"></a>Encriptação dinâmica
Serviços de multimédia do Azure permite-lhe proteger os seus suportes de dados a partir do momento em que deixa seu computador por meio de armazenamento, processamento e entrega. Serviços de multimédia permite-lhe fornecer o conteúdo encriptado dinamicamente com AES Advanced Encryption Standard () (usando as chaves de encriptação de 128 bits) e a encriptação comum (CENC) com o PlayReady e/ou Widevine DRM. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e PlayReady licenças para os clientes autorizados.

Atualmente, pode criptografar os seguintes formatos de transmissão em fluxo: HLS, MPEG DASH e Smooth Streaming. Não é possível encriptar transferências progressivas.

Se desejar para serviços de multimédia para encriptar um elemento, terá de associar o seu elemento uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) e também configurar políticas de autorização da chave.

Se quer transmitir um ativo de criptografado de armazenamento, tem de configurar política de entrega de elementos para poder especificar a forma como pretende distribuir seus recursos.

Quando um fluxo é solicitado por um jogador, serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo com uma encriptação de envelope (com AES) ou a encriptação comum (PlayReady ou Widevine). Para descriptografar o fluxo, o leitor solicitará a chave do serviço de entrega de chave. Para decidir se é ou não o utilizador está autorizado a obter a chave, o serviço avalia as políticas de autorização que especificou para a chave.

### <a name="token-restriction"></a>Restrição de token
A política de autorização da chave de conteúdo pode ter um ou mais restrições de autorização: abrir, restrição ou restrição de IP do token. A política de token restrito tem de ser acompanhada por um token emitido por um Serviço de Token Seguro (STS). Serviços de multimédia suportam tokens no formato simples Web Tokens (SWT) e no formato JSON Web Token (JWT). Serviços de multimédia não fornece serviços de Token seguro. Pode criar um STS personalizado. O STS deve ser configurado para criar um token assinado com as declarações de chave e o problema especificadas que especificou na configuração de restrição de token. O serviço de entrega de chave de serviços de multimédia devolverá a chave de pedido (ou licença) para o cliente se o token é válido e as afirmações na correspondência de token que aquelas configuradas para a chave (ou uma licença).

Quando configurar o token de política de restrição, tem de especificar a chave de verificação primária, emissor e parâmetros de público-alvo. A chave de verificação primária contém a chave de que o token foi assinado com, o emissor é o serviço de token seguro que emite o token. O público-alvo (por vezes denominado âmbito) descreve a intenção do token ou o recurso o token de acesso a autoriza. O serviço de entrega de chave de serviços de multimédia valida que estes valores no token correspondem aos valores no modelo.

Para obter mais informações, veja os artigos seguintes:
- [Proteger a descrição geral do conteúdo](media-services-content-protection-overview.md)
- [Proteger com AES-128](media-services-protect-with-aes128.md)
- [Proteger com PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>A entregar
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>Empacotamento dinâmico
Ao trabalhar com os Media Services, é recomendado para codificar seus arquivos de mezanino para uma conjunto de MP4 de velocidade de transmissão adaptável e, em seguida, converter o conjunto para o formato desejado, com o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Ponto final de transmissão em fluxo
Um StreamingEndpoint representa um serviço de transmissão em fluxo que pode entregar conteúdo diretamente a uma aplicação de leitor cliente ou para uma rede de entrega de conteúdos (CDN) para uma maior distribuição (Serviços de multimédia do Azure fornece agora a integração da CDN do Azure.) O fluxo de saída de um serviço de ponto final de transmissão em fluxo pode ser uma transmissão em direto ou um elemento de vídeo a pedido na sua conta de Media Services. Os clientes dos Serviços de Multimédia escolhem um ponto final de transmissão em fluxo **Standard** ou um ou mais pontos finais de transmissão em fluxo **Premium**, de acordo com as respetivas necessidades. Ponto final de transmissão em fluxo Standard é adequado para a maioria das cargas de trabalho de transmissão em fluxo. 

O Ponto Final de Transmissão em Fluxo Standard é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Pontos finais de transmissão em fluxo Standard oferece a flexibilidade para distribuir os seus conteúdos para praticamente todos os dispositivos através do empacotamento dinâmico para HLS, MPEG-DASH e Smooth Streaming, bem como a encriptação dinâmica para o Microsoft PlayReady, Widevine da Google, Apple Fairplay, e AES128.  Eles também dimensionar de muito pequena para públicos-alvo muito grande com milhares de espetadores em simultâneo através da integração da CDN do Azure. Se tiver uma carga de trabalho avançada ou seus requisitos de capacidade de transmissão em fluxo não se ajustam padrão destinos de débito do ponto de extremidade transmissão em fluxo ou se quiser controlar a capacidade do serviço StreamingEndpoint para lidar com crescimento necessidades de largura de banda, é recomendado Faça a alocação de unidades de escala (também conhecidas como unidades transmissão em fluxo premium).

Recomenda-se para utilizar o empacotamento dinâmico e/ou a encriptação dinâmica.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Para obter mais informações, veja [este](media-services-portal-manage-streaming-endpoints.md) tópico.

Por predefinição, pode ter até 2 pontos finais na sua conta de serviços de multimédia de transmissão em fluxo. Para pedir um limite maior, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).

É-lhe cobrada apenas quando o seu StreamingEndpoint está no estado de execução.

### <a name="asset-delivery-policy"></a>Política de entrega de elemento
Uma das etapas do fluxo de trabalho de entrega de conteúdos de serviços de suporte de dados está a configurar [políticas de entrega para ativos](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)que deseja ser transmitidos em fluxo. A política de entrega de elementos informa os serviços de multimédia que pretende para o seu elemento seja entregue: em qual protocolo de transmissão em fluxo deve seu elemento ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se pretende encriptar dinamicamente ou não o elemento e como (envelope ou encriptação comum).

Se tiver um recurso de armazenamento encriptado, antes do seu elemento pode ser transmitido em fluxo, o servidor de transmissão em fluxo remove a encriptação de armazenamento e transmite os seus conteúdos através da política de entrega especificado. Por exemplo, para fornecer o seu elemento encriptado com a chave de encriptação Advanced Encryption Standard (AES), defina o tipo de política para DynamicEnvelopeEncryption. Para remover a encriptação de armazenamento e transmitir em fluxo o elemento transparente, defina o tipo de política para NoDynamicEncryption.

### <a name="progressive-download"></a>Transferência progressiva
Transferência progressiva permite-lhe iniciar a reprodução de mídia antes de todo o ficheiro foi transferido. Pode transferir apenas progressivamente ficheiros MP4.

>[!NOTE]
>Deve descriptografar ativos encriptados, se desejar que estejam disponíveis para transferência progressiva.

Para fornecer aos utilizadores com URLs de transferência progressiva, primeiro tem de criar um localizador de OnDemandOrigin. Criar o localizador, dá-lhe o caminho de base para o elemento. Em seguida, é necessário acrescentar o nome de ficheiro MP4. Por exemplo:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>URLs de transmissão em fluxo
O conteúdo para clientes de transmissão em fluxo. Para fornecer aos utilizadores URLs de transmissão em fluxo, primeiro tem de criar um localizador de OnDemandOrigin. Criar o localizador, dá-lhe o caminho de base para o elemento que contém o conteúdo que pretende transmitir em fluxo. No entanto, para poder transmitir este conteúdo terá de modificar este caminho ainda mais. Para construir um URL completo para o arquivo de manifesto de transmissão em fluxo, deve concatenar o valor do caminho o localizador e o manifesto (filename.ism) nome de ficheiro. Em seguida, anexe /Manifest e um formato adequado (se necessário) para o caminho de localizador.

Também pode transmitir o seu conteúdo numa conexão SSL. Para fazer isso, certifique-se de que suas URLs de transmissão em fluxo começam com HTTPS. Atualmente, o AMS não suporta SSL com domínios personalizados.  

>[!NOTE]
>Apenas pode transmitir através de SSL se o ponto final de transmissão em fluxo do que forneça o conteúdo tiver sido criado depois de 10 de Setembro de 2014. Se os URLs de transmissão em fluxo baseiam-se os pontos finais de transmissão em fluxo criados após 10 de Setembro, o URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de transmissão em fluxo que contêm "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o URL está no formato antigo e pretende ser capaz de transmitir em fluxo através de SSL, crie um novo ponto de final de transmissão em fluxo. Utilize URLs criadas com base no novo ponto de final de transmissão em fluxo para transmitir o seu conteúdo através de SSL.

A lista seguinte descreve os diferentes formatos de transmissão em fluxo e fornece exemplos:

* Transmissão em Fluxo Uniforme

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Live Streaming (HLS) V4

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Live Streaming (HLS) V3

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

