---
title: Implementar um modelo de aprendizagem automática para funções Azure com cache Azure para Redis
description: Neste artigo, irá implementar um modelo da Azure Machine Learning como uma aplicação de função em Azure Functions usando uma Cache Azure para a instância Redis. Azure Cache para Redis é extremamente performante e escalável – quando emparelhado com um modelo Azure Machine Learning, ganha baixa latência e alta produção na sua aplicação.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 83fc88a57a1cdbec35a8f939a81698799d290d70
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183629"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Implementar um modelo de aprendizagem automática para funções Azure com cache Azure para Redis 

Neste artigo, irá implementar um modelo da Azure Machine Learning como uma aplicação de função em Azure Functions usando uma Cache Azure para a instância Redis.  

Azure Cache para Redis é extremamente performante e escalável – quando emparelhado com um modelo Azure Machine Learning, ganha baixa latência e alta produção na sua aplicação. Um par de cenários em que uma cache é particularmente benéfica é quando inferenumos os dados e para os resultados reais da inferência do modelo. Em qualquer dos cenários, os meta-dados ou resultados são armazenados na memória, o que leva a um aumento do desempenho. 

> [!NOTE]
> Embora tanto o Azure Machine Learning como o Azure Functions estejam geralmente disponíveis, a capacidade de embalar um modelo do serviço de Machine Learning para Funções está em pré-visualização.  
>

