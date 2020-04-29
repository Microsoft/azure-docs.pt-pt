---
title: 'Dados de Importação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de dados de importação em Azure Machine Learning para carregar dados num pipeline de aprendizagem automática a partir de serviços de dados em nuvem existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456204"
---
# <a name="import-data-module"></a>Módulo de Dados de Importação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para carregar dados num pipeline de aprendizagem automática a partir dos serviços de dados em nuvem existentes. 

> [!Note]
> Todas as funcionalidades fornecidas por este módulo podem ser feitas por **datastore** e **conjuntos** de dados na página de aterragem worksapce. Recomendamos que utilize **datastore** e **dataset,** que inclua funcionalidades adicionais como monitorização de dados. Para saber mais, consulte [como aceder a dados](../how-to-access-data.md) e como registar o artigo [Datasets.](../how-to-create-register-datasets.md)
> Depois de registar um conjunto de dados, pode encontrá-lo na categoria **Datasets** -> **My Datasets** na interface do designer. Este módulo está reservado para utilizadores do Studio (clássico) para uma experiência familiar. 
>

O suporte do módulo **de dados de importação** lê dados de fontes seguintes:

- URL via HTTP
- Armazenamentos em nuvem azure através [**de Datastores)**](../how-to-access-data.md)
    - Recipiente de blob azure
    - Partilha de Ficheiros do Azure
    - Azure Data Lake
    - Lago de Dados Azure Gen2
    - Base de Dados SQL do Azure
    - Azure PostgreSQL    

Antes de utilizar o armazenamento em nuvem, tem de registar primeiro uma loja de dados no seu espaço de trabalho Azure Machine Learning. Para mais informações, consulte [Como Aceder aos Dados](../how-to-access-data.md). 

Depois de definir os dados que pretende e ligar à fonte, **[os Dados de Importação](./import-data.md)** inferem o tipo de dados de cada coluna com base nos valores que contém e carregam os dados no seu pipeline de design. A produção de Dados de **Importação** é um conjunto de dados que pode ser usado com qualquer pipeline de design.

Se os seus dados de origem forem alterações, pode atualizar o conjunto de dados e adicionar novos dados reexecutando [dados de importação](./import-data.md).

## <a name="how-to-configure-import-data"></a>Como configurar dados de importação

1. Adicione o módulo **de Dados de Importação** ao seu pipeline. Pode encontrar este módulo na categoria **De Entrada e Saída** de Dados no designer.

1. Selecione o módulo para abrir o painel direito.

1. Selecione **fonte de dados**e escolha o tipo de fonte de dados. Pode ser HTTP ou datastore.

    Se escolher a datastore, pode selecionar as lojas de dados existentes que já estão registadas no seu espaço de trabalho azure Machine Learning ou criar uma nova loja de dados. Em seguida, defina o caminho dos dados para importar na loja de dados. Você pode facilmente navegar no caminho clicando **em Caminho de** ![Navegação-caminho de importação-data](media/module/import-data-path.png)

1. Selecione o esquema de pré-visualização para filtrar as colunas que pretende incluir. Também pode definir configurações avançadas como Delimiter em opções de Parsing.

    ![importação-dados-pré-visualização](media/module/import-data.png)

1. A caixa de verificação, regerar a **saída,** decide se executa o módulo para regenerar a saída no tempo de funcionamento. 

    É por padrão não selecionado, o que significa que se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de funcionar. 

    Se for selecionado, o sistema executará novamente o módulo para regenerar a saída. Por isso, selecione esta opção quando os dados subjacentes no armazenamento forem atualizados, pode ajudar a obter os dados mais recentes.


1. Submeta o oleoduto.

    Quando os Dados de Importação carregam os dados no designer, infere o tipo de dados de cada coluna com base nos valores que contém, numéricos ou categóricos.

    Se estiver presente um cabeçalho, o cabeçalho é utilizado para nomear as colunas do conjunto de dados de saída.

    Se não existirem cabeçalhos de coluna existentes nos dados, novos nomes de colunas são gerados usando o formato col1, col2,... , coln*.

## <a name="results"></a>Resultados

Quando a importação estiver concluída, clique no conjunto de dados de saída e **selecione Visualize** para ver se os dados foram importados com sucesso.

Se pretender guardar os dados para reutilização, em vez de importar um novo conjunto de dados sempre que o gasoduto for executado, selecione o ícone de conjunto de **dados Do Registo** sob o separador **Saídas** no painel certo do módulo. Escolha um nome para o conjunto de dados. O conjunto de dados guardado preserva os dados no momento da poupança, o conjunto de dados não é atualizado quando o gasoduto é reexecutado, mesmo que o conjunto de dados no pipeline mude. Isto pode ser útil para tirar fotos de dados.

Após a importação dos dados, pode necessitar de alguns preparativos adicionais para modelação e análise:

- Utilize a [Edição de Metadados](./edit-metadata.md) para alterar nomes de colunas, para manusear uma coluna como um tipo de dados diferente, ou para indicar que algumas colunas são etiquetas ou funcionalidades.

- Utilize [colunas selecione no Dataset](./select-columns-in-dataset.md) para selecionar um subconjunto de colunas para transformar ou utilizar na modelação. As colunas transformadas ou removidas podem ser facilmente reunidas ao conjunto de dados original utilizando o módulo [Adicionar Colunas.](./add-columns.md)  

- Utilize [a Partilha e a Amostra](./partition-and-sample.md) para dividir o conjunto de dados, efetuar amostras ou obter as linhas de topo n.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
