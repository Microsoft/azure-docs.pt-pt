---
title: 'Quickstart: A sua primeira consulta no portal'
description: Neste arranque rápido, siga os passos para executar a sua primeira consulta a partir do portal Azure usando o Azure Resource Graph Explorer.
ms.date: 05/20/2020
ms.topic: quickstart
ms.openlocfilehash: 69fb1262de706185d8968e9381bb34dd0d84a3b7
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872090"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Quickstart: Execute a sua primeira consulta de gráfico de recursos usando o Explorador de Gráficos de Recursos Azure

A potência do Azure Resource Graph está disponível diretamente no portal Azure através do Azure Resource Graph Explorer. O Resource Graph Explorer fornece informações de navegação sobre os tipos de recursos do Gestor de Recursos Azure e propriedades que pode consultar. O Resource Graph Explorer também fornece uma interface limpa para trabalhar com múltiplas consultas, avaliar os resultados e até converter os resultados de algumas consultas num gráfico que pode ser fixado a um dashboard Azure.

No final deste quickstart, terá usado o portal Azure e o Resource Graph Explorer para executar a sua primeira consulta de Gráfico de Recursos e fixou os resultados num dashboard.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Abra o [portal Azure](https://portal.azure.com) para encontrar e utilizar o Explorador de Gráficos de Recursos seguindo estes passos para executar a sua primeira consulta de Gráfico de Recursos:

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Resource Graph Explorer**.

1. Na **parte** de consulta 1 da janela, introduza a consulta `Resources | project name, type | limit 5` e selecione Consulta de **execução**.

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador tipo `order by` como, executar esta consulta várias vezes é provável que produza um conjunto diferente de recursos por pedido.

1. Reveja a resposta de consulta no separador **Resultados.** Selecione o separador **Mensagens** para ver detalhes sobre a consulta, incluindo a contagem de resultados e a duração da consulta. Os erros, caso existam, são apresentados sob este separador.

1. Atualize a consulta à `order by` propriedade **Nome:** `Resources | project name, type | limit 5 | order by name asc` . Em seguida, selecione **A consulta de execução**.

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando primeiro limita os resultados da consulta e, em seguida, ordena-os.

1. Atualize a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados: `Resources | project name, type | order by name asc | limit 5` . Em seguida, selecione **A consulta de execução**.

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

### <a name="schema-browser"></a>Navegador Schema

O navegador schema está localizado no painel esquerdo do Resource Graph Explorer. Esta lista de recursos mostra todos os tipos de _recursos_ do Azure que são ambos apoiados pelo Azure Resource Graph e que existem num inquilino a que você tem acesso. Expandir um tipo de recurso ou subpropriedades mostram propriedades infantis que podem ser usadas para criar uma consulta de Gráfico de Recursos.

Selecionando o tipo de recurso `where type =="<resource type>"` coloca-se na caixa de consulta. A seleção de uma das propriedades da criança `where <propertyName> == "INSERT_VALUE_HERE"` adiciona-se à caixa de consulta.
O navegador schema é uma ótima maneira de descobrir propriedades para uso em consultas. Certifique-se de que substitui o _VALOR DE INSERÇÃO \_ \_ AQUI_ pelo seu próprio valor, ajuste a consulta com condições, operadores e funções para obter os resultados pretendidos.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Criar um gráfico da consulta do Gráfico de Recursos

Depois de executar a última consulta acima, se selecionar o separador **Gráficos,** recebe uma mensagem de que "o conjunto de resultados não é compatível com uma visualização de gráficos de tortas". As consultas que os resultados da lista não podem ser feitas num gráfico, mas as consultas que fornecem contagens de recursos podem. Utilizando a [consulta da amostra - Conte as máquinas virtuais por tipo OS,](./samples/starter.md#count-virtual-machines-by-os-type)vamos criar uma visualização a partir da consulta do Graph de Recursos.

1. Na parte 1 da **janela,** introduza a seguinte consulta e selecione Consulta de **execução**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecione o separador **Resultados** e note que a resposta para esta consulta fornece contagens.

1. Selecione o separador **Gráficos.** Agora, a consulta resulta em visualizações. Mude o tipo de _Select chart type..._ para o gráfico de _barras_ ou gráfico _de Donut_ para experimentar as opções de visualização disponíveis.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Fixar a visualização da consulta a um dashboard

Quando se tem resultados de uma consulta que pode ser visualizada, essa visualização de dados pode então ser fixada a um dos seus dashboards. Depois de executar a consulta acima, siga estes passos:

1. Selecione **Guardar** e fornecer o nome "VMs by OS Type". Em seguida, selecione **Guardar** na parte inferior do painel direito.

1. Selecione **A consulta** de executar para reexecutar a consulta agora que foi guardada.

1. No separador **Gráficos,** selecione uma visualização de dados. Em seguida, selecione **Pin para painel de instrumentos**.

1. Selecione a notificação do portal que aparece ou selecione **dashboard** a partir do painel esquerdo.

A consulta está agora disponível no seu painel de instrumentos com o título do azulejo correspondente ao nome de consulta. Se a consulta não foi guardada quando foi fixada, chama-se "Consulta 1".

A consulta e a visualização de dados resultantes funcionam e atualizam cada vez que o dashboard carrega, fornecendo informações em tempo real e dinâmicas para o seu ambiente Azure diretamente no seu fluxo de trabalho.

> [!NOTE]
> As consultas que resultam numa lista também podem ser fixadas no painel de instrumentos. A funcionalidade não se limita a visualizações de dados de consultas.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Exemplo de importação Painel de Instrumentos do Explorador de Gráficos de Recursos

Para fornecer exemplos de consultas de gráficos de recursos e como o Resource Graph Explorer pode ser usado para melhorar o fluxo de trabalho do portal Azure, experimente estes dashboards exemplo.

- [Explorador de gráficos de recursos - painel de amostras #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Imagem de exemplo para #1 do Painel de Amostras](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Explorador de gráficos de recursos - sample dashboard #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Imagem de exemplo para #2 do Painel de Amostras](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Contagens e gráficos no exemplo acima, as imagens do dashboard variam dependendo do seu ambiente Azure.

1. Selecione e baixe o painel de dados que pretende avaliar.

1. No portal Azure, selecione **Dashboard** a partir do painel esquerdo.

1. Selecione **Upload**e, em seguida, localize e selecione o ficheiro de painel de dados de amostra descarregado. Em seguida, selecione **Open**.

O painel importado é automaticamente apresentado. Uma vez que existe agora no seu portal Azure, poderá explorar e fazer alterações conforme necessário ou criar novos dashboards a partir do exemplo para partilhar com as suas equipas. Para obter mais informações sobre o trabalho com dashboards, consulte [Create e partilhe dashboards no portal Azure](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se desejar remover os dashboards do Resource Graph da amostra do seu portal Azure, pode fazê-lo com os seguintes passos:

1. Selecione **dashboard** a partir do painel esquerdo.

1. A partir do dashboard drop-down, selecione o painel de instrumentos do Resource Graph da amostra que pretende eliminar.

1. **Selecione Eliminar** do menu do painel de instrumentos na parte superior do painel de instrumentos e selecione **Ok** para confirmar.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usaste o Azure Resource Graph Explorer para executar a tua primeira consulta e olhaste para os exemplos do dashboard alimentados pelo Resource Graph. Para saber mais sobre a linguagem do Graph de Recursos, continue na página de detalhes da linguagem da consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre a linguagem de consulta](./concepts/query-language.md)