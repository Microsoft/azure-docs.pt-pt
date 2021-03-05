---
title: Criar gatilhos baseados em eventos na Azure Data Factory
description: Saiba como criar um gatilho na Azure Data Factory que executa um oleoduto em resposta a um evento.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: ff8c549f74b59706de5203f2d2e46867d6cb1d0a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177793"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Crie um gatilho que executa um oleoduto em resposta a um evento de armazenamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve os Gatilhos do Evento de Armazenamento que pode criar nos seus oleodutos data factory.

A arquitetura orientada para eventos (EDA) é um padrão comum de integração de dados que envolve produção, deteção, consumo e reação a eventos. Os cenários de integração de dados exigem frequentemente que os clientes da Data Factory desencadeiem oleodutos com base em eventos que ocorram na conta de armazenamento, como a chegada ou eliminação de um ficheiro na conta de Armazenamento Azure Blob. A Data Factory integra-se de forma nativa com [a Azure Event Grid,](https://azure.microsoft.com/services/event-grid/)que permite desencadear oleodutos em tais eventos.

Para uma introdução e demonstração de dez minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> A integração descrita neste artigo depende da [Grelha de Eventos Azure.](https://azure.microsoft.com/services/event-grid/) Certifique-se de que a sua subscrição está registada no fornecedor de recursos da Grade de Eventos. Para obter mais informações, consulte [fornecedores e tipos de recursos.](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) Você deve ser capaz de fazer a ação *Microsoft.EventGrid/eventSubscriptions/** Esta ação faz parte do papel integrado do Colaborador de Subscrição de Eventos Da EventGrid.

## <a name="data-factory-ui"></a>IU do Data Factory

Esta secção mostra-lhe como criar um gatilho de evento de armazenamento dentro da Interface de Utilizador da Fábrica de Dados Azure.

1. Mude para o **separador Editar,** mostrado com um símbolo de lápis. 

1. Selecione **Trigger** no menu e, em seguida, selecione **Novo/Editar**. 

1. Na página **'Adicionar Gatilhos',** **selecione Escolha o gatilho...** e, em seguida, selecione **+New**. 

1. Selecione **o evento de armazenamento** do tipo de gatilho

    ![Criar novo gatilho de evento de armazenamento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selecione a sua conta de armazenamento a partir do dropdown de subscrição Azure ou utilizando manualmente o seu ID de recurso de conta de armazenamento. Escolha em que recipiente deseja que os eventos ocorram. A seleção do contentor é opcional, mas tenha em atenção que selecionar todos os recipientes pode levar a um grande número de eventos.

   > [!NOTE]
   > O Trigger do Evento de Armazenamento suporta atualmente apenas as contas de armazenamento do Azure Data Lake Storage Gen2 e da versão 2 para fins gerais. Devido a uma limitação da Grelha de Eventos Azure, a Azure Data Factory suporta apenas um máximo de 500 gatilhos de eventos de armazenamento por conta de armazenamento.

   > [!NOTE]
   > Para criar e modificar um novo Trigger de Evento de Armazenamento, a conta Azure utilizada para iniciar sessão na Data Factory deve ter pelo menos permissão *do Proprietário* na conta de armazenamento. Não é necessária qualquer autorização adicional: O diretor de serviço da Fábrica de Dados Azure _não_ necessita de autorização especial para a conta de Armazenamento ou para a Grelha de Eventos.

1. O **caminho blob começa** e o caminho **blob termina com** propriedades que lhe permitem especificar os recipientes, pastas e nomes blob para os quais deseja receber eventos. O gatilho do seu evento de armazenamento requer que pelo menos uma destas propriedades seja definida. Você pode usar variedade de padrões para ambos os **caminhos Blob começa com** e o caminho **blob termina com** propriedades, como mostrado nos exemplos mais tarde neste artigo.

    * **O caminho da bolha começa com:** O caminho da bolha deve começar com um caminho de pasta. Valores válidos incluem `2018/` e `2018/april/shoes.csv` . Este campo não pode ser selecionado se um recipiente não for selecionado.
    * **O caminho da bolha termina com:** O caminho da bolha deve terminar com um nome de ficheiro ou extensão. Valores válidos incluem `shoes.csv` e `.csv` . O nome do recipiente e da pasta é opcional, mas, quando especificado, deve ser separado por um `/blobs/` segmento. Por exemplo, um contentor denominado "encomendas" pode ter um valor de `/orders/blobs/2018/april/shoes.csv` . Para especificar uma pasta em qualquer recipiente, omita o caractere principal '/' principal. Por exemplo, `april/shoes.csv` irá desencadear um evento em qualquer ficheiro denominado em pasta chamada `shoes.csv` 'abril' em qualquer recipiente.
    * Nota: O caminho blob **começa e** **termina com** o único padrão que combina no Detonador de Eventos de Armazenamento. Outros tipos de correspondência wildcard não são suportados para o tipo de gatilho.

1. Selecione se o seu gatilho irá responder a um evento **criado pela Blob,** evento **apagado blob,** ou ambos. No local de armazenamento especificado, cada evento irá acionar os oleodutos data factory associados ao gatilho.

    ![Configure o gatilho do evento de armazenamento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Selecione se o seu gatilho ignora ou não bolhas com bytes zero.

1. Uma vez configurado o gatilho, clique em **Seguinte: Pré-visualização de dados**. Este ecrã mostra as bolhas existentes correspondidas pela configuração do gatilho do evento de armazenamento. Certifique-se de que tem filtros específicos. Configurar filtros demasiado largos pode corresponder a um grande número de ficheiros criados/eliminados e podem ter um impacto significativo no seu custo. Uma vez verificadas as condições do filtro, clique em **Terminar**.

    ![Pré-visualização de dados do evento de armazenamento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Para anexar um pipeline a este gatilho, vá à tela do gasoduto e clique em **Adicionar gatilho** e selecione **Novo/Editar**. Quando o navegador lateral aparecer, clique no **gatilho Escolha...** dropdown e selecione o gatilho que criou. Clique **em seguida: Pré-visualização de dados** para confirmar que a configuração está correta e, em seguida, a **próxima** para validar a pré-visualização de Dados está correta.

1. Se o seu oleoduto tiver parâmetros, pode especirá-los no navegador lateral do gatilho. O gatilho do evento de armazenamento captura o caminho da pasta e o nome do ficheiro da bolha nas propriedades `@triggerBody().folderPath` e `@triggerBody().fileName` . Para utilizar os valores destas propriedades num oleoduto, é necessário mapear as propriedades para os parâmetros do gasoduto. Depois de mapear as propriedades para parâmetros, pode aceder aos valores capturados pelo gatilho através da `@pipeline().parameters.parameterName` expressão em todo o pipeline. Clique **em Terminar** assim que terminar.

    ![Mapeamento de propriedades para parâmetros de pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

No exemplo anterior, o gatilho é configurado para disparar quando um caminho de bolha que termina em .csv é criado no teste de eventos de pasta nos dados da amostra do recipiente. As **propriedades de pastaPath** e **fileName** captam a localização da nova bolha. Por exemplo, quando MoviesDB.csv é adicionado ao caminho de amostra-dados/testes de eventos, `@triggerBody().folderPath` tem um valor de e tem um valor de `sample-data/event-testing` `@triggerBody().fileName` `moviesDB.csv` . Estes valores são mapeados no exemplo dos parâmetros do gasoduto `sourceFolder` e que podem ser `sourceFile` utilizados em toda a tubagem como `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile` respectivamente.

## <a name="json-schema"></a>Esquema JSON

A tabela a seguir fornece uma visão geral dos elementos de esquema que estão relacionados com os gatilhos do evento de armazenamento:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores Permitidos** | **Obrigatório** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **âmbito** | O ID de recursos do Azure Resource Manager da Conta de Armazenamento. | String | ID do gestor de recursos Azure | Sim |
| **eventos** | O tipo de eventos que causam este gatilho a disparar. | Matriz    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação destes valores. |
| **blobPathBeginsWith** | O caminho da bolha deve começar com o padrão previsto para o gatilho disparar. Por exemplo, `/records/blobs/december/` apenas dispara o gatilho para as bolhas na `december` pasta debaixo do `records` recipiente. | String   | | Tem de fornecer um valor para pelo menos uma destas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith` . |
| **blobPathEndsWith** | O caminho da bolha deve terminar com o padrão previsto para o gatilho disparar. Por exemplo, `december/boxes.csv` apenas dispara o gatilho para bolhas nomeadas `boxes` numa `december` pasta. | String   | | Tem de fornecer um valor para pelo menos uma destas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith` . |
| **ignorarEmptyBlobs** | Se as bolhas de byte zero desencadearão ou não uma corrida ao gasoduto. Por defeito, isto é definido como verdadeiro. | Booleano | true ou false | Não |

## <a name="examples-of-storage-event-triggers"></a>Exemplos de detonadores de eventos de armazenamento

Esta secção fornece exemplos de configurações de gatilho de eventos de armazenamento.

> [!IMPORTANT]
> Tem de incluir o `/blobs/` segmento do percurso, como mostra os seguintes exemplos, sempre que especificar o recipiente e a pasta, o recipiente e o ficheiro, ou o contentor, a pasta e o ficheiro. Para **blobPathBeginsWith,** a UI da Fábrica de Dados adicionará automaticamente `/blobs/` entre a pasta e o nome do recipiente no gatilho JSON.

| Propriedade | Exemplo | Descrição |
|---|---|---|
| **O caminho da bolha começa com** | `/containername/` | Recebe eventos para qualquer bolha no recipiente. |
| **O caminho da bolha começa com** | `/containername/blobs/foldername/` | Recebe eventos para quaisquer bolhas no `containername` recipiente e `foldername` pasta. |
| **O caminho da bolha começa com** | `/containername/blobs/foldername/subfoldername/` | Também pode fazer referência a uma sub-dobradeira. |
| **O caminho da bolha começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para uma bolha nomeada `file.txt` na pasta debaixo do `foldername` `containername` recipiente. |
| **O caminho da bolha termina com** | `file.txt` | Recebe eventos para uma bolha nomeada `file.txt` em qualquer caminho. |
| **O caminho da bolha termina com** | `/containername/blobs/file.txt` | Recebe eventos para uma bolha chamada `file.txt` sob `containername` recipiente. |
| **O caminho da bolha termina com** | `foldername/file.txt` | Recebe eventos para uma bolha nomeada `file.txt` em pasta sob qualquer `foldername` recipiente. |

## <a name="next-steps"></a>Passos seguintes

* Para obter informações detalhadas sobre os gatilhos, consulte [a execução do Pipeline e os gatilhos](concepts-pipeline-execution-triggers.md#trigger-execution).
* Saiba como fazer referência aos metadados do gatilho no pipeline, ver [Metadados de Gatilho de Referência em Pipeline Runs](how-to-use-trigger-parameterization.md)
