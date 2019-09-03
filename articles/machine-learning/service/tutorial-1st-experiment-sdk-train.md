---
title: 'Tutorial: Treinar seu primeiro modelo de ML'
titleSuffix: Azure Machine Learning service
description: Neste tutorial, você aprende os padrões de design básico no serviço Azure Machine Learning e treina um modelo scikit simples com base no conjunto de dados diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/03/2019
ms.openlocfilehash: 8065284d65f1b0a91fc05846b451305906c74fc8
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231112"
---
# <a name="tutorial-train-your-first-ml-model"></a>Tutorial: Treinar seu primeiro modelo de ML

Este tutorial é a **segunda parte de uma série composta por duas partes**. No tutorial anterior, você [criou um espaço de trabalho e escolheu um ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup.md). Neste tutorial, você aprende os padrões de design básico no serviço Azure Machine Learning e treina um modelo scikit simples com base no conjunto de dados diabetes. Depois de concluir este tutorial, você terá o conhecimento prático do SDK para escalar verticalmente para o desenvolvimento de experimentos e fluxos de trabalho mais complexos.

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Conectar seu espaço de trabalho e criar um experimento
> * Carregar dados e treinar modelos scikit-Learn
> * Exibir resultados de treinamento no portal
> * Obter o melhor modelo

## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito é executar a parte um deste tutorial, o [ambiente de instalação e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md).

Nesta parte do tutorial, você executa o código no bloco de anotações `tutorials/tutorial-1st-experiment-sdk-train.ipynb` Jupyter de exemplo aberto no final da parte 1. Este artigo percorre o mesmo código que está no bloco de anotações.

## <a name="launch-jupyter-web-interface"></a>Iniciar interface da Web do Jupyter

1. Na página do seu espaço de trabalho no portal do Azure, selecione **VMs do notebook** à esquerda.

