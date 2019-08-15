---
title: Vários arquivos de entrada e propriedades de componente com o codificador Premium – Azure | Microsoft Docs
description: Este tópico explica como usar setruntimeproperties para usar vários arquivos de entrada e passar dados personalizados para o processador de mídia Media Encoder Premium Workflow.
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
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015439"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Usando vários arquivos de entrada e propriedades de componente com o codificador Premium
## <a name="overview"></a>Descrição geral
Há cenários em que talvez seja necessário personalizar as propriedades do componente, especificar o conteúdo XML da lista de clipes ou enviar vários arquivos de entrada ao enviar uma tarefa com o processador de mídia **Media Encoder Premium Workflow** . Alguns exemplos incluem:

* Sobreposição de texto em vídeo e definição do valor de texto (por exemplo, a data atual) em tempo de execução para cada vídeo de entrada.
* Personalização do XML da lista de clipes (para especificar um ou vários arquivos de origem, com ou sem corte, etc.).
* Sobreposição de uma imagem de logotipo no vídeo de entrada enquanto o vídeo é codificado.
* Codificação de vários idiomas de áudio.

Para permitir que o **Media Encoder Premium Workflow** saiba que você está alterando algumas propriedades no fluxo de trabalho quando você cria a tarefa ou envia vários arquivos de entrada, você precisa usar uma cadeia de caracteres de configuração que contenha setruntimeproperties e/ou  **transcodificação**. Este tópico explica como usá-los.

## <a name="configuration-string-syntax"></a>Sintaxe da cadeia de caracteres de configuração
A cadeia de caracteres de configuração a ser definida na tarefa de codificação usa um documento XML parecido com este:

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

Este é o C# código que lê a configuração XML de um arquivo, atualiza-o com o nome de arquivo de vídeo correto e o transmite para a tarefa em um trabalho:

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

## <a name="customizing-component-properties"></a>Personalizando Propriedades do componente
### <a name="property-with-a-simple-value"></a>Propriedade com um valor simples
Em alguns casos, é útil personalizar uma propriedade de componente junto com o arquivo de fluxo de trabalho que será executado por Media Encoder Premium Workflow.

Suponha que você tenha criado um fluxo de trabalho que sobreponha texto em seus vídeos e que o texto (por exemplo, a data atual) deva ser definido em tempo de execução. Você pode fazer isso enviando o texto a ser definido como o novo valor para a propriedade Text do componente de sobreposição da tarefa de codificação. Você pode usar esse mecanismo para alterar outras propriedades de um componente no fluxo de trabalho (como a posição ou a cor da sobreposição, a taxa de bits do codificador AVC, etc.).

setruntimeproperties é usado para substituir uma propriedade nos componentes do fluxo de trabalho.

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

### <a name="property-with-an-xml-value"></a>Propriedade com um valor XML
Para definir uma propriedade que espera um valor XML, encapsulado usando `<![CDATA[ and ]]>`.

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
> Certifique-se de não colocar um retorno de carro `<![CDATA[`logo após.

### <a name="propertypath-value"></a>valor de propertyPath
Nos exemplos anteriores, propertyPath era "/Media File Input/filename" ou "/inactiveTimeout" ou "clipListXml".
Isso é, em geral, o nome do componente e o nome da propriedade. O caminho pode ter mais ou menos níveis, como "/primarySourceFile" (porque a propriedade está na raiz do fluxo de trabalho) ou "/video de processamento/sobreposição de gráfico/opacidade" (porque a sobreposição está em um grupo).    

Para verificar o caminho e o nome da propriedade, use o botão de ação que está imediatamente ao lado de cada propriedade. Você pode clicar nesse botão de ação e selecionar **Editar**. Isso mostrará o nome real da propriedade e imediatamente acima dela, o namespace.

