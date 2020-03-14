---
title: Múltiplos ficheiros de entrada e propriedades componentes com Premium Encoder - Azure / Microsoft Docs
description: Este tópico explica como usar o setRuntimeProperties para usar vários ficheiros de entrada e passar dados personalizados para o processador de mídia Encoder Premium Workflow.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.openlocfilehash: 27bdf82d4515678e28eadf07fe325860fe5df063
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251001"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Utilização de vários ficheiros de entrada e propriedades componentes com Premium Encoder
## <a name="overview"></a>Descrição geral
Existem cenários em que poderá ser necessário personalizar as propriedades dos componentes, especificar o conteúdo da Clip List XML ou enviar vários ficheiros de entrada quando submeter uma tarefa com o processador de mídia **Encoder Premium Workflow.** Alguns exemplos incluem:

* Sobreposição de texto em vídeo e definição do valor do texto (por exemplo, a data atual) no tempo de execução para cada vídeo de entrada.
* Personalização da Lista de Clipes XML (para especificar um ou vários ficheiros de origem, com ou sem aparar, etc.).
* Sobrepondo uma imagem de logotipo no vídeo de entrada enquanto o vídeo é codificado.
* Várias codificações de linguagem áudio.

Para que o **Media Encoder Premium Workflow** saiba que está a alterar algumas propriedades no fluxo de trabalho quando cria a tarefa ou envia vários ficheiros de entrada, tem de utilizar uma cadeia de configuração que contenha **setRuntimeProperties** e/ou **transcodeSource**. Este tópico explica como usá-los.

## <a name="configuration-string-syntax"></a>Sintaxe de cordas de configuração
A cadeia de configuração para definir na tarefa de codificação utiliza um documento XML que se parece com este:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Segue-se C# o código que lê a configuração XML a partir de um ficheiro, atualizá-lo com o nome de ficheiro de vídeo certo e passa-o para a tarefa num trabalho:

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Personalizando propriedades de componentes
### <a name="property-with-a-simple-value"></a>Imóvel com um valor simples
Em alguns casos, é útil personalizar uma propriedade componente juntamente com o ficheiro workflow que vai ser executado pela Media Encoder Premium Workflow.

Suponha que tenha projetado um fluxo de trabalho que sobreponha texto nos seus vídeos, e o texto (por exemplo, a data atual) deveria ser definido no tempo de execução. Pode fazê-lo enviando o texto para ser definido como o novo valor para a propriedade de texto do componente sobreposto da tarefa de codificação. Pode utilizar este mecanismo para alterar outras propriedades de um componente no fluxo de trabalho (como a posição ou a cor da sobreposição, a bitrate do codificador AVC, etc.).

**setRuntimeProperties** é usado para sobrepor uma propriedade nos componentes do fluxo de trabalho.

Exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Imóvel com valor XML
Para definir uma propriedade que espera um valor XML, encapsular usando `<![CDATA[ and ]]>`.

Exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Certifique-se de que não volta a carruagem logo após `<![CDATA[`.

### <a name="propertypath-value"></a>propriedadeValorcaminho
Nos exemplos anteriores, o propertyPath foi "/Media File Input/filename" ou "/inactiveTimeout" ou "clipListXml".
Este é, em geral, o nome do componente, então o nome da propriedade. O caminho pode ter mais ou menos níveis, como "/primarySourceFile" (porque a propriedade está na raiz do fluxo de trabalho) ou "/Processamento de Vídeo/Sobreposição Gráfica/Opacidade" (porque a sobreposição está em um grupo).    

Para verificar o caminho e o nome da propriedade, utilize o botão de ação que está imediatamente ao lado de cada propriedade. Pode clicar neste botão de ação e selecionar **Editar**. Isto mostrar-lhe-á o nome real da propriedade, e imediatamente acima dela, o espaço de nome.

