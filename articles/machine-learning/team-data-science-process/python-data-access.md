---
title: Conjuntos de dados de acesso com biblioteca de clientes Python - Processo de Ciência de Dados de Equipa
description: Instale e utilize a biblioteca do cliente Python para aceder e gerir os dados de Aprendizagem automática Azure de forma segura a partir de um ambiente python local.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6c8e6fee2b9f01b8d7ab48990760aa4c4d6e11b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361505"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Utilizar a biblioteca de clientes de Python do Azure Machine Learning para aceder a conjuntos de dados com Python
A pré-visualização da biblioteca de clientes Microsoft Azure Machine Learning Python pode permitir o acesso seguro aos seus conjuntos de dados de Aprendizagem de Máquinas Azure a partir de um ambiente python local e permite a criação e gestão de conjuntos de dados num espaço de trabalho.

Este tópico fornece instruções sobre como:

* instalar a biblioteca de clientes Machine Learning Python
* aceder e carregar conjuntos de dados, incluindo instruções sobre como obter autorização para aceder a conjuntos de dados de Aprendizagem de Máquinas Azure a partir do seu ambiente python local
* aceder a conjuntos de dados intermédios a partir de experiências
* utilizar a biblioteca de clientes Python para enumerar conjuntos de dados, aceder a metadados, ler o conteúdo de um conjunto de dados, criar novos conjuntos de dados e atualizar conjuntos de dados existentes

## <a name="prerequisites"></a><a name="prerequisites"></a>Pré-requisitos
A biblioteca de clientes Python foi testada nos seguintes ambientes:

* Windows, Mac e Linux
* Python 2.7, 3.3 e 3.4

Tem uma dependência dos seguintes pacotes:

* pedidos
* python-dateutil
* pandas

