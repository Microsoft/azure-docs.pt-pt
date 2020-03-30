---
title: Transformações e Empregos nos Serviços de Media
titleSuffix: Azure Media Services
description: Aprenda a criar um Transforms para descrever as regras para o processamento dos seus vídeos nos Serviços De Mídia Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571242"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformações e Empregos nos Serviços de Media

Este tema dá detalhes sobre [Transforms](https://docs.microsoft.com/rest/api/media/transforms) e [Jobs](https://docs.microsoft.com/rest/api/media/jobs) e explica a relação entre estas entidades.

## <a name="overview"></a>Descrição geral

### <a name="transformsjobs-workflow"></a>Transforms/Empregofluxo de trabalho

O diagrama seguinte mostra transformações/trabalhos fluxo de trabalho:

![Transforma e emprego fluxo de trabalho nos Serviços De Mídia Azure](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma Transformação.
2. Submeta empregos sob a transformação.
3. List Transforma.'
4. Apague um Transform, se não estiver a planear usá-lo no futuro.

#### <a name="example"></a>Exemplo

Suponha que queria extrair o primeiro quadro de todos os seus vídeos como uma imagem de miniatura- os passos que daria são:

1. Defina a receita, ou a regra para o processamento dos seus vídeos: "use o primeiro quadro do vídeo como miniatura".
2. Para cada vídeo, você diria ao serviço:
    1. Onde encontrar o vídeo.
    2. Onde escrever a imagem da miniatura de saída.

A **Transform** ajuda-o a criar a receita uma vez (Passo 1) e a submeter jobs usando essa receita (Passo 2).

> [!NOTE]
> As propriedades de **Transform** e **Job** do tipo Datetime estão sempre em formato UTC.

## <a name="transforms"></a>Transformações

Utilize **transformes** para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transform** descreve uma receita ou um fluxo de trabalho de tarefas para processar os seus ficheiros de vídeo ou áudio. Um único Transform pode aplicar mais do que uma regra. Por exemplo, um Transform poderia especificar que cada vídeo seria codificado num ficheiro MP4 a uma dada bitrate, e que uma imagem de miniatura seria gerada a partir do primeiro quadro do vídeo. Adicionaria uma entrada TransformOutput para cada regra que pretende incluir na sua Transformação. Utilize predefinições para dizer ao Transform como os ficheiros de mídia de entrada devem ser processados.

### <a name="viewing-schema"></a>Esquema de visualização

Nos Serviços de Media v3, as predefinições são entidades fortemente digitadas na própria API. Pode encontrar a definição "schema" para estes objetos em [Especificação API Aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Também pode ver as definições predefinidas (como **StandardEncoderPreset)** no [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)ou outra documentação de referência de Media Services v3 SDK.

### <a name="creating-transforms"></a>Criar Transformações

Pode criar Transforms utilizando REST, CLI ou qualquer um dos SDKs publicados. O Media Services v3 API é conduzido pelo Gestor de Recursos Azure, para que também possa utilizar modelos de Gestor de Recursos para criar e implementar Transforms na sua conta de Media Services. O controlo de acesso baseado em funções pode ser utilizado para bloquear o acesso à Transforms.

### <a name="updating-transforms"></a>Atualizando transformações

Se precisar de atualizar o seu [Transform,](https://docs.microsoft.com/rest/api/media/transforms)utilize a operação **Atualização.** Destina-se a fazer alterações na descrição, ou as prioridades dos TransformOutputs subjacentes. Recomenda-se que tais atualizações sejam feitas quando todos os trabalhos em curso estiverem concluídos. Se pretende reescrever a receita, precisa criar um novo Transform.

### <a name="transform-object-diagram"></a>Transformar diagrama de objeto

O diagrama seguinte mostra o objeto **Transform** e os objetos que refere, incluindo as relações de derivação. As setas cinzentas mostram um tipo que as referências de Trabalho e as setas verdes mostram relações de derivação de classe.

Selecione a imagem para vê-la em tamanho real.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Tarefas

A **Job** é o pedido real aos Serviços de Media para aplicar a **Transform** a um determinado conteúdo de vídeo ou áudio de entrada. Uma vez criada a Transform, pode submeter postos de trabalho utilizando APIs de Serviços de Media, ou qualquer um dos SDKs publicados. O **Trabalho** especifica informações como a localização do vídeo de entrada e a localização para a saída. Pode especificar a localização do seu vídeo de entrada utilizando: URLs HTTPS, URLs SAS ou [Ativos](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Entrada de emprego de HTTPS

Utilize [a entrada de trabalho do HTTPS](job-input-from-http-how-to.md) se o seu conteúdo já estiver acessível através de um URL e não necessitar de armazenar o ficheiro fonte em Azure (por exemplo, importar a partir de S3). Este método também é adequado se tiver o conteúdo no armazenamento da Blob Azure, mas não tem necessidade de o ficheiro estar num Ativo. Atualmente, este método suporta apenas um único ficheiro para entrada.

### <a name="asset-as-job-input"></a>Ativo como entrada de trabalho

Utilize o [Ativo como entrada](job-input-from-local-file-how-to.md) de trabalho se o conteúdo de entrada já estiver num Ativo ou se o conteúdo for armazenado em ficheirolocal. Também é uma boa opção se planeia publicar o ativo de entrada para streaming ou download (diga que quer publicar o mp4 para download, mas também quer fazer discurso para texto ou deteção facial). Este método suporta ativos multi-ficheiros (por exemplo, conjuntos de streaming MBR que foram codificados localmente).

### <a name="checking-job-progress"></a>Verificação do progresso do emprego

O progresso e o estado dos empregos podem ser obtidos através da monitorização de eventos com a Event Grid. Para mais informações, consulte [os eventos do Monitor utilizando eventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Atualização de empregos

A operação Update na entidade [Job](https://docs.microsoft.com/rest/api/media/jobs) pode ser utilizada para modificar a *descrição* e as propriedades *prioritárias* após a apresentação do trabalho. Uma mudança para a propriedade *prioritária* só é eficaz se o trabalho ainda estiver em estado de fila. Se o trabalho começou a ser processado, ou tiver terminado, mudar a prioridade não tem qualquer efeito.

### <a name="job-object-diagram"></a>Diagrama de objeto de trabalho

O diagrama seguinte mostra o objeto **de trabalho** e os objetos que refere, incluindo as relações de derivação.

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Configure Media Reserved Units

Para os Trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados por Media Services v3 ou Video Indexer, é altamente recomendado fornecer a sua conta com 10 Unidades Reservadas de Meios De S3 (MRUs). Se precisar de mais de 10 MrUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).

Para mais detalhes, consulte [o processamento de meios de escala com cli](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="see-also"></a>Consulte também

* [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtragem, encomenda, paging de entidades de Serviços de Media](entities-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Antes de começar a desenvolver, reveja [o Desenvolvimento com Os Serviços de Media v3 APIs](media-services-apis-overview.md) (inclui informações sobre o acesso a APIs, convenções de nomeação, etc.)
- Confira estes tutoriais:

    - [Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo](stream-files-tutorial-with-rest.md)
    - [Tutorial: Upload, codificação e streaming de vídeos](stream-files-tutorial-with-api.md)
    - [Tutorial: Analisar vídeos com Media Services v3](analyze-videos-tutorial-with-api.md)
