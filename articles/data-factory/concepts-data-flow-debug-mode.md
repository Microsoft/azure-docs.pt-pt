---
title: Modo de depuração do fluxo de dados de mapeamento de fábrica de dados do Azure
description: Inicie uma sessão de depuração interativos quando construção com dados de fluxos
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 6bde6e74279fc16237c0c0436296e2d21c3a57c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736622"
---
# <a name="mapping-data-flow-debug-mode"></a>Modo de depuração de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

O Azure Data Factory mapeamento de fluxo de dados tem um modo de depuração, o que pode ser ativado com o botão de depuração na parte superior da superfície de design. Quando a criação de fluxos de dados, definir o modo de depuração no permitirá que veja interativamente os dados a transformação de forma, enquanto que criar e depurar seus fluxos de dados.

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>Descrição geral
Quando o modo de depuração está ativado, irá criar interativamente o fluxo de dados com um cluster de interativo do Azure Databricks em execução. A sessão será fechada depois de desativar depuração no Azure Data Factory. Deve estar ciente de custos por hora, incorridos ao Azure Databricks durante o tempo que tem a sessão de depuração ativada.

Na maioria dos casos, é uma boa prática para criar seus fluxos de dados no modo de depuração para que possa validar sua lógica de negócio e ver as transformações de dados antes de publicar o seu trabalho no Azure Data Factory.

## <a name="debug-mode-on"></a>Modo de depuração no
Quando mudar no modo de depuração, será solicitado um formulário de painel lateral que irá pedir-lhe para apontar para o cluster do Azure Databricks interativo e selecione as opções para a amostragem de origem. Tem de utilizar um cluster interativo do Azure Databricks e selecione um tamanho de amostragem de cada um de seus transformações de origem ou escolher um ficheiro de texto a utilizar para os seus dados de teste.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Quando em execução no modo de depuração no fluxo de dados, os dados não serão escritos para o Sink de transformação. Uma sessão de depuração deve ser usado como um teste > equipamento para as transformações. Coletores de não serem necessários durante a depuração e são ignoradas no seu fluxo de dados. Se pretender testar a gravação dos dados > no seu coletor, executar o fluxo de dados a partir de um Pipeline de fábrica de dados do Azure e utilizar a execução de depuração a partir de um pipeline.

## <a name="debug-settings"></a>Configurações de depuração
Configurações de depuração podem ser aparecerá no painel do lado de cada origem a partir do seu fluxo de dados e também pode ser editada, selecionando "definições da origem" no fluxo de dados a barra de ferramentas de design. Pode selecionar os limites de e/ou a origem de ficheiros a utilizar para cada aqui sua transformação de origem. Também pode selecionar qual cluster do Databricks que deseja usar para depuração.

## <a name="cluster-status"></a>Estado do cluster
Há um indicador de estado do cluster na parte superior da superfície de design que fica verde quando o cluster estiver pronto para depuração. Se o cluster já está quente, em seguida, o indicador de verde aparecerá quase instantaneamente. Se o seu cluster não foi já em execução ao introduzir o modo de depuração, em seguida, terá de aguardar 5 a 7 minutos para o cluster acelerar. A luz indicadora será amarela até que ele está pronto. Assim que o cluster estiver pronto para depuração de fluxo de dados, a luz indicadora fica verde.

Quando tiver terminado com a sua vez de depuração, a depuração optar por desativar, de modo a que pode terminar o cluster do Azure Databricks.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Pré-visualização de dados
Com depuração no, a guia visualização de dados será light cópia de segurança no painel inferior. Sem o modo de depuração no fluxo de dados mostrará apenas os metadados atual e para cada um das transformações no separador Inspect. A pré-visualização de dados só irá consultar o número de linhas que tiver definido como o limite de nas definições da origem. Poderá ter de clicar em "Obter dados" para atualizar a pré-visualização de dados.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Perfis de dados
Selecionar colunas individuais na sua aba de pré-visualização de dados serão um gráfico na extremidade direita da sua grade de dados com estatísticas detalhadas sobre cada campo de pop-up. O Azure Data Factory fará com que a determinação com base na amostragem de dados, o tipo de gráfico para apresentar. Campos de alta cardinalidade serão predefinido para NULL / NOT NULL gráficos, enquanto os dados categóricos e numéricos que tem a cardinalidade baixa apresentará os gráficos de barras que mostra a frequência de valor de dados. Também verá máximo / len comprimento dos campos de cadeia de caracteres, Mín / Máx. de valores nos campos numéricos, desenvolvimento padrão, percentis, contagens e média. 

<img src="media/data-flow/chart.png" width="400">
