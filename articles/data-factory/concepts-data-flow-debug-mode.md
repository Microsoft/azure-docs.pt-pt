---
title: Mapeando o fluxo de dados Modo Debug
description: Inicie uma sessão interativa de depuração quando os fluxos de dados de construção
ms.author: makromer
author: kromerm
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/06/2019
ms.openlocfilehash: 4a763a3bb4d46ba03808423d4d1283381c1174a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605401"
---
# <a name="mapping-data-flow-debug-mode"></a>Mapeando o fluxo de dados Modo Debug

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Descrição geral

O modo de depuração de dados do fluxo de dados da Azure Data Factory permite-lhe ver a forma de dados transformar-se interactivamente enquanto constrói e depura os fluxos de dados. A sessão de depuração pode ser usada tanto em sessões de design de fluxo de dados como durante a execução de depuração de gasodutos de fluxos de dados. Para ligar o modo de depuração, utilize o botão "Data Flow Debug" na parte superior da superfície de design.

![Deslizador de depurado](media/data-flow/debugbutton.png "Deslizador de depurado")

Assim que ligar o slider, será solicitado que selecione qual a configuração de tempo de execução de integração que pretende utilizar. Se o AutoResolveIntegrationRuntime for escolhido, será criado um cluster com oito núcleos de computação geral com um tempo de 60 minutos para viver. Para obter mais informações sobre os tempos de execução da integração do fluxo de dados, consulte o desempenho do fluxo de [dados.](concepts-data-flow-performance.md#increasing-compute-size-in-azure-integration-runtime)

![Seleção de Debug IR](media/data-flow/debugbutton2.png "Seleção de Debug IR")

Quando o modo Debug estiver ligado, irá sondar interativamente o seu fluxo de dados com um cluster Spark ativo. A sessão encerrará assim que desligar o depurado na Azure Data Factory. Deve estar ciente das tarifas horárias incorridas pela Azure Databricks durante o tempo em que tem a sessão de depuração ligada.

Na maioria dos casos, é uma boa prática construir os seus Fluxos de Dados em modo de depuração para que possa validar a sua lógica de negócio e ver as suas transformações de dados antes de publicar o seu trabalho na Azure Data Factory. Utilize o botão "Depuração" no painel do gasoduto para testar o fluxo de dados num gasoduto.

## <a name="cluster-status"></a>Estado do cluster

O indicador de estado do cluster na parte superior da superfície de design fica verde quando o cluster está pronto para depuração. Se o seu cluster já estiver quente, então o indicador verde aparecerá quase instantaneamente. Se o seu cluster já não estava a funcionar quando entrou no modo de depuração, então terá de esperar 5-7 minutos para o cluster girar. O indicador girará até estar pronto.

Quando terminar a sua depuração, desligue o desligamento do Debug para que o seu cluster Debricks Azure possa terminar e deixará de ser cobrado para atividade de depuração.

## <a name="debug-settings"></a>Definições de depuração

As definições de depuração podem ser editadas clicando em "Definições de depuração" na barra de ferramentas de lona Data Flow. Pode selecionar o limite de linha ou a fonte de ficheiro para utilizar para cada uma das suas transformações de Origem aqui. Os limites da linha neste ajuste são apenas para a atual sessão de depuração. Também pode selecionar o serviço de paragem ligado a ser utilizado para uma fonte SQL DW. 

![Definições de depuração](media/data-flow/debug-settings.png "Definições de depuração")

Se tiver parâmetros no seu Fluxo de Dados ou em qualquer um dos seus conjuntos de dados referenciados, pode especificar quais os valores a utilizar durante a depuração, selecionando o separador **Parâmetros.**

![Parâmetros de definições de depuração](media/data-flow/debug-settings2.png "Parâmetros de definições de depuração")

## <a name="data-preview"></a>Pré-visualização dos dados

Com o depurado ligado, o separador 'Visualização de Dados' acende-se no painel inferior. Sem o modo de depuração ligado, o Data Flow mostrar-lhe-á apenas os metadados atuais dentro e fora de cada uma das suas transformações no separador Inspecionar. A pré-visualização de dados só irá consultar o número de linhas que definiu como o seu limite nas definições de depuração. Clique em **Refresh** para obter a pré-visualização de dados.

![Pré-visualização dos dados](media/data-flow/datapreview.png "Pré-visualização dos dados")

> [!NOTE]
> As fontes de ficheiro limitam apenas as linhas que vês, não as filas que estão a ser lidas. Para conjuntos de dados muito grandes, recomenda-se que pegue numa pequena parte desse ficheiro e o utilize para os seus testes. Pode selecionar um ficheiro temporário nas Definições de Debug para cada fonte que seja um tipo de conjunto de dados de ficheiros.

Ao correr no Modo Debug no Fluxo de Dados, os seus dados não serão escritos para a transformação da Pia. Uma sessão de Debug destina-se a servir de arnês de teste para as suas transformações. Os lavatórios não são necessários durante o depurador e são ignorados no fluxo de dados. Se desejar testar a escrita dos dados no seu Lavatório, execute o Fluxo de Dados de um Pipeline azure data factory e utilize a execução de Debug a partir de um pipeline.

### <a name="testing-join-conditions"></a>Testar condições de adesão

Quando os testes de unidade se juntam, existem ou procurem transformações, certifique-se de que utiliza um pequeno conjunto de dados conhecidos para o seu teste. Pode utilizar a opção Definições de Debug acima para definir um ficheiro temporário para utilizar para os seus testes. Isto é necessário porque ao limitar ou provar linhas de um grande conjunto de dados, não é possível prever quais as linhas e quais as teclas que serão lidas no fluxo para testes. O resultado não é determinístico, o que significa que as suas condições de adesão podem falhar.

### <a name="quick-actions"></a>Ações rápidas

Assim que vir a pré-visualização dos dados, pode gerar uma rápida transformação na máquina de escrever, remover ou fazer uma modificação numa coluna. Clique no cabeçalho da coluna e, em seguida, selecione uma das opções da barra de ferramentas de pré-visualização de dados.

![Ações rápidas](media/data-flow/quick-actions1.png "Ações rápidas")

Uma vez selecionada uma modificação, a pré-visualização de dados irá imediatamente atualizar. Clique em **Confirmar** no canto superior direito para gerar uma nova transformação.

![Ações rápidas](media/data-flow/quick-actions2.png "Ações rápidas")

**Typecast** e **Modificar** gerarão uma transformação de Coluna Derivada e **remover** irá gerar uma transformação Select.

![Ações rápidas](media/data-flow/quick-actions3.png "Ações rápidas")

> [!NOTE]
> Se editar o seu Fluxo de Dados, terá de rebuscar a pré-visualização de dados antes de adicionar uma transformação rápida.

### <a name="data-profiling"></a>Perfis de dados

Selecionando uma coluna no separador de pré-visualização de dados e clicando **em estatísticas** na barra de ferramentas de pré-visualização de dados irá aparecer um gráfico na extrema-direita da sua grelha de dados com estatísticas detalhadas sobre cada campo. A Azure Data Factory fará uma determinação com base na amostragem de dados de que tipo de gráfico a exibir. Os campos de alta cardealidade serão indefinidos para os gráficos NULO/NÃO NULO, enquanto dados categóricos e numéricos com baixa cardeal mostrarão gráficos de barras que mostram a frequência de valor dos dados. Você também verá o comprimento máximo/len de campos de cordas, valores min/max em campos numéricos, dev padrão, percentículos, contagens e média.

![Estatísticas da coluna](media/data-flow/stats.png "Estatísticas da coluna")

## <a name="next-steps"></a>Passos seguintes

* Assim que terminar de construir e depurar o fluxo de dados, [execute-o a partir de um oleoduto.](control-flow-execute-data-flow-activity.md)
* Ao testar o seu pipeline com um fluxo de dados, utilize a opção de [execução de execução de depuração do gasoduto Debug.](iterative-development-debugging.md)
