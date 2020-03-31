---
title: Criar gatilhos baseados em eventos na Fábrica de Dados Azure
description: Saiba como criar um gatilho na Azure Data Factory que gere um oleoduto em resposta a um evento.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: e4301afafb48fb9a1b0c9e36dde9800e2b8390f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443927"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um gatilho que executa um oleoduto em resposta a um evento

Este artigo descreve os gatilhos baseados em eventos que pode criar nos seus oleodutos data Factory.

A arquitetura orientada por eventos (EDA) é um padrão comum de integração de dados que envolve produção, deteção, consumo e reação a eventos. Os cenários de integração de dados exigem frequentemente que os clientes da Data Factory desencadeiem oleodutos com base em eventos como a chegada ou eliminação de um ficheiro na sua conta de Armazenamento Azure. A Data Factory está agora integrada na [Azure Event Grid,](https://azure.microsoft.com/services/event-grid/)que permite desencadear oleodutos num evento.

Para uma introdução de dez minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> A integração descrita neste artigo depende da Grelha de [Eventos Azure.](https://azure.microsoft.com/services/event-grid/) Certifique-se de que a sua subscrição está registada no fornecedor de recursos da Rede de Eventos. Para mais informações, consulte [fornecedores e tipos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)de recursos.

## <a name="data-factory-ui"></a>IU do Data Factory

Esta secção mostra-lhe como criar um gatilho de evento dentro da Interface de Utilizador da Fábrica de Dados Azure.

1. Ir para a **Tela de Autor**

1. No canto inferior esquerdo, clique no botão **Triggers**

1. Clique **+ Novo** que abrirá o nav do lado do gatilho

1. Selecione **evento** do tipo gatilho

    ![Criar novo gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selecione a sua conta de armazenamento a partir do dropdown de subscrição do Azure ou utilizando manualmente o id de recursos da conta de armazenamento. Escolha em que recipiente pretende que os eventos ocorram. A seleção de contentores é opcional, mas tenha em ação de que a seleção de todos os recipientes pode levar a um grande número de eventos.

   > [!NOTE]
   > O Trigger do Evento suporta atualmente apenas as contas de armazenamento do Lago De dados Azure e da versão 2 da versão 2. Devido à limitação da rede de eventos Azure, a Azure Data Factory apenas suporta um máximo de 500 gatilhos de eventos por conta de armazenamento.

1. O **caminho Blob começa com** e o caminho **blob termina com** propriedades que lhe permitem especificar os recipientes, pastas e nomes blob para os quais pretende receber eventos. O gatilho do seu evento requer que pelo menos uma destas propriedades seja definida. Você pode usar variedade de padrões para ambos **o caminho Blob começa com** com e o caminho **blob termina com** propriedades, como mostram os exemplos mais tarde neste artigo.

    * **O caminho da bolha começa com:** O caminho da bolha deve começar com um caminho de pasta. Os valores válidos incluem `2018/` e `2018/april/shoes.csv`. Este campo não pode ser selecionado se um recipiente não for selecionado.
    * **O caminho da bolha termina com:** O caminho da bolha deve terminar com um nome de ficheiro ou extensão. Os valores válidos incluem `shoes.csv` e `.csv`. O nome do recipiente e da pasta são opcionais, mas, quando especificados, devem ser separados por um `/blobs/` segmento. Por exemplo, um recipiente chamado "encomendas" `/orders/blobs/2018/april/shoes.csv`pode ter um valor de . Para especificar uma pasta em qualquer recipiente, omita o carácter principal '/'. Por exemplo, `april/shoes.csv` irá desencadear um `shoes.csv` evento em qualquer ficheiro nomeado na pasta a chamada 'april' em qualquer contentor. 

1. Selecione se o seu gatilho responderá a um evento **criado pela Blob,** **evento eliminado blob,** ou ambos. No local de armazenamento especificado, cada evento irá desencadear os gasodutos data Factory associados ao gatilho.

    ![Configure o gatilho do evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Selecione se o seu gatilho ignora ou não bolhas com zero bytes.

1. Depois de configurar o gatilho, clique em **Seguinte: Pré-visualização de dados**. Este ecrã mostra as bolhas existentes correspondidas à configuração do gatilho do evento. Certifique-se de ter filtros específicos. Configurar filtros demasiado largos pode coincidir com um grande número de ficheiros criados/eliminados e podem afetar significativamente o seu custo. Uma vez verificadas as condições do filtro, clique em **Terminar**.

    ![Pré-visualização de dados do gatilho do evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Para fixar um gasoduto a este gatilho, vá à tela do gasoduto e clique **em Adicionar gatilho** e selecione **New/Edit**. Quando aparecer a navegação lateral, clique no **gatilho Escolha...** dropdown e selecione o gatilho que criou. **Clique em Seguida: A pré-visualização de dados** para confirmar a configuração está correta e, em seguida, a **seguir** para validar a pré-visualização do Dados está correta.

1. Se o seu gasoduto tiver parâmetros, pode especificá-los no gatilho executa a navegação lateral do parâmetro. O gatilho do evento captura o caminho da pasta `@triggerBody().folderPath` e `@triggerBody().fileName`o nome do ficheiro da bolha nas propriedades e . Para utilizar os valores destas propriedades num oleoduto, deve mapear as propriedades para os parâmetros do gasoduto. Depois de mapear as propriedades para parâmetros, pode aceder `@pipeline().parameters.parameterName` aos valores capturados pelo gatilho através da expressão em todo o oleoduto. Clique em **Terminar** assim que terminar.

    ![Mapeando propriedades para parâmetros de gasoduto](media/how-to-create-event-trigger/event-based-trigger-image4.png)

No exemplo anterior, o gatilho é configurado para disparar quando um caminho de bolha que termina em .csv é criado no teste de eventos da pasta nos dados da amostra do recipiente. As propriedades **do folderPath** e **do fileName** captam a localização da nova bolha. Por exemplo, quando o MoviesDB.csv é adicionado ao caminho `@triggerBody().folderPath` amostra-dados/teste de eventos, tem um valor e `sample-data/event-testing` `@triggerBody().fileName` tem um valor de `moviesDB.csv`. Estes valores são mapeados no `sourceFolder` exemplo `sourceFile` dos parâmetros do `@pipeline().parameters.sourceFolder` gasoduto `@pipeline().parameters.sourceFile` e que podem ser utilizados em todo o oleoduto como e respectivamente.

## <a name="json-schema"></a>Esquema jSON

A tabela seguinte fornece uma visão geral dos elementos do esquema que estão relacionados com os gatilhos baseados em eventos:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores Permitidos** | **Necessário** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **âmbito** | O Id de recurso do Gestor de Recursos Azure da Conta de Armazenamento. | Cadeia | ID do Gestor de Recursos Azure | Sim |
| **eventos** | O tipo de eventos que causam este gatilho a disparar. | Matriz    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação destes valores. |
| **blobPathcomeça Com** | O caminho da bolha deve começar com o padrão previsto para o gatilho disparar. Por exemplo, `/records/blobs/december/` apenas dispara o gatilho `december` para `records` bolhas na pasta sob o recipiente. | Cadeia   | | Tem de fornecer um valor para pelo `blobPathBeginsWith` menos `blobPathEndsWith`uma destas propriedades: ou . |
| **blobPathEndsWith** | O caminho da bolha deve terminar com o padrão previsto para o gatilho disparar. Por exemplo, `december/boxes.csv` apenas dispara o `boxes` gatilho `december` para bolhas nomeadas numa pasta. | Cadeia   | | Tem de fornecer um valor para pelo `blobPathBeginsWith` menos `blobPathEndsWith`uma destas propriedades: ou . |
| **ignorar EmptyBlobs** | Se as bolhas de byte zero desencadearão ou não uma execução de gasoduto. Por defeito, isto está definido para verdade. | Booleano | true ou false | Não |

## <a name="examples-of-event-based-triggers"></a>Exemplos de gatilhos baseados em eventos

Esta secção fornece exemplos de definições de gatilho baseadas em eventos.

> [!IMPORTANT]
> Tem de incluir `/blobs/` o segmento do caminho, como mostram os seguintes exemplos, sempre que especificar recipiente e pasta, contentor e arquivo, ou contentor, pasta e ficheiro. Para **blobPathBeginsWith**, a UI da `/blobs/` fábrica de dados irá adicionar automaticamente entre a pasta e o nome do recipiente no gatilho JSON.

| Propriedade | Exemplo | Descrição |
|---|---|---|
| **O caminho blob começa com** | `/containername/` | Recebe eventos para qualquer bolha no recipiente. |
| **O caminho blob começa com** | `/containername/blobs/foldername/` | Recebe eventos para quaisquer `containername` bolhas `foldername` no recipiente e na pasta. |
| **O caminho blob começa com** | `/containername/blobs/foldername/subfoldername/` | Também pode fazer referência a uma subpasta. |
| **O caminho blob começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para uma `file.txt` bolha `foldername` nomeada `containername` na pasta sob o recipiente. |
| **O caminho blob termina com** | `file.txt` | Recebe eventos para uma `file.txt` bolha nomeada em qualquer caminho. |
| **O caminho blob termina com** | `/containername/blobs/file.txt` | Recebe eventos para uma `file.txt` bolha `containername`chamada em contentor . |
| **O caminho blob termina com** | `foldername/file.txt` | Recebe eventos para uma `file.txt` `foldername` bolha nomeada em pasta sob qualquer recipiente. |

## <a name="next-steps"></a>Passos seguintes
Para obter informações detalhadas sobre os gatilhos, consulte a [execução do Gasoduto e os gatilhos](concepts-pipeline-execution-triggers.md#triggers).
