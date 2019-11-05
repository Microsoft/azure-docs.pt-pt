---
title: Acessar conjuntos de dados com a biblioteca de cliente do Python-processo de ciência de dado da equipe
description: Instale e use a biblioteca de cliente do Python para acessar e gerenciar Azure Machine Learning dados com segurança de um ambiente Python local.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e9daf1be1f931bb13cda446cbb9d6e37acce3bcf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498100"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Utilizar a biblioteca de clientes de Python do Azure Machine Learning para aceder a conjuntos de dados com Python
A visualização de Microsoft Azure Machine Learning biblioteca de cliente Python pode permitir o acesso seguro aos conjuntos de seus Azure Machine Learning de um ambiente local do Python e permite a criação e o gerenciamento de conjuntos de clientes em um espaço de trabalho.

Este tópico fornece instruções sobre como:

* instalar a biblioteca de cliente Machine Learning Python
* Acesse e carregue conjuntos de informações, incluindo instruções sobre como obter autorização para acessar conjuntos de Azure Machine Learning de um ambiente Python local
* acessar conjuntos de acesso intermediários de experimentos
* usar a biblioteca de cliente do Python para enumerar conjuntos de clientes, acessar metadados, ler o conteúdo de um conjunto de valores, criar novos conjuntos de os e atualizar conjuntos de os existentes

## <a name="prerequisites"></a>Pré-requisitos
A biblioteca de cliente do Python foi testada nos seguintes ambientes:

* Windows, Mac e Linux
* Python 2,7, 3,3 e 3,4

Ele tem uma dependência nos seguintes pacotes:

* Pedido
* Python-DateUtil
* pandas