![Ação/Edição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Vários ficheiros de entrada
Cada tarefa que submete ao Fluxo de Trabalho Premium Media **Encoder** requer dois ativos:

* O primeiro é um *Ativo workflow* que contém um ficheiro de fluxo de trabalho. Pode conceber ficheiros de fluxo de trabalho utilizando o [Workflow Designer](media-services-workflow-designer.md).
* O segundo é um *Media Asset* que contém os ficheiros de mídia que pretende codificar.

Quando está a enviar vários ficheiros de mídia para o codificador de fluxo de **trabalho media encoder Premium,** aplicam-se os seguintes constrangimentos:

* Todos os ficheiros dos meios de comunicação devem estar no mesmo *Media Asset.* A utilização de vários Meios de Comunicação Social não é suportada.
* Tem de definir o ficheiro principal neste Media Asset (idealmente, este é o ficheiro de vídeo principal que o codificador é solicitado a processar).
* É necessário passar dados de configuração que incluam o **conjuntoRuntimeProperties** e/ou **elemento transcodeSource** para o processador.
  * **setRuntimeProperties** é usado para sobrepor a propriedade de nome de ficheiro ou outra propriedade nos componentes do fluxo de trabalho.
  * **transcodeSource** é usado para especificar o conteúdo Da Lista de Clipe XML.

Ligações no fluxo de trabalho:

* Se utilizar um ou vários componentes de entrada de ficheiros de mídia e planeia utilizar **o setRuntimeProperties** para especificar o nome do ficheiro, não conecte o pino do componente de ficheiro primário aos mesmos. Certifique-se de que não existe qualquer ligação entre o objeto de ficheiro primário e a entrada de ficheiros de mídia.
* Se preferir utilizar a Clip List XML e um componente Media Source, pode ligar os dois.

![Nenhuma ligação do Ficheiro Fonte Primária à Entrada de Ficheiros de Mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Não existe nenhuma ligação desde o ficheiro primário até ao componente de entrada de ficheiros de mídia se utilizar o setRuntimeProperties para definir a propriedade de nome de ficheiro.*

![Ligação da Lista de Clipes XML à Fonte da Lista de Clipes](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Pode ligar a Lista de Clipes XML a Media Source e utilizar o transcodeSource.*

### <a name="clip-list-xml-customization"></a>Personalização Da Lista de Clipes XML
Pode especificar a Lista de Clipe XML no fluxo de trabalho em tempo de execução utilizando **o transcodeSource** na cadeia de configuração XML. Isto requer que o pino Clip List XML seja ligado ao componente Media Source no fluxo de trabalho.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Se pretender especificar/primarySourceFile para utilizar esta propriedade para nomear os ficheiros de saída utilizando 'Expressões', recomendamos que passe a Lista de Clip E xML como uma propriedade *após* a propriedade /primarySourceFile, para evitar que a Lista de Clipes seja ultrapassada pela definição /primarySourceFile.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Com aparas adicionais precisas de quadros:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exemplo 1: Sobreponha uma imagem em cima do vídeo

### <a name="presentation"></a>Apresentação
Considere um exemplo no qual pretende sobrepor uma imagem de logotipo no vídeo de entrada enquanto o vídeo está codificado. Neste exemplo, o vídeo de entrada chama-se "Microsoft_HoloLens_Possibilities_816p24.mp4" e o logótipo chama-se "logo.png". Deve executar os seguintes passos:

* Crie um Ativo workflow com o ficheiro workflow (ver o seguinte exemplo).
* Crie um Media Asset, que contém dois ficheiros: MyInputVideo.mp4 como ficheiro primário e MyLogo.png.
* Envie uma tarefa ao processador de mídia Encoder Premium Workflow com os ativos de entrada acima e especifique a seguinte cadeia de configuração.

Configuração:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

No exemplo acima, o nome do ficheiro de vídeo é enviado para o componente de entrada de ficheiros de mídia e para a propriedade principalSourceFile. O nome do ficheiro do logotipo é enviado para outra entrada de ficheiro sinuoso que está ligada ao componente de sobreposição gráfica.

> [!NOTE]
> O nome do ficheiro de vídeo é enviado para a propriedade principalSourceFile. A razão para isso é usar esta propriedade no fluxo de trabalho para construir o nome de ficheiro de saída correto usando Expresss, por exemplo.

### <a name="step-by-step-workflow-creation"></a>Criação passo a passo do fluxo de trabalho
Aqui estão os passos para criar um fluxo de trabalho que leva dois ficheiros como entrada: um vídeo e uma imagem. Sobrepor-se-á à imagem em cima do vídeo.

Open **Workflow Designer** e selecione **File** > **New Workspace** > **Transcode Blueprint**.

O novo fluxo de trabalho mostra três elementos:

* Arquivo fonte primário
* Lista de Clipes XML
* Ficheiro/Ativo de Saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho de codificação*

Para aceitar o ficheiro de entrada, comece por adicionar um componente de entrada de ficheiro sinuoso. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa repositório e arraste a entrada desejada no painel do designer.

Em seguida, adicione o ficheiro de vídeo a utilizar para conceber o seu fluxo de trabalho. Para isso, clique no painel de fundo do Workflow Designer e procure a propriedade Primary Source File no painel de propriedade da mão direita. Clique no ícone da pasta e selecione o ficheiro de vídeo apropriado.

![Fonte de arquivo primário](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Fonte de arquivo primário*

Em seguida, especifique o ficheiro de vídeo no componente de entrada de ficheiros de mídia.   

![Fonte de entrada de ficheiros de mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Fonte de entrada de ficheiros de mídia*

Assim que isto for feito, o componente de entrada de ficheiros de mídia irá inspecionar o ficheiro e povoar os seus pinos de saída para refletir o ficheiro que inspecionou.

O próximo passo é adicionar um "Atualização do Tipo de Dados de Vídeo" para especificar o espaço de cores para Rec.709. Adicione um "Conversor de Formato de Vídeo" que é definido para o tipo de layout de dados/layout = Planar configurável. Isto converterá o fluxo de vídeo para um formato que pode ser tomado como uma fonte do componente sobreposto.

![atualizador de tipo de dados de vídeo e conversor de formato](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Atualizador de tipo de dados de vídeo e conversor de formato*

![Tipo de layout = Planar Configurável](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo de layout é Planar Configurável*

Em seguida, adicione um componente de sobreposição de vídeo e ligue o pino de vídeo (descomprimido) ao pino de vídeo (descomprimido) da entrada do ficheiro de mídia.

Adicione outra Entrada de Ficheiros de Mídia (para carregar o ficheiro do logotipo), clique neste componente e mude o nome para "Media File Input Logo", e selecione uma imagem (um ficheiro .png, por exemplo) na propriedade do ficheiro. Ligue o pino de imagem não comprimido ao pino de imagem descomprimido da sobreposição.

![Sobreposição de componente e fonte de ficheiro de imagem](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Sobreposição de componente e fonte de ficheiro de imagem*

Se quiser modificar a posição do logótipo no vídeo (por exemplo, é melhor posicioná-lo a 10% do canto superior esquerdo do vídeo), limpe a caixa de verificação "Manual Input". Pode fazê-lo porque está a utilizar uma Entrada de Ficheiros de Mídia para fornecer o ficheiro de logotipo ao componente de sobreposição.

![Posição de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição de sobreposição*

Para codificar o fluxo de vídeo para H.264, adicione os componentes codificadores de vídeo AVC e AAC à superfície do designer. Ligue os pinos.
Configurar o codificador AAC e selecionar conversão/predefinição do formato áudio: 2.0 (L, R).

![Encoders de áudio e vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Encoders de áudio e vídeo*

Adicione agora os componentes **iso Mpeg-4 Multiplexer** e **File Output** e ligue os pinos como mostrado.

![MP4 multiplexer e saída de ficheiro](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer e saída de ficheiro*

Tens de definir o nome para o ficheiro de saída. Clique no componente de saída de **ficheiros** e edite a expressão para o ficheiro:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome de saída de ficheiros](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome de saída de ficheiros*

Pode executar o fluxo de trabalho localmente para verificar se está a funcionar corretamente.

Depois de terminar, pode executá-lo na Azure Media Services.

Em primeiro lugar, prepare um ativo na Azure Media Services com dois ficheiros: o ficheiro de vídeo e o logótipo. Pode fazê-lo utilizando a API .NET ou REST. Também pode fazê-lo utilizando o portal Azure ou o [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Este tutorial mostra-lhe como gerir os ativos com a AMSE. Existem duas formas de adicionar ficheiros a um ativo:

* Crie uma pasta local, copie os dois ficheiros e arraste e deixe cair a pasta no separador **'Activo'.**
* Faça upload do ficheiro de vídeo como um ativo, exiba as informações do ativo, vá ao separador de ficheiros e carregue um ficheiro adicional (logotipo).

> [!NOTE]
> Certifique-se de que define um ficheiro primário no ativo (o ficheiro principal de vídeo).

![Ficheiros de ativos na AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Ficheiros de ativos na AMSE*

Selecione o ativo e opte por codificá-lo com O Codificador Premium. Faça upload do fluxo de trabalho e selecione-o.

Clique no botão para passar dados para o processador e adicione o seguinte XML para definir as propriedades do tempo de execução:

![Premium Encoder na AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium Encoder na AMSE*

Em seguida, colar os seguintes dados XML. Tem de especificar o nome do ficheiro de vídeo tanto para a entrada de ficheiros de mídia como para o PrincipalSourceFile. Especifique o nome do nome do ficheiro para o logotipo também.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Se utilizar o SDK .NET para criar e executar a tarefa, estes dados XML têm de ser passados como a cadeia de configuração.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Após a conclusão do trabalho, o ficheiro MP4 no ativo de saída exibe a sobreposição!

![Sobreposição no vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição no vídeo*

Você pode baixar o fluxo de trabalho da amostra do [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exemplo 2 : Codificação múltipla de linguagem áudio

Um exemplo de múltiplos fluxos de trabalho de codificação de linguagem áudio está disponível no [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Esta pasta contém um fluxo de trabalho de amostra que pode ser usado para codificar um ficheiro MXF a um ativo de ficheiros multiMP4 com múltiplas faixas de áudio.

Este fluxo de trabalho pressupõe que o ficheiro MXF contém uma faixa de áudio; as faixas de áudio adicionais devem ser passadas como ficheiros áudio separados (WAV ou MP4...).

Para codificar, siga estes passos:

* Criar um ativo de Media Services com o ficheiro MXF e os ficheiros Áudio (0 a 18 ficheiros áudio).
* Certifique-se de que o ficheiro MXF está definido como um ficheiro primário.
* Criar um trabalho e uma tarefa utilizando o processador Premium Workflow Encoder. Utilize o fluxo de trabalho fornecido (MultiMP4-1080p-19audio-v1.workflow).
* Passe os dados setruntime.xml para a tarefa (se utilizar o Azure Media Services Explorer, utilize o botão "passar dados xml para o fluxo de trabalho").
  * Por favor, atualize os dados do XML para especificar os nomes de ficheiros e etiquetas de idiomas corretos.
  * O fluxo de trabalho tem componentes áudio chamados Áudio 1 a Áudio 18.
  * O RFC5646 é suportado para a etiqueta linguística.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* O ativo codificado conterá faixas áudio multilingidiomas e estas faixas devem ser selecionáveis no Azure Media Player.

## <a name="see-also"></a>Consulte também
* [Introdução de Codificação Premium nos Serviços De Mídia Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Como utilizar a Codificação Premium nos Serviços De Mídia Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Codificação de conteúdos a pedido com a Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow formats and codecs](media-services-premium-workflow-encoder-formats.md) (Formatos e codecs do Media Encoder Premium Workflow)
* [Ficheiros de fluxo de trabalho de amostra](https://github.com/Azure/azure-media-services-samples)
* [Ferramenta Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
