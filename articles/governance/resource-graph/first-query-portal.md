---
title: 'Quickstart: A sua primeira consulta de portal'
description: Neste arranque rápido, siga os passos para executar a sua primeira consulta a partir do portal Azure usando o Azure Resource Graph Explorer.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 07e336121c6338b27c018acb5b2332653663e1b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533086"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Quickstart: Executar a sua primeira consulta de gráfico de recurso usando O Explorador de Gráficos de Recursos Azure

A potência do Azure Resource Graph está disponível diretamente no portal Azure através do Azure Resource Graph Explorer. O Resource Graph Explorer fornece informações navegaveis sobre os tipos e propriedades de recursos do Azure Resource Manager que pode consultar. O Resource Graph Explorer também fornece uma interface limpa para trabalhar com múltiplas consultas, avaliar os resultados e até converter os resultados de algumas consultas num gráfico que pode ser fixado a um dashboard Azure.

No final deste quickstart, terás usado o portal Azure e o Resource Graph Explorer para executar a tua primeira consulta de Gráfico de Recursos e fixaste os resultados num dashboard.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Abra o [portal Azure](https://portal.azure.com) para encontrar e utilizar o Explorador de Gráficos de Recursos seguindo estes passos para executar a sua primeira consulta de Gráfico de Recurso:

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Resource Graph Explorer**.

1. Na **parte da consulta 1** da janela, introduza a consulta `Resources | project name, type | limit 5` e selecione **consulta de execução**.

   > [!NOTE]
   > Como este exemplo de consulta não fornece um tipo modificador `order by` como, executar esta consulta várias vezes é provável que produza um conjunto diferente de recursos por pedido.

1. Reveja a resposta de consulta no separador **Resultados.** Selecione o separador **Mensagens** para ver detalhes sobre a consulta, incluindo a contagem de resultados e a duração da consulta. Erros, se houver, são apresentados sob este separador.

1. Atualizar a consulta para `order by` a propriedade **Nome:** `Resources | project name, type | limit 5 | order by name asc` . Em seguida, selecione **'Executar consulta' ( 'Executar'.**

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Atualize a consulta primeiro `order by` para a propriedade **Name** e, em seguida, para os `limit` cinco primeiros resultados: `Resources | project name, type | order by name asc | limit 5` . Em seguida, selecione **'Executar consulta' ( 'Executar'.**

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

### <a name="schema-browser"></a>Navegador Schema

O navegador de esquema está localizado no painel esquerdo do Explorador de Gráficos de Recursos. Esta lista de recursos mostra todos os tipos de _recursos_ de recursos Azure que são ambos suportados pelo Azure Resource Graph e que existem num inquilino a que você tem acesso. A expansão de um tipo de recurso ou subpropriações mostram propriedades infantis que podem ser usadas para criar uma consulta de Gráfico de Recurso.

Selecionando os locais do tipo de recurso `where type =="<resource type>"` na caixa de consulta. Selecionar uma das propriedades da criança `where <propertyName> == "INSERT_VALUE_HERE"` adiciona-se na caixa de consulta.
O navegador schema é uma ótima maneira de descobrir propriedades para uso em consultas. Certifique-se de que substitui _o VALOR INSERIDA \_ \_ AQUI_ pelo seu próprio valor, ajuste a consulta com condições, operadores e funções para obter os resultados pretendidos.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Criar um gráfico a partir da consulta de gráfico de recurso

Depois de executar a consulta anterior, se selecionar o **separador Charts,** obtém uma mensagem de que "o conjunto de resultados não é compatível com uma visualização de gráficos de tartes". As consultas que listam resultados não podem ser feitas num gráfico, mas consultas que fornecem contagens de recursos podem. Utilizando a [consulta de amostra - Conte máquinas virtuais por tipo DE,](./samples/starter.md#count-os)vamos criar uma visualização a partir da consulta de Gráfico de Recurso.

1. Na **parte da janela de consulta 1,** introduza a seguinte consulta e selecione **consulta de execução**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecione o separador **Resultados** e note que a resposta para esta consulta fornece contagens.

1. Selecione o **separador Gráficos.** Agora, a consulta resulta em visualizações. Altere o tipo de gráfico selecionado _do tipo de gráfico select..._ para gráfico de _bar_ ou gráfico _de Donut_ para experimentar com as opções de visualização disponíveis.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Fixar a visualização de consulta a um painel de instrumentos

Quando tiver resultados de uma consulta que pode ser visualizada, essa visualização de dados pode então ser fixada a um dos seus dashboards. Depois de executar a consulta anterior, siga estes passos:

1. **Selecione Guardar** e fornecer o nome "VMs by OS Type". Em seguida, **selecione Guarde** na parte inferior do painel direito.

1. Selecione **Executar consulta** para refazer a consulta agora que foi guardada.

1. No **separador Gráficos,** selecione uma visualização de dados. Em seguida, selecione Pin para painel de **instrumentos**.

1. Selecione a notificação do portal que aparece ou selecione **Dashboard** a partir do painel esquerdo.

A consulta está agora disponível no seu painel de instrumentos com o título do azulejo correspondente ao nome de consulta. Se a consulta não foi salva quando foi presa, chama-se "Consulta 1" em vez disso.

A consulta e a visualização de dados resultantes executam e atualizam cada vez que o painel de instrumentos carrega, fornecendo insights em tempo real e dinâmicos para o seu ambiente Azure diretamente no seu fluxo de trabalho.

> [!NOTE]
> As consultas que resultam numa lista também podem ser fixadas ao painel de instrumentos. A funcionalidade não se limita a visualizações de dados de consultas.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Painel de instrumentos de importância Resource Graph Explorer

Para fornecer exemplos de consultas de gráficos de recursos e como o Resource Graph Explorer pode ser usado para melhorar o fluxo de trabalho do portal Azure, experimente estes dashboards de exemplo.

- [Explorador de gráficos de recurso - painel de amostras #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  :::image type="content" source="./media/arge-sample1-small.png" alt-text="Imagem de exemplo para amostra de dashboard #1" lightbox="./media/arge-sample1-large.png":::

- [Explorador de gráficos de recurso - painel de amostras #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  :::image type="content" source="./media/arge-sample2-small.png" alt-text="Imagem de exemplo para amostra de dashboard #2" lightbox="./media/arge-sample2-large.png":::

> [!NOTE]
> As contagens e gráficos nas imagens do painel de instrumentos acima variam consoante o seu ambiente Azure.

1. Selecione e descarregue o painel de amostras que pretende avaliar.

1. No portal Azure, selecione **Dashboard** a partir do painel esquerdo.

1. Selecione **Upload** e, em seguida, localize e selecione o ficheiro do painel de instrumentos de amostra descarregado. Em seguida, **selecione Abrir**.

O painel de instrumentos importado é exibido automaticamente. Uma vez que existe agora no seu portal Azure, pode explorar e fazer alterações conforme necessário ou criar novos dashboards a partir do exemplo para partilhar com as suas equipas. Para obter mais informações sobre o trabalho com os dashboards, consulte [Criar e partilhar dashboards no portal Azure.](../../azure-portal/azure-portal-dashboards.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover os dashboards de gráficos de recursos da amostra do seu ambiente do portal Azure, pode fazê-lo com os seguintes passos:

1. Selecione **Dashboard** a partir do painel esquerdo.

1. A partir do drop-down do painel de instrumentos, selecione o painel de instrumentos de gráfico de recursos da amostra que pretende eliminar.

1. Selecione **Eliminar** do menu do painel de instrumentos na parte superior do painel de instrumentos e selecione **Ok** para confirmar.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, usou o Azure Resource Graph Explorer para executar a sua primeira consulta e analisou exemplos de dashboards alimentados por Resource Graph. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)