É recomendável usar uma distribuição do Python, como [Anaconda](http://continuum.io/downloads#all) ou [Canopy](https://store.enthought.com/downloads/), que vem com Python, ipython e os três pacotes listados acima instalados. Embora IPython não seja estritamente necessário, é um ótimo ambiente para manipular e Visualizar dados interativamente.

### <a name="installation"></a>Como instalar a biblioteca de cliente do Azure Machine Learning Python
A biblioteca de cliente do Azure Machine Learning Python também deve ser instalada para concluir as tarefas descritas neste tópico. Ele está disponível no [índice do pacote do Python](https://pypi.python.org/pypi/azureml). Para instalá-lo em seu ambiente do Python, execute o seguinte comando em seu ambiente local do Python:

    pip install azureml

Como alternativa, você pode baixar e instalar a partir das fontes no [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se você tiver o Git instalado em seu computador, poderá usar o Pip para instalar diretamente do repositório git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Usar trechos de código do estúdio para acessar conjuntos de os
A biblioteca de cliente Python oferece acesso programático a seus conjuntos de os existentes de experimentos que foram executados.

Na interface da Web do estúdio, você pode gerar trechos de código que incluem todas as informações necessárias para baixar e desserializar conjuntos de dados como objetos de dataframe do pandas em seu computador local.

### <a name="security"></a>Segurança para acesso a dados
Os trechos de código fornecidos pelo Studio para uso com a biblioteca de cliente do Python incluem a ID do espaço de trabalho e o token de autorização. Eles fornecem acesso completo ao seu espaço de trabalho e devem ser protegidos, como uma senha.

Por motivos de segurança, a funcionalidade de trecho de código só está disponível para usuários que têm sua função definida como **proprietário** para o espaço de trabalho. Sua função é exibida no Azure Machine Learning Studio (clássico) na página **usuários** em **configurações**.

![Segurança][security]

Se sua função não estiver definida como **proprietário**, você poderá solicitar que seja reconvite como um proprietário ou pedir ao proprietário do espaço de trabalho que forneça o trecho de código.

Para obter o token de autorização, você pode executar um dos seguintes procedimentos:

* Solicitar um token de um proprietário. Os proprietários podem acessar seus tokens de autorização na página Configurações de seu espaço de trabalho no estúdio. Selecione **configurações** no painel esquerdo e clique em **tokens de autorização** para ver os tokens primário e secundário. Embora os tokens de autorização primários ou secundários possam ser usados no trecho de código, é recomendável que os proprietários compartilhem apenas os tokens de autorização secundários.

![Tokens de autorização](./media/python-data-access/ml-python-access-settings-tokens.png)

* Peça para ser promovido à função de proprietário. Para fazer isso, um proprietário atual do espaço de trabalho precisa primeiro removê-lo do espaço de trabalho e, em seguida, convidá-lo novamente como proprietário.

Depois que os desenvolvedores obtiverem a ID do espaço de trabalho e o token de autorização, eles poderão acessar o espaço de trabalho usando o trecho de código, independentemente de sua função.

Os tokens de autorização são gerenciados na página **tokens de autorização** em **configurações**. Você pode gerá-los novamente, mas esse procedimento revoga o acesso ao token anterior.

### <a name="accessingDatasets"></a>Acessar conjuntos de os de um aplicativo Python local
1. Em Machine Learning Studio, clique em **conjuntos de valores** na barra de navegação à esquerda.
2. Selecione o conjunto de um que você deseja acessar. Você pode selecionar qualquer um dos conjuntos de valores na lista **meus conjuntos de valores** ou na lista de **exemplos** .
3. Na barra de ferramentas inferior, clique em **gerar código de acesso a dados**. Se os dados estiverem em um formato incompatível com a biblioteca de cliente do Python, esse botão será desabilitado.
   
    ![Conjuntos de dados][datasets]
4. Selecione o trecho de código na janela que aparece e copie-o para a área de transferência.
   
    ![Botão gerar código de acesso a dados][dataset-access-code]
5. Cole o código no bloco de anotações do seu aplicativo Python local.
   
    ![Colar o código no bloco de anotações][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Acessar conjuntos de valores intermediários de Machine Learning experimentos
Depois que um experimento é executado no Machine Learning Studio, é possível acessar os conjuntos de resultados intermediários dos nós de saída dos módulos. Os conjuntos de dados intermediários são aqueles que foram criados e usados para etapas intermediárias quando uma ferramenta de modelo foi executada.

Os conjuntos de dados intermediários podem ser acessados, desde que o formato do dado seja compatível com a biblioteca de cliente do Python.

Há suporte para os seguintes formatos (constantes para eles estão na classe `azureml.DataTypeIds`):

* Comum
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Você pode determinar o formato passando o mouse sobre um nó de saída do módulo. Ele é exibido junto com o nome do nó, em uma dica de ferramenta.

Alguns dos módulos, como o módulo [dividir][split] , geram uma saída para um formato chamado `Dataset`, que não tem suporte na biblioteca de cliente do Python.

![Formato do conjunto de um][dataset-format]

Você precisa usar um módulo de conversão, como [converter em CSV][convert-to-csv], para obter uma saída em um formato com suporte.

![Formato GenericCSV][csv-format]

As etapas a seguir mostram um exemplo que cria um experimento, executa-o e acessa o conjunto de testes intermediário.

1. Crie um novo experimento.
2. Inserir um módulo de **conjunto de censo de classificação binária de renda de adulto** .
3. Insira um módulo de [divisão][split] e conecte sua entrada à saída do módulo do conjunto de dados.
4. Insira um módulo [converter para CSV][convert-to-csv] e conecte sua entrada a uma das saídas do módulo de [divisão][split] .
5. Salve o experimento, execute-o e aguarde a conclusão da execução.
6. Clique no nó saída no módulo [converter em CSV][convert-to-csv] .
7. Quando o menu de contexto for exibido, selecione **gerar código de acesso a dados**.
   
    ![Menu de contexto][experiment]
8. Selecione o trecho de código e copie-o para a área de transferência na janela que aparece.
   
    ![Gerar código de acesso no menu de contexto][intermediate-dataset-access-code]
9. Cole o código no bloco de anotações.
   
    ![Colar código no bloco de anotações][ipython-intermediate-dataset]
10. Você pode visualizar os dados usando o matplotlib. Isso é exibido em um histograma para a coluna idade:
    
    ![Histograma][ipython-histogram]

## <a name="clientApis"></a>Use a biblioteca de cliente Machine Learning Python para acessar, ler, criar e gerenciar conjuntos de os
### <a name="workspace"></a>Área de trabalho
O espaço de trabalho é o ponto de entrada para a biblioteca de cliente do Python. Forneça a classe `Workspace` com a ID do espaço de trabalho e o token de autorização para criar uma instância:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar conjuntos de os
Para enumerar todos os conjuntos de dados em um determinado espaço de trabalho:

    for ds in ws.datasets:
        print(ds.name)

Para enumerar apenas os conjuntos de valores criados pelo usuário:

    for ds in ws.user_datasets:
        print(ds.name)

Para enumerar apenas os conjuntos de valores de exemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Você pode acessar um conjunto de dados por nome (que diferencia maiúsculas de minúsculas):

    ds = ws.datasets['my dataset name']

Ou você pode acessá-lo por índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadados
Conjuntos de valores têm metadados, além de conteúdo. (Os conjuntos de valores intermediários são uma exceção a essa regra e não têm nenhum metadado.)

Alguns valores de metadados são atribuídos pelo usuário no momento da criação:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Outros são valores atribuídos pelo Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte a classe `SourceDataset` para saber mais sobre os metadados disponíveis.

### <a name="read-contents"></a>Ler conteúdo
Os trechos de código fornecidos por Machine Learning Studio (clássico) baixam e desserializam automaticamente o conjunto de objetos para um objeto pandas dataframe. Isso é feito com o método `to_dataframe`:

    frame = ds.to_dataframe()

Se você preferir baixar os dados brutos e executar a desserialização por conta própria, essa é uma opção. No momento, essa é a única opção para formatos como ' ARFF ', que a biblioteca de cliente do Python não pode desserializar.

Para ler o conteúdo como texto:

    text_data = ds.read_as_text()

Para ler o conteúdo como binário:

    binary_data = ds.read_as_binary()

Você também pode simplesmente abrir um fluxo para o conteúdo:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Criar um novo conjunto de um
A biblioteca de cliente do Python permite que você carregue conjuntos de arquivos do seu programa Python. Esses conjuntos de valores estão disponíveis para uso em seu espaço de trabalho.

Se você tiver seus dados em um data frame do pandas, use o seguinte código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se os dados já estiverem serializados, você poderá usar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A biblioteca de cliente do Python é capaz de serializar um dataframe do pandas nos seguintes formatos (constantes para eles estão na classe `azureml.DataTypeIds`):

* Comum
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de um existente
Se você tentar carregar um novo conjunto de um com um nome que corresponda a um conjunto de um existente, você deverá obter um erro de conflito.

Para atualizar um conjunto de um existente, primeiro você precisa obter uma referência para o conjunto de existentes:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Em seguida, use `update_from_dataframe` para serializar e substituir o conteúdo do conjunto de um no Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se você quiser serializar os dados para um formato diferente, especifique um valor para o parâmetro opcional `data_type_id`.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, você pode definir uma nova descrição especificando um valor para o parâmetro `description`.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, você pode definir um novo nome especificando um valor para o parâmetro `name`. De agora em diante, você recuperará o conjunto de os usando apenas o novo nome. O código a seguir atualiza os dados, o nome e a descrição.

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

Os parâmetros `data_type_id`, `name` e `description` são opcionais e padrão para seu valor anterior. O parâmetro `dataframe` é sempre necessário.

Se os dados já estiverem serializados, use `update_from_raw_data` em vez de `update_from_dataframe`. Se você apenas passar `raw_data` em vez de `dataframe`, ele funcionará de maneira semelhante.

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

