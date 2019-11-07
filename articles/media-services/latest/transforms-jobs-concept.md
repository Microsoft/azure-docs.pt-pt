---
title: Transformações e trabalhos nos serviços de mídia
titleSuffix: Azure Media Services
description: Saiba como criar transformações para descrever as regras para processar seus vídeos nos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571242"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformações e trabalhos nos serviços de mídia

Este tópico fornece detalhes sobre [transformações](https://docs.microsoft.com/rest/api/media/transforms) e [trabalhos](https://docs.microsoft.com/rest/api/media/jobs) e explica a relação entre essas entidades.

## <a name="overview"></a>Descrição geral

### <a name="transformsjobs-workflow"></a>Fluxo de trabalho de transformações/trabalhos

O diagrama a seguir mostra o fluxo de trabalho de transformações/trabalhos:

![Fluxo de trabalho de transformações e trabalhos nos serviços de mídia do Azure](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Crie uma transformação.
2. Envie trabalhos sob essa transformação.
3. Transformações de lista.
4. Exclua uma transformação se você não estiver planejando usá-la no futuro.

#### <a name="example"></a>Exemplo

Suponha que você quisesse extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura – as etapas que você executaria são:

1. Defina a receita ou a regra para processar seus vídeos: "usar o primeiro quadro do vídeo como a miniatura".
2. Para cada vídeo, você diria ao serviço:
    1. Onde encontrar esse vídeo.
    2. Onde gravar a imagem em miniatura de saída.

Uma **transformação** ajuda a criar a receita uma vez (etapa 1) e a enviar trabalhos usando essa receita (etapa 2).

> [!NOTE]
> As propriedades da **transformação** e do **trabalho** do tipo DateTime estão sempre no formato UTC.

## <a name="transforms"></a>Transformações

Use **transformações** para configurar tarefas comuns para codificação ou análise de vídeos. Cada **transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar seus arquivos de vídeo ou áudio. Uma única transformação pode aplicar mais de uma regra. Por exemplo, uma transformação pode especificar que cada vídeo seja codificado em um arquivo MP4 em uma determinada taxa de bits e que uma imagem em miniatura seja gerada a partir do primeiro quadro do vídeo. Você adicionaria uma entrada TransformOutput para cada regra que deseja incluir em sua transformação. Você usa predefinições para informar à transformação como os arquivos de mídia de entrada devem ser processados.

### <a name="viewing-schema"></a>Exibindo esquema

Nos serviços de mídia v3, as predefinições são entidades fortemente tipadas na própria API. Você pode encontrar a definição de "esquema" para esses objetos em [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode exibir as definições predefinidas (como **StandardEncoderPreset**) na [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), no [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)ou em outra documentação de referência do SDK dos serviços de mídia v3.

### <a name="creating-transforms"></a>Criando transformações

Você pode criar transformações usando REST, CLI ou qualquer um dos SDKs publicados. A API dos serviços de mídia v3 é orientada por Azure Resource Manager, de modo que você também pode usar modelos do Resource Manager para criar e implantar transformações em sua conta de serviços de mídia. O controle de acesso baseado em função pode ser usado para bloquear o acesso a transformações.

### <a name="updating-transforms"></a>Atualizando transformações

Se você precisar atualizar sua [transformação](https://docs.microsoft.com/rest/api/media/transforms), use a operação de **atualização** . Ele se destina a fazer alterações na descrição ou nas prioridades do TransformOutputs subjacente. É recomendável que essas atualizações sejam feitas quando todos os trabalhos em andamento forem concluídos. Se você pretende reescrever a receita, precisa criar uma nova transformação.

### <a name="transform-object-diagram"></a>Diagrama de objeto de transformação

O diagrama a seguir mostra o objeto de **transformação** e os objetos referenciados por ele, incluindo as relações de derivação. As setas cinzas mostram um tipo que o trabalho referencia e as setas verdes mostram relações de derivação de classe.

Selecione a imagem para exibi-la no tamanho total.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Tarefas

Um **trabalho** é a solicitação real aos serviços de mídia para aplicar a **transformação** a um determinado conteúdo de áudio ou vídeo de entrada. Depois que a transformação tiver sido criada, você poderá enviar trabalhos usando as APIs dos serviços de mídia ou qualquer um dos SDKs publicados. O **trabalho** especifica informações como o local do vídeo de entrada e o local para a saída. Você pode especificar o local do vídeo de entrada usando: URLs HTTPS, URLs SAS ou [ativos](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Entrada de trabalho do HTTPS

Use a [entrada de trabalho do HTTPS](job-input-from-http-how-to.md) se seu conteúdo já estiver acessível por meio de uma URL e você não precisar armazenar o arquivo de origem no Azure (por exemplo, importar do S3). Esse método também é adequado se você tiver o conteúdo no armazenamento de BLOBs do Azure, mas não precisar que o arquivo esteja em um ativo. Atualmente, esse método dá suporte apenas a um único arquivo para entrada.

### <a name="asset-as-job-input"></a>Ativo como entrada do trabalho

Use o [ativo como entrada de trabalho](job-input-from-local-file-how-to.md) se o conteúdo de entrada já estiver em um ativo ou se o conteúdo estiver armazenado no arquivo local. Também é uma boa opção se você planeja publicar o ativo de entrada para streaming ou download (digamos que você deseja publicar o MP4 para download, mas também deseja fazer fala para detecção de texto ou facial). Esse método dá suporte a ativos de vários arquivos (por exemplo, conjuntos de streaming MBR que foram codificados localmente).

### <a name="checking-job-progress"></a>Verificando o andamento do trabalho

O progresso e o estado dos trabalhos podem ser obtidos monitorando eventos com a grade de eventos. Para obter mais informações, consulte [monitorar eventos usando EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Atualizando trabalhos

A operação de atualização na entidade de [trabalho](https://docs.microsoft.com/rest/api/media/jobs) pode ser usada para modificar a *Descrição* e as propriedades de *prioridade* depois que o trabalho tiver sido enviado. Uma alteração na propriedade *Priority* só será eficaz se o trabalho ainda estiver em um estado enfileirado. Se o trabalho tiver começado a ser processado ou tiver terminado, a alteração da prioridade não terá efeito.

### <a name="job-object-diagram"></a>Diagrama de objeto de trabalho

O diagrama a seguir mostra o objeto de **trabalho** e os objetos que ele faz referência, incluindo as relações de derivação.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Configurar unidades reservadas de mídia

Para trabalhos de análise de vídeo e análise de áudio disparados pelos serviços de mídia V3 ou Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs (unidades reservadas de mídia) S3. Se você precisar de mais de 10 S3 MRUs, abra um tíquete de suporte usando o [portal do Azure](https://portal.azure.com/).

Para obter detalhes, consulte [escala de processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Consultar também

* [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtragem, ordenação, paginação de entidades de serviços de mídia](entities-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura, etc.)
- Confira estes tutoriais:

    - [Tutorial: codificar um arquivo remoto com base na URL e transmitir o vídeo](stream-files-tutorial-with-rest.md)
    - [Tutorial: carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
    - [Tutorial: analisar vídeos com os serviços de mídia v3](analyze-videos-tutorial-with-api.md)
