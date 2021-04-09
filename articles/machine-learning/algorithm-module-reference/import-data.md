---
title: 'Dados de Importação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de Dados de Importação em Azure Machine Learning para carregar dados num pipeline de aprendizagem automática a partir dos serviços de dados em nuvem existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 69d27c102ca059974da87224e44f0ad7aa103fff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592639"
---
# <a name="import-data-module"></a>Módulo de Dados de Importação

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para carregar dados num oleoduto de aprendizagem automática a partir dos serviços de dados em nuvem existentes. 

> [!Note]
> Todas as funcionalidades fornecidas por este módulo podem ser feitas por **datastore** e **conjuntos de dados** na página de aterragem do WorkApce. Recomendamos que utilize **datastore** e **conjunto de dados** que inclua funcionalidades adicionais como monitorização de dados. Para saber mais, consulte [Como Aceder aos Dados](../how-to-access-data.md) e [Como Registar Datasets.](../how-to-create-register-datasets.md)
> Depois de registar um conjunto de dados, pode encontrá-lo na categoria **Datasets**  ->  **My Datasets** na interface do designer. Este módulo está reservado para utilizadores do Studio(clássico) para uma experiência familiar. 
>

O suporte do módulo **de dados de importação** lê dados de fontes seguintes:

- URL via HTTP
- Armazenamento em nuvem Azure através de [**Datastores)**](../how-to-access-data.md)
    - Recipiente Azure Blob
    - Partilha de Ficheiros do Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Base de Dados SQL do Azure
    - Azure PostgreSQL    

Antes de utilizar o armazenamento em nuvem, tem de registar primeiro uma loja de dados no seu espaço de trabalho Azure Machine Learning. Para mais informações, consulte [Como Aceder aos Dados.](../how-to-access-data.md) 

Depois de definir os dados que pretende e ligar à fonte, os **[Dados](./import-data.md)** de Importação inferem o tipo de dados de cada coluna com base nos valores que contém e carregam os dados no seu pipeline de designers. A saída de **Dados** de Importação é um conjunto de dados que pode ser usado com qualquer pipeline de designer.

Se os dados de origem alterarem, pode atualizar o conjunto de dados e adicionar novos dados repetindo [dados de importação.](./import-data.md)

> [!WARNING]
> Se o seu espaço de trabalho estiver numa rede virtual, tem de configurar as suas datastores para utilizar as funcionalidades de visualização de dados do designer. Para obter mais informações sobre como utilizar datastores e conjuntos de dados numa rede virtual, consulte [o estúdio Use Azure Machine Learning numa rede virtual Azure](../how-to-enable-studio-virtual-network.md).


## <a name="how-to-configure-import-data"></a>Como configurar dados de importação

1. Adicione o módulo **de Dados de Importação** ao seu oleoduto. Pode encontrar este módulo na categoria entrada e saída de **dados** no designer.

1. Selecione o módulo para abrir o painel direito.

1. Selecione **Data source**, e escolha o tipo de fonte de dados. Pode ser HTTP ou datastore.

    Se escolher a datastore, pode selecionar as datas-lojas existentes que já se registaram no seu espaço de trabalho Azure Machine Learning ou criar uma nova loja de dados. Em seguida, defina o caminho dos dados para importar na datastore. Pode navegar facilmente no caminho clicando **No Screenshot do caminho de navegação** mostra a ![ ligação do caminho de Navegação que abre a caixa de diálogo de seleção path.](media/module/import-data-path.png)

    > [!NOTE]
    > O módulo **de dados de importação** destina-se apenas aos dados **tabulares.**
    > Se pretender importar vários ficheiros de dados tabulares uma vez, requer as seguintes condições, caso contrário ocorrerão erros:
    > 1. Para incluir todos os ficheiros de dados na pasta, é necessário inserir `folder_name/**` o **Path**.
    > 2. Todos os ficheiros de dados devem ser codificados no unicode-8.
    > 3. Todos os ficheiros de dados devem ter os mesmos números de colunas e nomes de colunas.
    > 4. O resultado da importação de vários ficheiros de dados está a concatenar todas as linhas de vários ficheiros em ordem.

1. Selecione o esquema de pré-visualização para filtrar as colunas que pretende incluir. Também pode definir configurações avançadas como Delimiter nas opções de Parsing.

    ![pré-visualização de dados de importação](media/module/import-data.png)

1. A caixa de verificação, **Regenerar a produção,** decide se executa o módulo para regenerar a saída em tempo de execução. 

    É por defeito não selecionado, o que significa que se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de funcionamento. 

    Se for selecionado, o sistema executará novamente o módulo para regenerar a saída. Por isso, selecione esta opção quando os dados subjacentes ao armazenamento são atualizados, pode ajudar a obter os dados mais recentes.


1. Envie o oleoduto.

    Quando os Dados de Importação carregam os dados no designer, infere o tipo de dados de cada coluna com base nos valores que contém, numéricos ou categóricos.

    Se estiver presente um cabeçalho, o cabeçalho é utilizado para nomear as colunas do conjunto de dados de saída.

    Se não houver cabeçalhos de coluna existentes nos dados, novos nomes de colunas são gerados usando o formato col1, col2,... Coln*.

## <a name="results"></a>Resultados

Quando a importação estiver concluída, clique com o botão direito no conjunto de dados de saída e **selecione Visualize** para ver se os dados foram importados com sucesso.

Se pretender guardar os dados para reutilização, em vez de importar um novo conjunto de dados cada vez que o pipeline for executado, selecione o ícone **do conjunto de dados Do Registo** sob o separador **Saídas+logs** no painel direito do módulo. Escolha um nome para o conjunto de dados. O conjunto de dados guardado preserva os dados no momento da poupança, o conjunto de dados não é atualizado quando o pipeline é reexecutado, mesmo que o conjunto de dados no pipeline mude. Isto pode ser útil para tirar fotos de dados.

Após a importação dos dados, poderá necessitar de preparações adicionais para modelação e análise:

- Utilize [metadados](./edit-metadata.md) de edição para alterar nomes de colunas, para manusear uma coluna como um tipo de dados diferente, ou para indicar que algumas colunas são etiquetas ou funcionalidades.

- Utilize [colunas selecionadas no conjunto](./select-columns-in-dataset.md) de dados para selecionar um subconjunto de colunas para transformar ou utilizar na modelação. As colunas transformadas ou removidas podem ser facilmente reunidas para o conjunto de dados originais utilizando o módulo [Add Columns.](./add-columns.md)  

- Utilize [a partição e](./partition-and-sample.md) a amostra para dividir o conjunto de dados, efetuar a amostragem ou obter as linhas de cima n.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
