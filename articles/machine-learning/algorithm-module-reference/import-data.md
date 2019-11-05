---
title: 'Importar dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo importar dados no Azure Machine Learning para carregar dados em um pipeline de Machine Learning de serviços de dados em nuvem existentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 5fa8d3984c758d0bf95372864f3bffeb6f302c83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497804"
---
# <a name="import-data-module"></a>Importar módulo de dados

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para carregar dados em um pipeline de Machine Learning dos serviços de dados de nuvem existentes. 

> [!Note]
> Toda a funcionalidade fornecida por esse módulo pode ser feita pelo **armazenamento de datastore** e por **conjuntos de valores** na página de aterrissagem espaço. É recomendável que você use o **armazenamento** de dados e o **DataSet** , que inclui recursos adicionais, como o monitoramento de dados. Para obter mais informações, consulte [o artigo como acessar dados](../service/how-to-access-data.md) e [como registrar os DataSets](../service/how-to-create-register-datasets.md) .
> Depois de registrar um conjunto de registros, você pode encontrá-lo nos **conjuntos** de -> categoria **meus conjuntos de valores** na interface do designer. Este módulo é reservado para usuários do Studio (clássicos) para uma experiência familiar. 
>

Primeiro, escolha a origem da qual você está lendo e conclua as configurações adicionais. O módulo **importar dados** dá suporte à leitura de dados das seguintes fontes:

- URL via HTTP
- Armazenamentos em nuvem do Azure por meio de [**repositórios de armazenamento**](../service/how-to-access-data.md))
    - Contêiner de blob do Azure
    - Partilha de Ficheiros do Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Base de Dados SQL do Azure
    - PostgreSQL do Azure    

Antes de usar o armazenamento em nuvem, você precisa registrar um repositório de armazenamento em seu espaço de trabalho Azure Machine Learning primeiro. Para obter mais informações, consulte [como acessar dados](../service/how-to-access-data.md). 

Depois de definir os dados desejados e conectar-se à origem, **[importar dados](./import-data.md)** infere o tipo de dados de cada coluna com base nos valores que ele contém e carrega os dados em seu pipeline de designer. A saída de **Import data** é um conjunto de dados que pode ser usado com qualquer pipeline de designer.

Se os dados de origem forem alterados, você poderá atualizar o conjunto de dados e adicionar novos data executando novamente os [dados de importação](./import-data.md). No entanto, se você não quiser ler novamente a origem toda vez que executar o pipeline, defina a opção **usar resultados em cache** como true. Quando essa opção é selecionada, o módulo verifica se o pipeline foi executado anteriormente usando a mesma origem e as mesmas opções de entrada. Se uma execução anterior for encontrada, os dados no cache serão usados, em vez de recarregar os dados da origem.

## <a name="how-to-configure-import-data"></a>Como configurar dados de importação

1. Adicione o módulo **importar dados** ao seu pipeline. Você pode encontrar esse módulo na categoria **entrada e saída de dados** no designer.

1. Clique em **iniciar o assistente de importação de dados** para configurar a fonte de dados usando um assistente.

    O assistente Obtém o nome e as credenciais da conta e ajuda você a configurar outras opções. Se você estiver editando uma configuração existente, ela carregará os valores atuais primeiro.

1. Selecione **fonte de dados**e escolha o tipo de fonte de dados. Pode ser HTTP ou datastore.

    Se você escolher repositório de armazenamento, poderá selecionar os repositórios de armazenamento existentes que já estão registrados em seu espaço de trabalho Azure Machine Learning ou criar um novo repositório de armazenamento. Em seguida, defina o caminho dos dados a serem importados no datastore. Você pode navegar facilmente pelo caminho clicando em **procurar caminho** ![importar-Data-Path](media/module/import-data-path.png)

1. Selecione o esquema de visualização para filtrar as colunas que você deseja incluir. Você também pode definir configurações avançadas como delimitador nas opções de análise.

    ![importação-dados-visualização](media/module/import-data.png)

1. Selecione a opção **usar resultados em cache** se você quiser armazenar em cache o conjunto de armazenamento para reutilização em execuções sucessivas.

    Supondo que não houvesse nenhuma outra alteração nos parâmetros do módulo, o pipeline carrega os dados somente na primeira vez em que o módulo é executado e depois usa uma versão armazenada em cache do conjunto de dados.

    Desmarque essa opção se precisar recarregar os dados cada vez que executar o pipeline.

1. Executar o pipeline.

    Quando os dados de importação carregam os dados no designer, ele infere o tipo de dados de cada coluna com base nos valores que ele contém, numéricos ou categóricos.

    Se um cabeçalho estiver presente, o cabeçalho será usado para nomear as colunas do conjunto de resultados de saída.

    Se não houver nenhum cabeçalho de coluna existente nos dados, novos nomes de coluna serão gerados usando o formato Col1, Col2,... , coln*.

## <a name="results"></a>Resultados

Quando a importação for concluída, clique no conjunto de dados de saída e selecione **Visualizar** para ver se os dados foram importados com êxito.

Se você quiser salvar os dados para reutilização, em vez de importar um novo conjunto de dados cada vez que o pipeline for executado, clique com o botão direito do mouse na saída e selecione **salvar como DataSet**. Escolha um nome para o conjunto de um. O DataSet salvo preserva os dados no momento do salvamento, e os dados não são atualizados quando o pipeline é executado novamente, mesmo se o conjunto do dados no pipeline for alterado. Isso pode ser útil para tirar instantâneos de dados.

Depois de importar os dados, talvez sejam necessários alguns preparativos adicionais para modelagem e análise:

- Use [Editar metadados](./edit-metadata.md) para alterar nomes de coluna, para manipular uma coluna como um tipo de dados diferente ou para indicar que algumas colunas são rótulos ou recursos.

- Use [selecionar colunas no conjunto](./select-columns-in-dataset.md) de linhas para selecionar um subconjunto de colunas para transformar ou usar na modelagem. As colunas transformadas ou removidas podem ser facilmente reassociadas ao conjunto de os originais usando o módulo [adicionar colunas](./add-columns.md) .  

- Use [Partition e Sample](./partition-and-sample.md) para dividir o conjunto de registros, executar amostragem ou obter as n primeiras linhas.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
