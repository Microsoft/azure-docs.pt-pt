---
title: Receitas para contentores do Docker
titleSuffix: Azure Cognitive Services
description: Utilize estes receitas de contentor para criar contentores de serviços cognitivos que podem ser reutilizadas. Contentores podem ser criados com algumas ou todas as definições de configuração para que não são necessários quando o contentor é iniciado. Assim que tiver esta nova camada de contentor (com configurações) e tiver testado localmente, pode armazenar o contentor num registo de contentor. Quando o contentor é iniciado, apenas terá essas definições que não são atualmente armazenadas no contentor.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 8a9d0d9f35c30636f4b9150c63b64098071e12e9
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271499"
---
# <a name="create-containers-for-reuse"></a>Criar contentores para reutilização

Utilize estes receitas de contentor para criar contentores de serviços cognitivos que podem ser reutilizadas. Contentores podem ser criados com algumas ou todas as definições de configuração para que fiquem _não_ necessárias quando o contentor é iniciado.

Assim que tiver esta nova camada de contentor (com configurações) e tiver testado localmente, pode armazenar o contentor num registo de contentor. Quando o contentor é iniciado, apenas terá essas definições que não são atualmente armazenadas no contentor. O contentor de registo privado fornece o espaço de configuração para que possa passar essas definições no.

## <a name="docker-run-syntax"></a>Executar a sintaxe do docker

Qualquer `docker run` exemplos neste documento partem do princípio de uma consola do Windows com um `^` caractere de continuação de linha. Considere o seguinte para sua utilização:

* Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do docker.
* Se estiver a utilizar o sistema operativo diferente do Windows ou uma consola que não seja a consola do Windows, utilize a consola/terminal correto, a sintaxe de pasta para monta e caracteres de continuação de linha para a sua consola e o sistema.  Uma vez que o contentor de serviços cognitivos é um sistema operativo Linux, a montagem de destino usa uma sintaxe de pasta de estilo do Linux.
* `docker run` exemplos utilizam o diretório de desativar o `c:` unidade para evitar conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como a entrada de diretório, poderá ter de conceder o docker permissão de serviço.

## <a name="store-no-configuration-settings-in-image"></a>Store sem definições de configuração na imagem

O exemplo `docker run` comandos para cada serviço não armazene as definições de configuração no contentor. Quando iniciar o contentor a partir de um serviço de registro ou a consola, tem de passar essas definições de configuração. O contentor de registo privado fornece o espaço de configuração para que possa passar essas definições no.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Reutilizar receita: armazenar todas as definições de configuração com o contentor

Para armazenar todas as definições de configuração, crie um `Dockerfile` com essas definições.

Problemas com essa abordagem:

* O contêiner do novo tem um nome separado e a etiqueta do contêiner original.
* Para alterar estas definições, terá de alterar os valores do Dockerfile, recriar a imagem e voltar a publicar para o seu registo.
* Se alguém obtiver acesso ao seu registo de contentor ou o anfitrião local, pode executar o contentor e utilizar os pontos de extremidade de serviços cognitivos.
* Se o seu serviço cognitivos não requer a entrada monta, não adicionar o `COPY` linhas para seu Dockerfile.

Crie o Dockerfile, extrair o contêiner de serviços cognitivos existente que pretende utilizar, em seguida, utilizar comandos do docker no Dockerfile para definir ou retirar informações o contêiner precisa.

Neste exemplo:

* Define o ponto final de faturação, `{BILLING_ENDPOINT}` a partir do anfitrião da chave de ambiente com `ENV`.
* Define a chave de API de faturação, `{ENDPOINT_KEY}` a partir do anfitrião da chave de ambiente com "ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Reutilizar receita: armazenar configurações de faturas com contentores

Este exemplo mostra como criar contentor de sentimentos dos análise de texto a partir de um Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Criar e executar o contentor [localmente](#how-to-use-container-on-your-local-host) ou a partir de seu [contentor de registo privado](#how-to-add-container-to-private-registry) conforme necessário.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Reutilizar receita: armazenar a faturação e as definições com o contentor de montagem

Este exemplo mostra como utilizar a compreensão de idiomas, poupando modelos de faturação e do Dockerfile.

* O modelo de compreensão de idiomas (LUIS) ficheiro a partir do anfitrião de cópias de ficheiros do sistema com `COPY`.
* O contentor de LUIS suporta mais de um modelo. Se todos os modelos são armazenados na mesma pasta, todos os precisa de um `COPY` instrução.
* Execute o ficheiro de docker do pai relativo do diretório de entrada de modelo. No exemplo seguinte, execute o `docker build` e `docker run` comandos do pai relativo de `/input`. A primeira `/input` sobre o `COPY` comando é o diretório do computador anfitrião. O segundo `/input` é o diretório do contentor.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Criar e executar o contentor [localmente](#how-to-use-container-on-your-local-host) ou a partir de seu [contentor de registo privado](#how-to-add-container-to-private-registry) conforme necessário.

## <a name="how-to-use-container-on-your-local-host"></a>Como utilizar o contentor no seu anfitrião local

Para criar o ficheiro do Docker, substitua `<your-image-name>` com o novo nome da imagem, em seguida, utilize:

```console
docker build -t <your-image-name> .
```

Para executar a imagem e removê-lo quando o contentor para (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Como adicionar contentor ao registo privado

Siga estes passos para utilizar o Dockerfile e coloque a nova imagem no seu registo de contentor privado.  

1. Criar um `Dockerfile` com o texto da receita de reutilização. A `Dockerfile` não tem uma extensão.

1. Substitua os valores existentes pelos colchetes angulares em seus próprios valores.

1. Compilar o arquivo numa imagem na linha de comandos ou terminal, utilizando o seguinte comando. Substitua os valores na colchetes angulares, `<>`, com seu próprio nome de contentor e a etiqueta.  

    A opção de etiqueta, `-t`, é uma forma de adicionar informações sobre o que lhe foram alterados para o contentor. Por exemplo, um nome de contentor `modified-LUIS` indica o contentor original foi disposto em camadas. Um nome de etiqueta de `with-billing-and-model` indica como o contentor de compreensão de idiomas (LUIS) foi modificado.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Inicie sessão na CLI do Azure partir de uma consola. Este comando abre um browser e precisa de autenticação. Uma vez autenticado, pode fechar o navegador e continuar a trabalhar na consola do.

    ```azure-cli
    az login
    ```

1. Entrar no seu registo privado com a CLI do Azure a partir de uma consola.

    Substitua os valores na colchetes angulares, `<my-registry>`, com o nome do registo.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    Pode também iniciar sessão com o início de sessão do docker se forem atribuídos um principal de serviço.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Etiqueta o contentor com a localização de registo privado. Substitua os valores na colchetes angulares, `<my-registry>`, com o nome do registo. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se não usar um nome de etiqueta, `latest` é implícito.

1. Envie a nova imagem para o registo de contentor privado. Quando vir o seu registo de contentor privado, o nome do contentor utilizado no comando da CLI seguinte será o nome do repositório.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar e utilizar a instância de contentor do Azure](azure-container-instance-recipe.md)

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