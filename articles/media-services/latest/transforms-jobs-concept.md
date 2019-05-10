---
title: Transformações e tarefas nos serviços de multimédia do Azure | Documentos da Microsoft
description: Ao utilizar os serviços de suporte de dados, terá de criar uma transformação para descrever as regras ou especificações para processar os seus vídeos. Este artigo fornece uma descrição geral da transformação é e como usá-lo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/08/2019
ms.author: juliako
ms.openlocfilehash: e64e980d42086603c9eb8ce39a96a9766a78afcb
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472467"
---
# <a name="transforms-and-jobs"></a>Transformações e Tarefas

Este tópico fornece detalhes sobre [transforma](https://docs.microsoft.com/rest/api/media/transforms) e [tarefas](https://docs.microsoft.com/rest/api/media/jobs) e explica a relação entre essas entidades. 

## <a name="overview"></a>Descrição geral 

### <a name="transformsjobs-workflow"></a>Tarefas de transformações/fluxo de trabalho

O diagrama seguinte mostra as tarefas de transformações/fluxo de trabalho.

![Transformações](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma transformação 
2. Submeter tarefas sob essa transformação 
3. Transformações de lista 
4. Elimine uma transformação, se não estiver a planear utilizá-lo no futuro. 

#### <a name="example"></a>Exemplo

Suponha que quisesse extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura – são os passos que tem de seguir: 

1. Definir a receita, ou a regra para processar os seus vídeos – "utilizar o primeiro quadro do vídeo como a miniatura". 
2. Para cada vídeo seria instruir o serviço: 
    1. Onde encontrar esse vídeo,  
    2. Onde gravar a imagem em miniatura de saída. 

R **transformar** ajuda-o a criar uma vez a receita (passo 1) e submeter tarefas usando essa receita (etapa 2).

> [!NOTE]
> Propriedades de **transformar** e **tarefa** que são de Datetime tipo são sempre em formato UTC.

## <a name="transforms"></a>Transformações

Uso **transforma** para configurar as tarefas comuns de codificação ou analisar vídeos. Cada **transformar** descreve uma recipe ou um fluxo de trabalho de tarefas para processar os ficheiros de vídeos ou áudio. Uma transformação única pode aplicar mais do que uma regra. Por exemplo, uma transformação poderia especificar que cada vídeo seja codificada num arquivo MP4 num determinado de velocidade de transmissão e que uma imagem em miniatura gerado a partir do primeiro quadro do vídeo. Adicionaria uma entrada de TransformOutput para cada regra que pretende incluir na sua transformação. Utilizar configurações predefinidas para informar a transformação como os ficheiros de suporte de dados de entrada devem ser processados.

Em serviços de multimédia v3, suas configurações predefinidas são com rigidez de tipos de entidades na API propriamente dita. Pode encontrar a definição de "schema" para esses objetos no [especificação de API aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Também pode ver as definições predefinidas (como **StandardEncoderPreset**) na [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [SDK de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou outra documentação de referência do serviços de multimédia v3 SDK).

Pode criar transformações usando REST, da CLI, ou utilizar qualquer um dos SDKs publicados. Transforma os serviços de multimédia v3 que API é orientada pelo Azure Resource Manager, para que também pode utilizar modelos do Resource Manager para criar e implementar na sua conta de Media Services. Controlo de acesso baseado em funções pode ser utilizado para bloquear o acesso às transformações.

Se precisar de atualizar seus [transformar](https://docs.microsoft.com/rest/api/media/transforms), utilize o **atualizar** operação. Destina-se para fazer alterações para a descrição ou as prioridades do TransformOutputs subjacente. Recomenda-se que essas atualizações ser executadas quando forem concluídas todas as tarefas em curso. Se pretende reescrever a receita, terá de criar uma nova transformação.

### <a name="transform-object-diagram"></a>Diagrama de objetos de transformação

O diagrama seguinte mostra os **transformar** objeto e os objetos, incluindo as relações de derivação faz referência. As setas cinzentos mostram um tipo que as referências de tarefa e as setas verde mostram relações de derivação de classe.<br/>Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Tarefas

R **tarefa** é o pedido real para os serviços de multimédia do Azure para aplicar a **transformar** para um determinado conteúdo vídeo ou áudio de entrada. Quando a transformação tiver sido criada, pode submeter trabalhos com APIs de serviços de suporte de dados ou qualquer um dos SDKs publicados. O **tarefa** Especifica informações como a localização do vídeo de entrada e a localização para a saída. Pode especificar a localização da sua utilização vídeo entrada: URLs HTTPS, URLs SAS, ou [ativos](https://docs.microsoft.com/rest/api/media/assets).  

Uso [entrada de HTTPS de tarefa](job-input-from-http-how-to.md) se o seu conteúdo já se encontra acessível através de uma URL e não precisa de armazenar o ficheiro de origem no Azure (por exemplo, importar a partir de S3). Esse método também é adequado se tiver o conteúdo no armazenamento de Blobs do Azure, mas não há necessidade do ficheiro para estar num elemento. Atualmente, este método suporta apenas um único ficheiro de entrada.
 
Uso [Asset como entrada da tarefa](job-input-from-local-file-how-to.md) se o conteúdo de entrada já está a ser um recurso ou o conteúdo é armazenado no arquivo local. Também é uma boa opção se pretender publicar o elemento de entrada para transmissão em fluxo ou download (Digamos que queira publicar mp4 para download, mas também Quero fazer a conversão de voz em texto ou face deteção). Este método suporta múltiplos ficheiros recursos (por exemplo, MBR conjuntos que foram codificados localmente de transmissão em fluxo).
 
O progresso e o estado das tarefas podem ser obtidos através da monitorização de eventos com o Event Grid. Para obter mais informações, consulte [monitorar eventos usando EventGrid](job-state-events-cli-how-to.md).

A operação de atualização no [tarefa](https://docs.microsoft.com/rest/api/media/jobs) entidade pode ser usada para modificar a *Descrição*e o *prioridade* propriedades depois da tarefa foi submetida. Uma alteração para o *prioridade* propriedade é eficaz apenas se a tarefa ainda estiver num Estado em fila. Se a tarefa foi iniciada processamento ou foi concluída, a alteração de prioridade não tem qualquer efeito.

### <a name="job-object-diagram"></a>Diagrama de objeto de tarefa

O diagrama seguinte mostra os **tarefa** objeto e os objetos, incluindo as relações de derivação faz referência.<br/>Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Configurar unidades reservadas de multimédia

Para a análise de áudio e tarefas de análise de vídeo que são acionados por serviços de multimédia v3 ou Video Indexer, recomenda-se elevada para aprovisionar a sua conta com 10 S3 unidades reservadas de multimédia (MRUs). Se precisar de mais de 10 S3 MRUs, abra um pedido de suporte através do [portal do Azure](https://portal.azure.com/).

Para obter detalhes, consulte [Dimensionar processamento de multimédia com a CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="see-also"></a>Consulte também

* [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtragem, ordenação, paginação de entidades de serviços de multimédia](entities-overview.md)

## <a name="next-steps"></a>Passos Seguintes

- Antes de começar a desenvolver, reveja [desenvolver com os serviços de multimédia v3 APIs](media-services-apis-overview.md) (inclui informações sobre como aceder a APIs, as convenções de nomenclatura, etc.)
- Veja estes tutoriais:

    - [Tutorial: Upload, encode, and stream videos using .NET](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir vídeos em fluxo com .NET)
    - [Tutorial: Analisar vídeos com os serviços de multimédia v3 através do .NET](analyze-videos-tutorial-with-api.md)
