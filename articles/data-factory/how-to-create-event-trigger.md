---
title: Criar acionadores baseados em eventos no Azure Data Factory | Documentos da Microsoft
description: Saiba como criar um acionador no Azure Data Factory que executa um pipeline em resposta a um evento.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576621"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um acionador que executa um pipeline em resposta a um evento

Este artigo descreve os acionadores baseados em eventos que podem ser criados em seus pipelines do Data Factory.

Arquitetura condicionada por eventos (EDA) é um padrão de integração de dados comum, que envolve a produção, de deteção, de consumo e de reação a eventos. Cenários de integração de dados requerem muitas vezes, os clientes do Data Factory acionar pipelines com base em eventos. Fábrica de dados está agora integrada [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), que permite acionar pipelines num evento.

Para obter uma introdução de dez minutos e demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> A integração descrita neste artigo depende [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Certifique-se de que a sua subscrição está registada com o fornecedor de recursos do Event Grid. Para mais informações, veja [fornecedores de recursos e os tipos de](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>IU do Data Factory

### <a name="create-a-new-event-trigger"></a>Criar um novo disparador de eventos

Um evento típico é a chegada de um ficheiro ou a eliminação de um arquivo, na sua conta de armazenamento do Azure. Pode criar um acionador que responda a este evento no seu pipeline de fábrica de dados.

> [!NOTE]
> Esta integração suporta apenas a versão 2 contas de armazenamento (para fins gerais).

![Criar novo acionador de eventos](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Configurar o acionador de eventos

Com o **caminho do Blob começa com** e **caminho do Blob termina com** propriedades, pode especificar os contentores, pastas e nomes de BLOBs para o qual pretende receber eventos. Pode usar a variedade de padrões para ambos **caminho do Blob começa com** e **caminho do Blob termina com** propriedades, conforme mostrado nos exemplos neste artigo. Pelo menos uma destas propriedades é necessária.

![Configurar o acionador de eventos](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Selecione o tipo de Acionador do evento

Assim que o ficheiro é recebido no seu local de armazenamento e o blob correspondente é criado, este evento aciona e executa o pipeline de fábrica de dados. Pode criar um acionador que responde a um evento de criação de blob, um evento de eliminação de BLOBs ou os dois eventos, em seus pipelines do Data Factory.

![Tipo de Acionador Selecione como evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Mapear as propriedades de Acionador para parâmetros do pipeline

Quando um evento é acionado para um blob específico, o evento captura o nome de ficheiro e caminho de pasta do blob para as propriedades `@triggerBody().folderPath` e `@triggerBody().fileName`. Para utilizar os valores dessas propriedades num pipeline, tem de mapear as propriedades para parâmetros do pipeline. Depois de mapear as propriedades para parâmetros, pode aceder os valores capturados pelo acionador através do `@pipeline().parameters.parameterName` expressão em todo o pipeline.

![O mapeamento de propriedades para parâmetros do pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Por exemplo, na captura de ecrã anterior. o acionador está configurado para acionar quando um caminho de blob que termine em `.csv` é criado na conta de armazenamento. Como resultado, quando um blob com o `.csv` extensão é criada em qualquer lugar na conta de armazenamento, o `folderPath` e `fileName` propriedades capturar a localização do blob novo. Por exemplo, `@triggerBody().folderPath` tem um valor como `/containername/foldername/nestedfoldername` e `@triggerBody().fileName` tem um valor como `filename.csv`. Estes valores são mapeados no exemplo para os parâmetros do pipeline `sourceFolder` e `sourceFile`. Pode usá-los em todo o pipeline como `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile` , respetivamente.

## <a name="json-schema"></a>JSON schema

A tabela seguinte fornece uma descrição geral dos elementos do esquema relacionados com acionadores baseados em eventos:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Necessário** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | O ID de recurso do Azure Resource Manager da conta de armazenamento. | String | ID de Gestor de recursos do Azure | Sim |
| **events** | O tipo de eventos que fazem com que este acionador a acionar. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação destes valores. |
| **blobPathBeginsWith** | O caminho do blob tem de começar com o padrão fornecido para o acionador a acionar. Por exemplo, `/records/blobs/december/` só é acionado o acionador para blobs no `december` pasta sob o `records` contentor. | String   | | Tem de fornecer um valor para, pelo menos, uma destas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **blobPathEndsWith** | O caminho do blob tem de terminar com o padrão fornecido para o acionador a acionar. Por exemplo, `december/boxes.csv` só é acionado o acionador para blobs com o nome `boxes` num `december` pasta. | String   | | Tem de fornecer um valor para, pelo menos, uma destas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |

## <a name="examples-of-event-based-triggers"></a>Exemplos de acionadores baseados em eventos

Esta secção fornece exemplos de definições do acionador baseado em evento.

> [!IMPORTANT]
> Tem de incluir o `/blobs/` segmento do caminho, conforme mostrado nos exemplos a seguir, sempre que especifique o contentor e pasta, contentor e a pasta de ficheiro ou contêiner e de ficheiros.

| Propriedade | Exemplo | Descrição |
|---|---|---|
| **Caminho do blob começa com** | `/containername/` | Recebe eventos para qualquer blob no contentor. |
| **Caminho do blob começa com** | `/containername/blobs/foldername/` | Recebe eventos para os blobs existentes no `containername` contentor e `foldername` pasta. |
| **Caminho do blob começa com** | `/containername/blobs/foldername/subfoldername/` | Também pode referenciar uma subpasta. |
| **Caminho do blob começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para um blob com o nome `file.txt` no `foldername` pasta sob o `containername` contentor. |
| **Caminho do blob termina com** | `file.txt` | Recebe eventos para um blob com o nome `file.txt` em qualquer caminho. |
| **Caminho do blob termina com** | `/containername/blobs/file.txt` | Recebe eventos para um blob com o nome `file.txt` no contentor `containername`. |
| **Caminho do blob termina com** | `foldername/file.txt` | Recebe eventos para um blob com o nome `file.txt` em `foldername` pasta em qualquer contentor. |

## <a name="next-steps"></a>Passos Seguintes
Para obter informações detalhadas sobre os acionadores, veja [execuções de pipelines e acionadores](concepts-pipeline-execution-triggers.md#triggers).
