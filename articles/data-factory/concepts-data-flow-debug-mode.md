---
title: Azure Data Factory o modo de depuração do fluxo de dados de mapeamento
description: Iniciar uma sessão de depuração interativa ao criar fluxos de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 925e52f9f62860d8cd0951b9e72af09cbb7800fc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027812"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapeando o modo de depuração do fluxo de dados



## <a name="overview"></a>Descrição geral

O modo de depuração do fluxo de dados de mapeamento de Azure Data Factory permite que você assista interativamente à transformação de forma de dados enquanto cria e depura seus fluxos de dados. A sessão de depuração pode ser usada em sessões de design de fluxo de dados, bem como durante a execução de depuração de pipeline de fluxos de dados. Para ativar o modo de depuração, use o botão "depuração de fluxo de dados" na parte superior da superfície de design.

(media/data-flow/debugbutton.png "Controle deslizante depurar depuração") do ![controle deslizante]

Depois de ativar o controle deslizante, você será solicitado a selecionar qual configuração do Integration Runtime você deseja usar. Se AutoResolveIntegrationRuntime for escolhido, um cluster com oito núcleos de computação geral com um tempo de vida de 60 minutos será girado. Para obter mais informações sobre tempos de execução de integração de fluxo de dados, consulte [desempenho do fluxo de dados](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime).

![Depurar]seleção de ir de depuração de seleção(media/data-flow/debugbutton2.png "ir")

Quando o modo de depuração estiver ativado, você criará interativamente seu fluxo de dados com um cluster do Spark ativo. A sessão será fechada depois que você desativar a depuração em Azure Data Factory. Você deve estar ciente dos encargos por hora incorridos por Azure Databricks durante o tempo em que a sessão de depuração está ativada.

Na maioria dos casos, é uma boa prática criar seus fluxos de dados no modo de depuração para que você possa validar sua lógica de negócios e exibir suas transformações de dados antes de publicar seu trabalho em Azure Data Factory. Use o botão "depurar" no painel de pipeline para testar o fluxo de dados em um pipeline.

## <a name="cluster-status"></a>Estado do cluster

O indicador de status do cluster na parte superior da superfície de design fica verde quando o cluster está pronto para depuração. Se o cluster já estiver quente, o indicador verde será exibido quase instantaneamente. Se o cluster ainda não estava em execução quando você inseriu o modo de depuração, você terá que aguardar 5-7 minutos para que o cluster seja girado. O indicador será girado até seu pronto.

Quando terminar de usar a depuração, desative a opção de depuração para que seu Azure Databricks cluster possa ser encerrado e você não será mais cobrado pela atividade de depuração.

## <a name="debug-settings"></a>Configurações de depuração

As configurações de depuração podem ser editadas clicando em "configurações de depuração" na barra de ferramentas da tela fluxo de dados. Você pode selecionar o limite de linha ou a fonte de arquivo a ser usada para cada uma das transformações de origem aqui. Os limites de linha nessa configuração são apenas para a sessão de depuração atual. Você também pode selecionar o serviço vinculado de preparo a ser usado para uma fonte do SQL DW. 

Configurações ![da depuração configurações]de(media/data-flow/debug-settings.png "depuração")

Se você tiver parâmetros em seu fluxo de dados ou em qualquer um de seus DataSets referenciados, poderá especificar quais valores usar durante a depuração, selecionando a guia **parâmetros** .

![Configurações de depuração parâmetros](media/data-flow/debug-settings2.png "configurações de depuração parâmetros")

## <a name="data-preview"></a>Visualização de dados

Com a depuração ativada, a guia Visualização de dados se acenderá no painel inferior. Sem o modo de depuração ativado, o fluxo de dados mostrará apenas os metadados atuais dentro e fora de cada uma de suas transformações na guia inspecionar. A visualização de dados somente consultará o número de linhas que você definiu como seu limite nas configurações de depuração. Clique em **Atualizar** para buscar a visualização de dados.

Visualização de(media/data-flow/datapreview.png "dados") de ![visualização de dados]

> [!NOTE]
> As fontes de arquivo limitam apenas as linhas que você vê, não as linhas que estão sendo lidas. Para conjuntos de grandes volumes de arquivos, é recomendável que você faça uma pequena parte desse arquivo e use-o para seu teste. Você pode selecionar um arquivo temporário nas configurações de depuração para cada fonte que seja um tipo de conjunto de um arquivo.

Quando executado no modo de depuração no fluxo de dados, seus dados não serão gravados na transformação do coletor. Uma sessão de depuração destina-se a servir como um equipamento de teste para suas transformações. Coletores não são necessários durante a depuração e são ignorados em seu fluxo de dados. Se você quiser testar a gravação dos dados em seu coletor, execute o fluxo de dados de um pipeline Azure Data Factory e use a execução de depuração de um pipeline.

### <a name="testing-join-conditions"></a>Testando condições de junção

Quando o teste de unidade une as transformações, existe ou pesquisa, certifique-se de usar um pequeno conjunto de dados conhecidos para seu teste. Você pode usar a opção configurações de depuração acima para definir um arquivo temporário a ser usado para o teste. Isso é necessário porque, ao limitar ou fazer amostragem de linhas de um grande conjunto de grandes, você não pode prever quais linhas e quais chaves serão lidas no fluxo para teste. O resultado é não determinístico, o que significa que suas condições de junção podem falhar.

### <a name="quick-actions"></a>Ações rápidas

Depois de ver a visualização de dados, você pode gerar uma transformação rápida para conversão, remover ou fazer uma modificação em uma coluna. Clique no cabeçalho da coluna e selecione uma das opções da barra de ferramentas de visualização de dados.

(media/data-flow/quick-actions1.png "Ações rápidas") de ![ações rápidas]

Depois de selecionar uma modificação, a visualização de dados será atualizada imediatamente. Clique em **confirmar** no canto superior direito para gerar uma nova transformação.

(media/data-flow/quick-actions2.png "Ações rápidas") de ![ações rápidas]

**Conversão** e **Modify** irão gerar uma transformação de coluna derivada e **Remove** irá gerar uma transformação SELECT.

(media/data-flow/quick-actions3.png "Ações rápidas") de ![ações rápidas]

> [!NOTE]
> Se você editar o fluxo de dados, precisará buscar novamente a visualização de dados antes de adicionar uma transformação rápida.

### <a name="data-profiling"></a>Criação de perfil de dados

Selecionar uma coluna na guia Visualização de dados e clicar em **estatísticas** na barra de ferramentas visualização de dados exibirá um gráfico na extrema direita da grade de dados com estatísticas detalhadas sobre cada campo. Azure Data Factory fará uma determinação com base na amostragem de dados do tipo de gráfico a ser exibido. Os campos de alta cardinalidade serão padronizados para gráficos nulos/não nulos enquanto dados categóricos e numéricos com baixa cardinalidade exibirão gráficos de barras mostrando a frequência do valor de dados. Você também verá o comprimento máximo/Len dos campos de cadeia de caracteres, os valores mínimo/máximo em campos numéricos, desenvolvimento padrão, percentils, contagens e média.

![](media/data-flow/stats.png "Estatísticas de coluna") de estatísticas de coluna

## <a name="next-steps"></a>Passos seguintes

* Depois de concluir a criação e a depuração do fluxo de dados, [Execute-o em um pipeline.](control-flow-execute-data-flow-activity.md)
* Ao testar seu pipeline com um fluxo de dados, use a [opção de execução de depuração](iterative-development-debugging.md) de pipeline.
