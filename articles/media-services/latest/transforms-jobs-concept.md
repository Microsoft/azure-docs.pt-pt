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
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 1c2ec576211741390ef91233101261a7881e4180
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652217"
---
# <a name="transforms-and-jobs"></a>Transformações e Tarefas

Este tópico fornece detalhes sobre [transforma](https://docs.microsoft.com/rest/api/media/transforms) e [tarefas](https://docs.microsoft.com/rest/api/media/jobs) e explica a relação entre essas entidades. O diagrama seguinte mostra as tarefas de transformações/fluxo de trabalho.

![Transformações](./media/encoding/transforms-jobs.png)

> [!NOTE]
> Propriedades de **transformar** e **tarefa** que são de Datetime tipo são sempre em formato UTC.

## <a name="transforms"></a>Transformações

Uso **transforma** para configurar as tarefas comuns de codificação ou analisar vídeos. Cada **transformar** descreve uma recipe ou um fluxo de trabalho de tarefas para processar os ficheiros de vídeos ou áudio. Uma transformação única pode aplicar mais do que uma regra. Por exemplo, uma transformação poderia especificar que cada vídeo seja codificada num arquivo MP4 num determinado de velocidade de transmissão e que uma imagem em miniatura gerado a partir do primeiro quadro do vídeo. Adicionaria uma entrada de TransformOutput para cada regra que pretende incluir na sua transformação. Pode criar transformações na sua conta de serviços de multimédia com a API do serviços de multimédia v3 ou utilizar qualquer um dos SDKs publicados. Transforma os serviços de multimédia v3 que API é orientada pelo Azure Resource Manager, para que também pode utilizar modelos do Resource Manager para criar e implementar na sua conta de Media Services. Controlo de acesso baseado em funções pode ser utilizado para bloquear o acesso às transformações.

A operação de atualização no [transformar](https://docs.microsoft.com/rest/api/media/transforms) entidade destina-se para efetuar alterações para a descrição ou as prioridades do TransformOutputs subjacente. Recomenda-se que essas atualizações ser executadas quando forem concluídas todas as tarefas em curso. Se pretende reescrever a receita, terá de criar uma nova transformação.

## <a name="jobs"></a>Tarefas

R **tarefa** é o pedido real para os serviços de multimédia do Azure para aplicar a **transformar** para um determinado conteúdo vídeo ou áudio de entrada. Quando a transformação tiver sido criada, pode submeter trabalhos com APIs de serviços de suporte de dados ou qualquer um dos SDKs publicados. O **tarefa** Especifica informações como a localização do vídeo de entrada e a localização para a saída. Pode especificar a localização da sua utilização vídeo entrada: URLs HTTPS, URLs SAS, ou [ativos](https://docs.microsoft.com/rest/api/media/assets).  

Uso [entrada de HTTPS de tarefa](job-input-from-http-how-to.md) se o seu conteúdo já se encontra acessível através de uma URL e não precisa de armazenar o ficheiro de origem no Azure (por exemplo, importar a partir de S3). Esse método também é adequado se tiver o conteúdo no armazenamento de Blobs do Azure, mas não há necessidade do ficheiro para estar num elemento. Atualmente, este método suporta apenas um único ficheiro de entrada.
 
Uso [Asset como entrada da tarefa](job-input-from-local-file-how-to.md) se o conteúdo de entrada já está a ser um recurso ou o conteúdo é armazenado no arquivo local. Também é uma boa opção se pretender publicar o elemento de entrada para transmissão em fluxo ou download (Digamos que queira publicar mp4 para download, mas também Quero fazer a conversão de voz em texto ou face deteção). Este método suporta múltiplos ficheiros recursos (por exemplo, MBR conjuntos que foram codificados localmente de transmissão em fluxo).
 
O progresso e o estado das tarefas podem ser obtidos através da monitorização de eventos com o Event Grid. Para obter mais informações, consulte [monitorar eventos usando EventGrid](job-state-events-cli-how-to.md).

A operação de atualização no [tarefa](https://docs.microsoft.com/rest/api/media/jobs) entidade pode ser usada para modificar a *Descrição*e o *prioridade* propriedades depois da tarefa foi submetida. Uma alteração para o *prioridade* propriedade é eficaz apenas se a tarefa ainda estiver num Estado em fila. Se a tarefa foi iniciada processamento ou foi concluída, a alteração de prioridade não tem qualquer efeito.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma transformação 
2. Submeter tarefas sob essa transformação 
3. Transformações de lista 
4. Elimine uma transformação, se não estiver a planear utilizá-lo no futuro. 

### <a name="example"></a>Exemplo

Suponha que quisesse extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura – são os passos que tem de seguir: 

1. Definir a receita, ou a regra para processar os seus vídeos – "utilizar o primeiro quadro do vídeo como a miniatura". 
2. Para cada vídeo seria instruir o serviço: 
    1. Onde encontrar esse vídeo,  
    2. Onde gravar a imagem em miniatura de saída. 

R **transformar** ajuda-o a criar uma vez a receita (passo 1) e submeter tarefas usando essa receita (etapa 2).

## <a name="job-error-codes"></a>Códigos de erro da tarefa

Ver [códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="paging"></a>Paginação

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="configure-media-reserved-units"></a>Configurar unidades reservadas de multimédia

Para a análise de áudio e tarefas de análise de vídeo que são acionados por serviços de multimédia v3 ou Video Indexer, recomenda-se elevada para aprovisionar a sua conta com 10 S3 unidades reservadas de multimédia (MRUs). Se precisar de mais de 10 S3 MRUs, abra um pedido de suporte através do [portal do Azure](https://portal.azure.com/).

Para obter detalhes, consulte [Dimensionar processamento de multimédia com a CLI](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Carregar, codificar e transmitir vídeos com .NET](stream-files-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com os serviços de multimédia v3 através do .NET](analyze-videos-tutorial-with-api.md)