![Ação/editar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Vários arquivos de entrada
Cada tarefa que você envia para a **Media Encoder Premium Workflow** requer dois ativos:

* O primeiro é um *ativo de fluxo de trabalho* que contém um arquivo de fluxo de trabalho. Você pode criar arquivos de fluxo de trabalho usando o [Designer de fluxo de trabalho](media-services-workflow-designer.md).
* O segundo é um *ativo de mídia* que contém os arquivos de mídia que você deseja codificar.

Quando você está enviando vários arquivos de mídia para o codificador **Media Encoder Premium Workflow** , as seguintes restrições se aplicam:

* Todos os arquivos de mídia devem estar no mesmo *ativo de mídia*. Não há suporte para o uso de vários ativos de mídia.
* Você deve definir o arquivo primário nesse ativo de mídia (idealmente, esse é o arquivo de vídeo principal que o codificador é solicitado a processar).
* É necessário passar os dados de configuração que incluem o elemento setruntimeproperties e/ou transcodification para o processador.
  * setruntimeproperties é usado para substituir a propriedade filename ou outra propriedade nos componentes do fluxo de trabalho.
  * o transcodificator é usado para especificar o conteúdo XML da lista de clipes.

Conexões no fluxo de trabalho:

* Se você usar um ou vários componentes de entrada de arquivo de mídia e planejar usar setruntimeproperties para especificar o nome do arquivo, não conecte o PIN do componente de arquivo primário a eles. Verifique se não há nenhuma conexão entre o objeto de arquivo primário e as entradas do arquivo de mídia.
* Se preferir usar XML de lista de clipes e um componente de origem de mídia, você poderá conectar-se juntos.

![Nenhuma conexão do arquivo de origem primário para a entrada do arquivo de mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Não haverá conexão do arquivo primário para os componentes de entrada do arquivo de mídia se você usar setruntimeproperties para definir a propriedade FileName.*

![Conexão do XML da lista de clipes à origem da lista de clipes](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Você pode conectar o XML da lista de clipes à origem de mídia e usar o transcodificador.*

### <a name="clip-list-xml-customization"></a>Personalização XML da lista de clipes
Você pode especificar o XML da lista de clipes no fluxo de trabalho em tempo de execução usando transcodification no XML da cadeia de caracteres de configuração. Isso requer que o pino XML da lista de clipes seja conectado ao componente de origem de mídia no fluxo de trabalho.

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

Se você quiser especificar/primarySourceFile para usar essa propriedade para nomear os arquivos de saída usando ' expressões ', é recomendável passar o XML da lista de clipes como uma propriedade *após* a propriedade/primarySourceFile, para evitar que a lista de clipes seja substituída por a configuração/primarySourceFile.

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

Com a remoção precisa de quadro adicional:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exemplo 1: Sobrepor uma imagem na parte superior do vídeo

### <a name="presentation"></a>Apresentação
Considere um exemplo no qual você deseja sobrepor uma imagem de logotipo no vídeo de entrada enquanto o vídeo é codificado. Neste exemplo, o vídeo de entrada é denominado "Microsoft_HoloLens_Possibilities_816p24. mp4" e o logotipo é denominado "logo. png". Você deve executar as seguintes etapas:

* Crie um ativo de fluxo de trabalho com o arquivo de fluxo de trabalho (consulte o exemplo a seguir).
* Crie um ativo de mídia que contenha dois arquivos: MyInputVideo. mp4 como o arquivo primário e MYLOGO. png.
* Envie uma tarefa para o processador de mídia Media Encoder Premium Workflow com os ativos de entrada acima e especifique a seguinte cadeia de caracteres de configuração.

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

No exemplo acima, o nome do arquivo de vídeo é enviado para o componente de entrada do arquivo de mídia e a propriedade primarySourceFile. O nome do arquivo de logotipo é enviado para outra entrada de arquivo de mídia que está conectada ao componente de sobreposição de gráfico.

> [!NOTE]
> O nome do arquivo de vídeo é enviado para a propriedade primarySourceFile. O motivo disso é usar essa propriedade no fluxo de trabalho para criar o nome de arquivo de saída correto usando expressões, por exemplo.

### <a name="step-by-step-workflow-creation"></a>Criação de fluxo de trabalho passo a passo
Aqui estão as etapas para criar um fluxo de trabalho que usa dois arquivos como entrada: um vídeo e uma imagem. Ele irá sobrepor a imagem na parte superior do vídeo.

Abra **Designer de fluxo de trabalho** e selecione **arquivo** > **novo espaço de trabalho** > **plano gráfico transcodificar**.

O novo fluxo de trabalho mostra três elementos:

* Arquivo de origem primário
* XML da lista de clipes
* Arquivo/ativo de saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho de codificação*

Para aceitar o arquivo de mídia de entrada, comece com a adição de um componente de entrada de arquivo de mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do repositório e arraste a entrada desejada para o painel designer.

Em seguida, adicione o arquivo de vídeo a ser usado para criar seu fluxo de trabalho. Para fazer isso, clique no painel plano de fundo em Designer de Fluxo de Trabalho e procure a propriedade arquivo de origem principal no painel de propriedades à direita. Clique no ícone de pasta e selecione o arquivo de vídeo apropriado.

![Origem do arquivo primário](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Origem do arquivo primário*

Em seguida, especifique o arquivo de vídeo no componente de entrada do arquivo de mídia.   

![Fonte de entrada do arquivo de mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Fonte de entrada do arquivo de mídia*

Assim que isso for feito, o componente de entrada do arquivo de mídia inspecionará o arquivo e preencherá seus PINs de saída para refletir o arquivo que ele inspecionou.

A próxima etapa é adicionar um "Atualizador de tipo de dados de vídeo" para especificar o espaço de cor para rec. 709. Adicione um "conversor de formato de vídeo" definido como layout de dados/tipo de layout = planar configurável. Isso converterá o fluxo de vídeo em um formato que pode ser usado como uma fonte do componente de sobreposição.

![Atualizador de tipo de dados de vídeo e conversor de formato](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Atualizador de tipo de dados de vídeo e conversor de formato*

![Tipo de layout = planar configurável](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*O tipo de layout é planar configurável*

Em seguida, adicione um componente de sobreposição de vídeo e conecte o pino de vídeo (descompactado) ao pino de vídeo (descompactado) da entrada do arquivo de mídia.

Adicione outra entrada de arquivo de mídia (para carregar o arquivo de logotipo), clique nesse componente e renomeie-o como "logotipo de entrada do arquivo de mídia" e selecione uma imagem (um arquivo. png, por exemplo) na propriedade arquivo. Conecte o pino da imagem descompactada ao pino da imagem descompactada da sobreposição.

![Origem do arquivo de imagem e componente de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Origem do arquivo de imagem e componente de sobreposição*

Se você quiser modificar a posição do logotipo no vídeo (por exemplo, você pode desejar posicioná-lo em 10% do canto superior esquerdo do vídeo), desmarque a caixa de seleção "entrada manual". Você pode fazer isso porque está usando uma entrada de arquivo de mídia para fornecer o arquivo de logotipo para o componente de sobreposição.

![Posição da sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição da sobreposição*

Para codificar a transmissão de vídeo para H. 264, adicione o codificador de vídeo AVC e os componentes do codificador AAC à superfície do designer. Conecte os Pins.
Configure o codificador AAC e selecione conversão/predefinição de formato de áudio: 2,0 (L, R).

![Codificadores de áudio e vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de áudio e vídeo*

Agora, adicione o **multiplexador ISO MPEG-4** e os componentes de **saída de arquivo** e conecte os Pins conforme mostrado.

![Multiplexador MP4 e saída de arquivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplexador MP4 e saída de arquivo*

Você precisa definir o nome para o arquivo de saída. Clique no componente **saída de arquivo** e edite a expressão para o arquivo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome da saída do arquivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome da saída do arquivo*

Você pode executar o fluxo de trabalho localmente para verificar se ele está sendo executado corretamente.

Após a conclusão, você pode executá-lo nos serviços de mídia do Azure.

Primeiro, prepare um ativo nos serviços de mídia do Azure com dois arquivos: o arquivo de vídeo e o logotipo. Você pode fazer isso usando a API REST ou .NET. Você também pode fazer isso usando o portal do Azure ou o AMSE ( [Gerenciador de serviços de mídia) do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) .

Este tutorial mostra como gerenciar ativos com o AMSE. Há duas maneiras de adicionar arquivos a um ativo:

* Crie uma pasta local, copie os dois arquivos nela e arraste e solte a pasta para a guia **ativo** .
* Carregue o arquivo de vídeo como um ativo, exiba as informações do ativo, vá para a guia arquivos e carregue um arquivo adicional (logotipo).

> [!NOTE]
> Certifique-se de definir um arquivo primário no ativo (o arquivo de vídeo principal).

![Arquivos de ativo no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Arquivos de ativo no AMSE*

Selecione o ativo e escolha codificá-lo com o codificador Premium. Carregue o fluxo de trabalho e selecione-o.

Clique no botão para passar dados para o processador e adicione o seguinte XML para definir as propriedades de tempo de execução:

![Codificador Premium no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium no AMSE*

Em seguida, Cole os dados XML a seguir. Você precisa especificar o nome do arquivo de vídeo para a entrada do arquivo de mídia e primarySourceFile. Especifique também o nome do nome do arquivo para o logotipo.

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

Se você usar o SDK do .NET para criar e executar a tarefa, esses dados XML precisarão ser passados como a cadeia de caracteres de configuração.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Depois que o trabalho for concluído, o arquivo MP4 no ativo de saída exibirá a sobreposição!

![Sobreposição no vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição no vídeo*

Você pode baixar o fluxo de trabalho de exemplo do [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exemplo 2: Codificação de vários idiomas de áudio

Um exemplo de fluxo de trabalho de codificação de vários idiomas de áudio está disponível no [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Esta pasta contém um fluxo de trabalho de exemplo que pode ser usado para codificar um arquivo MXF para um ativo de arquivos MP4 múltiplos com várias faixas de áudio.

Este fluxo de trabalho pressupõe que o arquivo MXF contém uma faixa de áudio; as faixas de áudio adicionais devem ser passadas como arquivos de áudio separados (WAV ou MP4...).

Para codificar, siga estas etapas:

* Crie um ativo dos serviços de mídia com o arquivo MXF e os arquivos de áudio (de 0 a 18 arquivos de áudio).
* Verifique se o arquivo MXF está definido como um arquivo primário.
* Crie um trabalho e uma tarefa usando o processador do codificador de fluxo de trabalhos Premium. Use o fluxo de trabalho fornecido (MultiMP4-1080p-19audio-v1. Workflow).
* Passe os dados de setruntime. xml para a tarefa (se você usar o Gerenciador de serviços de mídia do Azure, use o botão "transmitir dados XML para o fluxo de trabalho").
  * Atualize os dados XML para especificar as marcas de idiomas e nomes de arquivo corretos.
  * O fluxo de trabalho tem componentes de áudio chamados áudio 1 para áudio 18.
  * RFC5646 tem suporte para a marca de idioma.

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

* O ativo codificado conterá faixas de áudio de vários idiomas e essas faixas devem ser selecionáveis no Player de Mídia do Azure.

## <a name="see-also"></a>Consulte também
* [Introdução à codificação Premium nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Como usar a codificação Premium nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Codificando conteúdo sob demanda com os serviços de mídia do Azure](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow formats and codecs](media-services-premium-workflow-encoder-formats.md) (Formatos e codecs do Media Encoder Premium Workflow)
* [Arquivos de fluxo de trabalho de exemplo](https://github.com/Azure/azure-media-services-samples)
* [Ferramenta Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
