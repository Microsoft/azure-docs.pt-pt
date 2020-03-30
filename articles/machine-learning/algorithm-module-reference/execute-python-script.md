---
title: 'Execute o roteiro python: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Execute Python Script em Azure Machine Learning para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 0f86d1ad03062797764af6a0d49beacaa3458a8f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365558"
---
# <a name="execute-python-script-module"></a>Execute módulo de script python

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design da Python, consulte [o seguinte artigo.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Com a Python, pode executar tarefas que não são suportadas atualmente por módulos existentes, tais como:

+ Visualizar dados usando`matplotlib`
+ Utilização de bibliotecas Python para enumerar conjuntos de dados e modelos no seu espaço de trabalho
+ Leitura, carregamento e manipulação de dados de fontes não suportadas pelo módulo [de dados](./import-data.md) de importação
+ Executa o seu próprio código de aprendizagem profunda 


O Azure Machine Learning utiliza a distribuição da Anaconda da Python, que inclui muitos utilitários comuns para o processamento de dados. Atualizaremos automaticamente a versão Anaconda. A versão atual é:
 -  Distribuição anaconda 4.5+ para Python 3.6 

As embalagens pré-instaladas são:
-    adal==1.2.2
-    applicationinsights==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0.61.1
-    azure-identidade==1.3.0
-    azure-mgmt-autorização==0,60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1.5.0
-    azure-armazenamento-common==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep===1.3.5
-    incumprimentos em azureml==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-interno==0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetria==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1.12.29
-    botocore==1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    click=7.1.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    criptografia==2.8
-    ciclor==0,10.0
-    dill=0.3.1.1
-    distro==1.4.0
-    estivador==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    flask==1.0.3
-    fusepy==3.0.1
-    gensim=3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicórnio==19.9.0
-    idna==2.9
-    desequilíbrio-aprendizagem==0,4.3
-    isodate==0.6.0
-    é perigoso==1.1.0
-    jeepney==0,4.3
-    jinja2==2.11.1
-    jmespath==0.9.5
-    joblib==0.14.0
-    json-logging-py==0,2
-    jsonpickle==1,3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1.1
-    matplotlib==3.1.3
-    mais itertools==6.0.0
-    extensões msal==0.1.3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1.18.2
-    oauthlib==3.1.0
-    pandas==0,25.3
-    pathspec==0.7.0
-    pip=20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0.16.0
-    pyasn1-módulos==0.2.8
-    pyasn1==0,4.8
-    pycparser==2,20
-    pycryptodomex==3.7.3
-    pyjwt==1.7.1
-    pyopenssl==19.1.0
-    pyparsing==2.4.6
-    pirsistente==0.16.0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    pedidos-oauthlib==1.3.0
-    pedidos==2.23.0
-    rsa==4.0
-    ruamel.yaml==0.15.89
-    s3transfer==0.3.3
-    scikit-learn==0.22.2
-    scipy==1.4.1
-    armazenamento secreto==3.1.2
-    ferramentas de configuração==46.1.1.post20200323
-    seis==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-client==0.57.0
-    werkzeug==0.16.1
-    roda==0,34.2

 Para instalar outros pacotes que não estão na lista pré-instalada, por *exemplo, scikit-misc,* adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Carregar ficheiros
O **Script Execute Python** suporta o upload de ficheiros utilizando o [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

O exemplo que se segue mostra como carregar um ficheiro de imagem no módulo **execute Python Script:**

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Após a execução do gasoduto, pode pré-visualizar a imagem no painel direito do módulo

> [!div class="mx-imgBorder"]
> ![Imagem carregada](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Como configurar o Script Python execute

O módulo **Execute Python Script** contém um código Python de amostra que pode utilizar como ponto de partida. Para configurar o módulo **Execute Python Script,** fornece um conjunto de inputs e código Python para executar na caixa de **texto** python.

1. Adicione o módulo **Execute Python Script** ao seu pipeline.

2. Adicione e ligue no **Dataset1** quaisquer conjuntos de dados do designer que pretende utilizar para a entrada. Refira este conjunto de dados no seu script Python como **DataFrame1**.

    A utilização de um conjunto de dados é opcional, se pretender gerar dados utilizando python, ou utilizar o código Python para importar os dados diretamente para o módulo.

    Este módulo suporta a adição de um segundo conjunto de dados no **Dataset2**. Consulte o segundo conjunto de dados no seu script Python como DataFrame2.

    Os conjuntos de dados armazenados no Azure Machine Learning são automaticamente convertidos em dados **pandas.frames** quando carregados com este módulo.

    ![Execute o mapa de entrada python](media/module/python-module.png)

4. Para incluir novos pacotes python ou código, adicione o ficheiro zipped contendo estes recursos personalizados no **pacote script**. A entrada para o **pacote Script** deve ser um ficheiro com fecho de mão enviado para o seu espaço de trabalho como um Conjunto de Dados do tipo Ficheiro. Pode fazer o upload do conjunto de dados na página de ativos **datasets** e pode arrastar e largar o módulo dataset da lista **my datasets** na árvore do módulo esquerdo na página de autor de designers. 

    Qualquer ficheiro contido no arquivo com fecho carregado pode ser utilizado durante a execução do gasoduto. Se o arquivo incluir uma estrutura de diretório, a estrutura é preservada, mas deve preparar um diretório chamado **SRC** para o caminho.

5. Na caixa de texto de **script Python,** escreva ou pasta script python válido.

    A caixa de texto de **script Python** é pré-povoada com algumas instruções em comentários, e código de amostra para acesso e saída de dados. Tem de editar ou substituir este código. Certifique-se de seguir as convenções python sobre o entalhe e o invólucro.

    + O script deve conter `azureml_main` uma função denominada como ponto de entrada para este módulo.
    + A função do ponto de entrada pode `Param<dataframe1>` conter até dois argumentos de entrada: e`Param<dataframe2>`
    + Os ficheiros com fecho ligados à terceira porta de entrada `.\Script Bundle`são desapertados e `sys.path`armazenados no diretório, que também é adicionado ao Python . 

    Portanto, se o seu `mymodule.py`ficheiro zip `import mymodule`contiver, importe-o usando .

    + Dois conjuntos de dados podem ser devolvidos ao `pandas.DataFrame`designer, que deve ser uma sequência de tipo . Pode criar outras saídas no seu código Python e escrevê-las diretamente no armazenamento do Azure.

6. Submeta o pipeline ou selecione o módulo e clique **em Executar selecionado** para executar apenas o script Python.

    Todos os dados e códigosão carregados numa máquina virtual e funcionam utilizando o ambiente python especificado.

## <a name="results"></a>Resultados

Os resultados de quaisquer cálculos efetuados pelo código Python incorporado devem ser fornecidos como pandas. DataFrame, que é automaticamente convertido para o formato de conjunto de dados Azure Machine Learning, para que possa utilizar os resultados com outros módulos no pipeline.

O módulo devolve dois conjuntos de dados:  
  
+ **Resultados Dataset 1**, definido pelo primeiro quadro de dados de pandas devolvidos no script Python

+ Conjunto de Dados de **Resultados 2**, definido pelo segundo quadro de dados de pandas devolvidos no script Python


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 