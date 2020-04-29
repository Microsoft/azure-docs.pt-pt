---
title: Aceder a conjuntos de dados com biblioteca de clientes Python - Team Data Science Process
description: Instale e utilize a biblioteca de clientes Python para aceder e gerir os dados de Aprendizagem automática azure de forma segura a partir de um ambiente python local.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720984"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Utilizar a biblioteca de clientes de Python do Azure Machine Learning para aceder a conjuntos de dados com Python
A pré-visualização da biblioteca de clientes Microsoft Azure Machine Learning Python pode permitir o acesso seguro aos seus conjuntos de dados de Aprendizagem automática Azure a partir de um ambiente python local e permite a criação e gestão de conjuntos de dados num espaço de trabalho.

Este tópico fornece instruções sobre como:

* instalar a biblioteca cliente Machine Learning Python
* aceder e carregar conjuntos de dados, incluindo instruções sobre como obter autorização para aceder a conjuntos de dados de Aprendizagem automática azure a partir do seu ambiente python local
* aceder a conjuntos de dados intermédios a partir de experiências
* usar a biblioteca de clientes Python para enumerar conjuntos de dados, aceder metadados, ler o conteúdo de um conjunto de dados, criar novos conjuntos de dados e atualizar conjuntos de dados existentes

## <a name="prerequisites"></a><a name="prerequisites"></a>Pré-requisitos
A biblioteca de clientes Python foi testada nos seguintes ambientes:

* Windows, Mac e Linux
* Python 2.7, 3.3 e 3.4

Tem uma dependência dos seguintes pacotes:

* pedidos
* python-dateutil
* pandas

