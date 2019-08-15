---
title: Criar gatilhos baseados em evento no Azure Data Factory | Microsoft Docs
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em resposta a um evento.
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
ms.openlocfilehash: 32edacb7dd66274757359c4eb0e8c169995026ce
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019529"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um gatilho que executa um pipeline em resposta a um evento

Este artigo descreve os gatilhos baseados em evento que você pode criar em seus pipelines de Data Factory.

A EDA (arquitetura orientada a eventos) é um padrão comum de integração de dados que envolve produção, detecção, consumo e reação a eventos. Cenários de integração de dados geralmente exigem que Data Factory clientes disparem pipelines com base em eventos como a chegada ou a exclusão de um arquivo em sua conta de armazenamento do Azure. O Data Factory agora está integrado à [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/), que permite disparar pipelines em um evento.

Para obter uma introdução e uma demonstração de dez minutos desse recurso, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> A integração descrita neste artigo depende da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/). Verifique se sua assinatura está registrada com o provedor de recursos da grade de eventos. Para obter mais informações, consulte [provedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>IU do Data Factory

Esta seção mostra como criar um gatilho de evento dentro da interface Azure Data Factory usuário.

1. Ir para a **tela de criação**

2. No canto inferior esquerdo, clique no botão gatilhos

3. Clique em **+ novo** , que abrirá a navegação lateral do gatilho criar

4. Selecionar **evento** de tipo de gatilho

![Criar novo gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

5. Selecione sua conta de armazenamento na lista suspensa assinatura do Azure ou manualmente usando sua ID de recurso da conta de armazenamento. Escolha em qual contêiner você deseja que os eventos ocorram. A seleção de contêiner é opcional, mas lembre-se de que a seleção de todos os contêineres pode levar a um grande número de eventos.

   > [!NOTE]
   > O gatilho de evento atualmente dá suporte apenas a Azure Data Lake Storage Gen2 e contas de armazenamento da versão 2 de uso geral. Devido a uma limitação da grade de eventos do Azure, Azure Data Factory dá suporte apenas a um máximo de 500 gatilhos de eventos por conta de armazenamento.

6. O **caminho do blob começa com** e o **caminho do blob termina com** as propriedades permitem que você especifique os contêineres, as pastas e os nomes de BLOB para os quais deseja receber eventos. Seu gatilho de evento requer que pelo menos uma dessas propriedades seja definida. Você pode usar uma variedade de padrões para o **caminho de blob começa com** e o **caminho de blob termina com** Propriedades, conforme mostrado nos exemplos mais adiante neste artigo.

    * **O caminho do blob começa com:** O caminho do blob deve começar com um caminho de pasta. Os valores válidos `2018/` incluem `2018/april/shoes.csv`e. Este campo não poderá ser selecionado se um contêiner não estiver selecionado.
    * **O caminho do blob termina com:** O caminho do blob deve terminar com um nome de arquivo ou extensão. Os valores válidos `shoes.csv` incluem `.csv`e. O nome do contêiner e da pasta são opcionais, mas, quando especificados, eles `/blobs/` devem ser separados por um segmento. Por exemplo, um contêiner chamado ' Orders ' pode ter um valor `/orders/blobs/2018/april/shoes.csv`de. Para especificar uma pasta em qualquer contêiner, omita o caractere '/' à esquerda. Por exemplo, `april/shoes.csv` disparará um evento em qualquer arquivo `shoes.csv` chamado na pasta a, chamada ' abril ', em qualquer contêiner. 

7. Selecione se o gatilho responderá a um evento de **blob criado** , evento **excluído de blob** ou ambos. No local de armazenamento especificado, cada evento irá disparar os pipelines de Data Factory associados ao gatilho.

    ![Configurar o gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

8. Depois de configurar o gatilho, clique em **avançar: Visualização**de dados. Esta tela mostra os BLOBs existentes que correspondem à sua configuração de gatilho de evento. Verifique se você tem filtros específicos. A configuração de filtros muito amplos pode corresponder a um grande número de arquivos criados/excluídos e pode impactar significativamente o seu custo. Depois que as condições de filtro tiverem sido verificadas, clique em **concluir**.

    ![Visualização de dados do gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

9. Para anexar um pipeline a esse gatilho, vá para a tela de pipeline e clique em **Adicionar gatilho** e selecione **novo/editar**. Quando a navegação lateral for exibida, clique na lista suspensa **escolher gatilho...** e selecione o gatilho que você criou. Clique **em Avançar: Visualização** de dados para confirmar se a configuração está correta e, em seguida, ao **lado** de validar, a visualização de dados está correta.

10. Se o pipeline tiver parâmetros, você poderá especificá-los na barra de navegação do lado do parâmetro do gatilho. O gatilho de evento captura o caminho da pasta e o nome do arquivo do blob nas `@triggerBody().folderPath` propriedades `@triggerBody().fileName`e. Para usar os valores dessas propriedades em um pipeline, você deve mapear as propriedades para parâmetros de pipeline. Depois de mapear as propriedades para parâmetros, você pode acessar os valores capturados pelo gatilho `@pipeline().parameters.parameterName` por meio da expressão em todo o pipeline. Clique em **concluir** quando terminar.

    ![Mapeando Propriedades para parâmetros de pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

No exemplo anterior, o gatilho é configurado para ser acionado quando um caminho de blob terminando em. csv é criado na pasta-teste de evento no contêiner de exemplo-dados. As propriedades **FolderPath** e **filename** capturam o local do novo BLOB. Por exemplo, quando MoviesDB. csv é adicionado ao caminho de exemplo-dados/teste de evento, `@triggerBody().folderPath` tem um valor de `sample-data/event-testing` e `@triggerBody().fileName` tem um valor de `moviesDB.csv`. Esses `sourceFolder` valores são mapeados no exemplo para os parâmetros de pipeline e `sourceFile` que podem ser usados em todo o pipeline `@pipeline().parameters.sourceFile` como `@pipeline().parameters.sourceFolder` e, respectivamente.

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos de esquema relacionados a gatilhos baseados em evento:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Necessário** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | A ID de recurso Azure Resource Manager da conta de armazenamento. | Cadeia | ID de Azure Resource Manager | Sim |
| **LostFocus** | O tipo de eventos que fazem com que esse gatilho seja acionado. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação desses valores. |
| **blobPathBeginsWith** | O caminho do blob deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `/records/blobs/december/` só dispara o gatilho para BLOBs `december` na pasta sob o `records` contêiner. | Cadeia   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou. `blobPathEndsWith` |
| **blobPathEndsWith** | O caminho do blob deve terminar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `december/boxes.csv` só dispara o gatilho para BLOBs nomeados `boxes` em `december` uma pasta. | Cadeia   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou. `blobPathEndsWith` |

## <a name="examples-of-event-based-triggers"></a>Exemplos de gatilhos baseados em eventos

Esta seção fornece exemplos de configurações de gatilho com base em eventos.

> [!IMPORTANT]
> Você precisa incluir o segmento `/blobs/` do caminho, conforme mostrado nos exemplos a seguir, sempre que especificar o contêiner e a pasta, o contêiner e o arquivo, o contêiner, a pasta e o arquivo. Para **blobPathBeginsWith**, a interface do usuário do data Factory `/blobs/` será adicionada automaticamente entre o nome da pasta e do contêiner no gatilho JSON.

| Propriedade | Exemplo | Descrição |
|---|---|---|
| **O caminho do blob começa com** | `/containername/` | Recebe eventos para qualquer blob no contêiner. |
| **O caminho do blob começa com** | `/containername/blobs/foldername/` | Recebe eventos para todos os BLOBs no `containername` contêiner e `foldername` na pasta. |
| **O caminho do blob começa com** | `/containername/blobs/foldername/subfoldername/` | Você também pode fazer referência a uma subpasta. |
| **O caminho do blob começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para um blob chamado `file.txt` `foldername` na pasta sob o `containername` contêiner. |
| **O caminho do blob termina com** | `file.txt` | Recebe eventos para um blob chamado `file.txt` em qualquer caminho. |
| **O caminho do blob termina com** | `/containername/blobs/file.txt` | Recebe eventos para um blob nomeado `file.txt` em contêiner `containername`. |
| **O caminho do blob termina com** | `foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na `foldername` pasta sob qualquer contêiner. |

## <a name="next-steps"></a>Passos Seguintes
Para obter informações detalhadas sobre gatilhos, consulte [execução de pipeline e gatilhos](concepts-pipeline-execution-triggers.md#triggers).
