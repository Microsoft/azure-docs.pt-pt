---
title: Azure Data Factory o modo de depuração do fluxo de dados de mapeamento
description: Iniciar uma sessão de depuração interativa ao criar fluxos de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 945d123c0901722a527e7cc8181c91f09e4e95ec
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014513"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapeando o modo de depuração do fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="overview"></a>Descrição geral

O modo de depuração do fluxo de dados de mapeamento de Azure Data Factory pode ser ativado com o botão "depuração de fluxo de dados" na parte superior da superfície de design. Ao criar fluxos de dados, ativar o modo de depuração permite que você assista interativamente à transformação de forma de dados enquanto cria e depura seus fluxos de dados. A sessão de depuração pode ser usada em sessões de design de fluxo de dados, bem como durante a execução de depuração de pipeline de fluxos de dados.

![Botão de depuração](media/data-flow/debugbutton.png "Botão de depuração")

Quando o modo de depuração estiver ativado, você criará interativamente seu fluxo de dados com um cluster do Spark ativo. A sessão será fechada depois que você desativar a depuração em Azure Data Factory. Você deve estar ciente dos encargos por hora incorridos por Azure Databricks durante o tempo em que a sessão de depuração está ativada.

Na maioria dos casos, é uma boa prática criar seus fluxos de dados no modo de depuração para que você possa validar sua lógica de negócios e exibir suas transformações de dados antes de publicar seu trabalho em Azure Data Factory. Use o botão "depurar" no painel de pipeline para testar o fluxo de dados em um pipeline.

> [!NOTE]
> Embora a luz do modo de depuração esteja verde na barra de ferramentas Data Factory, você será cobrado pela taxa de depuração de fluxo de dados de 8 núcleos/h de computação geral com um tempo de vida de 60 minutos 

## <a name="cluster-status"></a>Estado do cluster

O indicador de status do cluster na parte superior da superfície de design fica verde quando o cluster está pronto para depuração. Se o cluster já estiver quente, o indicador verde será exibido quase instantaneamente. Se o cluster ainda não estava em execução quando você inseriu o modo de depuração, você terá que aguardar 5-7 minutos para que o cluster seja girado. O indicador será girado até seu pronto.

Quando terminar de usar a depuração, desative a opção de depuração para que seu Azure Databricks cluster possa ser encerrado e você não será mais cobrado pela atividade de depuração.

## <a name="debug-settings"></a>Configurações de depuração

As configurações de depuração podem ser editadas clicando em "configurações de depuração" na barra de ferramentas da tela fluxo de dados. Você pode selecionar o limite de linha ou a fonte de arquivo a ser usada para cada uma das transformações de origem aqui. Os limites de linha nessa configuração são apenas para a sessão de depuração atual. Você também pode selecionar o serviço vinculado de preparo a ser usado para uma fonte do SQL DW. 

![Configurações de depuração](media/data-flow/debug-settings.png "Configurações de depuração")

Se você tiver parâmetros em seu fluxo de dados ou em qualquer um de seus DataSets referenciados, poderá especificar quais valores usar durante a depuração, selecionando a guia **parâmetros** .

![Parâmetros de configurações de depuração](media/data-flow/debug-settings2.png "Parâmetros de configurações de depuração")

## <a name="data-preview"></a>Visualização de dados

Com a depuração ativada, a guia Visualização de dados se acenderá no painel inferior. Sem o modo de depuração ativado, o fluxo de dados mostrará apenas os metadados atuais dentro e fora de cada uma de suas transformações na guia inspecionar. A visualização de dados somente consultará o número de linhas que você definiu como seu limite nas configurações de depuração. Clique em **Atualizar** para buscar a visualização de dados.

![Visualização de dados](media/data-flow/datapreview.png "Visualização de dados")

Quando executado no modo de depuração no fluxo de dados, seus dados não serão gravados na transformação do coletor. Uma sessão de depuração destina-se a servir como um equipamento de teste para suas transformações. Coletores não são necessários durante a depuração e são ignorados em seu fluxo de dados. Se você quiser testar a gravação dos dados em seu coletor, execute o fluxo de dados de um pipeline Azure Data Factory e use a execução de depuração de um pipeline.

### <a name="quick-actions"></a>Ações rápidas

Depois de ver a visualização de dados, você pode gerar uma transformação rápida para conversão, remover ou fazer uma modificação em uma coluna. Clique no cabeçalho da coluna e selecione uma das opções da barra de ferramentas de visualização de dados.

![Ações rápidas](media/data-flow/quick-actions1.png "Ações rápidas")

Depois de selecionar uma modificação, a visualização de dados será atualizada imediatamente. Clique em **confirmar** no canto superior direito para gerar uma nova transformação.

![Ações rápidas](media/data-flow/quick-actions2.png "Ações rápidas")

**Conversão** e **Modify** irão gerar uma transformação de coluna derivada e **Remove** irá gerar uma transformação SELECT.

![Ações rápidas](media/data-flow/quick-actions3.png "Ações rápidas")

> [!NOTE]
> Se você editar o fluxo de dados, precisará buscar novamente a visualização de dados antes de adicionar uma transformação rápida.

### <a name="data-profiling"></a>Criação de perfil de dados

Selecionar colunas na guia Visualização de dados e clicar em **estatísticas** na barra de ferramentas visualização de dados exibirá um gráfico na extrema direita da grade de dados com estatísticas detalhadas sobre cada campo. Azure Data Factory fará uma determinação com base na amostragem de dados do tipo de gráfico a ser exibido. Os campos de alta cardinalidade serão padronizados para gráficos nulos/não nulos enquanto dados categóricos e numéricos com baixa cardinalidade exibirão gráficos de barras mostrando a frequência do valor de dados. Você também verá o comprimento máximo/Len dos campos de cadeia de caracteres, os valores mínimo/máximo em campos numéricos, desenvolvimento padrão, percentils, contagens e média.

![Estatísticas de coluna](media/data-flow/stats.png "Estatísticas de coluna")

## <a name="next-steps"></a>Passos Seguintes

* Depois de concluir a criação e a depuração do fluxo de dados, [Execute-o em um pipeline.](control-flow-execute-data-flow-activity.md)
* Ao testar seu pipeline com um fluxo de dados, use a [opção de execução de depuração](iterative-development-debugging.md) de pipeline.
