---
title: Receitas para recipientes Docker
titleSuffix: Azure Cognitive Services
description: Aprenda a construir, testar e armazenar recipientes com algumas ou todas as definições de configuração para implantação e reutilização.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2226e4e7c5df60a4a601408da04a1f2e67c4dc4f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067780"
---
# <a name="create-containers-for-reuse"></a>Criar contentores para reutilização

Utilize estas receitas de recipientes para criar recipientes de serviços cognitivos que podem ser reutilizados. Os recipientes podem ser construídos com algumas ou todas as definições de modo a _não_ serem necessários quando o recipiente é iniciado.

Uma vez que tenha esta nova camada de recipiente (com configurações) e o tenha testado localmente, pode guardar o recipiente num registo de contentores. Quando o recipiente começar, só necessitará das configurações que não estão atualmente armazenadas no recipiente. O contentor de registo privado fornece espaço de configuração para que possa passar as definições.

## <a name="docker-run-syntax"></a>Sintaxe de execução de docker

Quaisquer `docker run` exemplos neste documento assumem uma consola Windows com um `^` carácter de continuação de linha. Considere o seguinte para uso próprio:

* Não altere a ordem dos argumentos a menos que esteja muito familiarizado com os recipientes de estivadores.
* Se estiver a utilizar um sistema operativo diferente do Windows, ou uma consola diferente da consola Windows, utilize a consola/terminal correta, a sintaxe da pasta para montagem e o carácter de continuação da linha para a consola e o sistema.  Como o recipiente de Serviços Cognitivos é um sistema operativo Linux, o suporte alvo utiliza uma sintaxe de pasta ao estilo Linux.
* `docker run` exemplos usam o diretório fora da `c:` unidade para evitar quaisquer conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como diretório de entrada, poderá ter de conceder a permissão de serviço do estivador.

## <a name="store-no-configuration-settings-in-image"></a>Não guarde as definições de configuração na imagem

Os `docker run` comandos de exemplo para cada serviço não armazenam nenhuma configuração no recipiente. Quando iniciar o contentor a partir de uma consola ou serviço de registo, essas definições de configuração precisam de passar. O contentor de registo privado fornece espaço de configuração para que possa passar as definições.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Receita de reutilização: guarde todas as configurações com recipiente

Para armazenar todas as definições de configuração, crie uma `Dockerfile` com estas definições.

Questões com esta abordagem:

* O novo recipiente tem um nome e etiqueta separados do recipiente original.
* Para alterar estas definições, terá de alterar os valores do Dockerfile, reconstruir a imagem e republicar o seu registo.
* Se alguém tiver acesso ao seu registo de contentores ou ao seu anfitrião local, pode executar o contentor e usar os pontos finais dos Serviços Cognitivos.
* Se o seu Serviço Cognitivo não necessitar de suportes de entrada, não adicione as `COPY` linhas ao seu Dockerfile.

Crie Dockerfile, retirando do recipiente de Serviços Cognitivos existente que pretende utilizar, em seguida, use comandos docker no Dockerfile para definir ou extrair informações que o recipiente necessita.

Este exemplo:

* Define o ponto final de faturação, `{BILLING_ENDPOINT}` a partir da chave ambiente do hospedeiro utilizando `ENV` .
* Define a chave API de faturação, `{ENDPOINT_KEY}` a partir da chave ambiente do hospedeiro utilizando 'ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Receita de reutilização: armazenar configurações de faturação com recipiente

Este exemplo mostra como construir o recipiente de sentimento text Analytics a partir de um Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Construa e coloque o recipiente [localmente](#how-to-use-container-on-your-local-host) ou a partir do seu [contentor de registo privado,](#how-to-add-container-to-private-registry) se necessário.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Receita de reutilização: armazenar faturação e montar configurações com recipiente

Este exemplo mostra como usar a Compreensão linguística, a poupança de faturação e modelos do Dockerfile.

* Copia o ficheiro modelo De Compreensão de Línguas (LUIS) do sistema de ficheiros do anfitrião utilizando `COPY` .
* O recipiente LUIS suporta mais do que um modelo. Se todos os modelos estiverem armazenados na mesma pasta, todos precisam de uma `COPY` declaração.
* Executar o ficheiro estivador do parente do diretório de entradas do modelo. Para o seguinte exemplo, executar os `docker build` comandos e `docker run` comandos do parente progenitor de `/input` . O primeiro `/input` no comando é o `COPY` diretório do computador anfitrião. O segundo `/input` é o diretório do contentor.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Construa e coloque o recipiente [localmente](#how-to-use-container-on-your-local-host) ou a partir do seu [contentor de registo privado,](#how-to-add-container-to-private-registry) se necessário.

## <a name="how-to-use-container-on-your-local-host"></a>Como usar o recipiente no seu anfitrião local

Para construir o ficheiro Docker, `<your-image-name>` substitua-o pelo novo nome da imagem e, em seguida, utilize:

```console
docker build -t <your-image-name> .
```

Para executar a imagem e removê-la quando o recipiente parar `--rm` ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Como adicionar recipiente ao registo privado

Siga estes passos para utilizar o Dockerfile e coloque a nova imagem no seu registo de contentores privados.  

1. Crie um `Dockerfile` com o texto da receita de reutilização. A `Dockerfile` não tem uma extensão.

1. Substitua os valores dos suportes angulares pelos seus próprios valores.

1. Construa o ficheiro numa imagem na linha de comando ou terminal, utilizando o seguinte comando. Substitua os valores nos suportes angulares, `<>` com o nome e etiqueta do seu próprio recipiente.  

    A opção de etiqueta, `-t` é uma forma de adicionar informações sobre o que mudou para o recipiente. Por exemplo, um nome do recipiente `modified-LUIS` indica que o recipiente original foi em camadas. Um nome de identificação indica como o recipiente de compreensão linguística `with-billing-and-model` (LUIS) foi modificado.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Inscreva-se no Azure CLI a partir de uma consola. Este comando abre um browser e requer autenticação. Uma vez autenticado, pode fechar o navegador e continuar a trabalhar na consola.

    ```azurecli
    az login
    ```

1. Inscreva-se no seu registo privado com o Azure CLI a partir de uma consola.

    Substitua os valores nos suportes `<my-registry>` angulares, com o seu próprio nome de registo.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Também pode iniciar sessão com o login do Docker se lhe for atribuído um principal de serviço.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Marque o recipiente com o local do registo privado. Substitua os valores nos suportes `<my-registry>` angulares, com o seu próprio nome de registo. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se não usar um nome de identificação, `latest` está implícito.

1. Empurre a nova imagem para o registo privado do contentor. Quando visualizar o seu registo de contentores privados, o nome do recipiente utilizado no seguinte comando CLI será o nome do repositório.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar e utilizar a instância do recipiente Azure](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->