Recomendamos a utilização de uma distribuição Python como [Anaconda](https://www.anaconda.com/) ou [Canopy,](https://store.enthought.com/downloads/)que vêm com Python, IPython e os três pacotes acima listados instalados. Embora a IPython não seja estritamente necessária, é um ótimo ambiente para manipular e visualizar dados de forma interativa.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Como instalar a biblioteca de clientes Azure Machine Learning Python
Instale a biblioteca de clientes Azure Machine Learning Python para completar as tarefas descritas neste tópico. Esta biblioteca está disponível no [Python Package Index.](https://pypi.python.org/pypi/azureml) Para instalá-lo no seu ambiente Python, executar o seguinte comando a partir do seu ambiente python local:

```console
pip install azureml
```

Em alternativa, pode descarregar e instalar a partir das fontes no [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

```console
python setup.py install
```

Se tiver uma pega instalada na sua máquina, pode utilizar pip para instalar diretamente a partir do repositório git:

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Use snippets de código para aceder a conjuntos de dados
A biblioteca de clientes Python dá-lhe acesso programático aos seus conjuntos de dados existentes a partir de experiências que foram executadas.

A partir da interface web do Azure Machine Learning Studio (clássico), pode gerar snippets de código que incluem todas as informações necessárias para descarregar e desseializar conjuntos de dados como objetos dataFrame pandas na sua máquina local.

### <a name="security-for-data-access"></a><a name="security"></a>Segurança para acesso a dados
Os snippets de código fornecidos pelo Azure Machine Learning Studio (clássico) para uso com a biblioteca do cliente Python incluem o seu ID do espaço de trabalho e o token de autorização. Estes fornecem acesso total ao seu espaço de trabalho e devem ser protegidos, como uma senha.

Por razões de segurança, a funcionalidade de corte de código apenas está disponível para utilizadores que tenham a sua função definida como **Proprietário** para o espaço de trabalho. O seu papel é apresentado no Azure Machine Learning Studio (clássico) na página **USERS** em **Definições**.

![O Screenshot mostra as definições na página utilizadores do Azure Machine Learning Studio.][security]

Se a sua função não estiver definida como **Proprietário,** pode solicitar para ser reenvidada como proprietária ou pedir ao proprietário do espaço de trabalho para lhe fornecer o corte de código.

Para obter o token de autorização, pode escolher uma destas opções:

* Peça um sinal de um dono. Os proprietários podem aceder aos seus tokens de autorização a partir da página Definições do seu espaço de trabalho no Azure Machine Learning Studio (clássico). Selecione **Definições** a partir do painel esquerdo e clique em **TOKENS DE AUTORIZAÇÃO** para ver as fichas primárias e secundárias. Embora os tokens de autorização primária ou secundária possam ser usados no corte de código, recomenda-se que os proprietários apenas partilhem os tokens de autorização secundária.

   ![Fichas de autorização](./media/python-data-access/ml-python-access-settings-tokens.png)

* Peça para ser promovido ao papel de proprietário: um atual proprietário do espaço de trabalho precisa primeiro removê-lo do espaço de trabalho e depois convidá-lo para ele como proprietário.

Uma vez que os desenvolvedores tenham obtido o ID do espaço de trabalho e o token de autorização, eles são capazes de aceder ao espaço de trabalho usando o corte de código independentemente do seu papel.

As fichas de autorização são geridas na página **TOKENS DE AUTORIZAÇÃO** em **DEFINIÇÕES**. Pode regenerar, mas este procedimento revoga o acesso ao símbolo anterior.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Aceder a conjuntos de dados a partir de uma aplicação python local
1. No Machine Learning Studio (clássico), clique em **DATASETS** na barra de navegação à esquerda.
2. Selecione o conjunto de dados a que pretende aceder. Pode selecionar qualquer um dos conjuntos de dados da lista **MY DATASETS** ou da lista **SAMPLES.**
3. A partir da barra de ferramentas inferior, clique em **Gerar Código de Acesso a Dados.** Se os dados estiverem num formato incompatível com a biblioteca do cliente Python, este botão é desativado.
   
    ![A screenshot mostra conjuntos de dados com o CÓDIGO DE ACESSO A DADOS GERAR.][datasets]
4. Selecione o corte de código da janela que aparece e copie-o para a sua área de transferência.
   
    ![Gerar botão de código de acesso a dados][dataset-access-code]
5. Cole o código no caderno da sua aplicação python local.
   
    ![Colar código no caderno][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Aceder a conjuntos de dados intermédios a partir de experiências de Machine Learning
Depois de uma experiência ser executada no Machine Learning Studio (clássico), é possível aceder aos conjuntos de dados intermédios a partir dos nós de saída dos módulos. Conjuntos de dados intermédios são dados que foram criados e utilizados para passos intermédios quando uma ferramenta modelo foi executada.

Os conjuntos de dados intermédios podem ser acedidos desde que o formato de dados seja compatível com a biblioteca do cliente Python.

Os seguintes formatos são suportados (as constantes para estes formatos estão na `azureml.DataTypeIds` classe):

* PlainText
* GenéricoCSV
* GenericTSV
* GenéricoCSVNoHeader
* GenéricoSVNoHeader

Pode determinar o formato pairando sobre um nó de saída do módulo. É exibido juntamente com o nome do nó, numa ponta de ferramenta.

Alguns dos módulos, como o módulo [Split,][split] são de saída para um formato denominado `Dataset` , que não é suportado pela biblioteca do cliente Python.

![Formato do conjunto de dados][dataset-format]

É necessário utilizar um módulo de conversão, como [o Convert para CSV,][convert-to-csv]para obter uma saída num formato suportado.

![Formato GenéricoCSV][csv-format]

Os passos a seguir mostram um exemplo que cria uma experiência, executa-a e acede ao conjunto de dados intermédio.

1. Criar uma nova experiência.
2. Insira um módulo **de conjunto de dados de classificação binária de rendimento de rendimento adulto.**
3. Insira um módulo [Split][split] e ligue a sua entrada à saída do módulo do conjunto de dados.
4. Insira um converse para o módulo [CSV][convert-to-csv] e ligue a sua entrada a uma das saídas do módulo [Split.][split]
5. Salve a experiência, corra e espere que o trabalho termine.
6. Clique no nó de saída do módulo [Converte para CSV.][convert-to-csv]
7. Quando o menu de contexto aparecer, **selecione 'Gerar Código de Acesso a Dados'.**
   
    ![Menu de Contexto][experiment]
8. Selecione o corte de código e copie-o para a sua área de transferência a partir da janela que aparece.
   
    ![Gerar código de acesso a partir do menu de contexto][intermediate-dataset-access-code]
9. Cole o código no seu caderno.
   
    ![Colar código em caderno][ipython-intermediate-dataset]
10. Pode visualizar os dados usando o matplotlib. Isto mostra num histograma para a coluna etária:
    
    ![Histograma][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Utilize a biblioteca de clientes Machine Learning Python para aceder, ler, criar e gerir conjuntos de dados
### <a name="workspace"></a>Área de trabalho
O espaço de trabalho é o ponto de entrada para a biblioteca do cliente Python. Forneça à classe o `Workspace` seu ID do espaço de trabalho e sinal de autorização para criar uma instância:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Enumerar conjuntos de dados
Para enumerar todos os conjuntos de dados num dado espaço de trabalho:

```python
for ds in ws.datasets:
    print(ds.name)
```

Para enumerar apenas os conjuntos de dados criados pelo utilizador:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Para enumerar apenas os conjuntos de dados de exemplo:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

Pode aceder a um conjunto de dados pelo nome (que é sensível a casos):

```python
ds = ws.datasets['my dataset name']
```

Ou pode acessá-lo por índice:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Metadados
Os conjuntos de dados têm metadados, além do conteúdo. (Os conjuntos de dados intermédios são uma exceção a esta regra e não têm metadados.)

Alguns valores de metadados são atribuídos pelo utilizador no momento da criação:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Outros são valores atribuídos pela Azure ML:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

Consulte a `SourceDataset` aula para mais informações sobre os metadados disponíveis.

### <a name="read-contents"></a>Ler conteúdos
Os snippets de código fornecidos pelo Machine Learning Studio (clássico) descarregam e deserializam automaticamente o conjunto de dados para um objeto DataFrame de pandas. Isto é feito com o `to_dataframe` método:

```python
frame = ds.to_dataframe()
```

Se preferir baixar os dados brutos e realizar a desseialização por si mesmo, essa é uma opção. Neste momento, esta é a única opção para formatos como 'ARFF', que a biblioteca de clientes Python não consegue desseerizar.

Para ler o conteúdo como texto:

```python
text_data = ds.read_as_text()
```

Para ler o conteúdo como binário:

```python
binary_data = ds.read_as_binary()
```

Também pode abrir um stream para o conteúdo:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Criar um novo conjunto de dados
A biblioteca de clientes Python permite-lhe fazer o upload de conjuntos de dados do seu programa Python. Estes conjuntos de dados estão então disponíveis para utilização no seu espaço de trabalho.

Se tiver os seus dados num DataFrame pandas, utilize o seguinte código:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Se os seus dados já estiverem serializados, pode utilizar:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

A biblioteca de clientes Python é capaz de serializar um DataFrame de pandas para os seguintes formatos (as constantes para estes estão na `azureml.DataTypeIds` classe):

* PlainText
* GenéricoCSV
* GenericTSV
* GenéricoCSVNoHeader
* GenéricoSVNoHeader

### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de dados existente
Se tentar fazer o upload de um novo conjunto de dados com um nome que corresponda a um conjunto de dados existente, deverá obter um erro de conflito.

Para atualizar um conjunto de dados existente, primeiro precisa de obter uma referência ao conjunto de dados existente:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Em seguida, utilize `update_from_dataframe` para serializar e substituir o conteúdo do conjunto de dados no Azure:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Se pretender serializar os dados para um formato diferente, especifique um valor para o `data_type_id` parâmetro opcional.

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Pode configurar opcionalmente uma nova descrição especificando um valor para o `description` parâmetro.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

Pode configurar opcionalmente um novo nome especificando um valor para o `name` parâmetro. A partir de agora, irá recuperar o conjunto de dados usando apenas o novo nome. O código seguinte atualiza os dados, nome e descrição.

```python
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
```

Os `data_type_id` `name` parâmetros e `description` parâmetros são opcionais e padrão ao seu valor anterior. O `dataframe` parâmetro é sempre necessário.

Se os seus dados já estiverem serializados, utilize `update_from_raw_data` em vez de `update_from_dataframe` . Se `raw_data` passares em vez  `dataframe` de, funciona da mesma forma.

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

