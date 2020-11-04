---
title: Modelos de pacote
titleSuffix: Azure Machine Learning
description: Embale um modelo. Os modelos podem ser embalados como uma imagem de estivador, que pode então descarregar, ou pode criar um Dockerfile e usá-lo para construir a imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 299eb316b534ddc9d5eee934cc15eae841276038
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312635"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Como embalar um modelo registado com Docker

Este artigo mostra como embalar um modelo de Aprendizagem automática Azure registrado com Docker.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já treinou e registou um modelo no seu espaço de trabalho de aprendizagem automática. Para aprender a treinar e registar um modelo de aprendizagem de scikit, [siga este tutorial.](how-to-train-scikit-learn.md)


## <a name="package-models"></a>Modelos de pacote

Em alguns casos, pode querer criar uma imagem Docker sem implementar o modelo (se, por exemplo, [pretender implantar-se no Azure App Service).](how-to-deploy-app-service.md) Ou talvez queira descarregar a imagem e executá-la numa instalação local do Docker. Pode até querer descarregar os ficheiros utilizados para construir a imagem, inspecioná-los, modificá-los e construir a imagem manualmente.

A embalagem do modelo permite-lhe fazer estas coisas. Embala todos os ativos necessários para hospedar um modelo como um serviço web e permite-lhe descarregar uma imagem docker totalmente construída ou os ficheiros necessários para construir um. Existem duas formas de utilizar a embalagem modelo:

**Faça o download de um modelo embalado:** Descarregue uma imagem do Docker que contenha o modelo e outros ficheiros necessários para a hospedar como um serviço web.

**Gerar um Dockerfile:** Descarregue o Dockerfile, modelo, script de entrada e outros ativos necessários para construir uma imagem do Docker. Em seguida, pode inspecionar os ficheiros ou fazer alterações antes de construir a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> Criar um pacote é semelhante à implementação de um modelo. Utiliza um modelo registado e uma configuração de inferência.

> [!IMPORTANT]
> Para descarregar uma imagem totalmente construída ou construir uma imagem localmente, você precisa ter [Docker](https://www.docker.com) instalado no seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Descarregue um modelo embalado

O exemplo a seguir constrói uma imagem, que está registada no registo do contentor Azure para o seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, pode usar `package.pull()` para puxar a imagem para o seu ambiente local do Docker. A saída deste comando mostrará o nome da imagem. Por exemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de descarregar o modelo, use o `docker images` comando para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Para iniciar um recipiente local com base nesta imagem, utilize o seguinte comando para iniciar um contentor nomeado a partir da concha ou da linha de comando. Substitua o `<imageid>` valor pelo ID de imagem devolvido pelo `docker images` comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Este comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para o porto no recipiente em que o serviço web está a ouvir (5001). Atribui também o nome `mycontainer` ao recipiente, o que facilita a paragem do recipiente. Após o início do contentor, pode submeter pedidos a `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um Dockerfile e dependências

O exemplo a seguir mostra como descarregar o Dockerfile, modelo e outros ativos necessários para construir uma imagem localmente. O `generate_dockerfile=True` parâmetro indica que quer os ficheiros, não uma imagem totalmente construída.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Este código descarrega os ficheiros necessários para construir a imagem para o `imagefiles` diretório. O Dockerfile incluído nos ficheiros guardados refere uma imagem base armazenada num registo de contentores Azure. Quando constrói a imagem na instalação local do Docker, tem de utilizar o endereço, o nome de utilizador e a palavra-passe para autenticar o registo. Utilize os seguintes passos para construir a imagem utilizando uma instalação local do Docker:

1. A partir de uma sessão de concha ou linha de comando, utilize o seguinte comando para autenticar Docker com o registo do contentor Azure. `<address>` `<username>` Substitua, e pelos `<password>` valores recuperados por `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para construir a imagem, utilize o seguinte comando. `<imagefiles>`Substitua-o pelo caminho do diretório onde `package.save()` guardou os ficheiros.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o nome de imagem para `myimage` .

Para verificar se a imagem foi construída, use o `docker images` comando. Deve ver a `myimage` imagem na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Para iniciar um novo recipiente com base nesta imagem, utilize o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Este comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para o porto no recipiente em que o serviço web está a ouvir (5001). Atribui também o nome `mycontainer` ao recipiente, o que facilita a paragem do recipiente. Após o início do contentor, pode submeter pedidos a `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Cliente de exemplo para testar o recipiente local

O seguinte código é um exemplo de um cliente Python que pode ser usado com o recipiente:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Por exemplo, clientes de outras linguagens de programação, consulte [os modelos Consume implementados como serviços web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Pare o recipiente Docker

Para parar o recipiente, utilize o seguinte comando a partir de uma concha ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas de uma implementação com falhas](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)
