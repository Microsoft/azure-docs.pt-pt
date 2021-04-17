---
title: Mapeamento do fluxo de dados Modo Debug
description: Inicie uma sessão de depurar interativa ao construir fluxos de dados
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2021
ms.openlocfilehash: 681a3643c04472cc42c1f672f4c9433da30e3955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565516"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapeamento do fluxo de dados Modo Debug

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Descrição Geral

O modo de depuração do fluxo de dados da Azure Data Factory permite-lhe ver interativamente a forma de dados transformar-se enquanto constrói e depura os fluxos de dados. A sessão de depuragem pode ser usada tanto em sessões de design de Fluxo de Dados como durante a execução de depurações de fluxos de dados. Para ligar o modo de depuração, utilize o botão **Data Flow Debug** na barra superior da tela de fluxo de dados ou tela de gasoduto quando tiver atividades de fluxo de dados.

![Screenshot que mostra onde está o depurador Debug 1](media/data-flow/debug-button.png)

![Screenshot que mostra onde está o depurador Debug 2](media/data-flow/debug-button-4.png)

Assim que ligar o slider, ser-lhe-á solicitado que selecione qual a configuração de tempo de execução de integração que pretende utilizar. Se o AutoResolveIntegrationRuntime for escolhido, um cluster com oito núcleos de cálculo geral com um tempo de vida padrão de 60 minutos será girado. Se desejar permitir uma equipa mais inativa antes do tempo de sessão, pode escolher uma definição de TTL mais alta. Para obter mais informações sobre os tempos de execução do fluxo de dados, consulte [o desempenho do fluxo de dados](concepts-data-flow-performance.md#ir).

![Seleção de DEBUG IR](media/data-flow/debug-new-1.png "Seleção de DEBUG IR")

Quando o modo Debug estiver ligado, irás construir interativamente o teu fluxo de dados com um cluster De spark ativo. A sessão encerrará assim que desligar a depuração na Azure Data Factory. Deve estar ciente das taxas horárias incorridas pela Azure Databricks durante o tempo em que tem a sessão de depuração ligada.

Na maioria dos casos, é uma boa prática construir os seus Fluxos de Dados em modo de depuração para que possa validar a sua lógica de negócio e ver as suas transformações de dados antes de publicar o seu trabalho na Azure Data Factory. Utilize o botão "Debug" no painel de gasodutos para testar o fluxo de dados num oleoduto.

![Ver sessões de depurar fluxo de dados](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Cada sessão de depuração que um utilizador começa a partir do seu UI do navegador ADF é uma nova sessão com o seu próprio cluster Spark. Pode utilizar a vista de monitorização para sessões de depurar acima para visualizar e gerir sessões de depuragem por fábrica. É cobrado por cada hora que cada sessão de depuragem está a executar, incluindo o tempo TTL.

## <a name="cluster-status&quot;></a>Estado do cluster

O indicador de estado do cluster na parte superior da superfície de design fica verde quando o cluster está pronto para depurar. Se o seu aglomerado já estiver quente, o indicador verde aparecerá quase instantaneamente. Se o seu cluster já não estava a funcionar quando entrou no modo de depuração, então o cluster Spark executará uma bota fria. O indicador girará até que o ambiente esteja pronto para depuração interativa.

Quando terminar a depuração, desligue o depuração de Debug para que o seu cluster Spark possa terminar e deixará de ser cobrado para a atividade de depuração.

## <a name=&quot;debug-settings&quot;></a>Definições de depurar

Assim que ligar o modo de depurar, pode editar como um fluxo de dados pré-visualiza os dados. As definições de depurar podem ser editadas clicando em &quot;Definições de Depurg&quot; na barra de ferramentas de tela de fluxo de dados. Pode selecionar o limite de linha ou a fonte de ficheiro para utilizar para cada uma das suas transformações De Origem aqui. Os limites de linha neste ajuste são apenas para a sessão de depuragem atual. Também pode selecionar o serviço ligado à encenação a ser utilizado para uma fonte Azure Synapse Analytics. 

![Definições de depurar](media/data-flow/debug-settings.png &quot;Definições de depurar")

Se tiver parâmetros no seu Fluxo de Dados ou em qualquer um dos seus conjuntos de dados referenciados, pode especificar quais os valores a utilizar durante a depuragem selecionando o separador **Parâmetros.**

Utilize as definições de amostragem aqui para indicar ficheiros de amostras ou tabelas de amostras de dados para que não tenha de alterar os seus conjuntos de dados de origem. Ao utilizar um ficheiro de amostra ou tabela aqui, pode manter a mesma lógica e definições de propriedade no fluxo de dados enquanto testa contra um subconjunto de dados.

![Parâmetros de definição de depurar](media/data-flow/debug-settings2.png "Parâmetros de definição de depurar")

O IR predefinido utilizado para o modo de depuração nos fluxos de dados ADF é um pequeno nó de trabalhador único de 4 núcleos com um nó de condutor único de 4 núcleos. Isto funciona bem com amostras menores de dados ao testar a sua lógica de fluxo de dados. Se expandir os limites de linha nas definições de depuração durante a pré-visualização de dados ou definir um maior número de linhas amostradas na sua fonte durante o depuração do gasoduto, então poderá considerar a possibilidade de definir um ambiente de computação maior num novo Tempo de Execução de Integração Azure. Em seguida, pode reiniciar a sua sessão de depurar utilizando um ambiente de computação maior.

## <a name="data-preview"></a>Pré-visualização dos dados

Com o depurg ligado, o separador de pré-visualização de dados acende-se no painel inferior. Sem o modo de depuragem ligado, o Data Flow mostrar-lhe-á apenas os metadados atuais dentro e fora de cada uma das suas transformações no separador Inspecionar. A pré-visualização de dados apenas irá consultar o número de linhas que definiu como limite nas definições de depurragem. Clique em **Refresh** para obter a pré-visualização de dados.

![Pré-visualização dos dados](media/data-flow/datapreview.png "Pré-visualização dos dados")

> [!NOTE]
> As fontes de ficheiros limitam apenas as linhas que vê, não as filas que estão a ser lidas. Para conjuntos de dados muito grandes, recomenda-se que pegue uma pequena parte desse ficheiro e o utilize para os seus testes. Pode selecionar um ficheiro temporário em Definições de Debug para cada fonte que seja um tipo de conjunto de dados de ficheiro.

Ao executar o modo Debug no Fluxo de Dados, os seus dados não serão escritos para a transformação da Pia. Uma sessão de Debug destina-se a servir de arnês de teste para as suas transformações. Os lavatórios não são necessários durante a depurada e são ignorados no fluxo de dados. Se desejar testar a escrita dos dados na pia, execute o Fluxo de Dados a partir de um Pipeline da Fábrica de Dados Azure e utilize a execução de Depurg a partir de um oleoduto.

Data Preview é uma imagem instantânea dos seus dados transformados usando limites de linha e amostragem de dados a partir de quadros de dados na memória Spark. Por conseguinte, os condutores de lavatórios não são utilizados ou testados neste cenário.

### <a name="testing-join-conditions"></a>Testes juntam condições de união

Quando o teste de unidade se junta, existe ou procura transformações, certifique-se de que utiliza um pequeno conjunto de dados conhecidos para o seu teste. Pode utilizar a opção Definições Depurg acima para definir um ficheiro temporário para utilizar para os seus testes. Isto é necessário porque ao limitar ou amostrar linhas de um conjunto de dados grandes, não é possível prever quais linhas e que teclas serão lidas no fluxo para testes. O resultado não é determinístico, o que significa que as suas condições de união podem falhar.

### <a name="quick-actions"></a>Ações rápidas

Uma vez que veja a pré-visualização dos dados, pode gerar uma rápida transformação para typecast, remover ou fazer uma modificação numa coluna. Clique no cabeçalho da coluna e, em seguida, selecione uma das opções da barra de ferramentas de pré-visualização de dados.

![A screenshot mostra a barra de ferramentas de pré-visualização de dados com opções: Typecast, Modificar, Estatísticas e Remover.](media/data-flow/quick-actions1.png "Ações rápidas")

Uma vez selecionada uma modificação, a pré-visualização dos dados irá imediatamente atualizar.000. Clique em **Confirmar** no canto superior direito para gerar uma nova transformação.

![A imagem mostra o botão Confirmar.](media/data-flow/quick-actions2.png "Ações rápidas")

**Typecast** e **Modificar** gerará uma transformação de Coluna Derivada e **remover** gerará uma transformação Select.

![A screenshot mostra as definições da Coluna Derivada.](media/data-flow/quick-actions3.png "Ações rápidas")

> [!NOTE]
> Se editar o fluxo de dados, tem de reencandidinhar a pré-visualização dos dados antes de adicionar uma transformação rápida.

### <a name="data-profiling"></a>Perfis de dados

Selecionar uma coluna no seu separador de pré-visualização de dados e clicar em **Estatísticas** na barra de ferramentas de pré-visualização de dados aparecerá um gráfico na extrema-direita da sua grelha de dados com estatísticas detalhadas sobre cada campo. A Azure Data Factory fará uma determinação com base na amostra de dados do tipo de gráfico a apresentar. Os campos de alto cardinalício deverão falhar em gráficos NUOS/NÃO NULOS, enquanto dados categóricos e numéricos que têm baixa cardinalidade apresentarão gráficos de barras mostrando a frequência do valor dos dados. Você também verá o comprimento máximo/len de campos de cordas, valores min/max em campos numéricos, dev padrão, percentiles, contagens e média.

![Estatísticas da coluna](media/data-flow/stats.png "Estatísticas da coluna")

## <a name="next-steps"></a>Passos seguintes

* Assim que terminar de construir e depurar o fluxo de dados, [execute-o a partir de um oleoduto.](control-flow-execute-data-flow-activity.md)
* Ao testar o seu pipeline com um fluxo de dados, utilize a [opção de execução de execução do pipeline Debug.](iterative-development-debugging.md)
