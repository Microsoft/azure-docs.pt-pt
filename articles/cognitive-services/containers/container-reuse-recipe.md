---
title: Receitas para recipientes Docker
titleSuffix: Azure Cognitive Services
description: Aprenda a construir, testar e armazenar recipientes com algumas ou todas as definições de configuração para implementação e reutilização.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717199"
---
# <a name="create-containers-for-reuse"></a>Criar contentores para reutilização

Utilize estas receitas de recipientes para criar recipientes de serviços cognitivos que podem ser reutilizados. Os recipientes podem ser construídos com algumas ou todas as configurações de configuração para que _não_ sejam necessários quando o recipiente é iniciado.

Assim que tiver esta nova camada de recipiente (com configurações), e tiver testado localmente, pode armazenar o recipiente num registo de contentores. Quando o recipiente começar, só necessitará das definições que não estão atualmente armazenadas no recipiente. O recipiente de registo privado fornece espaço de configuração para que você passe essas definições.

## <a name="docker-run-syntax"></a>Docker executar sintaxe

Qualquer `docker run` exemplos deste documento assumem uma consola Windows com um carácter de continuação de linha `^`. Considere o seguinte para seu próprio uso:

* Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do docker.
* Se estiver a utilizar um sistema operativo diferente do Windows, ou uma consola que não seja a consola do Windows, utilize a consola/terminal correto, a sintaxe de pastas para suportes e o carácter de continuação da linha para a consola e o sistema.  Como o recipiente dos Serviços Cognitivos é um sistema operativo Linux, o suporte-alvo usa uma sintaxe de pasta ao estilo Linux.
* `docker run` exemplos usam o diretório fora da unidade `c:` para evitar quaisquer conflitos de permissões no Windows. Se precisar de utilizar um diretório específico como a entrada de diretório, poderá ter de conceder o docker permissão de serviço.

## <a name="store-no-configuration-settings-in-image"></a>Não guarde nenhuma definição de configuração na imagem

O exemplo `docker run` comandos para cada serviço não armazenam quaisquer configurações de configuração no recipiente. Quando iniciar o recipiente a partir de um serviço de consola ou registo, essas definições de configuração têm de passar. O recipiente de registo privado fornece espaço de configuração para que você passe essas definições.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Receita de reutilização: guarde todas as definições de configuração com recipiente

Para armazenar todas as configurações de configuração, crie uma `Dockerfile` com essas definições.

Questões com esta abordagem:

* O novo recipiente tem um nome e etiqueta separados do recipiente original.
* Para alterar estas definições, terá de alterar os valores do Dockerfile, reconstruir a imagem e republicar para o seu registo.
* Se alguém tiver acesso ao seu registo de contentores ou ao seu anfitrião local, pode executar o contentor e usar os pontos finais dos Serviços Cognitivos.
* Se o seu Serviço Cognitivo não necessitar de suportes de entrada, não adicione as linhas `COPY` ao seu Dockerfile.

Crie o Dockerfile, retirando do recipiente de Serviços Cognitivos existente que pretende utilizar, em seguida, use comandos de estivador no Dockerfile para definir ou puxar informações que o recipiente precisa.

Este exemplo:

* Define o ponto final da faturação, `{BILLING_ENDPOINT}` da chave ambiental do hospedeiro usando `ENV`.
* Define a chave API de faturação, `{ENDPOINT_KEY}` da chave ambiental do hospedeiro utilizando a 'ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Receita de reutilização: configurações de faturação de loja com recipiente

Este exemplo mostra como construir o recipiente de sentimento do Text Analytics a partir de um Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Construa e corra o recipiente [localmente](#how-to-use-container-on-your-local-host) ou a partir do seu [recipiente de registo privado,](#how-to-add-container-to-private-registry) conforme necessário.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Receita de reutilização: configuração de faturação e montagem de loja com recipiente

Este exemplo mostra como usar a Compreensão da Linguagem, poupando faturação e modelos do Dockerfile.

* Copie o ficheiro do modelo Language Understanding (LUIS) do sistema de ficheiros do anfitrião utilizando `COPY`.
* O recipiente LUIS suporta mais do que um modelo. Se todos os modelos estiverem armazenados na mesma pasta, todos precisam de uma declaração `COPY`.
* Execute o ficheiro do estivador do parente do diretório de entrada do modelo. Para o seguinte exemplo, execute os comandos `docker build` e `docker run` do progenitor relativo da `/input`. A primeira `/input` no comando `COPY` é o diretório do computador anfitrião. A segunda `/input` é o diretório do contentor.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Construa e corra o recipiente [localmente](#how-to-use-container-on-your-local-host) ou a partir do seu [recipiente de registo privado,](#how-to-add-container-to-private-registry) conforme necessário.

## <a name="how-to-use-container-on-your-local-host"></a>Como usar o recipiente no seu hospedeiro local

Para construir o ficheiro Docker, substitua `<your-image-name>` com o novo nome da imagem e, em seguida, use:

```console
docker build -t <your-image-name> .
```

Para executar a imagem e removê-la quando o recipiente parar (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Como adicionar o contentor ao registo privado

Siga estes passos para utilizar o Dockerfile e coloque a nova imagem no registo de contentores privados.  

1. Crie um `Dockerfile` com o texto da receita de reutilização. Um `Dockerfile` não tem uma extensão.

1. Substitua quaisquer valores nos suportes angulares com os seus próprios valores.

1. Construa o ficheiro numa imagem na linha de comando ou terminal, utilizando o seguinte comando. Substitua os valores nos suportes angulares, `<>`, com o seu próprio nome e etiqueta do recipiente.  

    A opção de etiqueta, `-t`, é uma forma de adicionar informações sobre o que mudou para o recipiente. Por exemplo, um nome de recipiente de `modified-LUIS` indica que o recipiente original foi colocado em camadas. Um nome de etiqueta de `with-billing-and-model` indica como o recipiente de Compreensão linguística (LUIS) foi modificado.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Inscreva-se no Azure CLI a partir de uma consola. Este comando abre um navegador e requer autenticação. Uma vez autenticado, pode fechar o navegador e continuar a trabalhar na consola.

    ```azurecli
    az login
    ```

1. Inscreva-se no seu registo privado com o Azure CLI a partir de uma consola.

    Substitua os valores nos suportes angulares, `<my-registry>`, com o seu próprio nome de registo.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Também pode iniciar sessão com o docker login se lhe for atribuído um diretor de serviço.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Marque o recipiente com a localização do registo privado. Substitua os valores nos suportes angulares, `<my-registry>`, com o seu próprio nome de registo. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se não usar um nome de etiqueta, `latest` está implícito.

1. Empurre a nova imagem para o seu registo de contentores privados. Quando visualizar o seu registo de contentores privados, o nome do recipiente utilizado no seguinte comando CLI será o nome do repositório.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar e utilizar a Instância de Contentores Azure](azure-container-instance-recipe.md)

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