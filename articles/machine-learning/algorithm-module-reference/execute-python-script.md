---
title: 'Executar script python: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de Script Execute Python no designer de aprendizagem automática Azure para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 01/02/2021
ms.openlocfilehash: 6003ca9156d8553604d7ebbf94c5c3373d077f0f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500704"
---
# <a name="execute-python-script-module"></a>Execute o módulo de script python

Este artigo descreve o módulo executo python script em Azure Machine Learning designer.

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design de Python, veja [como executar o código Python em Azure Machine Learning designer.](../how-to-designer-python.md)

Com python, você pode executar tarefas que os módulos existentes não suportam, tais como:

+ Visualizar dados utilizando `matplotlib` .
+ Usando bibliotecas Python para enumerar conjuntos de dados e modelos no seu espaço de trabalho.
+ Ler, carregar e manipular dados de fontes que o módulo [de Dados de Importação](./import-data.md) não suporta.
+ Executar o seu próprio código de aprendizagem profunda. 

## <a name="supported-python-packages"></a>Pacotes Python suportados

A Azure Machine Learning utiliza a distribuição de Anaconda de Python, que inclui muitos utilitários comuns para o processamento de dados. Atualizaremos automaticamente a versão Anaconda. A versão atual é:
 -  Anaconda 4.5+ distribuição para Python 3.6 

