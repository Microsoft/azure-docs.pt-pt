---
title: Tarefa em várias etapas para construir, testar & imagem de patch
description: Introdução a tarefas em várias etapas, uma característica das Tarefas ACR no Registo de Contentores Azure que fornece fluxos de trabalho baseados em tarefas para construir, testar e remendar imagens de contentores na nuvem.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399692"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Executar tarefas de construção, teste e patch em várias etapas em tarefas ACR

As tarefas em várias etapas alargam a capacidade única de construção e impulso de imagem das tarefas ACR com fluxos de trabalho multi-etapas e multi-contentores. Utilize tarefas em várias etapas para construir e empurrar várias imagens, em série ou em paralelo. Em seguida, executar essas imagens como comandos dentro de uma única tarefa executada. Cada passo define uma construção ou operação de impulso de imagem de recipiente, e também pode definir a execução de um recipiente. Cada passo numa tarefa em várias etapas utiliza um recipiente como o seu ambiente de execução.

> [!IMPORTANT]
> Se anteriormente tiver criado tarefas durante a pré-visualização com o comando `az acr build-task`, essas tarefas têm de ser recriadas com o [az acr task][az-acr-task].

Por exemplo, pode executar uma tarefa com passos que automatizam a seguinte lógica:

1. Construir uma imagem de aplicação web
1. Executar o recipiente de aplicação web
1. Construir uma imagem de teste de aplicação web
1. Executar o recipiente de teste de aplicação web que eexecuta testes contra o recipiente de aplicação em execução
1. Se os testes passarem, construa um pacote de arquivo de gráfico helm
1. Execute `helm upgrade` um usando o novo pacote de arquivo de gráficohelm

Todos os passos são realizados dentro do Azure, descarregando o trabalho para os recursos computacionais do Azure e libertando-o da gestão de infraestruturas. Além do registo de contentores Azure, paga apenas pelos recursos que utiliza. Para obter informações sobre os preços, consulte a secção **construção** de contentores no registo de [contentores azure][pricing].


## <a name="common-task-scenarios"></a>Cenários de tarefa comuns

Tarefas em várias etapas permitem cenários como a seguinte lógica:

* Construa, marque e empurre uma ou mais imagens de contentores, em série ou em paralelo.
* Executar e capturar resultados de teste da unidade e cobertura de código.
* Executar e capturar testes funcionais. A ACR Tasks suporta a execução de mais de um contentor, executando uma série de pedidos entre eles.
* Executar execução baseada em tarefas, incluindo etapas pré/post de uma construção de imagem de recipiente.
* Coloque um ou mais recipientes com o seu motor de implantação favorito para o seu ambiente alvo.

## <a name="multi-step-task-definition"></a>Definição de tarefa sem etapas

Uma tarefa em várias etapas em Tarefas ACR é definida como uma série de passos dentro de um ficheiro YAML. Cada passo pode especificar dependências na conclusão bem sucedida de um ou mais passos anteriores. Estão disponíveis os seguintes tipos de passos de tarefa:

* [`build`](container-registry-tasks-reference-yaml.md#build): Construir uma ou mais `docker build` imagens de contentores utilizando sintaxe familiar, em série ou em paralelo.
* [`push`](container-registry-tasks-reference-yaml.md#push): Empurre as imagens construídas para um registo de contentores. Registos privados como o Registo de Contentores de Azure são apoiados, assim como o centro público de Docker.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Executar um recipiente, de modo a poder funcionar como uma função no contexto da tarefa de funcionamento. Pode passar parâmetros para os `[ENTRYPOINT]`do recipiente e especificar propriedades como env, desprender e outros parâmetros familiares. `docker run` O `cmd` tipo de passo permite a unidade e os testes funcionais, com execução simultânea do contentor.

Os seguintes cortes mostram como combinar estes tipos de passos de tarefa. Tarefas em várias etapas podem ser tão simples como construir uma única imagem a partir de um Dockerfile e empurrar para o seu registo, com um ficheiro YAML semelhante a:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Ou mais complexa, como esta definição fictícia de várias etapas que inclui passos para construção, teste, pacote de leme e implantação do leme (registo de contentores e configuração de repositório helm não mostrada):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Consulte exemplos de tarefas para [ficheiros](container-registry-tasks-samples.md) YAML de tarefas em várias etapas e ficheiros Docker para vários cenários.

## <a name="run-a-sample-task"></a>Executar uma tarefa de amostra

As tarefas suportam a execução manual, chamada "corrida rápida", e execução automatizada em Git cometer ou atualização de imagem base.

Para executar uma tarefa, primeiro define os passos da tarefa num ficheiro YAML e, em seguida, executa o comando Azure CLI [az acr run][az-acr-run].

Aqui está um exemplo de comando Azure CLI que executa uma tarefa usando um ficheiro YAML de tarefa de amostra. Os seus passos constroem-se e depois empurram uma imagem. Atualize `\<acrName\>` com o nome do seu próprio registo de contentores Azure antes de executar o comando.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Quando executa a tarefa, a saída deve mostrar o progresso de cada passo definido no ficheiro YAML. Na saída seguinte, os `acb_step_0` passos aparecem como e `acb_step_1`.

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Para obter mais informações sobre as construções automatizadas sobre o compromisso de Git ou a atualização de imagem base, consulte as construções de [imagem Automate](container-registry-tutorial-build-task.md) e a atualização de [imagem base constrói artigos](container-registry-tutorial-base-image-update.md) tutoriais.

## <a name="next-steps"></a>Passos seguintes

Pode encontrar aqui referência seleções em várias etapas e exemplos:

* [Referência de tarefa](container-registry-tasks-reference-yaml.md) - Tipos de etapas de tarefa, suas propriedades e utilização.
* [Exemplos de tarefas](container-registry-tasks-samples.md) - Exemplo `task.yaml` e ficheiros Docker para vários cenários, simples de complexo.
* [Cmd repo](https://github.com/AzureCR/cmd) - Uma coleção de recipientes como comandos para tarefas ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task