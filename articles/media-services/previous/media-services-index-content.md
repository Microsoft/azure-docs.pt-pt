---
title: Indexação de ficheiros de mídia com indexador de mídia Azure
description: O Azure Media Indexer permite-lhe tornar o conteúdo dos seus ficheiros de mídia pesjável e gerar uma transcrição de texto completo para legendas e palavras-chave fechadas. Este tópico mostra como usar o Media Indexer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: c575904d994232726cf8d0d9152b02130fd29cea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013258"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexação de ficheiros de mídia com indexador de mídia Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Recomenda-se que os clientes migram do Indexer v1 e Indexer v2 para utilizar o [modo Básico AudioAnalyzerPreset dos Media Services v3 AudioAnalyzerPreset](../latest/analyzing-video-audio-files-concept.md). O processador de mídia [Azure Media Indexer](media-services-index-content.md) e os processadores [Azure Media Indexer 2 Preview](./legacy-components.md) estão a ser retirados. Para as datas de aposentadoria, consulte este tema [dos componentes do legado.](legacy-components.md)

O Azure Media Indexer permite-lhe tornar o conteúdo dos seus ficheiros de mídia pesjável e gerar uma transcrição de texto completo para legendas e palavras-chave fechadas. Pode processar um ficheiro de mídia ou vários ficheiros de mídia num lote.  

Ao indexar o conteúdo, certifique-se de que utiliza ficheiros de mídia que tenham uma fala clara (sem música de fundo, ruído, efeitos ou assobios de microfone). Alguns exemplos de conteúdo apropriado são: reuniões gravadas, palestras ou apresentações. Os seguintes conteúdos podem não ser adequados para a indexação: filmes, séries de TV, qualquer coisa com efeitos áudio e som mistos, conteúdo mal gravado com ruído de fundo (assobios).

Um trabalho de indexação pode gerar as seguintes saídas:

* Ficheiros de legendas fechados nos seguintes formatos: **TTML** e **WebVTT**.
  
    Os ficheiros de legendas fechados incluem uma etiqueta chamada Reconhecebilidade, que marca um trabalho de indexação baseado no quão reconhecível é o discurso no vídeo de origem.  Pode utilizar o valor da Reconhecebilidade para rastrear ficheiros de saída para usabilidade. Uma pontuação baixa significaria maus resultados de indexação devido à qualidade do áudio.
* Ficheiro de palavras-chave (XML).

Este artigo mostra como criar empregos indexantes para **Indexar um ativo** e **Index vários ficheiros**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Utilização de ficheiros de configuração e manifesto para indexar tarefas
Pode especificar mais detalhes para as suas tarefas de indexação utilizando uma configuração de tarefa. Por exemplo, pode especificar quais metadados utilizar para o seu ficheiro de mídia. Estes metadados são usados pelo motor linguístico para expandir o seu vocabulário, e melhora consideravelmente a precisão do reconhecimento da fala.  Também é capaz de especificar os ficheiros de saída pretendidos.

Também pode processar vários ficheiros de mídia de uma só vez utilizando um ficheiro manifesto.

Para obter mais informações, consulte [a Predefinição de Tarefa para Indexer Azure Media](./legacy-components.md).

## <a name="index-an-asset"></a>Indexar um ativo
O método seguinte carrega um ficheiro de media como um ativo e cria um trabalho para indexar o ativo.

Se não for especificado nenhum ficheiro de configuração, o ficheiro de mídia é indexado a todas as definições predefinidas.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Ficheiros de saída
Por predefinição, um trabalho de indexação gera os seguintes ficheiros de saída. Os ficheiros são armazenados no primeiro ativo de saída.

Quando há mais de um ficheiro de meios de entrada, o Indexer gera um ficheiro manifesto para as saídas de trabalho, denominado "JobResult.txt". Para cada ficheiro de meios de entrada, os ficheiros TTML, WebVTT e palavras-chave resultantes são sequencialmente numerados e nomeados utilizando o "Alias".