Para obter uma lista completa, consulte a secção [Pacotes Python pré-instalados](#preinstalled-python-packages).

Para instalar pacotes que não estejam na lista pré-instalada (por exemplo, *scikit-misc),* adicione o seguinte código ao seu script: 

```python
import os
os.system(f"pip install scikit-misc")
```

Utilize o seguinte código para instalar pacotes para um melhor desempenho, especialmente para inferência:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> Se o seu pipeline contiver vários módulos executados do Script Python que precisam de pacotes que não estão na lista pré-instalada, instale as embalagens em cada módulo.

> [!WARNING]
> O módulo Excute Python Script não suporta a instalação de pacotes que dependem de bibliotecas extra-nativas com comando como "apt-get", como Java, PyODBC e etc. Isto porque este módulo é executado num ambiente simples com Python pré-instalado apenas e com permissão não administrada.  

## <a name="access-to-current-workspace-and-registered-datasets"></a>Acesso ao espaço de trabalho atual e conjuntos de dados registados

Pode consultar o seguinte código de amostra para aceder aos [conjuntos de dados registados](../how-to-create-register-datasets.md) no seu espaço de trabalho:

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    #access to current workspace
    ws = run.experiment.workspace

    #access to registered dataset of current workspace
    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>Carregar ficheiros
O módulo de script de Python executante suporta o upload de ficheiros utilizando o [Azure Machine Learning Python SDK](/python/api/azureml-core/azureml.core.run%28class%29#upload-file-name--path-or-stream-).

O exemplo a seguir mostra como carregar um ficheiro de imagem no módulo Executar Python Script:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
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
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Depois de terminado o curso do gasoduto, pode visualizar a imagem no painel direito do módulo.

> [!div class="mx-imgBorder"]
> ![Pré-visualização da imagem carregada](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Como configurar executar o script python

O módulo de script execute python contém o código Python de amostra que pode usar como ponto de partida. Para configurar o módulo executar o script python, forneça um conjunto de entradas e código Python para executar na caixa de texto do **script Python.**

1. Adicione o módulo **de script de Python executar** ao seu oleoduto.

2. Adicione e conecte no **Dataset1** quaisquer conjuntos de dados do designer que pretende utilizar para entrada. Consulte este conjunto de dados no seu script Python como **DataFrame1**.

    A utilização de um conjunto de dados é opcional. Use-os se quiser gerar dados utilizando python, ou use o código Python para importar os dados diretamente para o módulo.

    Este módulo suporta a adição de um segundo conjunto de dados no **Dataset2**. Consulte o segundo conjunto de dados no seu script Python como **DataFrame2**.

    Os conjuntos de dados armazenados no Azure Machine Learning são automaticamente convertidos para quadros de dados de pandas quando carregados com este módulo.

    ![Execute o mapa de entrada python](media/module/python-module.png)

4. Para incluir novos pacotes ou código Python, ligue o ficheiro com fecho que contém estes recursos personalizados à porta **do pacote script.** Ou se o seu script for maior que 16 KB, use a porta **script Bundle** para evitar erros como *o CommandLine excede o limite de 16597 caracteres*. 

    
    1. Embrulhe o script e outros recursos personalizados para um ficheiro zip.
    1. Faça o upload do ficheiro zip como um **conjunto de dados de ficheiro** para o estúdio. 
    1. Arraste o módulo de conjunto de dados da lista *datasets* no painel do módulo esquerdo na página de autoria do designer. 
    1. Ligue o módulo de conjunto de dados à porta do Pacote de **Scripts** do Módulo **de Script De Executo Python.**
    
    Qualquer ficheiro contido no arquivo com fecho de correr carregado pode ser utilizado durante a execução do gasoduto. Se o arquivo incluir uma estrutura de diretório, a estrutura é preservada.
 
    > [!WARNING]
    > **Não** utilize **a aplicação** como o nome da pasta ou do seu script, uma vez que **a aplicação** é uma palavra reservada para serviços incorporados. Mas pode usar outros espaços de nome `app123` como.
   
    Segue-se um exemplo de pacote de scripts, que contém um ficheiro de script python e um ficheiro txt:
      
    > [!div class="mx-imgBorder"]
    > ![Exemplo do pacote de script](media/module/python-script-bundle.png)  

    Segue-se o conteúdo `my_script.py` de:

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Segue-se o código de amostra que mostra como consumir os ficheiros no pacote de scripts:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. Na caixa de texto do **script Python,** escreva ou cole um script Python válido.

    > [!NOTE]
    >  Tenha cuidado ao escrever o seu guião. Certifique-se de que não existem erros de sintaxe, tais como a utilização de variáveis não declaradas ou de módulos ou funções não declarados. Preste uma atenção extra à lista de módulos pré-instalados. Para importar módulos que não estejam listados, instale os pacotes correspondentes no seu script, tais como:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    A caixa de texto **do script Python** é pré-solicitada com algumas instruções em comentários, e código de amostra para acesso e saída de dados. Tem de editar ou substituir este código. Siga as convenções de Python para o entalhe e o invólucro:

    + O script deve conter uma função nomeada `azureml_main` como ponto de entrada para este módulo.
    + A função de ponto de entrada deve ter dois argumentos de entrada `Param<dataframe1>` `Param<dataframe2>` e, mesmo quando estes argumentos não são usados no seu script.
    + Os ficheiros com fecho ligados à terceira porta de entrada são desapertados e armazenados no diretório `.\Script Bundle` , que também é adicionado ao Python `sys.path` . 

    Se o seu ficheiro .zip `mymodule.py` contiver, importe-o `import mymodule` utilizando.

    Dois conjuntos de dados podem ser devolvidos ao designer, que deve ser uma sequência de tipo `pandas.DataFrame` . Pode criar outras saídas no seu código Python e escrevê-las diretamente no armazenamento Azure.

    > [!WARNING]
    > **Não** é recomendado ligar-se a uma base de dados ou a outros armazenamentos externos no **Módulo de Script De Python Executado.** Pode utilizar [módulo de dados de importação](./import-data.md) e módulo de [dados de exportação](./export-data.md)     

6. Envie o oleoduto.

    Se o módulo estiver concluído, verifique se a saída é esperada.

    Se o módulo falhar, tem de fazer uma resolução de problemas. Selecione o módulo e abra **saídas+registos** no painel direito. Abra **70_driver_log.txt** e procure **em azureml_main,** então poderá descobrir qual a linha que causou o erro. Por exemplo, "File "/tmp/tmp01_ID/user_script.py", linha 17, em azureml_main" indica que o erro ocorreu na linha 17 da sua escrita python.

## <a name="results"></a>Resultados

Os resultados de quaisquer cálculos pelo código Python incorporado devem ser fornecidos como `pandas.DataFrame` , que é automaticamente convertido para o formato de conjunto de dados Azure Machine Learning. Em seguida, pode utilizar os resultados com outros módulos na tubagem.

O módulo devolve dois conjuntos de dados:  
  
+ **Resultados Dataset 1**, definido pelo primeiro quadro de dados de pandas devolvidos num script Python.

+ **Resultado Dataset 2**, definido pelo segundo quadro de dados de pandas devolvidos em um script Python.

## <a name="preinstalled-python-packages"></a>Pacotes Python pré-instalados
Os pacotes pré-instalados são:
-    adal==1.2.2
-    aplicações==0.11.9
-    attrs==19.3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0.61.1
-    azure-identidade==1.3.0
-    azure-mgmt-autorização==0,60.0
-    azure-mgmt-contentoresregistry==2,8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-recurso==8.0.1
-    azure-mgmt-storage==8.0.0
-    azure-storage-blob==1,5.0
-    azure-storage-common=1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep=1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-classic-modules==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-internal=0.0.18
-    azureml-model-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core=1.1.5
-    azureml-telemetria==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref=1.0.post1
-    boto3==1.12.29
-    botocore=1.15.29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1.12.3
-    chardet==3.0.4
-    clique==7.1.1
-    cloudpickle==1.3.0
-    configparser==3.7.4
-    contextlib2==0.6.0.post1
-    criptografia==2.8
-    ciclor==0.10.0
-    endro==0.3.1.1
-    distro==1.4.0
-    estivador==4.2.0
-    docutils==0.15.2
-    dotnetcore2==2.1.13
-    frasco==1.0.3
-    fusepy==3.0.1
-    gensim==3.8.1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19.9.0
-    idna==2.9
-    desequilíbrio-aprendizagem==0.4.3
-    isodate==0.6.0
-    itsdangerous==1.1.0
-    jeepney=0.4.3
-    jinja2==2.11.1
-    jmespath==0,9.5
-    joblib==0.14.0
-    json-logging-py==0.2
-    jsonpickle==1.3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe=1.1.1
-    matplotlib==3.1.3
-    mais itertools==6.0.0
-    msal-extensões==0.1.3
-    msal==1.1.0
-    msrest==0.6.11
-    msrestazure==0.6.3
-    ndg-httpsclient==0.5.1
-    nimbusml==1.6.1
-    numpy==1.18.2
-    oauthlib==3.1.0
-    pandas==0.25.3
-    pathspec==0.7.0
-    pip==20.0.2
-    portalocker==1.6.0
-    protobuf==3.11.3
-    pyarrow==0.16.0
-    pyasn1-módulos==0.2.8
-    pyasn1==0.4.8
-    pycparser==2,20
-    pycryptodomex=3.7.3
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
-    scikit-learn=0.22.2
-    scipy==1.4.1
-    secretstorage==3.1.2
-    setuptools==46.1.1.post20200323
-    seis==1.14.0
-    smart-open==1.10.0
-    urllib3==1.25.8
-    websocket-cliente==0.57.0
-    werkzeug==0.16.1
-    roda==0.34.2

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.