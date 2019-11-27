---
title: Tarefa de várias etapas para compilar, testar & imagem de patch
description: Introdução a tarefas de várias etapas, um recurso de tarefas ACR no registro de contêiner do Azure que fornece fluxos de trabalho baseados em tarefas para criação, teste e aplicação de patch de imagens de contêiner na nuvem.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 3ed071fa2027e91ee5bc6c07738dc66763454847
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456170"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Executar tarefas de compilação, teste e patch de várias etapas em tarefas ACR

Tarefas de várias etapas estendem a funcionalidade de compilação e envio de imagem única de tarefas ACR com fluxos de trabalho de várias etapas com base em vários contêineres. Use tarefas de várias etapas para criar e enviar por push várias imagens, em série ou em paralelo. Em seguida, execute essas imagens como comandos em uma única tarefa executada. Cada etapa define uma operação de criação de imagem de contêiner ou push e também pode definir a execução de um contêiner. Cada etapa em uma tarefa de várias etapas usa um contêiner como seu ambiente de execução.

> [!IMPORTANT]
> Se você tiver criado previamente tarefas durante a visualização com o comando `az acr build-task`, essas tarefas precisarão ser recriadas usando o comando [AZ ACR Task][az-acr-task] .

Por exemplo, você pode executar uma tarefa com etapas que automatizam a seguinte lógica:

1. Criar uma imagem de aplicativo Web
1. Executar o contêiner do aplicativo Web
1. Criar uma imagem de teste de aplicativo Web
1. Executar o contêiner de teste de aplicativo Web que executa testes no contêiner do aplicativo em execução
1. Se os testes forem aprovados, crie um pacote de arquivo gráfico do Helm
1. Executar um `helm upgrade` usando o novo pacote de arquivo de gráfico Helm

Todas as etapas são executadas no Azure, descarregando o trabalho para os recursos de computação do Azure e liberando você do gerenciamento de infraestrutura. Além do registro de contêiner do Azure, você paga apenas pelos recursos que usar. Para obter informações sobre preços, consulte a seção **Build de contêiner** em [preços do registro de contêiner do Azure][pricing].


## <a name="common-task-scenarios"></a>Cenários comuns de tarefas

As tarefas de várias etapas permitem cenários como a seguinte lógica:

* Crie, marque e envie por push uma ou mais imagens de contêiner, em série ou em paralelo.
* Executar e capturar os resultados de teste de unidade e de cobertura de código.
* Executar e capturar testes funcionais. As tarefas ACR dão suporte à execução de mais de um contêiner, executando uma série de solicitações entre eles.
* Execute a execução baseada em tarefas, incluindo etapas anteriores/posteriores de uma compilação de imagem de contêiner.
* Implante um ou mais contêineres com seu mecanismo de implantação favorito em seu ambiente de destino.

## <a name="multi-step-task-definition"></a>Definição de tarefa de várias etapas

Uma tarefa de várias etapas em tarefas ACR é definida como uma série de etapas em um arquivo YAML. Cada etapa pode especificar dependências na conclusão bem-sucedida de uma ou mais etapas anteriores. Os seguintes tipos de etapa de tarefa estão disponíveis:

* [`build`](container-registry-tasks-reference-yaml.md#build): Crie uma ou mais imagens de contêiner usando sintaxe de `docker build` familiar, em série ou em paralelo.
* [`push`](container-registry-tasks-reference-yaml.md#push): envie por push imagens compiladas para um registro de contêiner. Registros privados, como o registro de contêiner do Azure, são suportados, assim como o Hub do Docker público.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): executar um contêiner, de modo que ele possa operar como uma função dentro do contexto da tarefa em execução. Você pode passar parâmetros para o `[ENTRYPOINT]`do contêiner e especificar propriedades como env, Detach e outros parâmetros de `docker run` familiares. O tipo de etapa `cmd` permite testes de unidade e funcionais, com execução simultânea de contêiner.

Os trechos de código a seguir mostram como combinar esses tipos de etapa de tarefa. Tarefas de várias etapas podem ser tão simples quanto criar uma única imagem de um Dockerfile e enviar por push para o registro, com um arquivo YAML semelhante a:

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Ou mais complexos, como esta definição de várias etapas fictícias, que inclui etapas para Build, Test, Helm Package e Helm Deploy (registro de contêiner e configuração de repositório Helm não mostrados):

```yml
version: v1.0.0
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

Consulte [exemplos de tarefas](container-registry-tasks-samples.md) para arquivos de YAML de tarefas de várias etapas e Dockerfiles para vários cenários.

## <a name="run-a-sample-task"></a>Executar uma tarefa de exemplo

As tarefas dão suporte à execução manual, chamada de "execução rápida" e execução automatizada na confirmação git ou na atualização da imagem base.

Para executar uma tarefa, primeiro defina as etapas da tarefa em um arquivo YAML e execute CLI do Azure o comando [AZ ACR Run][az-acr-run].

Aqui está um exemplo CLI do Azure comando que executa uma tarefa usando um arquivo YAML de tarefa de exemplo. Suas etapas criam e enviam uma imagem por push. Atualize `\<acrName\>` com o nome do seu próprio registro de contêiner do Azure antes de executar o comando.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Quando você executa a tarefa, a saída deve mostrar o progresso de cada etapa definida no arquivo YAML. Na saída a seguir, as etapas aparecem como `acb_step_0` e `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
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

Para obter mais informações sobre as compilações automatizadas na confirmação git ou na atualização da imagem base, consulte os artigos sobre como [automatizar](container-registry-tutorial-build-task.md) compilações de imagem e [compilações de atualização de imagem base](container-registry-tutorial-base-image-update.md) .

## <a name="next-steps"></a>Passos seguintes

Você pode encontrar exemplos e referência de tarefas de várias etapas aqui:

* [Referência de tarefa](container-registry-tasks-reference-yaml.md) -tipos de etapa de tarefa, suas propriedades e uso.
* [Exemplos de tarefas](container-registry-tasks-samples.md) – exemplo `task.yaml` e arquivos do Docker para vários cenários, simples a complexo.
* [Repositório cmd](https://github.com/AzureCR/cmd) – uma coleção de contêineres como comandos para tarefas ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task