---
title: Indexar os ficheiros de multimédia com o indexador de multimédia do Azure
description: O indexador de multimédia do Azure permite-lhe para tornar o conteúdo dos seus ficheiros multimédia pesquisáveis e gerar uma transcrição de texto completo para as legendas de áudio e palavras-chave fechado. Este tópico mostra como utilizar o indexador de multimédia.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: a11ae0414d6737f1588515ec19524bcf499f0c74
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189109"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexar os ficheiros de multimédia com o indexador de multimédia do Azure
O indexador de multimédia do Azure permite-lhe para tornar o conteúdo dos seus ficheiros multimédia pesquisáveis e gerar uma transcrição de texto completo para as legendas de áudio e palavras-chave fechado. Pode processar um ficheiro de multimédia ou vários num lote.  

> [!IMPORTANT]
> Quando precisa de indexar conteúdo, certifique-se utilizar ficheiros de suporte de dados que tenham clara de voz (sem música em segundo plano, ruído, efeitos ou hiss microfone). Alguns exemplos de conteúdo apropriado são: registadas reuniões, palestras ou apresentações. O seguinte conteúdo pode não ser adequado para indexação: filmes, programas de TV, qualquer coisa com áudio misto e efeitos sonoros, mal registadas conteúdo com o barulho de fundo (hiss).
> 
> 

Uma tarefa de indexação pode gerar os seguintes resultados:

* Ficheiros de legendas nos seguintes formatos: **SAMI**, **TTML**, e **WebVTT**.
  
    Ficheiros com legendagem incluem uma etiqueta denominada Recognizability, quais as classificações de uma tarefa de indexação com base na conversão de voz no vídeo de origem como reconhecível.  Pode utilizar o valor de Recognizability para ficheiros de saída de tela para facilidade de utilização. Uma pontuação baixa significaria resultados de indexação fraco devido a qualidade do áudio.
