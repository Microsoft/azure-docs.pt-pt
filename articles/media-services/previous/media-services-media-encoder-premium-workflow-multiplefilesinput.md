---
title: Vários ficheiros de entrada e propriedades do componente com o codificador Premium - Azure | Documentos da Microsoft
description: Este tópico explica como utilizar setRuntimeProperties para utilizar vários ficheiros de entrada e passar dados personalizados para o processador de multimédia do Media Encoder Premium Workflow.
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
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 608ca4bc3b58dd3c718d6239f90260154d2f6c3a
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189483"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Utilizar vários ficheiros de entrada e propriedades do componente com o codificador Premium
## <a name="overview"></a>Descrição geral
Existem cenários em que poderá ter de personalizar as propriedades de componente, especifique o conteúdo do Clip lista XML, ou envie vários ficheiros de entrada ao submeter uma tarefa com o **Media Encoder Premium Workflow** processador de multimédia. Alguns exemplos incluem:

* Sobrepor o texto em vídeo e definindo o valor de texto (por exemplo, a data atual) em tempo de execução para cada vídeo de entrada.
* Personalizando o XML de lista de Clip (para especificar um ou vários ficheiros de origem, com ou sem limitação, etc.).
* Sobreposição de uma imagem de logótipo no vídeo de entrada enquanto o vídeo está codificado.
* Vários idiomas de áudio codificação.

Para permitir que o **Media Encoder Premium Workflow** sabe que está a alterar algumas propriedades no fluxo de trabalho ao criar a tarefa ou enviar vários ficheiros de entrada, precisa usar uma cadeia de configuração que contém  **setRuntimeProperties** e/ou **transcodeSource**. Este tópico explica como usá-los.

## <a name="configuration-string-syntax"></a>Sintaxe da cadeia de configuração
A cadeia de configuração para definir a tarefa de codificação usa um documento XML que é semelhante a este:

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

Segue-se a C# código que lê a configuração XML de um arquivo, atualize-o com o nome de ficheiro de vídeo à direita e o transmite para a tarefa numa tarefa:

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

## <a name="customizing-component-properties"></a>Personalizar propriedades do componente
### <a name="property-with-a-simple-value"></a>Propriedade com um valor simples
Em alguns casos, é útil personalizar uma propriedade de componente, juntamente com o ficheiro de fluxo de trabalho que vai ser executado por Media Encoder Premium Workflow.

Suponha que criado um fluxo de trabalho esse texto sobreposições em seus vídeos e o texto (por exemplo, a data atual) deve ser definido em tempo de execução. Pode fazê-lo ao enviar o texto a ser definido como o novo valor para a propriedade text do componente de sobreposição entre a tarefa de codificação. Pode usar esse mecanismo para alterar outras propriedades de um componente no fluxo de trabalho (por exemplo, a posição ou cor de sobreposição, a velocidade de transmissão do codificador de AVC, etc.).

**setRuntimeProperties** é utilizada para substituir uma propriedade nos componentes do fluxo de trabalho.

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
Para definir uma propriedade que espera um valor XML, encapsular utilizando `<![CDATA[ and ]]>`.

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
> Lembre-se de que não colocar um carro retorno imediatamente após `<![CDATA[`.

### <a name="propertypath-value"></a>valor de propertyPath
Nos exemplos anteriores, foi o propertyPath "/ ficheiro do suporte de dados de entrada/nome de ficheiro" ou "/ inactiveTimeout" ou "clipListXml".
Isto é, em geral, o nome do componente, em seguida, o nome da propriedade. O caminho pode ter mais ou menos níveis, como "/ primarySourceFile" (porque a propriedade está na raiz do fluxo de trabalho) ou "/ vídeo sobreposição/opacidade de processamento/gráfico" (porque é a sobreposição de um grupo).    

Para verificar o nome de caminho e a propriedade, utilize o botão de ação que é imediatamente ao lado de cada propriedade. Pode clicar neste botão de ação e selecione **editar**. Isto irá mostrar-lhe o nome real da propriedade e, imediatamente acima dela, o espaço de nomes.

