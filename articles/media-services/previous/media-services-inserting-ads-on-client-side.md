---
title: Inserir publicidade no lado do cliente | Documentos da Microsoft
description: Este tópico mostra como inserir publicidade no lado do cliente.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c3c2101576f9b0d0c7908e62bd5cc1d6e6eeb0b2
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189806"
---
# <a name="inserting-ads-on-the-client-side"></a>Inserir publicidade no lado do cliente
Este artigo contém informações sobre como inserir vários tipos de publicidade no lado do cliente.

Para obter informações sobre o suporte de legendas de áudio e o ad fechado nos vídeos de transmissão em fluxo em direto, consulte [suportado fechadas as legendas de áudio e padrões de inserção de Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> O leitor de multimédia do Azure não suporta atualmente anúncios.
> 
> 

## <a id="insert_ads_into_media"></a>Inserir publicidade no suporte de dados de
Serviços de multimédia do Azure fornece suporte para inserção de publicidade através da plataforma de suporte de dados do Windows: Estruturas de Player. Estruturas de Player com suporte ad estão disponíveis para dispositivos Windows 8, Silverlight, Windows Phone 8 e iOS. Cada arquitetura do leitor contém o código de exemplo que mostra como implementar uma aplicação de leitor. Existem três tipos diferentes de anúncios, que pode inserir em seu suporte de dados: lista.

* **Linear** – completa de anúncios de quadro que colocar em pausa o vídeo principal.
* **Não lineares** – anúncios de sobreposição que são apresentados como está a reproduzir o vídeo principal, normalmente, um logótipo ou outra imagem estática colocado no leitor.
* **Complementar** – anúncios que são apresentados fora o jogador.

Anúncios podem ser colocados em qualquer ponto na linha de tempo do vídeo principal. Informe o jogador quando reproduzir o ad e os anúncios para reproduzir. Isso é feito usando um conjunto de arquivos padrão baseada em XML: Anúncio de vídeo de serviço de modelo (VAST), de vídeo Digital várias listas de reprodução do Ad (VMAP), suporte de dados abstraem o modelo de sequenciamento (MAST) e a definição de Interface de Ad do leitor de vídeo Digital (VPAID). Os arquivos de grandes especificam quais anúncios para apresentar. Os arquivos VMAP especificam quando reproduzir vários anúncios e conter XML grande. Arquivos MAST são outra forma de anúncios de sequência que também pode conter XML grande. Ficheiros VPAID definem uma interface entre o player de vídeo e o ad ou o servidor do ad.

Cada arquitetura do leitor funciona de forma diferente e cada uma será abordado em seu próprio artigo. Este artigo descreve os mecanismos básicos usados para inserir publicidade. Aplicações de leitor de vídeo do pedido de anúncios de um servidor do ad. O servidor do ad pode responder de diversas formas:

* Devolver um ficheiro grande
* Devolver um ficheiro VMAP (com embedded VAST)
* Devolver um ficheiro MAST (com embedded VAST)
* Devolver um arquivo grande com anúncios VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Através de um ficheiro de modelo (VAST) do serviço de anúncio de vídeo
Um arquivo grande Especifica quais ad ou anúncios para apresentar. O seguinte XML é um exemplo de um arquivo grande para um ad linear:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

O ad linear é descrito pela <**Linear**> elemento. Especifica a duração do ad, eventos de controlo, clique nos, controlo de cliques em e em diversas **MediaFile** elementos. Eventos de rastreio são especificados na <**TrackingEvents**> elemento e permitir que um servidor de ad controlar vários eventos que ocorrem enquanto vê o ad. Neste caso, o início, o ponto médio, concluído e expanda eventos são controlados. O evento de inicialização ocorre quando é apresentado o ad. O evento de ponto médio ocorre quando pelo menos 50% da linha de tempo do ad foram visualizado. O evento completa ocorre quando o ad foi executada ao fim. O evento de expansão ocorre quando o utilizador expande-se o player de vídeo para o ecrã inteiro. Clickthroughs são especificados com uma <**ClickThrough**> elemento dentro de uma <**VideoClicks**> elemento e especifica um URI para um recurso a apresentar quando o usuário clica no ad. ClickTracking é especificada num <**ClickTracking**> elemento, também dentro do <**VideoClicks**> elemento e especifica um recurso de controle para o jogador pedir quando o usuário clica no ad . A <**MediaFile**> elementos especificam informações sobre uma codificação específica de um anúncio. Quando existe mais do que um <**MediaFile**> elemento, o leitor de vídeo pode escolher a melhor codificação para a plataforma.

Anúncios lineares podem ser exibidos numa ordem especificada. Para tal, adicione adicionais <Ad> elementos para o VAST de ficheiros e especificar a ordem usando o atributo de sequência. O exemplo a seguir ilustra isso:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Anúncios não lineares são especificados num <Creative> elemento também. A exemplo a seguir mostra um <Creative> elemento que descreve um ad não linear.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

A <**NonLinearAds**> elemento pode conter um ou mais <**NonLinear**> elementos, cada um dos quais pode descrever um ad não linear. A <**NonLinear**> elemento Especifica o recurso do ad não linear. O recurso pode ser um <**StaticResource**>, um <**IFrameResource**>, ou um <**HTMLResource**>. <**StaticResource**> descreve um recurso de não-HTML e define um atributo de creativeType que especifica como o recurso é apresentado:

Imagem/gif, imagem/jpeg, png/imagem – o recurso é apresentado num HTML <**img**> etiqueta.

Aplicação/x-javascript – o recurso é apresentado num HTML <**script**> etiqueta.

Aplicação/x-shockwave-flash – o recurso será exibido num Flash player.

**IFrameResource** descreve um recurso HTML que pode ser exibido num IFrame. **HTMLResource** descreve um trecho de código HTML que pode ser inserido numa página da web. **TrackingEvents** especificar eventos de controlo e o URI para pedir quando o evento ocorre. Neste exemplo, os eventos acceptInvitation e fechar são controlados. Para obter mais informações sobre o **NonLinearAds** elemento e seus filhos, consulte IAB.NET/VAST. Tenha em atenção que o **TrackingEvents** elemento está localizado no **NonLinearAds** elemento em vez do **NonLinear** elemento.

Anúncios de complementar são definidos dentro de um <CompanionAds> elemento. O <CompanionAds> elemento pode conter um ou mais <Companion> elementos. Cada <Companion> elemento descreve um ad complementar e pode conter um <StaticResource>, <IFrameResource>, ou <HTMLResource> que são especificados da mesma forma como num ad não linear. Um arquivo grande pode conter vários anúncios de complementar e a aplicação de leitor pode escolher o ad mais adequado para apresentar. Para obter mais informações sobre VAST, consulte [3.0 grande](http://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Usando um vídeo Digital vários ficheiros de lista de reprodução (VMAP) do Ad
Um ficheiro VMAP permite-lhe especificar quando ocorrem quebras de ad, quanto tempo dura cada quebra, quantos anúncios podem ser apresentados dentro de um intervalo e que tipos de anúncios poderão ser apresentado durante um intervalo. O seguinte num arquivo VMAP de exemplo que define uma quebra de única do ad:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Um ficheiro VMAP começa com uma <VMAP> elemento que contém um ou mais <AdBreak> elementos, definindo cada uma garantia de reparação do ad. Cada quebra de ad Especifica um tipo de garantia de reparação, o ID de garantia de reparação e a compensação de tempo. O atributo breakType Especifica o tipo do ad que pode ser reproduzida durante a garantia de reparação: linear, não lineares, ou exibir. Exibir o mapa de anúncios para anúncios de grande complementar. Mais de um tipo de ad pode ser especificado numa lista separada por vírgulas (sem espaços). O breakID é um identificador opcional para o ad. O timeOffset Especifica quando o ad deverá ser apresentada. Ele pode ser especificado em uma das seguintes formas:

1. Tempo – no formato hh: mm: ou hh:mm:ss.mmm cadê .mmm milissegundos. O valor deste atributo especifica o tempo desde o início da linha do tempo de vídeo para o início da quebra ad.
2. Percentagem – no formato de n % em que n é a porcentagem da linha cronológica de vídeo para reproduzir antes de reprodução do ad
3. Início/fim – Especifica que um ad deverá ser apresentado antes ou depois do vídeo foi exibido
4. Posicionar – Especifica a ordem de quebras de ad de quando o período de tempo de quebras de ad for desconhecido, como no caso de transmissão em direto. A ordem de cada quebra de ad é especificada no formato #n onde n é um número inteiro 1 ou superior. 1 significa que o ad deve ser reproduzida na primeira oportunidade, 2 significa que o ad deve ser reproduzida na segunda oportunidade e assim por diante.

Dentro de <AdBreak> elemento, pode haver um <**AdSource**> elemento. A <**AdSource**> elemento contém os seguintes atributos:

1. ID – Especifica um identificador para a origem do ad
2. allowMultipleAds – um valor booleano que especifica se vários anúncios podem ser apresentados durante o intervalo de ad
3. followRedirects – um valor booleano opcional que especifica se o player de vídeo deve honrar redireciona dentro de uma resposta de ad

A <**AdSource**> elemento fornece o leitor de uma resposta de ad inline ou uma referência a uma resposta do ad. Ele pode conter um dos seguintes elementos:

* <VASTAdData> indica que uma resposta de ad grande é incorporada dentro do arquivo VMAP
* <AdTagURI> um URI que faz referência a uma resposta de ad de outro sistema
* <CustomAdData> -uma cadeia arbitrária que representa uma resposta não grande

Neste exemplo, uma resposta em linha ad for especificada com um <VASTAdData> elemento que contém uma resposta de ad grande. Para obter mais informações sobre os outros elementos, consulte [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

A <**AdBreak**> elemento também pode conter um <**TrackingEvents**> elemento. A <**TrackingEvents**> elemento permite-lhe controlar o início ou de fim de uma garantia de reparação do ad ou se Ocorreu um erro durante a garantia de reparação do ad. A <**TrackingEvents**> elemento contém um ou mais <**controlo**> elementos, cada um dos quais Especifica um evento de controlo e um URI de controlo. Os eventos de rastreio possíveis são:

1. breakStart – controla o início de uma garantia de reparação do ad
2. breakEnd – controlar a conclusão de uma garantia de reparação do ad
3. Erro – controla um erro que ocorreu durante a garantia de reparação do ad

O exemplo seguinte mostra um arquivo VMAP que especifica os eventos de rastreio

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Para obter mais informações sobre a <**TrackingEvents**> elemento e seus filhos, consulte http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Usando um resumo de suporte de dados sequenciar o ficheiro de modelo (MAST)
Um ficheiro MAST permite-lhe especificar os acionadores que definem quando é apresentado um anúncio. Segue-se um exemplo de arquivo MAST que contém acionadores para um ad de roll pré, um anúncio de agregação médio e um anúncio de pós-implementação.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Um ficheiro MAST começa com uma **MAST** elemento que contém um **acionadores** elemento. O <triggers> elemento contém um ou mais **acionador** elementos que definem quando um anúncio deve ser reproduzido.

O **acionador** elemento contém um **startConditions** elemento que especificam quando um ad deve começar a reproduzir. O **startConditions** elemento contém um ou mais <condition> elementos. Quando cada <condition> avalia como VERDADEIRO, um acionador é iniciado ou revogado dependendo se o <condition> está contido dentro de um **startConditions** ou **endConditions** elemento respectivamente. Quando vários <condition> elementos estão presentes, são tratadas como um OR implícito, qualquer condição avaliar como true fará com que o acionador iniciar. <condition> elementos podem ser aninhados. Quando filho <condition> elementos são a configuração predefinidos, são tratadas como uma e implícita, todas as condições têm de avaliar como verdadeiro para o acionador iniciar. O <condition> elemento contém os seguintes atributos que definem a condição:

1. **tipo de** – Especifica o tipo de condição, eventos ou propriedade
2. **nome** – o nome da propriedade ou evento a ser utilizado durante a avaliação
3. **valor** – o valor que será avaliada em relação a uma propriedade
4. **operador** – a operação para utilizar durante a avaliação: EQ (igual), EQ (diferente), GTR (superior), GEQ (maior ou igual), LT (inferior), LEQ (menor ou igual), MOD (módulo)

**endConditions** também conter <condition> elementos. Quando a condição for avaliada como true o acionador é reposta. O <trigger> elemento também contém um <sources> elemento que contém um ou mais <source> elementos. O <source> elementos definem o URI para a resposta do ad e o tipo de resposta do ad. Neste exemplo, um URI é atribuído a uma grande resposta.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Utilizar a definição de Interface de anúncio de Player de vídeo (VPAID)
VPAID é uma API para ativar a unidades de anúncios executável comunicar com um leitor de vídeo. Assim, experiências de ad altamente interativas. O usuário pode interagir com o ad e o ad pode responder a ações executadas pelo Visualizador. Por exemplo, um anúncio pode ser apresentado botões que permitem ao usuário ver mais informações ou uma versão mais tempo do ad. O leitor de vídeo tem de suportar a API de VPAID e o ad executável tem de implementar a API. Quando um jogador solicita que um anúncio de um servidor de ad o servidor de pode responder com uma grande resposta que contém um ad VPAID.

Um executável ad é criado no código que deve ser executado num ambiente de tempo de execução como Adobe Flash™ ou JavaScript que pode ser executado num navegador da web. Quando um servidor de ad retorna uma resposta grande que contém um ad VPAID, o valor da apiFramework atributo o <MediaFile> elemento tem de ser "VPAID". Esse atributo Especifica que o ad contido é um anúncio de executável VPAID. O atributo de tipo tem de ser definido para o tipo de MIME do executável, tal como "application/x-shockwave-flash" ou "application/x-javascript". O fragmento XML seguinte mostra o <MediaFile> elemento a partir de uma grande resposta que contém um anúncio de executável VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Um executável ad pode ser inicializado com o <AdParameters> elemento dentro do <Linear> ou <NonLinear> elementos numa grande resposta. Para obter mais informações sobre o <AdParameters> elemento, consulte [3.0 grande](http://www.iab.net/media/file/VASTv3.0.pdf). Para obter mais informações sobre a API de VPAID, consulte [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementação de um Windows ou Windows Phone 8 Player com suporte do Ad
A plataforma de multimédia do Microsoft: Player Framework para Windows 8 e Windows Phone 8 contém uma coleção de aplicativos de exemplo que lhe mostram como implementar uma aplicação de leitor de vídeo com o framework. Pode baixar o Player Framework e os exemplos de [Player Framework para Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Ao abrir a solução de Microsoft.PlayerFramework.Xaml.Samples, verá um número de pastas no projeto. A pasta de publicidade contém o código de exemplo relevante para a criação de um leitor de vídeo com suporte do ad. Dentro da publicidade pasta é um número de ficheiros XAML/cs sempre que mostram como inserir anúncios de uma maneira diferente. A lista seguinte descreve cada:

* AdPodPage.xaml mostra como exibir um pod do ad.
* AdSchedulingPage.xaml mostra como agendar anúncios.
* FreeWheelPage.xaml mostra como utilizar o plug-in de FreeWheel para agendar anúncios.
* MastPage.xaml mostra como agendar anúncios com um arquivo MAST.
* ProgrammaticAdPage.xaml mostra como agendar programaticamente anúncios num vídeo.
* ScheduleClipPage.xaml mostra como agendar um ad sem um arquivo grande.
* VastLinearCompanionPage.xaml mostra como inserir um linear e ad complementar.
* VastNonLinearPage.xaml mostra como inserir um ad não lineares.
* VmapPage.xaml mostra como especificar anúncios com um arquivo VMAP.

Cada um destes exemplos usa a classe MediaPlayer definida pela estrutura de player. A maioria dos exemplos utilizam Plug-ins que adicionar suporte para vários formatos de resposta do ad. O exemplo de ProgrammaticAdPage programaticamente interage com uma instância do MediaPlayer.

### <a name="adpodpage-sample"></a>Exemplo de AdPodPage
Este exemplo usa o AdSchedulerPlugin para definir quando um anúncio for exibido. Neste exemplo, um anúncio de agregação médio está agendado para ser reproduzida depois de cinco segundos. O pod do ad (um grupo de anúncios para apresentar na ordem) é especificado num arquivo grande devolvido a partir de um servidor do ad. O URI para o ficheiro grande é especificado no <RemoteAdSource> elemento.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Para obter mais informações sobre o AdSchedulerPlugin, consulte [publicidade no Framework Player no Windows 8 e Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Este exemplo também usa o AdSchedulerPlugin. Ele agenda o três ads, um anúncio de pré-implementação, um anúncio de agregação médio e um anúncio de pós-implementação. O URI para o VAST para cada anúncio é especificado num <RemoteAdSource> elemento.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
Este exemplo utiliza o FreeWheelPlugin que especifica um atributo de origem que especifica um URI que aponta para um ficheiro de SmartXML que especifique o conteúdo do ad, bem como informações de agendamento do ad.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Este exemplo utiliza o MastSchedulerPlugin que permite que use um arquivo MAST. O atributo de origem Especifica a localização do ficheiro MAST.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Este exemplo programaticamente interage com o MediaPlayer. O ficheiro de ProgrammaticAdPage.xaml instancia o MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

O ficheiro de ProgrammaticAdPage.xaml.cs cria um AdHandlerPlugin, adiciona um TimelineMarker para especificar quando um ad deverá ser apresentado e, em seguida, adiciona um manipulador para o evento de MarkerReached que carrega um RemoteAdSource especificando um URI para um ficheiro grande e, em seguida, reproduz o ad.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
Este exemplo utiliza o AdSchedulerPlugin para agendar um anúncio de agregação médio, especificando um arquivo. wmv que contém o ad.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Este exemplo ilustra como usar o AdSchedulerPlugin para agendar um ad linear de agregação médio com um ad complementar. O <RemoteAdSource> elemento Especifica a localização do ficheiro grande.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
Este exemplo utiliza o AdSchedulerPlugin para agendar uma linear e um não-lineares ad. A localização do ficheiro grande é especificada com o <RemoteAdSource> elemento.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
Este exemplo utiliza o VmapSchedulerPlugin para agendar anúncios através de um ficheiro VMAP. O URI para o ficheiro VMAP é especificado no atributo de origem do <VmapSchedulerPlugin> elemento.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementando um Player de vídeo com suporte do Ad do iOS
A plataforma de multimédia do Microsoft: Arquitetura do leitor para iOS contém uma coleção de aplicativos de exemplo que lhe mostram como implementar uma aplicação de leitor de vídeo com o framework. Pode baixar o Player Framework e os exemplos de [do Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). A página do GitHub tem uma ligação para um Wiki que contém informações adicionais sobre a arquitetura do leitor e uma introdução ao exemplo de player: [Wiki do leitor de multimédia do Azure](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Agendamento de anúncios com VMAP
O exemplo seguinte mostra como agendar anúncios através de um ficheiro VMAP.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Agendamento de anúncios com VAST
O exemplo a seguir mostra como agendar um ad grande de vinculação tardia.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   O exemplo a seguir mostra como agendar um ad grande de vinculação inicial.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

O exemplo a seguir mostra como inserir um anúncio com aproximada cortar edição (origem)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

O exemplo seguinte mostra como agendar um pod do ad.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

O exemplo seguinte mostra como agendar um anúncio de agregação médio não adesivo. Um ad não adesivo é tocado apenas uma vez, independentemente de qualquer buscando executa o Visualizador.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

O exemplo seguinte mostra como agendar um anúncio de agregação médio adesivo. Um ad auto-adesiva é apresentado sempre que for atingido o ponto especificado na linha do tempo de vídeo.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

O exemplo a seguir mostra como agendar um anúncio de pós-implementação.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

O exemplo a seguir mostra como agendar um anúncio de pré-implementação.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

O exemplo a seguir mostra como agendar um anúncio de sobreposição de agregação médio.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)

