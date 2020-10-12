---
title: Transformações e Empregos em Serviços de Media
titleSuffix: Azure Media Services
description: Saiba como criar um Transforms para descrever as regras para o processamento dos seus vídeos nos Serviços Azure Media.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: inhenkel
ms.openlocfilehash: a9e34c4aaeb6fc2c151233b05ed5584e0e624148
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297982"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformações e Empregos em Serviços de Media

Este tópico dá detalhes sobre [Transforms](/rest/api/media/transforms) e [Jobs](/rest/api/media/jobs) e explica a relação entre estas entidades.

## <a name="overview"></a>Descrição geral

### <a name="transformsjobs-workflow"></a>Fluxo de trabalho transformado/emprego

O seguinte diagrama mostra transformações/fluxo de trabalho:

![Transforma e empregos fluxo de trabalho nos Serviços Azure Media](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma Transformação.
2. Submeta empregos ao abrigo da transformação.
3. Lista Transforma.
4. Apague um Transform, se não estiver a planear usá-lo no futuro.

#### <a name="example"></a>Exemplo

Suponha que queria extrair a primeira moldura de todos os seus vídeos como uma imagem de miniatura - os passos que você iria tomar são:

1. Defina a receita, ou a regra para o processamento dos seus vídeos: "use a primeira moldura do vídeo como miniatura".
2. Para cada vídeo, dir-se-ia ao serviço:
    1. Onde encontrar o vídeo.
    2. Onde escrever a imagem da miniatura da saída.

A **Transform** ajuda-o a criar a receita uma vez (Passo 1) e a submeter Jobs usando essa receita (Passo 2).

> [!NOTE]
> As propriedades de **Transform** e **Job** do tipo Datetime estão sempre em formato UTC.

## <a name="transforms"></a>Transformações

Utilizar **Transformações** para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transform** descreve uma receita ou um fluxo de trabalho de tarefas para o processamento dos seus ficheiros de vídeo ou áudio. Uma única Transformação pode aplicar mais do que uma regra. Por exemplo, um Transform poderia especificar que cada vídeo seria codificado num ficheiro MP4 a uma dada bitrate, e que uma imagem de miniatura seria gerada a partir do primeiro quadro do vídeo. Adicionaria uma entrada TransformOutput para cada regra que pretende incluir no seu Transform. Utilize predefinições para dizer ao Transform como os ficheiros de meios de entrada devem ser processados.

### <a name="viewing-schema"></a>Esquema de visualização

Nos Serviços de Comunicação Social v3, as predefinições são entidades fortemente dactilografada na própria API. Pode encontrar a definição de "esquema" para estes objetos em [Especificação API Aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Também pode ver as definições predefinidas (como **o StandardEncoderPreset)** na [API REST](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK,](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)ou outros Serviços de Mídia v3 SDK documentação de referência.

### <a name="creating-transforms"></a>Criar Transformações

Pode criar Transforms usando REST, CLI ou qualquer um dos SDKs publicados. A API dos Serviços de Mídia v3 é impulsionada pelo Azure Resource Manager, pelo que também pode utilizar modelos de Gestor de Recursos para criar e implementar Transformações na sua conta de Media Services. O controlo de acesso baseado em funções pode ser usado para bloquear o acesso a Transforms.

### <a name="updating-transforms"></a>Atualização de Transformações

Se precisar de atualizar o seu [Transform,](/rest/api/media/transforms)utilize a operação **Atualização.** Destina-se a fazer alterações à descrição, ou às prioridades dos TransformOutputs subjacentes. Recomenda-se que tais atualizações sejam feitas quando todos os trabalhos em curso estiverem concluídos. Se pretende reescrever a receita, precisa de criar um novo Transform.

### <a name="transform-object-diagram"></a>Transforme o diagrama do objeto

O diagrama seguinte mostra o objeto **Transform** e os objetos que refere, incluindo as relações de derivação. As setas cinzentas mostram um tipo que as referências de Job e as setas verdes mostram relações de derivação de classe.

Selecione a imagem para vê-la em tamanho real.  

[![Diagrama mostrando o objeto Transform e os objetos que refere, incluindo as relações de derivação de classe entre os objetos.](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>Tarefas

A **Job** é o pedido real aos Serviços de Comunicação social para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. Uma vez criado o Transform, pode submeter empregos usando APIs de Serviços de Mídia ou qualquer um dos SDKs publicados. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização para a saída. Pode especificar a localização do seu vídeo de entrada utilizando: URLs HTTPS, URLs SAS ou [Ativos](/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Entrada de trabalho da HTTPS

Utilize [a entrada de trabalho do HTTPS](job-input-from-http-how-to.md) se o seu conteúdo já estiver acessível através de um URL e não necessitar de armazenar o ficheiro de origem em Azure (por exemplo, importar a partir de S3). Este método também é adequado se tiver o conteúdo no armazenamento Azure Blob, mas não tiver necessidade de o ficheiro estar num Ativo. Atualmente, este método suporta apenas um único ficheiro para entrada.

### <a name="asset-as-job-input"></a>Ativo como entrada de Job

Utilize [o Ativo como entrada de trabalho](job-input-from-local-file-how-to.md) se o conteúdo de entrada já estiver num Ativo ou se o conteúdo for armazenado em ficheiro local. Também é uma boa opção se planeia publicar o ativo de entrada para streaming ou download (diga que pretende publicar o mp4 para download, mas também quer fazer discurso a texto ou deteção facial). Este método suporta ativos multi-ficheiros (por exemplo, conjuntos de streaming MBR que foram codificados localmente).

### <a name="checking-job-progress"></a>Verificação do progresso do trabalho

O progresso e o estado dos postos de trabalho podem ser obtidos através da monitorização de eventos com a Grade de Eventos. Para obter mais informações, consulte [os eventos do Monitor utilizando o EventGrid.](job-state-events-cli-how-to.md)

### <a name="updating-jobs"></a>Atualização de Postos de Trabalho

A operação De Atualização na entidade [Job](/rest/api/media/jobs) pode ser usada para modificar a *descrição* e as propriedades *prioritárias* após a apresentação do trabalho. Uma mudança na propriedade *prioritária* só é eficaz se o trabalho ainda estiver em fila. Se o trabalho começou a processar, ou terminou, mudar de prioridade não tem efeito.

### <a name="job-object-diagram"></a>Diagrama de objeto de trabalho

O diagrama seguinte mostra o objeto **Job** e os objetos que refere, incluindo as relações de derivação.

Clique na imagem para visualizá-lo tamanho completo.  

[![Diagrama mostrando o objeto De Trabalho e os objetos que refere, incluindo as relações de derivação de classe entre os objetos.](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>Configure unidades reservadas para meios de comunicação

Para os Trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados pelos Media Services v3 ou Por Video Indexer, é altamente recomendado a provisionar a sua conta com 10 Unidades Reservadas para Os Meios de Comunicação Social (MRUs). Se precisar de mais de 10 MRUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).

Para mais informações, consulte [o processamento de mídia scale com CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="see-also"></a>Consulte também

* [Códigos de erro](/rest/api/media/jobs/get#joberrorcode)
* [Filtragem, encomenda, paging de entidades de Serviços de Comunicação Social](entities-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Antes de começar a desenvolver, [reveja o Desenvolvimento com os Serviços de Comunicação Social v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, convenções de nomeação, etc.)
- Confira estes tutoriais:

    - [Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo](stream-files-tutorial-with-rest.md)
    - [Tutorial: Carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
    - [Tutorial: Analisar vídeos com Serviços de Comunicação Social v3](analyze-videos-tutorial-with-api.md)
