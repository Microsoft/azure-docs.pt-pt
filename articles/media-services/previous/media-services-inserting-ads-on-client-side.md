---
title: Inserindo anúncios no lado do cliente | Microsoft Docs
description: Este artigo demonstra como inserir anúncios em sua mídia no lado do cliente.
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
ms.openlocfilehash: f56c897fd6f5ce7e5129a4500ecaacbaf0a75f3b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895968"
---
# <a name="inserting-ads-on-the-client-side"></a>Inserindo anúncios no lado do cliente
Este artigo contém informações sobre como inserir vários tipos de anúncios no lado do cliente.

Para obter informações sobre legendas codificadas e suporte ao AD em vídeos de transmissão ao vivo, consulte [padrões de legendagem e inserção de anúncios com suporte](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Atualmente, o Player de Mídia do Azure não oferece suporte a anúncios.
> 
> 

## <a id="insert_ads_into_media"></a>Inserindo anúncios em sua mídia
Os serviços de mídia do Azure fornecem suporte para inserção de anúncios por meio da plataforma Windows Media: frameworks de Player. As estruturas de Player com suporte ao AD estão disponíveis para dispositivos Windows 8, Silverlight, Windows Phone 8 e iOS. Cada estrutura de Player contém um código de exemplo que mostra como implementar um aplicativo de Player. Há três tipos diferentes de anúncios que você pode inserir em sua lista de mídia:.

* **Lineares** – anúncios de quadro completos que pausam o vídeo principal.
* Não **lineares** – anúncios de sobreposição que são exibidos como o vídeo principal está sendo reproduzido, geralmente um logotipo ou outra imagem estática colocada no Player.
* **Companion** – anúncios que são exibidos fora do Player.

Os anúncios podem ser colocados em qualquer ponto na linha do tempo do vídeo principal. Você deve informar ao Player quando reproduzir o anúncio e quais anúncios jogar. Isso é feito usando um conjunto de arquivos padrão baseados em XML: modelo de serviço de anúncio de vídeo (grande), VMAP (Digital Video Multiple ad playlist), MAST (Media abstract Sequenciating template) e VPAID (Digital Video Player ad interface definition). Os VASTOs arquivos especificam quais anúncios devem ser exibidos. Os arquivos VMAP especificam quando reproduzir vários anúncios e contêm amplo XML. Os arquivos MAST são outra maneira de sequenciar anúncios que também podem conter grandes XML. Os arquivos VPAID definem uma interface entre o player de vídeo e o AD ou o servidor AD.

Cada estrutura do Player funciona de maneira diferente e cada uma será abordada em seu próprio artigo. Este artigo descreve os mecanismos básicos usados para inserir anúncios. Os aplicativos de player de vídeo solicitam anúncios de um servidor do AD. O servidor do AD pode responder de várias maneiras:

* Retornar um vasto arquivo
* Retornar um arquivo VMAP (com VASTOs incorporados)
* Retornar um arquivo MAST (com VASTOs incorporados)
* Retornar um vasto arquivo com anúncios de VPAID

### <a name="using-a-video-ad-service-template-vast-file"></a>Usando um arquivo de modelo de serviço de anúncio de vídeo (vasto)
Um vasto arquivo especifica o que o AD ou o ADS exibir. O XML a seguir é um exemplo de um grande arquivo para um anúncio linear:

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

O anúncio linear é descrito pelo < elemento >**linear**. Ele especifica a duração do anúncio, eventos de rastreamento, cliques, cliques de rastreamento e vários elementos **mediafile** . Os eventos de rastreamento são especificados no elemento <**TrackingEvents**> e permitem que um servidor do AD rastreie vários eventos que ocorrem durante a exibição do anúncio. Nesse caso, os eventos Start, central, complete e Expand são rastreados. O evento iniciar ocorre quando o anúncio é exibido. O evento de ponto médio ocorre quando pelo menos 50% da linha do tempo do anúncio tiver sido exibida. O evento completo ocorre quando o anúncio foi executado até o final. O evento de expansão ocorre quando o usuário expande o player de vídeo para tela inteira. Os clickthroughs são especificados com um elemento <**ClickThrough**> dentro de um elemento <**VideoClicks**> e especifica um URI para um recurso a ser exibido quando o usuário clica no anúncio. ClickTracking é especificado em um elemento <**ClickTracking**>, também dentro do elemento <**VideoClicks**> e especifica um recurso de controle para o Player solicitar quando o usuário clica no anúncio. Os elementos do <**mediafile**> especificam informações sobre uma codificação específica de um anúncio. Quando há mais de um elemento <**mediafile**>, o player de vídeo pode escolher a melhor codificação para a plataforma.

Anúncios lineares podem ser exibidos em uma ordem especificada. Para fazer isso, adicione elementos `<Ad>` adicionais ao vasto arquivo e especifique a ordem usando o atributo Sequence. O exemplo seguinte ilustra isso mesmo:

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

Anúncios não lineares também são especificados em um elemento `<Creative>`. O exemplo a seguir mostra um elemento `<Creative>` que descreve um anúncio não linear.

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

O elemento <**NonLinearAds**> pode conter um ou mais elementos de > não**lineares**<, cada um deles pode descrever um anúncio não linear. O < elemento > não**linear**especifica o recurso para o anúncio não linear. O recurso pode ser um > <**StaticResource**, um <**IFrameResource**> ou um < de >**HTMLResource**. \<**StaticResource**> descreve um recurso não HTML e define um atributo creativetype que especifica como o recurso é exibido:

Image/gif, image/jpeg, image/png – o recurso é exibido em uma marcação HTML <**img**>.

Application/x-JavaScript – o recurso é exibido em um**script**< HTML > marca.

Application/x-Shockwave-Flash – o recurso é exibido em um Flash Player.

**IFrameResource** descreve um recurso HTML que pode ser exibido em um iframe. **HTMLResource** descreve uma parte do código HTML que pode ser inserida em uma página da Web. **TrackingEvents** especifique eventos de rastreamento e o URI a ser solicitado quando o evento ocorrer. Neste exemplo, os eventos os acceptinvitation e Collapse são rastreados. Para obter mais informações sobre o elemento **NonLinearAds** e seus filhos, consulte IAB.net/Vast. Observe que o elemento **TrackingEvents** está localizado dentro do elemento **NonLinearAds** em vez do elemento **Nonlinear** .

Os anúncios complementares são definidos dentro de um elemento `<CompanionAds>`. O elemento `<CompanionAds>` pode conter um ou mais elementos `<Companion>`. Cada elemento `<Companion>` descreve um anúncio complementar e pode conter um `<StaticResource>`, `<IFrameResource>`ou `<HTMLResource>` que são especificados da mesma maneira que em um anúncio não linear. Um vasto arquivo pode conter vários anúncios complementares e o aplicativo de player pode escolher o anúncio mais apropriado a ser exibido. Para obter mais informações sobre o vasto, consulte [grande 3,0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Usando um arquivo VMAP (Digital Video Multiple ad playlist)
Um arquivo VMAP permite que você especifique quando as quebras de anúncio ocorrem, quanto tempo cada interrupção é, quantos anúncios podem ser exibidos em um intervalo e quais tipos de anúncios podem ser exibidos durante um intervalo. O seguinte em um arquivo VMAP de exemplo que define um único intervalo de anúncio:

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

Um arquivo VMAP começa com um elemento `<VMAP>` que contém um ou mais elementos `<AdBreak>`, cada um definindo um intervalo de anúncio. Cada quebra de anúncio especifica um tipo de interrupção, ID de interrupção e deslocamento de tempo. O atributo breaktype especifica o tipo de anúncio que pode ser reproduzido durante o intervalo: linear, não linear ou de exibição. Exibir o mapa de anúncios para anúncios de VASTOs Companion. Mais de um tipo de anúncio pode ser especificado em uma lista separada por vírgula (sem espaços). O breakid é um identificador opcional para o anúncio. O timeoffset especifica quando o anúncio deve ser exibido. Ele pode ser especificado de uma das seguintes maneiras:

1. Tempo – em formato hh: mm: SS ou hh: mm: SS. mmm, em que. mmm é milissegundos. O valor desse atributo especifica a hora desde o início da linha do tempo do vídeo até o início do intervalo de anúncio.
2. Porcentagem – em formato n%, em que n é a porcentagem da linha do tempo do vídeo a ser reproduzida antes da reprodução do anúncio
3. Início/fim – especifica que um anúncio deve ser exibido antes ou depois que o vídeo foi exibido
4. Position – especifica a ordem das quebras de anúncio quando o tempo das quebras de anúncio é desconhecido, como na transmissão ao vivo. A ordem de cada intervalo de anúncio é especificada no formato de #n onde n é um inteiro 1 ou maior. 1 significa que o anúncio deve ser reproduzido na primeira oportunidade, 2 significa que o anúncio deve ser reproduzido na segunda oportunidade e assim por diante.

Dentro do elemento `<AdBreak>`, pode haver um < elemento >**AdSource**. O elemento <**AdSource**> contém os seguintes atributos:

1. ID – especifica um identificador para a origem do AD
2. allowMultipleAds – um valor booliano que especifica se vários anúncios podem ser exibidos durante o intervalo de anúncio
3. followRedirects – um valor booliano opcional que especifica se o player de vídeo deve respeitar redirecionamentos dentro de uma resposta de anúncio

O elemento <**AdSource**> fornece ao Player uma resposta de anúncio embutida ou uma referência a uma resposta de anúncio. Ele pode conter um dos seguintes elementos:

* `<VASTAdData>` indica que uma vasta resposta do anúncio está incorporada no arquivo VMAP
* `<AdTagURI>` um URI que faz referência a uma resposta do AD de outro sistema
* `<CustomAdData>`-uma cadeia de caracteres arbitrária que representa uma resposta não vasta

Neste exemplo, uma resposta de anúncio embutida é especificada com um elemento `<VASTAdData>` que contém uma vasta resposta de anúncio. Para obter mais informações sobre os outros elementos, consulte [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

O elemento <**AdBreak**> também pode conter um elemento < > do**TrackingEvents**. O elemento <**TrackingEvents**> permite que você acompanhe o início ou término de um intervalo de anúncio ou se ocorreu um erro durante o intervalo de anúncio. O elemento <**TrackingEvents**> contém um ou mais elementos > de**rastreamento**de <, cada um dos quais especifica um evento de rastreamento e um URI de rastreamento. Os eventos de acompanhamento possíveis são:

1. breakStart – controla o início de um intervalo de anúncio
2. breakEnd – acompanhe a conclusão de um intervalo de anúncio
3. erro – rastreia um erro que ocorreu durante o intervalo de anúncio

O exemplo a seguir mostra um arquivo VMAP que especifica eventos de rastreamento

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

Para obter mais informações sobre o elemento <**TrackingEvents**> e seus filhos, consulte http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Usando um arquivo de modelo de sequenciamento de MAST (Media abstract)
Um arquivo MAST permite que você especifique os gatilhos que definem quando um anúncio é exibido. Veja a seguir um exemplo de arquivo MAST que contém gatilhos para um anúncio anterior, um anúncio mid-roll e um anúncio post-roll.

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


Um arquivo MAST começa com um elemento **Mast** que contém um elemento **triggers** . O elemento `<triggers>` contém um ou mais elementos **Trigger** que definem quando um anúncio deve ser reproduzido.

O elemento **Trigger** contém um elemento **startConditions** que especifica quando um anúncio deve começar a reproduzir. O elemento **startConditions** contém um ou mais elementos `<condition>`. Quando cada `<condition>` é avaliada como true, um gatilho é iniciado ou revogado, dependendo se o `<condition>` está contido em um elemento **startConditions** ou **endconditions** , respectivamente. Quando vários elementos de `<condition>` estão presentes, eles são tratados como um implícito ou qualquer condição avaliada como true fará com que o gatilho seja iniciado. elementos de `<condition>` podem ser aninhados. Quando os elementos de `<condition>` filho são predefinidos, eles são tratados como um implícito e todas as condições devem ser avaliadas como true para que o gatilho seja iniciado. O elemento `<condition>` contém os seguintes atributos que definem a condição:

1. **tipo** – especifica o tipo de condição, evento ou propriedade
2. **nome** – o nome da propriedade ou evento a ser usado durante a avaliação
3. **valor** – o valor em relação ao qual uma propriedade será avaliada
4. **operador** – a operação a ser usada durante a avaliação: EQ (igual), NEQ (não igual), GTR (maior), GEQ (maior ou igual), lt (menor que), Leq (menor que ou igual), mod (módulo)

as **condições** endtambém contêm elementos `<condition>`. Quando uma condição é avaliada como true, o gatilho é redefinido. O elemento `<trigger>` também contém um elemento `<sources>` que contém um ou mais elementos `<source>`. Os elementos `<source>` definem o URI para a resposta do anúncio e o tipo de resposta do anúncio. Neste exemplo, um URI é fornecido a uma vasta resposta.

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

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Usando o player de vídeo-definição de interface do AD (VPAID)
VPAID é uma API para permitir que unidades do AD executáveis se comuniquem com um player de vídeo. Isso permite experiências de anúncios altamente interativas. O usuário pode interagir com o AD e o anúncio pode responder às ações realizadas pelo Visualizador. Por exemplo, um anúncio pode exibir botões que permitem ao usuário exibir mais informações ou uma versão mais longa do anúncio. O player de vídeo deve dar suporte à API VPAID e o AD executável deve implementar a API. Quando um Player solicita um anúncio de um servidor do AD, o servidor pode responder com uma vasta resposta que contém um anúncio do VPAID.

Um anúncio executável é criado no código que deve ser executado em um ambiente de tempo de execução, como Adobe Flash™ ou JavaScript, que pode ser executado em um navegador da Web. Quando um servidor do AD retorna uma vasta resposta contendo um VPAID AD, o valor do atributo apiFramework no elemento `<MediaFile>` deve ser "VPAID". Esse atributo especifica que o AD independente é um VPAID executável do AD. O atributo Type deve ser definido como o tipo MIME do executável, como "application/x-Shockwave-Flash" ou "application/x-JavaScript". O trecho de código XML a seguir mostra o elemento `<MediaFile>` de uma grande resposta que contém um anúncio executável VPAID.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Um anúncio executável pode ser inicializado usando o elemento `<AdParameters>` dentro dos elementos `<Linear>` ou `<NonLinear>` em uma vasta resposta. Para obter mais informações sobre o elemento `<AdParameters>`, consulte [vasta 3,0](https://www.iab.net/media/file/VASTv3.0.pdf). Para obter mais informações sobre a API do VPAID, consulte [VPAID 2,0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementando um player do Windows ou do Windows Phone 8 com suporte do AD
O Microsoft Media Platform: Player Framework para Windows 8 e Windows Phone 8 contém uma coleção de aplicativos de exemplo que mostram como implementar um aplicativo de player de vídeo usando a estrutura. Você pode baixar a estrutura do Player e os exemplos da [estrutura do Player para Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando você abrir a solução Microsoft. PlayerFramework. XAML. Samples, verá um número de pastas dentro do projeto. A pasta de anúncio contém o código de exemplo relevante para a criação de um player de vídeo com suporte ao AD. Dentro da pasta de anúncio há um número de arquivos XAML/cs que mostram como inserir anúncios de forma diferente. A lista a seguir descreve cada um deles:

* AdPodPage. XAML mostra como exibir um pod do AD.
* AdSchedulingPage. XAML mostra como agendar anúncios.
* FreeWheelPage. XAML mostra como usar o plug-in FreeWheel para agendar anúncios.
* MastPage. XAML mostra como agendar anúncios com um arquivo MAST.
* ProgrammaticAdPage. XAML mostra como agendar anúncios programaticamente em um vídeo.
* ScheduleClipPage. XAML mostra como agendar um anúncio sem um vasto arquivo.
* VastLinearCompanionPage. XAML mostra como inserir um anúncio linear e complementar.
* VastNonLinearPage. XAML mostra como inserir um anúncio não linear.
* VmapPage. XAML mostra como especificar anúncios com um arquivo VMAP.

Cada um desses exemplos usa a classe MediaPlayer definida pela estrutura do Player. A maioria dos exemplos usa plug-ins que adicionam suporte para vários formatos de resposta do anúncio. O exemplo ProgrammaticAdPage interage programaticamente com uma instância do MediaPlayer.

### <a name="adpodpage-sample"></a>Exemplo de AdPodPage
Este exemplo usa o AdSchedulerPlugin para definir quando exibir um anúncio. Neste exemplo, um anúncio de distribuição mid está agendado para ser reproduzido após cinco segundos. O Pod do AD (um grupo de anúncios a serem exibidos em ordem) é especificado em um vasto arquivo retornado de um servidor do AD. O URI para o vasto arquivo é especificado no elemento `<RemoteAdSource>`.

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

Para obter mais informações sobre o AdSchedulerPlugin, consulte [publicidade na estrutura do Player no Windows 8 e Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
Este exemplo também usa o AdSchedulerPlugin. Ele agenda três anúncios, um anúncio pré-roll, um anúncio intermediário e um anúncio post-roll. O URI para a VASTAção de cada anúncio é especificado em um elemento `<RemoteAdSource>`.

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
Este exemplo usa o FreeWheelPlugin que especifica um atributo de origem que especifica um URI que aponta para um arquivo Smartxml, que especifica o conteúdo do AD, bem como informações de agendamento do AD.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
Este exemplo usa o MastSchedulerPlugin que permite que você use um arquivo MAST. O atributo de origem especifica o local do arquivo MAST.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Este exemplo interage programaticamente com o MediaPlayer. O arquivo ProgrammaticAdPage. XAML instancia o MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

O arquivo ProgrammaticAdPage.xaml.cs cria um AdHandlerPlugin, adiciona um TimelineMarker para especificar quando um anúncio deve ser exibido e, em seguida, adiciona um manipulador para o evento MarkerReached que carrega um RemoteAdSource especificando um URI para um vasto arquivo e, em seguida, reproduz o anúncio.

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
Este exemplo usa o AdSchedulerPlugin para agendar um anúncio mid-roll especificando um arquivo. wmv que contém o anúncio.

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
Este exemplo ilustra como usar o AdSchedulerPlugin para agendar um anúncio linear de distribuição média com um anúncio complementar. O elemento `<RemoteAdSource>` especifica o local do vasto arquivo.

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
Este exemplo usa o AdSchedulerPlugin para agendar um anúncio linear e não linear. O vasto local de arquivo é especificado com o elemento `<RemoteAdSource>`.

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
Este exemplo usa o VmapSchedulerPlugin para agendar anúncios usando um arquivo VMAP. O URI para o arquivo VMAP é especificado no atributo Source do elemento `<VmapSchedulerPlugin>`.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementando um player de vídeo iOS com suporte ao AD
A Microsoft Media Platform: Player Framework para iOS contém uma coleção de aplicativos de exemplo que mostram como implementar um aplicativo de player de vídeo usando a estrutura. Você pode baixar a estrutura do Player e os exemplos do [player de mídia do Azure Framework](https://github.com/Azure/azure-media-player-framework). A página do GitHub tem um link para um wiki que contém informações adicionais sobre a estrutura do Player e uma introdução ao exemplo do Player: [player de mídia do Azure wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Agendando anúncios com o VMAP
O exemplo a seguir mostra como agendar anúncios usando um arquivo VMAP.

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

### <a name="scheduling-ads-with-vast"></a>Agendando anúncios com vasto
O exemplo a seguir mostra como agendar um anúncio vasto de associação tardia.


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

   O exemplo a seguir mostra como agendar um anúncio de primeira ligação antecipada.

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

O exemplo a seguir mostra como inserir um anúncio usando a RCE (edição de corte áspero)

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

O exemplo a seguir mostra como agendar um pod do AD.

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

O exemplo a seguir mostra como agendar um anúncio intermediário não adesivo. Um anúncio não adesivo é reproduzido apenas uma vez, independentemente de qualquer busca realizada pelo Visualizador.

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

O exemplo a seguir mostra como agendar um anúncio de acúmulo intermediário. Um anúncio adesivo é exibido toda vez que o ponto especificado na linha do tempo do vídeo é atingido.

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

O exemplo a seguir mostra como agendar um anúncio post-roll.

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

O exemplo a seguir mostra como agendar um anúncio pré-roll.

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

O exemplo a seguir mostra como agendar um anúncio de sobreposição de distribuição média.

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

## <a name="see-also"></a>Consulte também
[Desenvolver aplicações de leitor de vídeo](media-services-develop-video-players.md)

