---
title: Conjuntos de dados de acesso com a biblioteca de cliente Python - Team Data Science Process
description: Instalar e utilizar a biblioteca de cliente Python para aceder e gerir dados do Azure Machine Learning com segurança a partir de um ambiente local do Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720984"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Utilizar a biblioteca de clientes de Python do Azure Machine Learning para aceder a conjuntos de dados com Python
A pré-visualização da biblioteca de clientes do Microsoft Azure Machine Learning Python pode permitir o acesso seguro aos conjuntos de dados do Azure Machine Learning de um ambiente de Python local e permite a criação e gestão de conjuntos de dados numa área de trabalho.

Este tópico fornece instruções sobre como:

* instalar a biblioteca de cliente Python do Machine Learning
* aceder e carregar conjuntos de dados, incluindo instruções sobre como obter autorização para aceder a conjuntos de dados do Azure Machine Learning do seu ambiente local do Python
* conjuntos de dados intermediários do acesso de experimentações
* usar a biblioteca de clientes Python para enumerar conjuntos de dados, aceder metadados, ler o conteúdo de um conjunto de dados, criar novos conjuntos de dados e atualizar conjuntos de dados existentes

## <a name="prerequisites"></a>Pré-requisitos
A biblioteca de cliente Python foi testada em seguintes ambientes:

* Windows, Mac e Linux
* Python 2.7, 3.3 e 3.4

Ele tem uma dependência nos seguintes pacotes:

* pedidos
* python-dateutil
* pandas

