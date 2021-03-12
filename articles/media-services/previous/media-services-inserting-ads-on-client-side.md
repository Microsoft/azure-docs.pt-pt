---
title: Inserir anúncios no lado do cliente | Microsoft Docs
description: Este artigo demonstra como inserir anúncios nos seus meios de comunicação do lado do cliente.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d942099d0abbdfc4ddfa0276184500166250728
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014966"
---
# <a name="inserting-ads-on-the-client-side"></a>Inserir anúncios no lado do cliente

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo contém informações sobre como inserir vários tipos de anúncios no lado do cliente.

Para obter informações sobre legendas fechadas e suporte a anúncios em vídeos de streaming ao vivo, consulte [as Normas de Legendagem Fechada Suportadas e De Inserção de Anúncios](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> A Azure Media Player não suporta atualmente anúncios.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Inserir anúncios nos seus meios de comunicação
A Azure Media Services fornece suporte para a inserção de anúncios através da Plataforma Windows Media: Quadros de Jogadores. As estruturas dos jogadores com suporte a anúncios estão disponíveis para dispositivos Windows 8, Silverlight, Windows Phone 8 e iOS. Cada estrutura do jogador contém um código de amostra que mostra como implementar uma aplicação do jogador. Existem três tipos diferentes de anúncios que pode inserir no seu media:list.

* **Linear** – anúncios de moldura completa que fazem uma pausa no vídeo principal.
* **Nonlinear** – sobreponha anúncios que são exibidos à medida que o vídeo principal está a ser reproduzido, normalmente um logotipo ou outra imagem estática colocada dentro do leitor.
* **Companheiro** – anúncios que são exibidos fora do leitor.

Os anúncios podem ser colocados em qualquer ponto da linha de tempo do vídeo principal. Tem de dizer ao jogador quando deve reproduzir o anúncio e quais os anúncios a reproduzir. Isto é feito usando um conjunto de ficheiros padrão baseados em XML: Modelo de serviço de anúncio de vídeo de vídeo (VAST), lista de reprodução de anúncios múltiplos de vídeo digital (VMAP), modelo de sequenciação abstrata de mídia (MAST) e Definição de Interface de Interface de Leitor de Vídeo Digital (VPAID). Os ficheiros VAST especificam quais os anúncios a exibir. Os ficheiros VMAP especificam quando reproduzir vários anúncios e contêm VAST XML. Os ficheiros MAST são outra forma de sequenciar anúncios que também podem conter VAST XML. Os ficheiros VPAID definem uma interface entre o leitor de vídeo e o servidor de anúncios ou anúncios.

Cada quadro de jogadores funciona de forma diferente e cada um será coberto pelo seu próprio artigo. Este artigo descreve os mecanismos básicos utilizados para inserir anúncios. As aplicações dos jogadores de vídeo solicitam anúncios a partir de um servidor de anúncios. O servidor de anúncios pode responder de várias formas:

* Devolva um ficheiro VAST
* Devolva um ficheiro VMAP (com VAST incorporado)
* Devolva um ficheiro MAST (com VAST incorporado)
* Devolva um ficheiro VAST com anúncios VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Usando um modelo de serviço de anúncio de vídeo (VAST)
Um ficheiro VAST especifica o anúncio ou anúncios a exibir. O seguinte XML é um exemplo de um ficheiro VAST para um anúncio linear:

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

O anúncio linear é descrito pelo elemento linear> **<.** Especifica a duração do anúncio, eventos de rastreio, clique, clique no tracking e uma série de elementos **MediaFile.** Os eventos de rastreio são especificados dentro do <**TrackingEvents**> elemento e permitem que um servidor de anúncios rastreie vários eventos que ocorrem durante a visualização do anúncio. Neste caso, o início, o ponto médio, os eventos completos e de expansão são acompanhados. O evento de início ocorre quando o anúncio é exibido. O evento de ponto médio ocorre quando pelo menos 50% da linha temporal do anúncio foi visualizada. O evento completo ocorre quando o anúncio corre até o fim. O evento Expandir ocorre quando o utilizador expande o leitor de vídeo para o ecrã completo. Os clickthroughs são especificados com um <**clickThrough**> elemento dentro de um <**VideoClicks**> elemento e especifica um URI para um recurso para exibir quando o utilizador clica no anúncio. O ClickTracking é especificado num elemento de> **clickTracking** <, também dentro do elemento <**VideoClicks**> e especifica um recurso de rastreio para o leitor solicitar quando o utilizador clica no anúncio. Os elementos <**MediaFile**> especificam informações sobre uma codificação específica de um anúncio. Quando há mais de um <**elemento> MediaFile,** o leitor de vídeo pode escolher a melhor codificação para a plataforma.

Os anúncios lineares podem ser exibidos numa ordem especificada. Para isso, adicione elementos adicionais `<Ad>` ao ficheiro VAST e especifique a ordem utilizando o atributo de sequência. O exemplo seguinte ilustra isso mesmo:

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

Os anúncios não lineares também são especificados num `<Creative>` elemento. O exemplo a seguir mostra um `<Creative>` elemento que descreve um anúncio não linear.

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

O elemento <**NonLinearAds**> pode conter um ou mais elementos <> **não lineares,** cada um dos quais pode descrever um anúncio não linear. O elemento **<NonLinear**> especifica o recurso para o anúncio não linear. O recurso pode ser um> **staticresource** <, um> **<IFrameResource,** ou um> **htmlresource** <. \<**StaticResource**> descreve um recurso não-HTML e define um atributo CreativeType que especifica como o recurso é exibido:

Imagem/gif, imagem/jpeg, imagem/png – o recurso é apresentado numa etiqueta de **<IMG**>.

Aplicação/x-javascript – o recurso é apresentado numa etiqueta de **> de script** <HTML.

Aplicação/x-shockwave-flash – o recurso é apresentado num leitor Flash.

**IFrameResource** descreve um recurso HTML que pode ser exibido num IFrame. **HTMLResource** descreve um pedaço de código HTML que pode ser inserido numa página web. **Os TrackingEvents** especificam eventos de rastreio e o URI para solicitar quando o evento ocorrer. Nesta amostra, os eventos de aceitação e colapso são rastreados. Para obter mais informações sobre o elemento **NonLinearAds** e as suas crianças, consulte IAB.NET/VAST. Note que o elemento **TrackingEvents** está localizado dentro do elemento **NonLinearAds** em vez do elemento **NonLinear.**

Os anúncios de acompanhantes são definidos dentro de um `<CompanionAds>` elemento. O `<CompanionAds>` elemento pode conter um ou mais `<Companion>` elementos. Cada `<Companion>` elemento descreve um anúncio de acompanhante e pode conter um , ou que são `<StaticResource>` `<IFrameResource>` `<HTMLResource>` especificados da mesma forma que em um anúncio não linear. Um ficheiro VAST pode conter vários anúncios de acompanhantes e a aplicação do jogador pode escolher o anúncio mais apropriado para exibir. Para mais informações sobre o VAST, consulte [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Usando um ficheiro de lista de anúncios de vários anúncios de vídeo digital (VMAP)
Um ficheiro VMAP permite especificar quando ocorrem quebras de anúncios, quanto tempo é cada pausa, quantos anúncios podem ser exibidos dentro de uma pausa e que tipos de anúncios podem ser exibidos durante uma pausa. O seguinte, num ficheiro VMAP de exemplo, que define uma única rutura de anúncio:

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

Um ficheiro VMAP começa com um `<VMAP>` elemento que contém um ou mais `<AdBreak>` elementos, cada um definindo uma rutura de anúncio. Cada rutura de anúncio especifica um tipo de rutura, identificação de rutura e compensação de tempo. O atributo breakType especifica o tipo de anúncio que pode ser reproduzido durante o intervalo: linear, não linear ou exibido. Exibir mapa de anúncios para anúncios de companheiros VAST. Mais de um tipo de anúncio pode ser especificado numa lista separada de vírgula (sem espaços). O breakID é um identificador opcional para o anúncio. O timeOffset especifica quando o anúncio deve ser exibido. Pode ser especificado de uma das seguintes formas:

1. Tempo – em hh:mm:ss ou hh:mm:ss.mmm onde .mmm é milissegundos. O valor deste atributo especifica o tempo desde o início da linha do tempo de vídeo até ao início da quebra de anúncios.
2. Percentagem – em formato n% onde n é a percentagem da linha do tempo de vídeo a reproduzir antes de reproduzir o anúncio
3. Início/Fim – especifica que um anúncio deve ser exibido antes ou depois de o vídeo ter sido exibido
4. Posição – especifica a ordem de intervalos de anúncios quando o tempo do anúncio é desconhecido, como em streaming ao vivo. A ordem de cada rutura de anúncio é especificada no formato #n onde n é um número inteiro 1 ou maior. 1 significa que o anúncio deve ser jogado na primeira oportunidade, 2 significa que o anúncio deve ser jogado na segunda oportunidade e assim por diante.

Dentro do `<AdBreak>` elemento, pode haver um <**elemento> AdSource.** O elemento **> <AdSource** contém os seguintes atributos:

1. Id – especifica um identificador para a fonte de anúncio
2. permitirMultipleAds – um valor booleano que especifica se vários anúncios podem ser exibidos durante a quebra de anúncios
3. followRedirects – um valor booleano opcional que especifica se o leitor de vídeo deve honrar redirecionamentos dentro de uma resposta de anúncio

O elemento **<AdSource**> fornece ao jogador uma resposta de anúncio inline ou uma referência a uma resposta de anúncio. Pode conter um dos seguintes elementos:

* `<VASTAdData>` indica que uma resposta de anúncio VAST está incorporada no ficheiro VMAP
* `<AdTagURI>` um URI que faz referência a uma resposta de anúncio de outro sistema
* `<CustomAdData>` -uma cadeia arbitrária que representa uma resposta não-VASTA

Neste exemplo, uma resposta de anúncio em linha é especificada com um `<VASTAdData>` elemento que contém uma resposta de anúncio VAST. Para obter mais informações sobre os outros elementos, consulte [vMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

O elemento **<AdBreak**> também pode conter um elemento <**TrackingEvents**>. O <**TrackingEvents**> elemento permite-lhe rastrear o início ou o fim de uma rutura de anúncio ou se ocorreu um erro durante a quebra de anúncios. O elemento <**TrackingEvents**> contém um ou mais elementos> **de rastreio** <, cada um dos quais especifica um evento de rastreio e um URI de rastreio. Os possíveis eventos de rastreio são:

1. breakStart – acompanha o início de uma rutura de anúncio
2. breakEnd - acompanhar a conclusão de uma quebra de anúncio
3. error – rastreia um erro que ocorreu durante a quebra de anúncios

O exemplo a seguir mostra um ficheiro VMAP que especifica eventos de rastreio

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

Para obter mais informações sobre o <**TrackingEvents**> elemento e seus filhos, consulte http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Usando um modelo de seqüência abstrata de mídia (MAST)
Um ficheiro MAST permite especificar os gatilhos que definem quando um anúncio é apresentado. Segue-se um ficheiro MAST de exemplo que contém gatilhos para um anúncio pré-roll, um anúncio de meio-rolo e um anúncio pós-roll.

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


Um ficheiro MAST começa com um elemento **MAST** que contém um elemento **de gatilho.** O `<triggers>` elemento contém um ou mais elementos de **gatilho** que definem quando um anúncio deve ser reproduzido.

O elemento **de gatilho** contém um elemento **startConditions** que especifica quando um anúncio deve começar a ser reproduzido. O elemento **startConditions** contém um ou mais `<condition>` elementos. Quando cada `<condition>` um avalia para verdadeiro um gatilho é iniciado ou revogado dependendo se o é contido dentro de `<condition>` um elemento **startConditions** ou **endConditions** respectivamente. Quando `<condition>` vários elementos estão presentes, são tratados como um OR implícito, qualquer condição que avalie verdadeiramente fará com que o gatilho inicie. `<condition>` elementos podem ser aninhados. Quando `<condition>` os elementos infantis estão predefinidos, são tratados como um E implícito, todas as condições devem avaliar-se verdadeiramente para o gatilho iniciar. O `<condition>` elemento contém os seguintes atributos que definem a condição:

1. **tipo** – especifica o tipo de condição, evento ou propriedade
2. **nome** – o nome do imóvel ou evento a utilizar durante a avaliação
3. **valor** – o valor que um imóvel será avaliado contra
4. **operador** – a operação a utilizar durante a avaliação: QE (igual), NEQ (não igual), GTR (maior), GEQ (maior ou igual), LT (Menos do que), LEQ (menos ou igual), MOD (modulo)

**as condições finais** também contêm `<condition>` elementos. Quando uma condição avalia para ser verdadeira, o gatilho é reiniciado. O `<trigger>` elemento também contém um elemento que contém um ou mais `<sources>` `<source>` elementos. Os `<source>` elementos definem o URI à resposta do anúncio e ao tipo de resposta a anúncios. Neste exemplo, um URI é dado a uma resposta VASTA.

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Usando a definição de interface de Player-Ad de vídeo (VPAID)
VPAID é uma API para permitir que unidades de anúncios executáveis se comuniquem com um leitor de vídeo. Isto permite experiências de anúncios altamente interativas. O utilizador pode interagir com o anúncio e o anúncio pode responder às ações tomadas pelo espectador. Por exemplo, um anúncio pode exibir botões que permitem ao utilizador visualizar mais informações ou uma versão mais longa do anúncio. O leitor de vídeo deve apoiar a API VPAID e o anúncio executável deve implementar a API. Quando um jogador solicita um anúncio a partir de um servidor de anúncios, o servidor pode responder com uma resposta VAST que contém um anúncio VPAID.

Um anúncio executável é criado em código que deve ser executado em um ambiente de tempo de execução como Adobe Flash™ ou JavaScript que pode ser executado em um navegador web. Quando um servidor de anúncios retorna uma resposta VAST contendo um anúncio VPAID, o valor do atributo apiFramework no `<MediaFile>` elemento deve ser "VPAID". Este atributo especifica que o anúncio contido é um anúncio executável VPAID. O atributo do tipo deve ser definido para o tipo MIME do executável, como "aplicação/x-shockwave-flash" ou "aplicação/x-javascript". O seguinte corte XML mostra o `<MediaFile>` elemento de uma resposta VAST contendo um anúncio executável VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Um anúncio executável pode ser inicializado usando o `<AdParameters>` elemento dentro ou `<Linear>` `<NonLinear>` elementos numa resposta VAST. Para obter mais informações sobre o `<AdParameters>` elemento, consulte [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf). Para obter mais informações sobre a API VPAID, consulte [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementação de um Windows ou Windows Phone 8 Player com suporte a anúncios
A Plataforma Microsoft Media: A Estrutura do Jogador para o Windows 8 e o Windows Phone 8 contém uma coleção de aplicações de amostra que mostram como implementar uma aplicação de leitor de vídeo utilizando a estrutura. Pode descarregar o Quadro de Jogadores e as amostras do [Player Framework para Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando abrir a solução Microsoft.PlayerFramework.Xaml.Samples, verá várias pastas dentro do projeto. A pasta Publicidade contém o código de amostra relevante para a criação de um leitor de vídeo com suporte a anúncios. Dentro da pasta Publicidade está uma série de ficheiros XAML/cs cada um dos quais mostram como inserir anúncios de uma forma diferente. A seguinte lista descreve cada uma:

* AdPodPage.xaml Mostra como exibir um vagem de anúncios.
* AdSchedulingPage.xaml Mostra como agendar anúncios.
* FreeWheelPage.xaml Mostra como usar o plugin FreeWheel para agendar anúncios.
* MastPage.xaml Mostra como agendar anúncios com um ficheiro MAST.
* ProgrammaticAdPage.xaml Mostra como programar anúncios programáticamente num vídeo.
* AgendaClipPage.xaml Mostra como agendar um anúncio sem um ficheiro VAST.
* VastLinearCompanionPage.xaml Mostra como inserir um anúncio linear e companheiro.
* VastNonLinearPage.xaml Mostra como inserir um anúncio não linear.
* VmapPage.xaml Mostra como especificar anúncios com um ficheiro VMAP.

Cada uma destas amostras utiliza a classe MediaPlayer definida pela estrutura do jogador. A maioria das amostras usa plugins que adicionam suporte para vários formatos de resposta a anúncios. A amostra ProgrammaticAdPage interage programáticamente com uma instância MediaPlayer.

### <a name="adpodpage-sample"></a>Amostra de AdPodPage
Esta amostra utiliza o AdSchedulerPlugin para definir quando deve apresentar um anúncio. Neste exemplo, um anúncio de meio-rolo está programado para ser reproduzido após cinco segundos. O pod de anúncios (um grupo de anúncios a exibir por ordem) é especificado num ficheiro VAST devolvido a partir de um servidor de anúncios. O URI para o ficheiro VAST é especificado no `<RemoteAdSource>` elemento.

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

Para obter mais informações sobre o AdSchedulerPlugin, consulte [publicidade no Quadro de Jogadores no Windows 8 e Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Esta amostra também usa o AdSchedulerPlugin. Ele agenda três anúncios, um anúncio pré-roll, um anúncio de meio-rolo, e um anúncio pós-roll. O URI para o VAST para cada anúncio é especificado num `<RemoteAdSource>` elemento.

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
Esta amostra utiliza o FreeWheelPlugin que especifica um atributo Source que especifica um URI que aponta para um ficheiro SmartXML que especifica o conteúdo de anúncios, bem como informações de agendamento de anúncios.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Esta amostra utiliza o MastSchedulerPlugin que lhe permite utilizar um ficheiro MAST. O atributo Fonte especifica a localização do ficheiro MAST.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Esta amostra interage programáticamente com o MediaPlayer. O ficheiro ProgrammaticAdPage.xaml instantaneamente o MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

O ficheiro ProgrammaticAdPage.xaml.cs cria um AdHandlerPlugin, adiciona um TimelineMarker para especificar quando um anúncio deve ser apresentado e, em seguida, adiciona um manipulador para o evento MarkerReached que carrega um RemoteAdSource especificando um URI para um ficheiro VAST e, em seguida, reproduz o anúncio.

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

### <a name="scheduleclippage"></a>AgendaClipPage
Esta amostra utiliza o AdSchedulerPlugin para agendar um anúncio de meio-rolo especificando um ficheiro .wmv que contém o anúncio.

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
Esta amostra ilustra como usar o AdSchedulerPlugin para agendar um anúncio linear de meio rolo com um anúncio de acompanhante. O `<RemoteAdSource>` elemento especifica a localização do ficheiro VAST.

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
Esta amostra utiliza o AdSchedulerPlugin para agendar um anúncio linear e não linear. A localização do ficheiro VAST é especificada com o `<RemoteAdSource>` elemento.

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
Esta amostra utiliza o VmapSchedulerPlugin para agendar anúncios usando um ficheiro VMAP. O URI ao ficheiro VMAP é especificado no atributo Fonte do `<VmapSchedulerPlugin>` elemento.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementação de um leitor de vídeo iOS com suporte a anúncios
A Plataforma Microsoft Media: Player Framework for iOS contém uma coleção de aplicações de amostra que mostram como implementar uma aplicação de leitor de vídeo usando a estrutura. Você pode baixar o Quadro de Jogadores e as amostras do [Azure Media Player Framework](https://github.com/CloudMetal/azure-media-player-framework). A página do GitHub tem um link para um Wiki que contém informações adicionais sobre a estrutura do jogador e uma introdução à amostra do jogador: [Azure Media Player Wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Agendamento de Anúncios com VMAP
O exemplo a seguir mostra como agendar anúncios usando um ficheiro VMAP.

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

### <a name="scheduling-ads-with-vast"></a>Agendamento de Anúncios com VAST
A amostra que se segue mostra como agendar um anúncio DE LIGAÇÃO TARDIO VAST.


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

   A amostra que se segue mostra como agendar um anúncio VAST de ligação antecipada.

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

A amostra que se segue mostra como inserir um anúncio utilizando a Edição de Corte Bruto (RCE)

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

O exemplo a seguir mostra como agendar uma cápsula de anúncio.

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

O exemplo a seguir mostra como agendar um anúncio de meio-rolo não pegajoso. Um anúncio não pegajoso só é reproduzido uma vez, independentemente de qualquer procura que o espectador realize.

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

O exemplo a seguir mostra como agendar um anúncio pegajoso a meio do rolo. Um anúncio pegajoso é exibido cada vez que o ponto especificado na linha do tempo de vídeo é alcançado.

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

A amostra que se segue mostra como agendar um anúncio pós-rolo.

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

A amostra que se segue mostra como agendar um anúncio pré-roll.

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

A amostra que se segue mostra como agendar um anúncio de sobreposição de rolos médios.

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