## <a name="prerequisites"></a>Pré-requisitos
* Azure subscription - [crie uma gratuitamente](https://azure.microsoft.com/free/).
* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte o [Artigo Criar um espaço de trabalho.](../machine-learning/how-to-manage-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli).
* Um modelo de aprendizagem automática treinado registado no seu espaço de trabalho. Se não tiver um modelo, use o tutorial de [classificação de imagem: modelo de comboio](../machine-learning/tutorial-train-models-with-aml.md) para treinar e registar um.

> [!IMPORTANT]
> Os códigos de corte neste artigo assumem que definiu as seguintes variáveis:
>
> * `ws` - O seu espaço de trabalho para aprendizagem de máquinas Azure.
> * `model` - O modelo registado que será implantado.
> * `inference_config` - A configuração da inferência para o modelo.
>
> Para obter mais informações sobre a definição destas variáveis, consulte [implementar modelos com Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma Cache Azure para a instância Redis 
Poderá implementar um modelo de machine learning para funções Azure com qualquer instância de cache Básica, Padrão ou Premium. Para criar uma instância de cache, siga estes passos.  

1. Vá à página inicial do portal Azure ou abra o menu da barra lateral e, em seguida, **selecione Criar um recurso**. 
   
1. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecione Azure Cache para Redis.":::
   
1. Na página **New Redis Cache,** configufique as definições para o seu novo cache.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenha apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será *\<DNS name> .redis.cache.windows.net*. | 
   | **Subscrição** | Desça e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Desça e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Localização** | Desça e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
   | **Escalão de preço** | Desça e selecione um [nível de preços](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](cache-overview.md). |

1. Selecione o **separador 'Rede'** ou clique no botão **'Rede'** na parte inferior da página.

1. No **separador 'Rede',** selecione o seu método de conectividade.

1. Selecione o **Seguinte: Separador avançado** ou clique no **seguinte: Botão avançado** na parte inferior da página.

1. No separador **Avançado** para uma instância de cache básica ou padrão, selecione o interruptor de ativação se pretender ativar uma porta não TLS.

1. No separador **Avançado** para a instância de cache premium, configurar as definições para a porta não-TLS, clustering e persistência de dados.

1. Selecione o **separador Seguinte: Tags** ou clique no botão **Seguinte: Tags** na parte inferior da página.

1. Opcionalmente, no separador **Tags, insira** o nome e o valor se desejar categorizar o recurso. 

1. Selecione **Rever + criar**. É levado para o separador 'Rever +' onde o Azure valida a sua configuração.

1. Depois de aparecer a mensagem de validação verde, selecione **Criar**.

Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada. 

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Antes de implementar, deve definir o que é necessário para executar o modelo como um serviço web. A lista a seguir descreve os itens essenciais necessários para uma implantação:

* Um __roteiro de entrada.__ Este script aceita pedidos, marca o pedido usando o modelo e devolve os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico do seu modelo; deve compreender o formato dos dados de pedido de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados devolvidos aos clientes.
    >
    > Se os dados do pedido estiverem num formato que não seja utilizável pelo seu modelo, o script pode transformá-lo num formato aceitável. Também pode transformar a resposta antes de devolvê-la ao cliente.
    >
    > Por predefinição, quando a embalagem para funções, a entrada é tratada como texto. Se estiver interessado em consumir os bytes crus da entrada (por exemplo, para os gatilhos Blob), deve utilizar [o AMLRequest para aceitar dados brutos](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data).

Para a função de funcionamento, certifique-se de que se liga a um ponto final do Redis.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para obter mais informações sobre o script de entrada, consulte [o código de pontuação De definir.](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

* **Dependências**, tais como scripts de ajuda ou pacotes Python/Conda necessários para executar o script de entrada ou modelo

Estas entidades são encapsuladas numa __configuração de inferência.__ A configuração de inferência referencia o script de entrada e outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para utilização com Funções Azure, deve utilizar um objeto [Ambiente.](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) Por favor, note que se estiver a definir um ambiente personalizado, deve adicionar azureml-padrão com >versão = 1.0.45 como dependência de pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web. O exemplo a seguir demonstra a criação de um objeto ambiental e a sua utilização com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes de formação e implantação.](../machine-learning/how-to-use-environments.md)

Para obter mais informações sobre a configuração de inferência, consulte [implementar modelos com Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> Ao implementar para funções, não necessita de criar uma __configuração de implementação__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instale o pacote de pré-visualização SDK para suporte a funções

Para construir pacotes para funções Azure, tem de instalar o pacote de pré-visualização SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem Docker que é implantada para funções Azure, utilize [azureml.contrib.functions.package](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) ou a função de pacote específica para o gatilho que está interessado em utilizar. O seguinte corte de código demonstra como criar um novo pacote com um gatilho HTTP a partir do modelo e configuração de inferência:

> [!NOTE]
> O código de corte assume que `model` contém um modelo registado, e que contém `inference_config` a configuração para o ambiente de inferência. Para obter mais informações, consulte [implementar modelos com Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Quando `show_output=True` , a saída do processo de construção do Docker é mostrada. Uma vez terminado o processo, a imagem foi criada no Registo do Contentor Azure para o seu espaço de trabalho. Uma vez construída a imagem, é visualizada a localização do registo do seu contentor Azure. A localização devolvida está no `<acrinstance>.azurecr.io/package@sha256:<imagename>` formato.

> [!NOTE]
> A embalagem para funções suporta atualmente os gatilhos HTTP, Blob e os gatilhos do autocarro service. Para obter mais informações sobre os gatilhos, consulte [as ligações Azure Functions](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Guarde as informações de localização, tal como é utilizada ao implementar a imagem.

## <a name="deploy-image-as-a-web-app"></a>Implementar a imagem como uma aplicação web

1. Utilize o seguinte comando para obter as credenciais de login do Registo do Contentor Azure que contém a imagem. `<myacr>`Substitua-o pelo valor devolvido anteriormente a partir `package.location` de: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A saída deste comando é semelhante ao seguinte documento JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Guarde o valor para __o nome de utilizador__ e uma das __palavras-passe.__

1. Se ainda não tiver um grupo de recursos ou um plano de serviço de aplicações para implementar o serviço, os seguintes comandos demonstram como criar ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Neste exemplo, é utilizado um nível de preços _básicos Linux_ `--sku B1` ( )

    > [!IMPORTANT]
    > As imagens criadas por Azure Machine Learning usam o Linux, pelo que deve utilizar o `--is-linux` parâmetro.

1. Crie a conta de armazenamento para usar para o armazenamento de trabalho na web e obtenha a sua cadeia de ligação. `<webjobStorage>`Substitua-o pelo nome que pretende utilizar.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Para criar a aplicação de função, utilize o seguinte comando. `<app-name>`Substitua-o pelo nome que pretende utilizar. Substitua `<acrinstance>` e `<imagename>` pelos valores devolvidos `package.location` anteriormente. `<webjobStorage>`Substitua-a pelo nome da conta de armazenamento do passo anterior:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Neste momento, a aplicação de função foi criada. No entanto, uma vez que não forneceu a cadeia de ligação para o gatilho HTTP ou credenciais para o Registo do Contentor Azure que contém a imagem, a aplicação de função não está ativa. Nos próximos passos, fornece-se a cadeia de ligação e as informações de autenticação para o registo do contentor. 

1. Para fornecer à aplicação de função as credenciais necessárias para aceder ao registo do contentor, utilize o seguinte comando. `<app-name>`Substitua-o pelo nome da aplicação de funções. Substitua `<acrinstance>` e `<imagetag>` pelos valores da chamada AZ CLI no passo anterior. Substitua `<username>` e `<password>` pela informação de login da ACR recuperada anteriormente:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Este comando devolve informações semelhantes ao seguinte documento JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Neste ponto, a aplicação de função começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar alguns minutos até que a imagem esteja carregada. Pode monitorizar o progresso utilizando o portal Azure.

## <a name="test-azure-functions-http-trigger"></a>Testar funções azure http trigger 

Vamos agora executar e testar o nosso gatilho Azure Functions HTTP.

1. Aceda à sua aplicação de função no portal Azure.
1. No âmbito do programador, selecione **Código + Teste**. 
1. No lado direito, selecione o **separador Entrada.** 
1. Clique no botão **Executar** para testar o gatilho Azure Functions HTTP. 

Implementou agora com sucesso um modelo da Azure Machine Learning como uma aplicação de função utilizando um Azure Cache para o exemplo de Redis. Saiba mais sobre a Azure Cache for Redis navegando para os links na secção abaixo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se terminar com o arranque rápido, pode apagar os recursos Azure que criou neste arranque rápido para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. Quando elimina um grupo de recursos, todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.

### <a name="to-delete-a-resource-group"></a>Para eliminar um grupo de recursos

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Na caixa **Filtrar por nome...**, escreva o nome do grupo de recursos. No grupo de recursos na lista de resultados, selecione **...** e, em seguida, selecione **Eliminar grupo de recursos**.

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e, em seguida, selecione **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os respetivos recursos são eliminados.

## <a name="next-steps"></a>Passos seguintes 

* Saiba mais sobre [a Azure Cache para Redis](./cache-overview.md)
* Aprenda a configurar a sua aplicação de função na documentação [funções.](../azure-functions/functions-create-function-linux-custom-image.md)
* [Referência da API](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) 
* Crie uma [aplicação Python que usa Azure Cache para Redis](./cache-python-get-started.md)