Recomendamos a utilização de uma distribuição Python, como [a Anaconda](http://continuum.io/downloads#all) ou [canopy,](https://store.enthought.com/downloads/)que vem com Python, IPython e os três pacotes listados acima instalados. Embora o IPython não é estritamente necessário, é um ótimo ambiente para manipular e visualizar dados de forma interativa.

### <a name="installation"></a>Como instalar a biblioteca de clientes Azure Machine Learning Python
Instale a biblioteca de clientes Azure Machine Learning Python para completar as tarefas descritas neste tópico. Esta biblioteca está disponível a partir do Índice de [Pacotes Python.](https://pypi.python.org/pypi/azureml) Para instalá-lo no seu ambiente de Python, execute o seguinte comando do seu ambiente de Python local:

    pip install azureml

Em alternativa, pode descarregar e instalar a partir das fontes do [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se tiver o git instalado no seu computador, pode utilizar o pip para instalar diretamente a partir do repositório de git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Utilize códigos para aceder a conjuntos de dados
A biblioteca de cliente Python oferece acesso programático a seus conjuntos de dados existentes de experimentações que tenham sido executadas.

A partir da interface web do Azure Machine Learning Studio (clássico), pode gerar fragmentos de código que incluem todas as informações necessárias para descarregar e desserializar conjuntos de dados como objetos Pandas DataFrame na sua máquina local.

### <a name="security"></a>Segurança para acesso a dados
Os códigos fornecidos pelo Azure Machine Learning Studio (clássico) para uso com a biblioteca de clientes Python inclui o seu ID de espaço de trabalho e ficha de autorização. Estes disponibilizar acesso total à sua área de trabalho e têm de ser protegidos, como uma palavra-passe.

Por razões de segurança, a funcionalidade de snippet de código só está disponível para utilizadores que tenham a sua função definida como **Proprietário** para o espaço de trabalho. O seu papel é apresentado no Azure Machine Learning Studio (clássico) na página **UTILIZADORES** em **Definições**.

![Segurança][security]

Se o seu papel não for definido como **Proprietário,** pode solicitar para ser reconvidado como proprietário, ou pedir ao proprietário do espaço de trabalho que lhe forneça o código.

Para obter o sinal de autorização, pode escolher uma destas opções:

* Pedir um token de um proprietário. Os proprietários podem aceder às suas fichas de autorização a partir da página Definições do seu espaço de trabalho no Azure Machine Learning Studio (clássico). Selecione **Definições** a partir do painel esquerdo e clique em **TOKENS de AUTORIZAÇÃO** para ver as fichas primárias e secundárias. Embora principal ou tokens de autorização secundário podem ser usados no trecho de código, recomenda-se que os proprietários de partilham apenas tokens de autorização secundário.

   ![Tokens de autorização](./media/python-data-access/ml-python-access-settings-tokens.png)

* Peça para ser promovido ao papel de proprietário: um atual proprietário do espaço de trabalho precisa primeiro removê-lo do espaço de trabalho e depois convidá-lo para ele como proprietário.

Uma vez que os desenvolvedores obtenham o ID do espaço de trabalho e o símbolo de autorização, eles são capazes de aceder ao espaço de trabalho usando o código snippet independentemente do seu papel.

As fichas de autorização são geridas na página **TOKENS** de AUTORIZAÇÃO em **DEFINIÇÕES**. Pode voltar a gerá-los, mas este procedimento revoga o acesso ao token anterior.

### <a name="accessingDatasets"></a>Aceder a conjuntos de dados de uma aplicação python local
1. No Machine Learning Studio (clássico), clique EM **DATASETS** na barra de navegação à esquerda.
2. Selecione o conjunto de dados que pretende aceder. Pode selecionar qualquer um dos conjuntos de dados da lista **MY DATASETS** ou da lista **DE AMOSTRAS.**
3. A partir da barra de ferramentas inferior, clique **em Gerar Código**de Acesso de Dados . Se os dados estão num formato incompatível com a biblioteca de cliente de Python, este botão está desativado.
   
    ![Conjuntos de dados][datasets]
4. Selecione o fragmento de código a partir da janela que aparece e copie-o para a área de transferência.
   
    ![Botão de código de acesso de dados gerar][dataset-access-code]
5. Cole o código no bloco de notas da sua aplicação Python local.
   
    ![Cole o código no bloco de notas][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Aceder a conjuntos de dados intermédios a partir de experiências de Machine Learning
Depois que um experimento é executado no Machine Learning Studio (clássico), é possível acessar os conjuntos de resultados intermediários dos nós de saída dos módulos. Conjuntos de dados intermediários são dados que foi criados e utilizados para obter os passos intermédios quando uma ferramenta de modelo tiver sido executada.

Conjuntos de dados intermediários podem ser acedidos, desde que o formato de dados é compatível com a biblioteca de cliente Python.

São suportados os seguintes formatos (as constantes para estes formatos estão na classe `azureml.DataTypeIds`):

* Texto sem formatação
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Pode determinar o formato passando o mouse sobre um nó de saída do módulo. É apresentado junto com o nome de nó, numa descrição.

Alguns dos módulos, como o módulo [Split,][split] são saídas para um formato chamado `Dataset`, que não é suportado pela biblioteca cliente Python.

![Formato de conjunto de dados][dataset-format]

É necessário utilizar um módulo de conversão, como [converter para CSV,][convert-to-csv]para obter uma saída num formato suportado.

![Formato de GenericCSV][csv-format]

Os passos seguintes mostram um exemplo que cria uma experimentação, executa-o e acessa o conjunto de dados intermediário.

1. Crie uma nova experimentação.
2. Insira um módulo de conjunto de dados de **classificação binária** de rendimento de censos adultos.
3. Insira um módulo [Split][split] e ligue a sua entrada à saída do módulo dataset.
4. Insira um módulo [Converte para CSV][convert-to-csv] e ligue a sua entrada a uma das saídas do módulo [Split.][split]
5. Salve a experiência, execute-a, e espere que o trabalho termine.
6. Clique no nó de saída do módulo [Converte para CSV.][convert-to-csv]
7. Quando o menu de contexto aparecer, **selecione Generate Data Access Code**.
   
    ![Menu de Contexto][experiment]
8. Selecione o fragmento de código e copiá-lo para a área de transferência a partir da janela que aparece.
   
    ![Gerar código de acesso a partir do menu de contexto][intermediate-dataset-access-code]
9. Cole o código no seu bloco de notas.
   
    ![Cole o código no bloco de notas][ipython-intermediate-dataset]
10. É possível visualizar os dados usando matplotlib. Isto é apresentado num histograma da coluna de idade:
    
    ![Histograma][ipython-histogram]

## <a name="clientApis"></a>Utilize a biblioteca de clientes Machine Learning Python para aceder, ler, criar e gerir conjuntos de dados
### <a name="workspace"></a>Área de trabalho
A área de trabalho é o ponto de entrada para a biblioteca de cliente Python. Forneça a classe `Workspace` com o seu ID do espaço de trabalho e o token de autorização para criar uma instância:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar conjuntos de dados
Para enumerar todos os conjuntos de dados numa determinada área de trabalho:

    for ds in ws.datasets:
        print(ds.name)

Para enumerar apenas os criados pelo utilizador conjuntos de dados:

    for ds in ws.user_datasets:
        print(ds.name)

Para enumerar apenas os conjuntos de dados de exemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Pode aceder a um conjunto de dados por nome (o que diferencia maiúsculas de minúsculas):

    ds = ws.datasets['my dataset name']

Ou pode acessá-lo pelo índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadados
Conjuntos de dados tem metadados, além de conteúdo. (Conjuntos de dados intermediários são uma exceção a esta regra e não tem quaisquer metadados).

Alguns valores de metadados são atribuídos pelo usuário no momento de criação:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Outras são valores atribuídos pelo Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte a `SourceDataset` aula para saber mais sobre os metadados disponíveis.

### <a name="read-contents"></a>Ler o conteúdo
Os fragmentos de código fornecidos pelo Machine Learning Studio (clássico) descarregam e desserializam automaticamente o conjunto de dados para um objeto dataFrame pandas. Isto é feito com o método `to_dataframe`:

    frame = ds.to_dataframe()

Se preferir transferir os dados não processados e efetuar a desserialização por conta própria, que é uma opção. No momento, esta é a única opção para formatos como "ARFF', não é possível anular a serialização da biblioteca de cliente Python.

Para ler o conteúdo como texto:

    text_data = ds.read_as_text()

Para ler o conteúdo como binário:

    binary_data = ds.read_as_binary()

Pode também simplesmente abrir um fluxo para o conteúdo:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Criar um novo conjunto de dados
A biblioteca de cliente Python permite-lhe carregar conjuntos de dados de seu programa de Python. Estes conjuntos de dados, em seguida, estão disponíveis para utilização na sua área de trabalho.

Se tiver os seus dados num pandas DataFrame, utilize o seguinte código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se os dados já são serializados, pode usar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A biblioteca de clientes Python é capaz de serializar um pandas DataFrame para os seguintes formatos (constantes para estes estão na classe `azureml.DataTypeIds`):

* Texto sem formatação
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de dados existente
Se tentar carregar um novo conjunto de dados com um nome que corresponde a um conjunto de dados existente, obterá um erro de conflito.

Para atualizar um conjunto de dados existente, tem primeiro de obter uma referência ao conjunto de dados existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Em seguida, utilize `update_from_dataframe` para serializar e substituir o conteúdo do conjunto de dados no Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se pretender serializar os dados para um formato diferente, especifique um valor para o parâmetro opcional `data_type_id`.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pode definir opcionalmente uma nova descrição especificando um valor para o parâmetro `description`.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Pode definir opcionalmente um novo nome especificando um valor para o parâmetro `name`. De agora em diante, irá obter o conjunto de dados com o novo nome. O código seguinte atualiza os dados, nome e descrição.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Os parâmetros `data_type_id`, `name` e `description` são opcionais e indefinidos ao valor anterior. O parâmetro `dataframe` é sempre necessário.

Se os seus dados já estiverem serializados, utilize `update_from_raw_data` em vez de `update_from_dataframe`. Se passarem por `raw_data` em vez de `dataframe`, funciona da mesma forma.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

