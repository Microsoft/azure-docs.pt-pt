---
title: Entrega de conteúdos para clientes | Documentos da Microsoft
description: Este tópico apresenta uma visão geral do que está envolvido no fornecimento de seu conteúdo com os serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 3314ad4558fdd55429a5a68326dd46b5920d7daa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316240"
---
# <a name="deliver-content-to-customers"></a>Fornecer conteúdo aos clientes
Quando a disponibilizar o conteúdo de transmissão em fluxo ou de vídeo a pedido aos clientes, seu objetivo é fornecer o vídeo de alta qualidade para vários dispositivos em condições de rede diferentes.

Para atingir esse objetivo, pode:

* Codifique a transmissão para um fluxo de vídeo múltipla (velocidade de transmissão adaptável). Isso se encarregará de condições de qualidade e de rede.
* Utilizar serviços de multimédia do Microsoft Azure [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) para dinamicamente novamente o pacote sua transmissão em fluxo para protocolos diferentes. Isso se encarregará de transmissão em fluxo em diferentes dispositivos. Serviços de multimédia suportam a distribuição das seguinte velocidade de transmissão adaptável tecnologias de transmissão em fluxo: <br/>
    * **HTTP Live Streaming** (HLS) - adicionar "(formato Format=m3u8-aapl)" caminho para a parte "/ manifesto" do URL para indicar ao servidor de origem de transmissão em fluxo para retornar o back-conteúdo HLS para consumo no **Apple iOS** dispositivos nativos (para obter detalhes, ver [localizadores](#locators) e [URLs](#URLs)),
    * **MPEG-DASH** -adicionar "(formato = mpd-time-csf)" caminho para a parte "/ manifesto" do URL para indicar ao servidor de origem de transmissão em fluxo para retornar fazer uma cópia de MPEG-DASH (para obter detalhes, consulte [localizadores](#locators) e [URLs](#URLs)),
    * **Transmissão em fluxo uniforme**.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Este artigo fornece uma descrição geral dos conceitos de entrega de conteúdos importantes.

Para verificar problemas conhecidos, consulte [problemas conhecidos](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Empacotamento dinâmico
Com o empacotamento dinâmico que os Media Services fornecem, que pode distribuir os seus conteúdos de velocidade de transmissão adaptável MP4 ou Smooth Streaming com codificação em transmissão em fluxo formatos suportados pelos Media Services (MPEG-DASH, HLS, Smooth Streaming) sem ter de voltar o pacote para estes formatos de transmissão em fluxo. Recomendamos a entrega dos seus conteúdos com o empacotamento dinâmico.

Para tirar partido do empacotamento dinâmico, terá de codificar o (origem) mezanino para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável.

Com o empacotamento dinâmico, armazenar e pagar pelos ficheiros num único formato de armazenamento. Media Services irão compilar e disponibilizar a resposta adequada com base nos seus pedidos.

Empacotamento dinâmico está disponível para standard e premium, pontos finais de transmissão em fluxo. 

Para obter mais informações, consulte [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtros e dos manifestos dinâmicos
Pode definir filtros para os seus ativos com os Media Services. Estes filtros são regras de lado do servidor que ajudam os seus clientes fazer coisas como reproduzir uma secção específica de um vídeo ou especificar um subconjunto de representações de áudio e vídeos que o dispositivo do seu cliente pode manipular (em vez de todas as representações que estão associadas a com elemento). Esta filtragem é assegurado através da *manifestos dinâmicos* que são criados quando o cliente pede para transmitir um vídeo com base em um ou mais filtros de especificado.

Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Localizadores
Para fornecer ao utilizador um URL que pode ser utilizado para transmitir ou transferir o conteúdo, tem primeiro de publicar o elemento mediante a criação de um localizador. Um localizador fornece um ponto de entrada para aceder aos ficheiros contidos num elemento. Os Media Services suportam dois tipos de localizadores:

* Localizadores OnDemandOrigin. Estes são utilizados para transmitir multimédia (por exemplo, MPEG-DASH, HLS ou Smooth Streaming) ou transferir progressivamente os ficheiros.
* Localizadores de URL do acesso partilhado (SAS) de assinatura. Estes são utilizados para transferir ficheiros de suporte de dados para o seu computador local.

Uma *política de acesso* é usada para definir as permissões (por exemplo, leitura, escrita e lista) e a duração para a qual um cliente tem acesso de um determinado ativo. Tenha em atenção que a permissão de lista (AccessPermissions.List) não deve ser usada na criação de um localizador de OnDemandOrigin.

Os localizadores têm data de validade. O portal do Azure define uma data de expiração de 100 anos no futuro para os localizadores.

> [!NOTE]
> Se utilizou o portal do Azure para criar localizadores antes de Março de 2015, os localizadores foram definidos para expirar após dois anos.
> 
> 

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Observe que, ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

Os localizadores não foram concebidos para gerir o controlo de acesso por utilizador. É possível conceder direitos de acesso diferentes para utilizadores individuais utilizando as soluções de gestão de direitos digitais (DRM). Para obter mais informações, consulte [proteger o suporte de dados](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Ao criar um localizador, pode haver um atraso de 30 segundos devido ao armazenamento e os processos de propagação no armazenamento do Azure.

## <a name="adaptive-streaming"></a>Transmissão em fluxo adaptável
Tecnologias de velocidade de transmissão adaptável permitir que as aplicações de leitor de vídeo determinar as condições de rede e selecionar a partir de várias velocidades de transmissão. Quando degrada a comunicação de rede, o cliente pode selecionar uma velocidade de transmissão mais baixa, para a reprodução pode continuar com a qualidade de vídeo reduzida. Como melhorar a condições de rede, o cliente pode mudar para uma velocidade de transmissão superior com qualidade de vídeo melhorada. Serviços de multimédia do Azure suporta as seguintes tecnologias de velocidade de transmissão adaptável: HTTP Live Streaming (HLS), Smooth Streaming e MPEG-DASH.

Para fornecer aos utilizadores URLs de transmissão em fluxo, primeiro tem de criar um localizador de OnDemandOrigin. Criar o localizador dá-lhe o caminho de base para o elemento que contém o conteúdo que pretende transmitir em fluxo. No entanto, para poder transmitir este conteúdo, terá de modificar este caminho ainda mais. Para construir um URL completo para o arquivo de manifesto de transmissão em fluxo, deve concatenar o valor do caminho o localizador e o manifesto (filename.ism) nome de ficheiro. Em seguida, anexe **/manifesto** e um formato adequado (se necessário) para o caminho de localizador.

> [!NOTE]
> Também pode transmitir o seu conteúdo numa conexão SSL. Para fazer isso, certifique-se de que suas URLs de transmissão em fluxo começam com HTTPS. Tenha em atenção que, atualmente, AMS não suporta SSL com domínios personalizados.  
> 

Apenas pode transmitir através de SSL se o ponto final de transmissão em fluxo do que forneça o conteúdo tiver sido criado depois de 10 de Setembro de 2014. Se os URLs de transmissão em fluxo baseiam-se os pontos finais de transmissão em fluxo criados após 10 de Setembro de 2014, o URL contém "streaming.mediaservices.windows.net." URLs de transmissão em fluxo que contêm "origin.mediaservices.windows.net" (o formato antigo) não suportam SSL. Se o URL está no formato antigo e pretende ser capaz de transmitir em fluxo através de SSL, crie um novo ponto de final de transmissão em fluxo. Utilize URLs com base no novo ponto de final de transmissão em fluxo para transmitir o seu conteúdo através de SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Formatos de URL de transmissão em fluxo

### <a name="mpeg-dash-format"></a>Formato de MPEG-DASH
{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato de V4 Apple HTTP Live Streaming (HLS)
{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato do Apple HTTP Live Streaming (HLS) V3
{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato de Apple HTTP Live Streaming (HLS) com o filtro de só de áudio
Por predefinição, faixas de só de áudio são incluídas no HLS manifesto. Isto é necessário para a certificação da Apple Store para redes de celular. Neste caso, se um cliente não tiver a largura de banda suficiente ou está ligado através de uma ligação de 2G, reprodução muda para só de áudio. Isto ajuda a manter a transmissão em fluxo conteúdo sem colocação em memória intermédia, mas não existe nenhum vídeo. Em alguns cenários, leitor de colocação em memória intermédia poderá ser preferido sobre só de áudio. Se pretende remover a faixa de só de áudio, adicione **só de áudio = false** para o URL.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

Para obter mais informações, consulte [funcionalidades adicionais de saída de suporte de composição de manifesto dinâmico e HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formato de transmissão em fluxo uniforme
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Manifesto do 2.0 de transmissão em fluxo uniforme (manifesto legado)
Por predefinição, o formato de manifesto de transmissão em fluxo uniforme contém a marca de repetições (etiqueta-r). No entanto, alguns leitores não suporta a etiqueta de r. Os clientes com estes leitores podem utilizar um formato que desativa a marca de r:

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Transferência progressiva
Com a transferência progressiva, pode começar a reprodução de mídia antes de todo o ficheiro foi transferido. Não é possível transferir progressivamente. ISM * (ismv, isma, ismt ou ismc) ficheiros.

Para transferir progressivamente os conteúdos, utilize o tipo de OnDemandOrigin de localizador. O exemplo seguinte mostra o URL que é baseado no tipo de localizador OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Tem a desencriptar quaisquer ativos de encriptação de armazenamento que pretende transmitir em fluxo do serviço de origem para transferência progressiva.

## <a name="download"></a>Transferência
Para transferir o conteúdo para um dispositivo de cliente, tem de criar um localizador SAS. O localizador SAS fornece acesso para o contentor de armazenamento do Azure onde está localizado o ficheiro. Para criar o URL de transferência, é necessário que incorporar o nome do arquivo entre o host e a assinatura SAS.

O exemplo seguinte mostra o URL que se baseia o localizador SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

As seguintes considerações aplicam-se:

* Tem a desencriptar quaisquer ativos de encriptação de armazenamento que pretende transmitir em fluxo do serviço de origem para transferência progressiva.
* Um download que ainda não terminou dentro de 12 horas irá falhar.

## <a name="streaming-endpoints"></a>Pontos finais de transmissões em fluxo

Um ponto de final de transmissão em fluxo representa um serviço de transmissão em fluxo que pode entregar conteúdo diretamente a uma aplicação de leitor cliente ou a uma rede de entrega de conteúdos (CDN) para uma maior distribuição. O fluxo de saída de um serviço de ponto final de transmissão em fluxo pode ser uma transmissão em direto ou um ativo de vídeo a pedido na sua conta de Media Services. Existem dois tipos de pontos finais de transmissão em fluxo **padrão** e **premium**. Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

## <a name="known-issues"></a>Problemas conhecidos
### <a name="changes-to-smooth-streaming-manifest-version"></a>Versão do manifesto de alterações para transmissão em fluxo uniforme
Antes do lançamento do serviço de Julho de 2016, quando ativos produziram pelo Media Encoder Standard, Media Encoder Premium Workflow ou o codificador de multimédia do Azure anteriores foram transmitidos em fluxo com o empacotamento dinâmico, a transmissão em fluxo uniforme manifesto devolvido seria estão de acordo com a versão 2.0. Na versão 2.0, as durações de fragmento não usar as marcas de repetição de chamada ('r'). Por exemplo:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Na versão do serviço de Julho de 2016, o manifesto de transmissão em fluxo uniforme de mensagens em fila gerado está em conformidade com a versão 2.2, com durações de fragmento utilizando as etiquetas de repetições. Por exemplo:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alguns dos clientes de transmissão em fluxo uniforme de mensagens em fila herdados podem não suportar as etiquetas de repetições e irão falhar ao carregar manifesto. Para atenuar este problema, pode utilizar o parâmetro de formato de manifesto herdado **(formato = fmp4 v20)** ou atualizar o cliente para a versão mais recente, que suporta etiquetas de repetições. Para obter mais informações, consulte [2.0 de transmissão em fluxo uniforme](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Atualizar os localizadores de serviços de multimédia depois de implementar chaves de armazenamento](media-services-roll-storage-access-keys.md)