1. Selecione **Jupyter** na coluna **URI** para a VM que você criou na parte um deste tutorial.

    ![Iniciar o servidor do Jupyter Notebook](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   O link inicia o servidor do bloco de anotações e abre a página da Web do Jupyter notebook em uma nova guia do navegador.  Esse link funcionará apenas para a pessoa que cria a VM. Cada usuário do espaço de trabalho deve criar sua própria VM.

1. Na página da Web do Jupyter notebook, selecione o Top FolderName, que tem seu nome de usuário.  


   Essa pasta existe na conta de [armazenamento](concept-workspace.md#resources) do espaço de trabalho em vez de na própria VM do notebook.  Se você excluir a VM do notebook, ainda manterá todo o seu trabalho.  Quando você criar uma nova VM de bloco de anotações mais tarde, ela carregará essa mesma pasta. Se você compartilhar seu espaço de trabalho com outras pessoas, ele verá sua pasta e você verá seus.

1. Abra o `samples-*` subdiretório e, em seguida, abra o notebook Jupyter`tutorials/tutorial-1st-experiment-sdk-train.ipynb`

   > [!Warning]
   > Certifique-se de `tutorial-1st-experiment-sdk-train.ipynb` abrir o arquivo, `.yml` **não** o arquivo do > mesmo nome. 

1. O restante deste artigo contém o mesmo conteúdo que você vê no bloco de anotações.  Alterne para o bloco de anotações agora se você quiser executar o código.


## <a name="connect-workspace-and-create-experiment"></a>Conectar espaço de trabalho e criar experimento

Importe a `Workspace` classe e carregue as informações de assinatura do arquivo `config.json` usando a função `from_config().` que procura o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")`o. Em um servidor de bloco de anotações de nuvem, o arquivo é automaticamente no diretório raiz.

Se o código a seguir solicitar autenticação adicional, basta colar o link em um navegador e inserir o token de autenticação.

> [!TIP]
> Se você for novo no Jupyter notebooks, execute o código clicando em uma célula de código e pressionando **Shift + Enter** para executar as células uma de cada vez. Como alternativa, para executar todo o bloco de anotações ao mesmo tempo, clique em **célula** na barra de menus superior e, em seguida, clique em **executar tudo**.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Agora, crie um experimento em seu espaço de trabalho. Um experimento é outro recurso de nuvem fundamental que representa uma coleção de avaliações (execuções de modelo individuais). Neste tutorial, você usa o experimento para criar execuções e acompanhar o treinamento do modelo no portal do Azure. Os parâmetros incluem sua referência de espaço de trabalho e um nome de cadeia de caracteres para o experimento.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Carregue dados e prepare-se para treinamento

Para este tutorial, você usa o conjunto de dados diabetes, que é um conjunto de dados previamente normalizado incluído no scikit-learn. Esse conjunto de dados usa recursos como idade, sexo e BMI para prever a progressão de doença diabetes. Carregue os dados da `load_diabetes()` função estática e divida-os em conjuntos de treinamento e teste usando. `train_test_split()` Essa função separa os dados para que o modelo tenha dados não vistos a serem usados para testar o treinamento a seguir.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Preparar um modelo

Treinar um modelo simples de scikit-Learn pode facilmente ser feito localmente para treinamento em pequena escala, mas ao treinar muitas iterações com dezenas de permutações de recursos diferentes e configurações de hiperparâmetro, é fácil perder o controle de quais modelos foram treinados e como você treinados. O padrão de design a seguir mostra como aproveitar o SDK para controlar facilmente seu treinamento na nuvem.

Crie um script que treina os modelos de ondulação em um loop por meio de valores Alfa de hiperparâmetro diferentes.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

O código acima realiza o seguinte:

1. Para cada valor de hiperparâmetro alfa na `alphas` matriz, uma nova execução é criada dentro do experimento. O valor alfa é registrado em log para diferenciar entre cada execução.
1. Em cada execução, um modelo de saliência é instanciado, treinado e usado para executar previsões. O erro raiz-Mean-quadrado-é calculado para os valores reais versus previstos e, em seguida, conectado à execução. Neste ponto, a execução tem metadados anexados para o valor alfa e a precisão RMSE.
1. Em seguida, o modelo para cada execução é serializado e carregado na execução. Isso permite que você baixe o arquivo de modelo da execução no Portal.
1. Ao final de cada iteração, a execução é concluída chamando `run.complete()`.

Após a conclusão do treinamento, chame a `experiment` variável para buscar um link para o experimento no Portal.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>Área de trabalho</th><th>Página de relatório</th><th>Página de documentos</th></tr><tr><td>diabetes-experimento</td><td>Your-Workspace-Name</td><td>Link para o portal do Azure</td><td>Link para a documentação</td></tr></table>

## <a name="view-training-results-in-portal"></a>Exibir resultados de treinamento no portal

Após o **link para o portal do Azure** , você será levado para a página principal do experimento. Aqui você vê todas as execuções individuais no experimento. Todos os valores personalizados registrados (`alpha_value` e `rmse`, nesse caso) tornam-se campos para cada execução e também ficam disponíveis para os gráficos e blocos na parte superior da página do experimento. Para adicionar uma métrica registrada a um gráfico ou bloco, passe o mouse sobre ela, clique no botão Editar e localize sua métrica personalizada registrada em log.

Ao treinar modelos em escala em centenas e milhares de execuções separadas, essa página facilita a visualização de todos os modelos treinados, especificamente de como eles foram treinados e como suas métricas exclusivas foram alteradas ao longo do tempo.

![Página principal do experimento no portal](./media/tutorial-quickstart/experiment-main.png)

Clicar em um link de número de execução `RUN NUMBER` na coluna leva você até a página para cada execução individual. Os **detalhes** da guia padrão mostram informações mais detalhadas sobre cada execução. Navegue até a guia **saídas** e você verá o `.pkl` arquivo para o modelo que foi carregado para a execução durante cada iteração de treinamento. Aqui você pode baixar o arquivo de modelo, em vez de ter que retreiná-lo manualmente.

![Página executar detalhes no portal](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Obter o melhor modelo

Além de poder baixar arquivos de modelo do experimento no portal, você também pode baixá-los programaticamente. O código a seguir itera em cada execução no experimento e acessa as métricas de execução registradas e os detalhes de execução (que contém o run_id). Isso mantém o controle da melhor execução, nesse caso, a execução com o menor erro de raiz-meio-de-quadrado.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Use a melhor ID de execução para buscar a execução individual usando `Run` o Construtor junto com o objeto experimento. Em seguida `get_file_names()` , chame para ver todos os arquivos disponíveis para download nesta execução. Nesse caso, você carregou apenas um arquivo para cada execução durante o treinamento.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Chame `download()` no objeto Run, especificando o nome do arquivo de modelo a ser baixado. Por padrão, essa função é baixada para o diretório atual.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Limpar recursos

Não conclua esta seção se você planeja executar outros tutoriais de serviço Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Parar a VM do notebook

Se você usou um servidor de bloco de anotações de nuvem, pare a VM quando não a estiver usando para reduzir o custo.

1. Em seu espaço de trabalho, selecione **máquinas virtuais do notebook**.

   ![Parar o servidor de VM](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Na lista, selecione a VM.

1. Selecione **parar**.

1. Quando você estiver pronto para usar o servidor novamente, selecione **Iniciar**.

### <a name="delete-everything"></a>Excluir tudo

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único espaço de trabalho. Exiba as propriedades do espaço de trabalho e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você fez as seguintes tarefas:

> [!div class="checklist"]
> * Conectou seu espaço de trabalho e criou um experimento
> * Dados carregados e modelos treinados scikit-Learn
> * Resultados de treinamento exibidos no portal e recuperados modelos

[Implementar o seu modelo](tutorial-deploy-models-with-aml.md) com o Azure Machine Learning.
Saiba como desenvolver experimentos automatizados de [aprendizado de máquina](tutorial-auto-train-models.md) .