| Nome de ficheiro | Description |
| --- | --- |
| **InputFileName.ttml**<br/>**InputFileName.vtt** |Ficheiros de legenda fechada (CC) em formatoS TTML e WebVTT.<br/><br/>Podem ser usados para tornar os ficheiros de áudio e vídeo acessíveis a pessoas com deficiência auditiva.<br/><br/>Os ficheiros de legenda fechada incluem uma etiqueta chamada <b>Reconhecebilidade</b> que marca um trabalho de indexação com base no quão reconhecível é o discurso no vídeo de origem.  Pode utilizar o valor da <b>Reconhecebilidade</b> para rastrear ficheiros de saída para usabilidade. Uma pontuação baixa significaria maus resultados de indexação devido à qualidade do áudio. |
| **InputFileName.kw.xml<br/> InputFileName.info** |Ficheiros de palavras-chave e informações. <br/><br/>O ficheiro palavra-chave é um ficheiro XML que contém palavras-chave extraídas do conteúdo da fala, com informações de frequência e compensação. <br/><br/>O ficheiro de informação é um ficheiro de texto simples que contém informações granulares sobre cada termo reconhecido. A primeira linha é especial e contém a pontuação de Reconhecimento. Cada linha subsequente é uma lista separada por separadores dos seguintes dados: hora de início, fim do tempo, palavra/frase, confiança. Os tempos são dados em segundos e a confiança é dada como um número de 0-1. <br/><br/>Linha exemplo: "1.20 1.45 palavra 0.67" <br/><br/>Estes ficheiros podem ser utilizados para uma série de finalidades, como, por exemplo, para realizar análises de discursos ou expostos a motores de busca como Bing, Google ou Microsoft SharePoint para tornar os ficheiros de mídia mais detetáveis, ou mesmo usados para fornecer anúncios mais relevantes. |
| **JobResult.txt** |Manifesto de saída, presente apenas na indexação de vários ficheiros, contendo as seguintes informações:<br/><br/><table border="1"><tr><th>Filtro de Entrada</th><th>Alias</th><th>MediaLength</th><th>Erro</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3.000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Se nem todos os ficheiros de meios de entrada forem indexados com sucesso, o trabalho de indexação falha com o código de erro 4000. Para obter mais informações, consulte [os códigos De erro](#error_codes).

## <a name="index-multiple-files"></a>Indexar vários ficheiros
O método seguinte envia vários ficheiros de mídia como um ativo, e cria um trabalho para indexar todos estes ficheiros num lote.

É criado um ficheiro manifesto com a extensão ".lst" e o upload para o ativo. O ficheiro manifesto contém a lista de todos os ficheiros de ativos. Para obter mais informações, consulte [a Predefinição de Tarefa para Indexer Azure Media](./legacy-components.md).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Trabalho parcialmente bem sucedido
Se nem todos os ficheiros de meios de entrada forem indexados com sucesso, a função de indexação falhará com o código de erro 4000. Para obter mais informações, consulte [os códigos De erro](#error_codes).

Os mesmos resultados (como empregos bem sucedidos) são gerados. Pode consultar o ficheiro manifesto de saída para saber quais os ficheiros de entrada que estão falhados, de acordo com os valores da coluna Error. Para ficheiros de entrada que falharam, os ficheiros TTML, WebVTT e palavras-chave resultantes NÃO serão gerados.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a> Predefinição de tarefa para Indexador de Meios Azure
O processamento do Azure Media Indexer pode ser personalizado fornecendo uma predefinição de tarefa opcional ao lado da tarefa.  O seguinte descreve o formato desta configuração xml.

| Name | Requerer | Description |
| --- | --- | --- |
| **entrada** |false |Ficheiros de ativos que pretende indexar.</p><p>O Azure Media Indexer suporta os seguintes formatos de ficheiros de mídia: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Pode especificar o (s) nome do ficheiro (s) no **nome** ou atributo da **lista** do elemento de **entrada** (como mostrado abaixo). Se não especificar qual o ficheiro de ativos a indexar, o ficheiro principal é escolhido. Se não for definido nenhum ficheiro de ativo primário, o primeiro ficheiro do ativo de entrada é indexado.</p><p>Para especificar explicitamente o nome do ficheiro do ativo, faça:<br/>`<input name="TestFile.wmv">`<br/><br/>Também pode indexar vários ficheiros de ativos de uma só vez (até 10 ficheiros). Para efetuar este procedimento:<br/><br/><ol class="ordered"><li><p>Crie um ficheiro de texto (ficheiro manifesto) e dê-lhe uma extensão .lst. </p></li><li><p>Adicione uma lista de todos os nomes de ficheiros do ativo no seu ativo de entrada a este ficheiro manifesto. </p></li><li><p>Adicione (carregar) o ficheiro manifesto ao ativo.  </p></li><li><p>Especifique o nome do ficheiro manifesto no atributo da lista de entradas.<br/>`<input list="input.lst">`</li></ol><br/><br/>Nota: Se adicionar mais de 10 ficheiros ao ficheiro manifesto, a função de indexação falhará com o código de erro de 2006. |
| **metadados** |false |Metadados para os ficheiros de ativos especificados utilizados para a Adaptação do Vocabulário.  Útil para preparar indexante para reconhecer palavras vocabulário não padrão, tais como substantivos apropriados.<br/>`<metadata key="..." value="..."/>` <br/><br/>Pode fornecer **valores** para **chaves** predefinidas . Atualmente são suportadas as seguintes teclas:<br/><br/>"título" e "descrição" - usado para adaptação de vocabulário para ajustar o modelo linguístico para o seu trabalho e melhorar a precisão do reconhecimento da fala.  Os valores pesquisam a Internet para encontrar documentos de texto contextualização relevantes, utilizando o conteúdo para aumentar o dicionário interno durante a duração da sua tarefa de Indexação.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **características** <br/><br/> Adicionado na versão 1.2. Atualmente, a única característica suportada é o reconhecimento de voz ("ASR"). |false |A função de Reconhecimento de Voz tem as seguintes teclas de definições:<table><tr><th><p>Chave</p></th>        <th><p>Descrição</p></th><th><p>Valor de exemplo</p></th></tr><tr><td><p>Linguagem</p></td><td><p>A linguagem natural a ser reconhecida no ficheiro multimédia.</p></td><td><p>Inglês, Espanhol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>uma lista separada de pontos de costura dos formatos de legenda de saída pretendidos (se houver)</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>É verdade; Falso</p></td></tr><tr><td><p>Gerar Palavras-chave</p></td><td><p>Uma bandeira booleana especificando se é ou não necessário um ficheiro XML de palavra-chave.</p></td><td><p>É verdade; Falso, falso. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Uma bandeira booleana especificando se deve ou não forçar legendas completas (independentemente do nível de confiança).  </p><p>O padrão é falso, caso em que as palavras e frases com um nível de confiança inferior a 50% são omitidas das saídas finais da legenda e substituídas por elipses ("...").  As elipses são úteis para o controlo de qualidade e auditoria da legenda.</p></td><td><p>É verdade; Falso, falso. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Códigos de erro
Em caso de erro, o Azure Media Indexer deve reportar um dos seguintes códigos de erro:

| Código | Name | Possíveis Razões |
| --- | --- | --- |
| 2000 |Configuração inválida |Configuração inválida |
| 2001 |Ativos de entrada inválidos |Falta de ativos de entrada ou ativo vazio. |
| 2002 |Manifesto inválido |O manifesto está vazio ou o manifesto contém itens inválidos. |
| 2003 |Falhou no download de ficheiros de mídia |URL inválido em ficheiro manifesto. |
| 2004 |Protocolo não suportado |O protocolo de URL de mídia não é suportado. |
| 2005 |Tipo de ficheiro não suportado |O tipo de ficheiro de suporte de entrada não é suportado. |
| 2006 |Demasiados ficheiros de entrada |Há mais de 10 ficheiros no manifesto de entrada. |
| 3.000 |Falhou em descodificar ficheiro de mídia |Codec de mídia não suportado <br/>ou<br/> Ficheiro de mídia corrupto <br/>ou<br/> Sem transmissão de áudio nos meios de entrada. |
| 4000 |Indexação de lote parcialmente bem sucedida |Alguns dos ficheiros dos meios de comunicação de entrada não estão indexados. Para obter mais informações, consulte <a href="#output_files">os ficheiros de saída</a>. |
| other |Erros internos |Por favor contacte a equipa de apoio. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Línguas apoiadas
Atualmente, as línguas inglesa e espanhola são apoiadas.  

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Visão geral da Azure Media Services Analytics](./legacy-components.md)

[Indexação de ficheiros de mídia com indexante de mídia Azure 2](./legacy-components.md)