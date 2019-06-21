---
title: Modo de depuração do fluxo de dados de mapeamento de fábrica de dados do Azure
description: Inicie uma sessão de depuração interativos quando construção com dados de fluxos
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147373"
---
# <a name="mapping-data-flow-debug-mode"></a>Modo de depuração de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Modo de depuração do Azure Data Factory mapeamento de fluxo de dados pode ser ativado com o botão "Dados de fluxo Debug" na parte superior da superfície de design. Quando a criação de fluxos de dados, ativando o modo de depuração permitirá que veja interativamente os dados a transformação de forma, enquanto que criar e depurar seus fluxos de dados. A sessão de depuração pode ser usada tanto em sessões de design de fluxo de dados, bem como durante a execução de depuração do pipeline de fluxos de dados.

![Botão de depuração](media/data-flow/debugbutton.png "botão de depuração")

## <a name="overview"></a>Descrição geral
Quando o modo de depuração está ativado, irá criar interativamente o fluxo de dados com um cluster do Spark Active Directory. A sessão será fechada depois de desativar depuração no Azure Data Factory. Deve estar ciente de custos por hora, incorridos ao Azure Databricks durante o tempo que tem a sessão de depuração ativada.

Na maioria dos casos, é uma boa prática para criar seus fluxos de dados no modo de depuração para que possa validar sua lógica de negócio e ver as transformações de dados antes de publicar o seu trabalho no Azure Data Factory. Utilize o botão de "Depuração" no painel de pipeline para testar o fluxo de dados dentro de um pipeline.

> [!NOTE]
> Embora a luz de modo de depuração seja verde na barra de ferramentas do Data Factory, será cobrado à taxa de depuração fluxo de dados de 8 núcleos/horas de computação geral com um minuto de 60 time-to-live 

> [!NOTE]
>Quando em execução no modo de depuração no fluxo de dados, os dados não serão escritos para o Sink de transformação. Uma sessão de depuração deve ser usado como equipamento de teste para as transformações. Coletores de não serem necessários durante a depuração e são ignoradas no seu fluxo de dados. Se pretender testar a gravação dos dados no seu coletor, executar o fluxo de dados a partir de um Pipeline de fábrica de dados do Azure e utilize a execução de depuração a partir de um pipeline.

## <a name="debug-settings"></a>Configurações de depuração
Depurar as definições podem ser editadas ao clicar em "Definições de depuração" na barra de ferramentas de tela do fluxo de dados. Pode selecionar os limites de e/ou a origem de ficheiros a utilizar para cada um dos aqui as transformações de origem. Os limites de linha nesta definição são apenas para a atual sessão de depuração. Também pode selecionar o serviço ligado de transição para ser utilizado para uma origem de armazém de dados SQL. 

![Configurações de depuração](media/data-flow/debug-settings.png "configurações de depuração")

## <a name="cluster-status"></a>Estado do cluster
Há um indicador de estado do cluster na parte superior da superfície de design que fica verde quando o cluster estiver pronto para depuração. Se o cluster já está quente, em seguida, o indicador de verde aparecerá quase instantaneamente. Se o cluster já não estava em execução ao introduzir o modo de depuração, em seguida, terá de aguardar 5 a 7 minutos para o cluster de girar. O indicador girará até seu pronto.

Quando tiver terminado com a sua depuração, desative a opção de depuração para que o cluster do Azure Databricks pode terminar e já não será cobrado para a atividade de depuração.

## <a name="data-preview"></a>Pré-visualização de dados
Com depuração no, a guia visualização de dados será light cópia de segurança no painel inferior. Sem o modo de depuração no fluxo de dados mostrará apenas os metadados atual e para cada um das transformações no separador Inspect. A pré-visualização de dados só irá consultar o número de linhas que tiver definido como o limite de nas suas definições de depuração. Poderá ter de clicar em "Obter dados" para atualizar a pré-visualização de dados.

![Pré-visualização de dados](media/data-flow/datapreview.png "pré-visualização de dados")

## <a name="data-profiles"></a>Perfis de dados
Selecionar colunas individuais na sua aba de pré-visualização de dados é exibido um gráfico na extremidade direita da sua grade de dados com estatísticas detalhadas sobre cada campo. O Azure Data Factory fará com que a determinação com base na amostragem de dados, o tipo de gráfico para apresentar. Campos de alta cardinalidade usará como padrão para gráficos nulo/não nulo, enquanto os dados categóricos e numéricos que tem a cardinalidade baixa apresentará os gráficos de barras que mostra a frequência de valor de dados. Também verá comprimento máximo/len de campos de cadeia de caracteres, valores mínimos/máximos em campos numéricos, de desenvolvimento padrão, percentis, contagens e média. 

![Estatísticas de coluna](media/data-flow/stats.png "estatísticas de coluna")

## <a name="next-steps"></a>Passos Seguintes

Quando tiver terminado de criar e depurar o fluxo de dados, [executá-lo a partir de um pipeline.](control-flow-execute-data-flow-activity.md)

Ao testar o seu pipeline com um fluxo de dados, utilizar o pipeline [depuração executa a opção de execução.](iterative-development-debugging.md)