* Ficheiro de palavra-chave (XML).
* Áudio a indexação de ficheiro blob (AIB) para utilização com o SQL server.
  
    Para obter mais informações, consulte [usando arquivos de AIB com o indexador de multimédia do Azure e o SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Este artigo mostra como criar tarefas de indexação **um recurso de índice** e **vários ficheiros de índice**.

Para as atualizações mais recentes do indexador de multimédia do Azure, consulte [blogs de serviços de multimédia](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Usando arquivos de configuração e de manifesto para tarefas de indexação
Pode especificar mais detalhes para as suas tarefas de indexação ao utilizar uma configuração de tarefas. Por exemplo, pode especificar quais metadados a utilizar para o ficheiro de suporte de dados. Estes metadados é utilizado pelo mecanismo de idioma para expandir o vocabulário e melhora consideravelmente a precisão de reconhecimento de voz.  Também são capazes de especificar os ficheiros de saída desejada.

Também pode processar vários ficheiros de suporte de dados ao mesmo tempo utilizando um ficheiro de manifesto.

Para obter mais informações, consulte [tarefas configuração predefinida para o Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Um recurso de índice
O método seguinte carrega um ficheiro de suporte de dados como um recurso e cria uma tarefa para o elemento de índice.

Se não for especificado nenhum ficheiro de configuração, o ficheiro de multimédia é indexado com todas as configurações padrão.

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
### <a id="output_files"></a>Ficheiros de saída
Por predefinição, uma tarefa de indexação gera os seguintes ficheiros de saída. Os ficheiros são armazenados no recurso de saída primeiro.

Quando existe mais do que um ficheiro de suporte de dados de entrada, o indexador gera um arquivo de manifesto para as saídas de tarefas, com o nome 'JobResult.txt'. Para cada ficheiro de suporte de dados de entrada, o AIB resultante, SAMI, TTML, WebVTT e arquivos de palavra-chave, são sequencialmente numerados e nomeados usando o "Alias".

| Nome de ficheiro | Descrição |
| --- | --- |
| **InputFileName.aib** |Arquivo de BLOBs de indexação áudio. <br/><br/> Arquivo de áudio BLOBs de indexação (AIB) é um arquivo binário que pode ser pesquisado no Microsoft SQL server utilizando a pesquisa em texto completo.  O ficheiro AIB é mais poderoso do que os ficheiros de legendas simples, porque contém alternativas para cada palavra, permitindo uma experiência de pesquisa muito mais sofisticada. <br/> <br/>Requer a instalação do Suplemento SQL de indexador num máquina com Microsoft SQL server 2008 ou posterior. Pesquisar AIB com o Microsoft SQL pesquisa de texto completo do servidor fornece os resultados da pesquisa mais precisos que a procura os ficheiros de legendagem de áudio gerados pelo WAMI. Isto acontece porque o AIB contém alternativas de palavras que soam de forma semelhantes, ao passo que os ficheiros de legendagem de áudio contêm a palavra mais alta de confiança para cada segmento do áudio. Se procurar por palavras faladas é de importância upmost, em seguida, é recomendado para utilizar o conjunto AIB com o Microsoft SQL Server.<br/><br/> Para transferir o suplemento, clique em <a href="https://aka.ms/indexersql">suplemento de SQL de indexador de multimédia do Azure</a>. <br/><br/>Também é possível utilizar outros mecanismos de pesquisa, como Apache Lucene/Solr, simplesmente indexar o vídeo com base na legendagem de áudio e arquivos XML de palavra-chave, mas isso resultará em menos precisos resultados da pesquisa. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Arquivos de legenda (CC) fechado em formatos SAMI TTML e WebVTT.<br/><br/>Eles podem ser usados para tornar os arquivos de áudio e vídeo acessível para pessoas portadoras de deficiência auditivas.<br/><br/>Ficheiros de legendas fechados incluem uma etiqueta denominada <b>Recognizability</b> pontua que uma tarefa de indexação com base na conversão de voz no vídeo de origem como reconhecível.  Pode usar o valor de <b>Recognizability</b> para ficheiros de saída de tela para facilidade de utilização. Uma pontuação baixa significaria resultados de indexação fraco devido a qualidade do áudio. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Palavra-chave e as informações de ficheiros. <br/><br/>Ficheiro de palavra-chave é um arquivo XML que contém palavras-chave extraído do conteúdo de discurso, com frequência e informações de deslocamento. <br/><br/>Ficheiro de informações é um ficheiro de texto simples que contém informações granulares sobre cada termo reconhecido. A primeira linha é especial e contém a pontuação de Recognizability. Cada linha subsequente é uma lista separada de separador dos seguintes dados: iniciar o tempo, a hora de fim, a palavra/expressão, confiança. Os tempos são indicados em segundos e a confiança é fornecida como um número entre 0-1. <br/><br/>Linha de exemplo: "1.20 1.45 word 0,67" <br/><br/>Estes ficheiros podem ser utilizados para diversas finalidades, como, realizar análises de voz, ou expostos a mecanismos de pesquisa, como o Bing, Google ou Microsoft SharePoint para tornar os ficheiros de multimédia mais detetável, ou até mesmo utilizadas para disponibilizar os anúncios mais relevantes. |
| **JobResult.txt** |Manifesto de saída, presente somente quando a indexação de vários ficheiros, que contém as seguintes informações:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Erro</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Se não todos os ficheiros de suporte de dados de entrada são indexados com êxito, a tarefa de indexação falha com o código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).

## <a name="index-multiple-files"></a>Vários ficheiros de índice
O seguinte método carrega vários ficheiros de suporte de dados como um recurso e cria uma tarefa para todos esses arquivos num lote de índice.

Um arquivo de manifesto com a extensão ".lst" é criado e carregado no elemento. O ficheiro de manifesto contém a lista de todos os arquivos de recurso. Para obter mais informações, consulte [tarefas configuração predefinida para o Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Tarefa parcialmente com êxito
Se não todos os ficheiros de suporte de dados de entrada são indexados com êxito, a tarefa de indexação irá falhar com o código de erro 4000. Para obter mais informações, consulte [códigos de erro](#error_codes).

As mesmas saídas (como tarefas com êxito) são geradas. Pode consultar o ficheiro de manifesto de saída para saber quais os ficheiros de entrada são falha, de acordo com os valores de coluna de erro. Para ficheiros de entrada que falhou, AIB resultante, SAMI, TTML, WebVTT e palavra-chave arquivos não serão gerados.

### <a id="preset"></a> Predefinição de tarefas para o indexador de multimédia do Azure
O processamento do indexador de multimédia do Azure pode ser personalizado, fornecendo uma configuração predefinida juntamente com a tarefa de tarefas opcionais.  A seguir descreve o formato esse XML de configuração.

| Name | Requerer | Descrição |
| --- | --- | --- |
| **input** |false |Ficheiros de recurso que deseja indexar.</p><p>O indexador de multimédia do Azure suporta os seguintes formatos de arquivo de mídia: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Pode especificar o nome de ficheiro (s) no **name** ou **lista** atributo do **entrada** elemento (conforme mostrado abaixo). Se não especificar qual arquivo de recurso para o índice, o ficheiro principal é escolhido. Não se for definido nenhum ficheiro de ativo primário, o primeiro arquivo no recurso de entrada é indexado.</p><p>Para especificar explicitamente o nome do arquivo de recurso, fazer:<br/>`<input name="TestFile.wmv">`<br/><br/>Também pode indexar vários ficheiros de elemento ao mesmo tempo (até 10 ficheiros). Para efetuar este procedimento:<br/><br/><ol class="ordered"><li><p>Crie um ficheiro de texto (ficheiro de manifesto) e dê a ele uma extensão de .lst. </p></li><li><p>Adicione uma lista de todos os nomes de arquivo de recurso no seu recurso de entrada para este ficheiro de manifesto. </p></li><li><p>Adicione ficheiro de manifesto (carregamento) para o elemento.  </p></li><li><p>Especifique o nome do ficheiro de manifesto no atributo de lista da entrada.<br/>`<input list="input.lst">`</li></ol><br/><br/>Nota: Se adicionar mais do que 10 ficheiros para o arquivo de manifesto, a tarefa de indexação irá falhar com o código de erro de 2006. |
| **metadata** |false |Metadados para o ficheiro ou ficheiros de ativo especificado utilizados para adaptação de vocabulário.  Útil para preparar o indexador para reconhecerem as palavras de vocabulário não padrão, tais como nomes próprios.<br/>`<metadata key="..." value="..."/>` <br/><br/>Pode fornecer **valores** para predefinidas **chaves**. Atualmente são suportadas as seguintes chaves:<br/><br/>"title" e "Descrição" - utilizado para adaptação de vocabulário para ajustar a linguagem de modelam para o seu trabalho e melhorar a precisão de reconhecimento de voz.  Os valores de efetuar o seeding pesquisas da Internet para encontrar os documentos de texto relevante contextualmente, usando o conteúdo para aumentar o dicionário interno para a duração da sua tarefa de indexação.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funcionalidades** <br/><br/> Adicionado na versão 1.2. Atualmente, a única funcionalidade suportada é o reconhecimento de fala ("ASR"). |false |A funcionalidade de reconhecimento de voz tem as seguintes chaves de definições:<table><tr><th><p>Chave</p></th>        <th><p>Descrição</p></th><th><p>Valor de exemplo</p></th></tr><tr><td><p>Idioma</p></td><td><p>Linguagem natural a ser reconhecida no ficheiro multimédia.</p></td><td><p>Inglês, espanhol</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>uma lista delimitada por vírgulas dos formatos de legendas de saída desejada (se houver)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Um sinalizador booleano que especifica se é ou não um ficheiro AIB necessário (para utilização com o SQL Server e o cliente IFilter indexador).  Para obter mais informações, consulte <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">usando arquivos de AIB com o indexador de multimédia do Azure e o SQL Server</a>.</p></td><td><p>TRUE; FALSO</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Um sinalizador booleano que especifica se é ou não é necessário um ficheiro XML de palavra-chave.</p></td><td><p>TRUE; FALSO. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Um sinalizador booleano que especifica se deve ou não forçar legendas completas (independentemente do nível de confiança).  </p><p>Padrão é false, caso em que palavras e expressões que têm um nível de confiança de 50% de inferior são omitidos das saídas de legenda final e substituídos por reticências (...").  As reticências são úteis para controle de qualidade de legenda e auditoria.</p></td><td><p>TRUE; FALSO. </p></td></tr></table> |

### <a id="error_codes"></a>Códigos de erro
No caso de um erro, o indexador de multimédia do Azure devem reportar fazer uma cópia de um dos seguintes códigos de erro:

| Código | Name | Razões possíveis |
| --- | --- | --- |
| 2000 |Configuração inválida |Configuração inválida |
| 2001 |Recursos de entrada inválidos |Recursos de entrada ou elemento vazio em falta. |
| 2002 |Manifesto inválido |Manifesto está vazio ou manifesto contém itens inválidos. |
| 2003 |Falha ao transferir o ficheiro de multimédia |URL inválido no ficheiro de manifesto. |
| 2004 |Protocolo não suportado |Não é suportado o protocolo do URL de multimédia. |
| 2005 |Tipo de ficheiro não suportado |Tipo de ficheiro do suporte de dados de entrada não é suportado. |
| 2006 |Demasiados ficheiros de entrada |Há mais de 10 ficheiros no manifesto de entrada. |
| 3000 |Falha ao descodificar ficheiro de multimédia |Codec de suporte de dados não suportado <br/>ou<br/> Ficheiro de multimédia danificada <br/>ou<br/> Sem transmissão de áudio no suporte de dados de entrada. |
| 4000 |Indexação do batch foi parcialmente bem-sucedido |Alguns dos ficheiros de suporte de dados de entrada são falha ser indexados. Para obter mais informações, consulte <a href="#output_files">ficheiros de saída</a>. |
| outro |Erros internos |Entre em contato com a equipa de suporte. indexer@microsoft.com |

## <a id="supported_languages"></a>Idiomas suportados
Atualmente, são suportados os idiomas inglês e espanhol. Para obter mais informações, consulte [a mensagem de blogue de lançamento de versão 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

[Usando arquivos AIB com o indexador de multimédia do Azure e o SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indexe ficheiros multimédia com a pré-visualização de 2 de indexador de multimédia do Azure](media-services-process-content-with-indexer2.md)

