---
title: 'Início rápido: sua primeira consulta do portal'
description: Neste guia de início rápido, você seguirá as etapas para executar sua primeira consulta de portal do Azure usando o Gerenciador de grafo de recursos do Azure.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 5cf355e78ad51e06d7ba27d48dd352f35b4c0740
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406804"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure

O poder do grafo de recursos do Azure está disponível diretamente no portal do Azure por meio do Gerenciador de grafo de recursos do Azure. O Gerenciador de grafo de recursos fornece informações navegáveis sobre o Azure Resource Manager tipos de recursos e propriedades que você pode consultar. O Gerenciador de gráficos de recursos também fornece uma interface limpa para trabalhar com várias consultas, avaliar os resultados e até mesmo converter os resultados de algumas consultas em um gráfico que pode ser fixado em um painel do Azure.

No final deste guia de início rápido, você terá usado portal do Azure e Gerenciador de grafo de recursos para executar sua primeira consulta de grafo de recursos e fixado os resultados em um painel.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Abra o [portal do Azure](https://portal.azure.com) para localizar e usar o Gerenciador de grafo de recursos seguindo estas etapas para executar sua primeira consulta de grafo de recursos:

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **Explorador de grafo de recursos**.

1. Na parte **consulta 1** da janela, insira o `Resources | project name, type | limit 5` de consulta e selecione **Executar consulta**.

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de classificação, como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Examine a resposta de consulta na guia **resultados** . Selecione a guia **mensagens** para ver detalhes sobre a consulta, incluindo a contagem de resultados e a duração da consulta. Os erros, se houver, são exibidos nessa guia.

1. Atualize a consulta para `order by` a propriedade **Name** : `Resources | project name, type | limit 5 | order by name asc`. Em seguida, selecione **Executar consulta**.

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, ou seja, primeiro limita os resultados da consulta e, em seguida, ordena-os.

1. Atualize a consulta para primeiro `order by` a propriedade **Name** e, em seguida, `limit` os cinco primeiros resultados: `Resources | project name, type | order by name asc | limit 5`. Em seguida, selecione **Executar consulta**.

Quando a consulta final é executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados são consistentes e conforme esperado – ordenados pela propriedade **Name** , mas ainda limitados aos cinco primeiros resultados.

### <a name="schema-browser"></a>Navegador de esquema

O navegador de esquema está localizado no painel esquerdo do Gerenciador de gráficos de recursos. Esta lista de recursos mostra todos os _tipos_ de recursos dos recursos do Azure com suporte do grafo de recursos do Azure e que existem em um locatário ao qual você tem acesso. Expandir um tipo de recurso ou subpropriedades mostra Propriedades filho que podem ser usadas para criar uma consulta de gráfico de recursos.

Selecionar o tipo de recurso coloca `where type =="<resource type>"` na caixa de consulta. A seleção de uma das propriedades filho adiciona `where <propertyName> == "INSERT_VALUE_HERE"` à caixa de consulta.
O navegador de esquema é uma ótima maneira de descobrir Propriedades para uso em consultas. Certifique-se de substituir _inserir\_valor\_aqui_ pelo seu próprio valor, ajuste a consulta com condições, operadores e funções para atingir os resultados pretendidos.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Criar um gráfico a partir da consulta de grafo de recursos

Depois de executar a última consulta acima, se você selecionar a guia **gráficos** , receberá uma mensagem informando que "o conjunto de resultados não é compatível com uma visualização de gráfico de pizza". As consultas que listam os resultados não podem ser feitas em um gráfico, mas as consultas que fornecem contagens de recursos podem. Usando a [consulta de exemplo – contagem de máquinas virtuais por tipo de so](./samples/starter.md#count-virtual-machines-by-os-type), vamos criar uma visualização da consulta de grafo de recursos.

1. Na parte **consulta 1** da janela, insira a consulta a seguir e selecione **Executar consulta**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecione a guia **resultados** e observe que a resposta para essa consulta fornece contagens.

1. Selecione a guia **gráficos** . Agora, a consulta resulta em visualizações. Altere o tipo de _Selecionar tipo de gráfico..._ para o _gráfico de barras_ ou o gráfico de _rosca_ para experimentar as opções de visualização disponíveis.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Fixar a visualização de consulta em um painel

Quando você tem resultados de uma consulta que pode ser visualizada, essa visualização de dados pode então ser fixada em um dos seus painéis. Depois de executar a consulta acima, siga estas etapas:

1. Selecione **salvar** e forneça o nome "VMs por tipo de sistema operacional". Em seguida, selecione **salvar** na parte inferior do painel direito.

1. Selecione **Executar consulta** para executar novamente a consulta agora que ela foi salva.

1. Na guia **gráficos** , selecione uma visualização de dados. Em seguida, selecione **fixar no painel**.

1. Selecione a notificação do portal que aparece ou selecione **painel** no painel esquerdo.

A consulta agora está disponível no painel com o título do bloco correspondente ao nome da consulta. Se a consulta não foi salva quando foi fixada, ela é chamada de "consulta 1".

A consulta e a visualização de dados resultante são executadas e atualizadas sempre que o painel é carregado, fornecendo informações dinâmicas e em tempo real para seu ambiente do Azure diretamente em seu fluxo de trabalho.

> [!NOTE]
> As consultas que resultam em uma lista também podem ser fixadas no painel. O recurso não está limitado a visualizações de dados de consultas.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Importar exemplo de painéis do Gerenciador de gráficos de recursos

Para fornecer exemplos de consultas de grafo de recursos e como o Gerenciador de grafo de recursos pode ser usado para aprimorar seu portal do Azure fluxo de trabalho, experimente esses painéis de exemplo.

- [Gerenciador de gráficos de recursos-Dashboard de exemplo #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![exemplo de imagem para o painel de exemplo #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Gerenciador de gráficos de recursos-Dashboard de exemplo #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![exemplo de imagem para o painel de exemplo #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> As contagens e os gráficos no exemplo acima capturas de tela do painel variam de acordo com o ambiente do Azure.

1. Selecione e baixe o painel de exemplo que você deseja avaliar.

1. Em portal do Azure, selecione **painel** no painel esquerdo.

1. Selecione **carregar**, localize e selecione o arquivo de painel de exemplo baixado. Em seguida, selecione **abrir**.

O painel importado é exibido automaticamente. Como agora existe no portal do Azure, você pode explorar e fazer alterações conforme necessário ou criar novos painéis do exemplo para compartilhar com suas equipes. Para obter mais informações sobre como trabalhar com painéis, consulte [criar e compartilhar painéis no portal do Azure](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser remover os painéis de gráfico de recursos de exemplo de seu ambiente portal do Azure, poderá fazer isso com as seguintes etapas:

1. Selecione **painel** no painel esquerdo.

1. Na lista suspensa painel, selecione o painel de gráfico de recursos de exemplo que você deseja excluir.

1. Selecione **excluir** no menu do painel na parte superior do painel e selecione **OK** para confirmar.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você usou o Gerenciador de gráficos de recursos do Azure para executar sua primeira consulta e examinou os exemplos de painel fornecidos pelo grafo de recursos. Para saber mais sobre a linguagem de gráfico de recursos, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)