Recomendamos a utilização de uma distribuição Python, como [a Anaconda](http://continuum.io/downloads#all) ou [canopy,](https://store.enthought.com/downloads/)que vem com Python, IPython e os três pacotes listados acima instalados. Embora o IPython não seja estritamente necessário, é um ótimo ambiente para manipular e visualizar dados interativamente.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Como instalar a biblioteca de clientes Azure Machine Learning Python
Instale a biblioteca de clientes Azure Machine Learning Python para completar as tarefas descritas neste tópico. Esta biblioteca está disponível a partir do Índice de [Pacotes Python.](https://pypi.python.org/pypi/azureml) Para instalá-lo no seu ambiente Python, execute o seguinte comando do seu ambiente python local:

    pip install azureml

Em alternativa, pode descarregar e instalar a partir das fontes do [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se tiver git instalado na sua máquina, pode utilizar pip para instalar diretamente a partir do repositório git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Utilize códigos para aceder a conjuntos de dados
A biblioteca de clientes Python dá-lhe acesso programático aos seus conjuntos de dados existentes a partir de experiências que foram executadas.

A partir da interface web do Azure Machine Learning Studio (clássico), pode gerar fragmentos de código que incluem todas as informações necessárias para descarregar e desserializar conjuntos de dados como objetos Pandas DataFrame na sua máquina local.

### <a name="security-for-data-access"></a><a name="security"></a>Segurança para acesso a dados
Os códigos fornecidos pelo Azure Machine Learning Studio (clássico) para uso com a biblioteca de clientes Python inclui o seu ID de espaço de trabalho e ficha de autorização. Estes fornecem acesso total ao seu espaço de trabalho e devem ser protegidos, como uma palavra-passe.

Por razões de segurança, a funcionalidade de snippet de código só está disponível para utilizadores que tenham a sua função definida como **Proprietário** para o espaço de trabalho. O seu papel é apresentado no Azure Machine Learning Studio (clássico) na página **UTILIZADORES** em **Definições**.

![Segurança][security]

Se o seu papel não for definido como **Proprietário,** pode solicitar para ser reconvidado como proprietário, ou pedir ao proprietário do espaço de trabalho que lhe forneça o código.

Para obter o sinal de autorização, pode escolher uma destas opções:

* Peça um sinal de um dono. Os proprietários podem aceder às suas fichas de autorização a partir da página Definições do seu espaço de trabalho no Azure Machine Learning Studio (clássico). Selecione **Definições** a partir do painel esquerdo e clique em **TOKENS de AUTORIZAÇÃO** para ver as fichas primárias e secundárias. Embora as fichas de autorização primária ou secundária possam ser utilizadas no código, recomenda-se que os proprietários partilhem apenas os tokens de autorização secundária.

   ![Fichas de autorização](./media/python-data-access/ml-python-access-settings-tokens.png)

* Peça para ser promovido ao papel de proprietário: um atual proprietário do espaço de trabalho precisa primeiro removê-lo do espaço de trabalho e depois convidá-lo para ele como proprietário.

Uma vez que os desenvolvedores obtenham o ID do espaço de trabalho e o símbolo de autorização, eles são capazes de aceder ao espaço de trabalho usando o código snippet independentemente do seu papel.

As fichas de autorização são geridas na página **TOKENS** de AUTORIZAÇÃO em **DEFINIÇÕES**. Pode regenerar, mas este procedimento revoga o acesso ao símbolo anterior.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Aceder a conjuntos de dados de uma aplicação python local
1. No Machine Learning Studio (clássico), clique EM **DATASETS** na barra de navegação à esquerda.
2. Selecione o conjunto de dados a que gostaria de aceder. Pode selecionar qualquer um dos conjuntos de dados da lista **MY DATASETS** ou da lista **DE AMOSTRAS.**
3. A partir da barra de ferramentas inferior, clique **em Gerar Código**de Acesso de Dados . Se os dados estiverem num formato incompatível com a biblioteca de clientes Python, este botão é desativado.
   
    ![Conjuntos de dados][datasets]
4. Selecione o corte de código da janela que aparece e copie-o para a sua área de prancheta.
   
    ![Gerar botão de código de acesso a dados][dataset-access-code]
5. Colhe o código no caderno da sua aplicação python local.
   
    ![Código de pasta no caderno][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Aceder a conjuntos de dados intermédios a partir de experiências de Machine Learning
Depois de uma experiência ser executada no Machine Learning Studio (clássico), é possível aceder aos conjuntos de dados intermédios a partir dos nódos de saída dos módulos. Conjuntos de dados intermédios são dados que foram criados e utilizados para passos intermédios quando uma ferramenta modelo foi executada.

Os conjuntos de dados intermédios podem ser acedidos desde que o formato de dados seja compatível com a biblioteca cliente Python.

São suportados os seguintes formatos (as `azureml.DataTypeIds` constantes para estes formatos estão na classe):

* PlainText
* Genéricocsv
* Genérico
* Genericcsvnoheader
* Generictsvnoheader

Pode determinar o formato pairando sobre um nó de saída do módulo. É exibido juntamente com o nome do nó, numa ponta de ferramenta.

Alguns dos módulos, como o módulo [Split,][split] são saídas para um formato chamado `Dataset`, que não é suportado pela biblioteca cliente Python.

![Formato dataset][dataset-format]

É necessário utilizar um módulo de conversão, como [converter para CSV,][convert-to-csv]para obter uma saída num formato suportado.

![Formato GenericcsV][csv-format]

Os seguintes passos mostram um exemplo que cria uma experiência, executa-a e acede ao conjunto de dados intermédio.

1. Criar uma nova experiência.
2. Insira um módulo de conjunto de dados de **classificação binária** de rendimento de censos adultos.
3. Insira um módulo [Split][split] e ligue a sua entrada à saída do módulo dataset.
4. Insira um módulo [Converte para CSV][convert-to-csv] e ligue a sua entrada a uma das saídas do módulo [Split.][split]
5. Salve a experiência, execute-a, e espere que o trabalho termine.
6. Clique no nó de saída do módulo [Converte para CSV.][convert-to-csv]
7. Quando o menu de contexto aparecer, **selecione Generate Data Access Code**.
   
    ![Menu de Contexto][experiment]
8. Selecione o corte de código e copie-o para a sua prancheta a partir da janela que aparece.
   
    ![Gerar código de acesso a partir do menu de contexto][intermediate-dataset-access-code]
9. Cola o código no teu caderno.
   
    ![Código de pasta em caderno][ipython-intermediate-dataset]
10. Pode visualizar os dados usando matplotlib. Isto exibe num histograma para a coluna etária:
    
    ![Histograma][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Utilize a biblioteca de clientes Machine Learning Python para aceder, ler, criar e gerir conjuntos de dados
### <a name="workspace"></a>Área de trabalho
O espaço de trabalho é o ponto de entrada para a biblioteca de clientes Python. Forneça `Workspace` à classe o seu ID do espaço de trabalho e o token de autorização para criar uma instância:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar conjuntos de dados
Para enumerar todos os conjuntos de dados num determinado espaço de trabalho:

    for ds in ws.datasets:
        print(ds.name)

Para enumerar apenas os conjuntos de dados criados pelo utilizador:

    for ds in ws.user_datasets:
        print(ds.name)

Para enumerar apenas os conjuntos de dados exemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Pode aceder a um conjunto de dados pelo nome (que é sensível a casos):

    ds = ws.datasets['my dataset name']

Ou pode acessá-lo por índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadados
Os conjuntos de dados têm metadados, além de conteúdos. (Os conjuntos de dados intermédios são uma exceção a esta regra e não têm metadados.)

Alguns valores de metadados são atribuídos pelo utilizador no momento da criação:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Outros são valores atribuídos pelo Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte `SourceDataset` a aula para mais informações sobre os metadados disponíveis.

### <a name="read-contents"></a>Ler conteúdos
Os fragmentos de código fornecidos pelo Machine Learning Studio (clássico) descarregam e desserializam automaticamente o conjunto de dados para um objeto dataFrame pandas. Isto é feito `to_dataframe` com o método:

    frame = ds.to_dataframe()

Se preferir descarregar os dados brutos e realizar a desserialização por si mesmo, essa é uma opção. Neste momento, esta é a única opção para formatos como 'ARFF', que a biblioteca de clientes Python não pode desserializar.

Para ler o conteúdo como texto:

    text_data = ds.read_as_text()

Para ler o conteúdo como binário:

    binary_data = ds.read_as_binary()

Também pode abrir um fluxo para o conteúdo:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Criar um novo conjunto de dados
A biblioteca de clientes Python permite-lhe fazer o upload de conjuntos de dados do seu programa Python. Estes conjuntos de dados estão então disponíveis para utilização no seu espaço de trabalho.

Se tiver os seus dados num DataFrame pandas, utilize o seguinte código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se os seus dados já estiverem serializados, pode utilizar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A biblioteca de clientes Python é capaz de serializar um Pandas DataFrame `azureml.DataTypeIds` para os seguintes formatos (constantes para estes estão na classe):

* PlainText
* Genéricocsv
* Genérico
* Genericcsvnoheader
* Generictsvnoheader

### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de dados existente
Se tentar fazer o upload de um novo conjunto de dados com um nome que corresponda a um conjunto de dados existente, deverá obter um erro de conflito.

Para atualizar um conjunto de dados existente, é necessário obter primeiro uma referência ao conjunto de dados existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Em `update_from_dataframe` seguida, utilize para serializar e substituir o conteúdo do conjunto de dados no Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se pretender serializar os dados para um formato diferente, especifique um valor para o parâmetro opcional. `data_type_id`

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pode definir opcionalmente uma nova descrição especificando um valor para o `description` parâmetro.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Pode definir opcionalmente um novo nome especificando `name` um valor para o parâmetro. A partir de agora, você vai recuperar o conjunto de dados usando apenas o novo nome. O código seguinte atualiza os dados, nome e descrição.

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

Os `data_type_id` `name` e `description` os parâmetros são opcionais e indefinidos ao seu valor anterior. O `dataframe` parâmetro é sempre necessário.

Se os seus dados já `update_from_raw_data` estiverem serializados, utilize em vez de `update_from_dataframe`. Se passares `raw_data` em `dataframe`vez de, funciona da mesma forma.

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