![Ação/editar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Vários ficheiros de entrada
Cada tarefa que tenha enviado para o **Media Encoder Premium Workflow** requer dois recursos:

* A primeira é uma *Asset de fluxo de trabalho* que contém um ficheiro de fluxo de trabalho. Pode criar arquivos de fluxo de trabalho utilizando o [Designer de fluxo de trabalho](media-services-workflow-designer.md).
* A segunda é um *elemento multimédia* que contém os ficheiros de suporte de dados que pretende codificar.

Quando estiver enviando vários arquivos de suporte de dados para o **Media Encoder Premium Workflow** aplicam do codificador, as seguintes restrições:

* Todos os ficheiros de suporte de dados tem de estar na mesma *elemento multimédia*. Usar vários recursos de suporte de dados não é suportada.
* Tem de definir o ficheiro principal deste elemento de multimédia (Idealmente, este é o principal arquivo de vídeo que o codificador é-lhe pedido para processar).
* É necessário passar dados de configuração que incluem a **setRuntimeProperties** e/ou **transcodeSource** elemento para o processador.
  * **setRuntimeProperties** é utilizada para substituir a propriedade de nome de ficheiro ou outra propriedade nos componentes do fluxo de trabalho.
  * **transcodeSource** é utilizado para especificar o conteúdo XML da lista de Clip.

Ligações no fluxo de trabalho:

* Se utilizar um ou vários componentes de entrada de arquivo de mídia e planeja usar **setRuntimeProperties** para especificar o nome de ficheiro, em seguida, não ligar o pin de componente do ficheiro primário aos mesmos. Certifique-se de que não existe nenhuma ligação entre o objeto de ficheiro primário e o Input(s) de ficheiro do suporte de dados.
* Se preferir usar o XML de lista de Clip e um componente de origem de mídia, em seguida, pode ligar os dois em conjunto.

![Nenhuma ligação a partir do ficheiro de origem principal à entrada de ficheiro do suporte de dados](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Não existe nenhuma ligação do arquivo primário para componentes de entrada de ficheiro do suporte de dados, se usar setRuntimeProperties para definir a propriedade de nome de ficheiro.*

![Ligação a partir da lista de Clip XML para recortar a origem da lista](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Pode ligar o XML de lista de Clip à origem de suporte de dados e utilizar transcodeSource.*

### <a name="clip-list-xml-customization"></a>Clip-personalização XML da lista
Pode especificar o XML de lista do Clip no fluxo de trabalho em tempo de execução usando **transcodeSource** na configuração do XML de cadeias de caracteres. Isto requer que o pin de XML de lista de Clip para ser ligado para o componente de origem de mídia no fluxo de trabalho.

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

Se pretender especificar /primarySourceFile para utilizar esta propriedade para nomear os ficheiros de saída usando 'Expressões', em seguida, recomendamos que passando o XML de lista do Clip como uma propriedade *depois de* a propriedade /primarySourceFile, para evitar que o Clip Lista de ser substituído pela definição da /primarySourceFile.

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

Com remoção de quadro precisos adicional:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exemplo 1: Sobreposição de uma imagem com base no vídeo

### <a name="presentation"></a>Apresentação
Considere um exemplo no qual pretende sobrepor uma imagem de logótipo no vídeo de entrada enquanto o vídeo está codificado. Neste exemplo, o vídeo de entrada com o nome "Microsoft_HoloLens_Possibilities_816p24.mp4" e o logótipo com o nome "logo.png". Deve executar os seguintes passos:

* Criar um recurso do fluxo de trabalho com o ficheiro de fluxo de trabalho (consulte o exemplo a seguir).
* Crie um elemento de multimédia, que contém dois arquivos: MyInputVideo.mp4 como o ficheiro primário e MyLogo.png.
* Enviar uma tarefa para o processador de multimédia do Media Encoder Premium Workflow com os ativos de entrada acima e especificar a seguinte cadeia de configuração.

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

No exemplo acima, o nome do ficheiro de vídeo é enviado para o componente de entrada de ficheiro do suporte de dados e a propriedade primarySourceFile. O nome do ficheiro do logótipo é enviado para outra entrada de arquivo de suporte de dados que está ligada ao componente de sobreposição de gráfico.

> [!NOTE]
> O nome de ficheiro de vídeo é enviado para a propriedade primarySourceFile. O motivo para isso é para utilizar esta propriedade no fluxo de trabalho para criar o nome de ficheiro de saída corretos usando expressões, por exemplo.

### <a name="step-by-step-workflow-creation"></a>Criação de fluxo de trabalho passo a passo
Eis os passos para criar um fluxo de trabalho que usa dois ficheiros como entrada: um vídeo e uma imagem. Ele será sobrepor a imagem com base no vídeo.

Open **Designer de fluxo de trabalho** e selecione **ficheiro** > **nova área de trabalho** > **transcodificar esquema**.

O novo fluxo de trabalho mostra três elementos:

* Ficheiro de origem principal
* Lista de clip XML
* Ficheiro/recurso de saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho de codificação*

Para aceitar o ficheiro de multimédia de entrada, comece por adicionar um componente de entrada de ficheiro do suporte de dados. Para adicionar um componente para o fluxo de trabalho, procurá-lo na caixa de pesquisa de repositório e arraste a entrada pretendida para o painel de designer.

Em seguida, adicione o ficheiro de vídeo a ser utilizado para conceber o seu fluxo de trabalho. Para tal, clique no painel em segundo plano no Designer de fluxo de trabalho e procure a propriedade de ficheiro de origem principal no painel do lado direito de propriedade. Clique no ícone de pasta e selecione o ficheiro de vídeo adequado.

![Origem de ficheiro primário](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Origem de ficheiro primário*

Em seguida, especifique o ficheiro de vídeo no componente de entrada de ficheiro do suporte de dados.   

![Origem de entrada de ficheiro de multimédia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Origem de entrada de ficheiro de multimédia*

Assim que isso é feito, o componente de entrada de ficheiro do suporte de dados irá inspecionar o arquivo e preencher seus pins de saída para refletir o arquivo que ele inspecioná-lo.

A próxima etapa é adicionar um "vídeo dados tipo Atualizador" para especificar o espaço de cor para Rec.709. Adicionar um "vídeo formato conversor" que está definido como tipo de dados. o Layout/Layout = configuráveis planas. Isto irá converter o fluxo de vídeo num formato que pode ser considerado uma origem do componente de sobreposição.

![Atualizador de tipo de dados e o conversor de formato de vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Atualizador de tipo de dados e o conversor de formato de vídeo*

![Tipo de layout = configuráveis planas](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo de layout é configurável planas*

Em seguida, adicione um componente de sobreposição de vídeo e ligar o pin de vídeo (não compactado) para o pin de vídeo (não compactado) da entrada de ficheiro do suporte de dados.

Adicionar entrada de ficheiro de suporte de dados a outro (ao carregar o ficheiro de logótipo), clique neste componente e renomeá-lo para "Logótipo de entrada do ficheiro de suporte de dados" e selecione uma imagem (um arquivo. png por exemplo) na propriedade de ficheiro. Ligue-se o pin não comprimida da imagem para o pin não comprimida da imagem de sobreposição.

![Origem de ficheiro de componente e de imagem de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Origem de ficheiro de componente e de imagem de sobreposição*

Se pretender modificar a posição do logotipo do vídeo (por exemplo, pode querer posicioná-lo em 10 por cento do canto superior esquerdo do vídeo), desmarque a caixa de verificação "Entrada Manual". Pode fazê-lo porque está a utilizar uma entrada de ficheiro do suporte de dados para fornecer o ficheiro do logótipo para o componente de sobreposição.

![Posição de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição de sobreposição*

Para codificar o fluxo de vídeo para H.264, adicione os componentes de codificador de codificador de vídeo AVC e AAC para a superfície de desenho. Ligue-se os pins.
Configurar o codificador AAC e selecione a conversão de formato de áudio, configuração predefinida: 2.0 (L, R).

![Codificadores de áudio e vídeos](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de áudio e vídeos*

Agora, adicione a **ISO Mpeg-4 Multiplexador** e **ficheiro de saída** componentes e ligue os pins, conforme mostrado.

![MP4 Multiplexador e de saída do ficheiro](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 Multiplexador e de saída do ficheiro*

Tem de definir o nome para o ficheiro de saída. Clique nas **saída de arquivo** componente e editar a expressão para o ficheiro:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome do ficheiro de saída](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome do ficheiro de saída*

Pode executar o fluxo de trabalho localmente para verificar que está a ser executado corretamente.

Depois de concluído, pode executá-lo nos serviços de multimédia do Azure.

Em primeiro lugar, preparar um recurso, nos serviços de multimédia do Azure, com dois arquivos: o ficheiro de vídeo e o logótipo. Pode fazê-lo com o .NET ou a REST API. Também pode fazer isso com o portal do Azure ou [Explorador de serviços de multimédia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Este tutorial mostra-lhe como gerir recursos com AMSE. Existem duas formas de adicionar ficheiros a um recurso:

* Crie uma pasta local, copie os dois ficheiros na mesma e arraste e solte a pasta para o **Asset** separador.
* Carregar o ficheiro de vídeo como um recurso, exibir as informações do asset, vá para o separador de ficheiros e carregar um arquivo adicional (logótipo).

> [!NOTE]
> Certifique-se definir um ficheiro primário no ativo (o principal arquivo de vídeo).

![Ficheiros de elemento no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Ficheiros de elemento no AMSE*

Selecione o elemento e optar por codificá-lo com o codificador Premium. Carregar o fluxo de trabalho e selecioná-lo.

Clique no botão para passar dados para o processador e adicione o seguinte XML para definir as propriedades de tempo de execução:

![Codificador Premium no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium no AMSE*

Em seguida, cole os seguintes dados XML. Tem de especificar o nome do ficheiro de vídeo para a entrada de ficheiro do suporte de dados e a primarySourceFile. Especifique o nome do nome do ficheiro para o logótipo demasiado.

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

Se utilizar o SDK do .NET para criar e executar a tarefa, esses dados XML devem ser passado como a cadeia de configuração.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

Depois da tarefa estiver concluída, o ficheiro MP4 no recurso de saída apresenta a sobreposição!

![Sobreposição do vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição do vídeo*

Pode baixar o fluxo de trabalho de exemplo da [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exemplo 2: Codificação do vários idiomas de áudio

Um exemplo de vários idiomas de áudio, codificação de fluxo de trabalho está disponível no [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Esta pasta contém um fluxo de trabalho de exemplo que pode ser utilizado para codificar um arquivo de ficheiros do MXF a um recurso de ficheiros MP4 com várias com várias faixas de áudio.

Este fluxo de trabalho parte do princípio de que o arquivo de ficheiros do MXF contém uma faixa de áudio; as faixas de áudio adicionais devem ser passadas como arquivos de áudio separados (WAV ou... MP4).

Para codificar, siga estes passos:

* Crie um recurso de serviços de multimédia com o arquivo de ficheiros do MXF e os arquivos de áudio (arquivos de áudio de 0 para 18).
* Certifique-se de que o arquivo de ficheiros do MXF está definido como um ficheiro primário.
* Crie um trabalho e uma tarefa usando o processador de codificador de fluxo de trabalho Premium. Utilize o fluxo de trabalho fornecido (MultiMP4-1080p-19audio-v1.workflow).
* Passar os dados de setruntime.xml para a tarefa (se utilizar o Explorador de serviços de multimédia do Azure, utilize o botão "passar dados xml para o fluxo de trabalho").
  * Atualize os dados XML para especificar as etiquetas de idiomas e nomes de ficheiro correto.
  * O fluxo de trabalho tem áudio componentes denominados 1 áudio para 18 de áudio.
  * RFC5646 é suportada para a etiqueta de idioma.

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

* O elemento codificado irá conter várias faixas de áudio de idioma e essas faixas devem ser selecionadas no leitor de multimédia do Azure.

## <a name="see-also"></a>Consulte também
* [Apresentando o Encoding de multimédia do Azure, serviços Premium](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Como utilizar o Premium Encoding nos serviços de multimédia do Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Codificação de conteúdo a pedido com os serviços de multimédia do Azure](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow formatos e codecs do](media-services-premium-workflow-encoder-formats.md)
* [Ficheiros de fluxo de trabalho de exemplo](https://github.com/Azure/azure-media-services-samples)
* [Ferramenta do Explorador dos serviços de multimédia do Azure](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
