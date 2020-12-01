---
title: Fluxo de trabalho de tarefa para gerir o conteúdo do registo público
description: Crie um fluxo automatizado de trabalho de registo de contentores Azure para rastrear, gerir e consumir conteúdos de imagem pública num registo privado de contentores Azure.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349287"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Como consumir e manter o conteúdo público com tarefas de registo de contentores Azure

Este artigo fornece uma amostra de fluxo de trabalho no Registo de Contentores Azure para ajudá-lo a gerir o consumo e a manutenção de conteúdos públicos:

1. Importar cópias locais de imagens públicas dependentes.
1. Validar imagens públicas através de verificação de segurança e testes funcionais.
1. Promover as imagens aos registos privados para uso interno.
1. Desencadear atualizações de imagem base para aplicações dependentes de conteúdo público.
1. Utilize [tarefas de registo de contentores Azure](container-registry-tasks-overview.md) para automatizar este fluxo de trabalho.

O fluxo de trabalho é resumido na seguinte imagem:

![Consumo de conteúdo público Fluxo de trabalho](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

O fluxo de trabalho de importação fechado ajuda a gerir as dependências da sua organização em artefactos geridos externamente - por exemplo, imagens provenientes de registos públicos, incluindo [Docker Hub,][docker-hub] [GCR,][gcr] [Quay,][quay] [Registo de Contentores GitHub,][ghcr]Registo de [Contentores da Microsoft,][mcr]ou mesmo [outros registos de contentores Azure.][acr] 

Para obter antecedentes sobre os riscos introduzidos pelas dependências de conteúdos públicos e como utilizar o Registo de Contentores Azure para os mitigar, consulte o post do [Blog de Conteúdo Público consumista][oci-consuming-public-content] do OCI e gere conteúdos [públicos com registo de contentores Azure.](buffer-gate-public-content.md)

Você pode usar o Azure Cloud Shell ou uma instalação local do Azure CLI para completar este walkthrough. Recomenda-se a versão 2.10 ou posterior do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-cli].

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

![componentes de fluxo de trabalho de importação](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Este walkthrough configura- se:

1. Três **registos de contentores,** representando:
   * Um [estivador][docker-hub] simulado para `publicregistry` suportar a alteração da imagem base
   * Registo de `contoso` equipas para partilhar imagens privadas
   * Registo partilhado empresa/equipa ( `baseartifacts` ) para conteúdo público importado
1. Uma **tarefa de ACR** em cada registo. As tarefas:  
   1. Construir uma imagem pública simulada `node`
   1. Importar e validar a `node` imagem para o registo partilhado da empresa/equipa
   1. Construir e implementar a `hello-world` imagem
1. **Definições de tarefas ACR,** incluindo configurações para:
1. Uma coleção de credenciais de **registo,** que são ponteiros para um cofre chave
1. Uma coleção de **segredos,** disponíveis dentro de `acr-task.yaml` um, que são ponteiros para um cofre chave
1. Uma coleção de **valores configurados** usados dentro de um `acr-task.yaml`
1. Um **cofre chave azul** para garantir todos os segredos
1. Uma **instância de contentores Azure,** que acolhe a `hello-world` aplicação de construção

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes passos configuram valores para recursos criados e utilizados na passagem.

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Configure variáveis únicas para o seu ambiente. Seguimos as melhores práticas para colocar recursos com conteúdo durável no seu próprio grupo de recursos para minimizar a eliminação acidental. No entanto, pode colocá-las num único grupo de recursos, se desejar.

Os exemplos deste artigo são formatados para a casca de bash.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Repositórios de git e fichas

Para simular o seu ambiente, garfo cada um dos seguintes git repos em repositórios que você pode gerir. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Em seguida, atualize as seguintes variáveis para os seus repositórios forcados.

O `:main` anexado ao fim dos URLs git representa o ramo de repositório padrão.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

Você precisa de um [token de acesso GitHub (PAT)][git-token] para tarefas ACR para clonar e estabelecer webhooks Git. Para obter medidas para criar um símbolo com as permissões necessárias para um repo privado, consulte [Criar um token de acesso GitHub](container-registry-tutorial-build-task.md#create-a-github-personal-access-token). 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Credenciais de Docker Hub  
Para evitar pedidos de estrangulamento e identidade ao retirar imagens do Docker Hub, crie um [token Docker Hub][docker-hub-tokens]. Em seguida, definir as seguintes variáveis ambientais:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Criar registos

Utilizando comandos Azure CLI, crie três registos de contentores de nível Premium, cada um no seu próprio grupo de recursos:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Crie cofre chave e definir segredos

Criar um cofre chave:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Definir nome de utilizador do Docker Hub e ficha no cofre da chave:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Desaça e verifique um Git PAT no cofre da chave:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Criar grupo de recursos para uma instância de contentor Azure

Este grupo de recursos é utilizado numa tarefa posterior ao implementar a `hello-world` imagem.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Criar `node` imagem de base pública

Para simular a `node` imagem no Docker Hub, crie uma tarefa [ACR][acr-task] para construir e manter a imagem pública. Esta configuração permite simular alterações pelos `node` conservadores de imagem.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Para evitar o estrangulamento do Docker, adicione [as credenciais do Docker Hub][docker-hub-tokens] à tarefa. O comando [de credenciais de tarefa acr][acr-task-credentials] pode ser usado para passar credenciais docker para qualquer registo, incluindo Docker Hub.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Conceder à tarefa acesso aos valores de leitura a partir do cofre chave:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[As tarefas podem ser desencadeadas][acr-task-triggers] por git commits, atualizações de imagem base, temporizadores ou execuções manuais. 

Executar a tarefa manualmente para gerar a `node` imagem:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Listar a imagem no registo público simulado:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Criar a `hello-world` imagem

Baseado na imagem pública `node` simulada, construa uma `hello-world` imagem.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Criar ficha para puxar acesso ao registo público simulado

Criar um [símbolo de acesso ao][acr-tokens] registo público simulado, com vista a `pull` . Em seguida, coloque-o no cofre da chave:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Criar ficha para o acesso de pull por Instâncias de Contentores Azure

Crie um [sinal de acesso ao][acr-tokens] registo que hospeda a `hello-world` imagem, com o miradoto a puxar. Em seguida, coloque-o no cofre da chave:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Criar tarefa para construir e manter a `hello-world` imagem

O seguinte comando cria uma tarefa a partir da definição `acr-tasks.yaml` no `hello-world` repo. Os passos de tarefa constroem a `hello-world` imagem e depois implantam-na em Instâncias de Contentores Azure. O grupo de recursos para instâncias de contentores Azure foi criado numa secção anterior. Ao convocar `az container create` a tarefa com apenas uma diferença no `image:tag` , a tarefa implementa-se na mesma instância ao longo desta passagem.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Adicione credenciais à tarefa para o registo público simulado:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Conceder à tarefa acesso aos valores de leitura a partir do cofre chave:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Conceder o acesso à tarefa para criar e gerir instâncias de contentores Azure, concedendo acesso ao grupo de recursos:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

Com a tarefa criada e configurada, executar a tarefa de construir e implementar a `hello-world` imagem:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Uma vez criado, obtenha o endereço IP do recipiente que hospeda a `hello-world` imagem.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

No seu browser, aceda ao endereço IP para ver a aplicação de execução.

## <a name="update-the-base-image-with-a-questionable-change"></a>Atualizar a imagem base com uma alteração "questionável"

Esta secção simula uma alteração na imagem base que pode causar problemas no ambiente.

1. Abra `Dockerfile` no repo de `base-image-node` garfo.
1. Mude o `BACKGROUND_COLOR` para `Orange` simular a mudança.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Comprometa a mudança e procure que as Tarefas ACR comecem automaticamente a construir.

Atenção à tarefa para começar a executar:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Deverá eventualmente ver status `Succeeded` com base num TRIGGER `Commit` de:

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Digite **Ctrl+C** para sair do comando do relógio e, em seguida, ver os registos para a execução mais recente:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Uma vez concluída a `node` imagem, `watch` para que as Tarefas ACR comecem automaticamente a construir a `hello-world` imagem:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Deverá eventualmente ver status `Succeeded` com base num TRIGGER `Image Update` de:

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Digite **Ctrl+C** para sair do comando do relógio e, em seguida, ver os registos para a execução mais recente:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Uma vez concluído, obtenha o endereço IP do site que hospeda a `hello-world` imagem atualizada:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

No seu navegador, vá ao site, que deve ter um fundo laranja (questionável).

### <a name="checking-in"></a>Check-in

Neste ponto, criaste uma `hello-world` imagem que é automaticamente construída em Git compromete-se e altera a imagem `node` base. Neste exemplo, a tarefa baseia-se numa imagem base no Registo do Contentor de Azure, mas qualquer registo suportado poderia ser utilizado.

A atualização da imagem base reencam e regista automaticamente a execução da tarefa quando a `node` imagem é atualizada. Como visto aqui, nem todas as atualizações são desejadas.

## <a name="gated-imports-of-public-content"></a>Importações fechadas de conteúdos públicos

Para evitar que alterações a montante partam cargas de trabalho críticas, podem ser adicionados testes de segurança e de funcionamento.

Nesta secção, cria-se uma tarefa ACR para:

* Construir uma imagem de teste
* Executar um script de teste funcional `./test.sh` contra a imagem de teste
* Se a imagem for bem sucedida, importe a imagem pública para o registo **de imagens base**

### <a name="add-automation-testing"></a>Adicionar testes de automação

Para portar qualquer conteúdo a montante, é implementado um teste automatizado. Neste exemplo, é fornecido um `test.sh` que verifica o `$BACKGROUND_COLOR` . Se o teste falhar, um `EXIT_CODE` dos é devolvido o que faz com que o passo de tarefa `1` ACR falhe, terminando a execução da tarefa. Os testes podem ser expandidos sob qualquer forma de ferramentas, incluindo resultados de registo. O portão é gerido por uma resposta de passe/falha no script, reproduzido aqui:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>Tarefa YAML 

Reveja `acr-task.yaml` o no `import-baseimage-node` repo, que executa os seguintes passos:

1. Construa a imagem base de teste utilizando o seguinte Dockerfile:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. Quando concluído, valide a imagem executando o recipiente, que funciona `./test.sh`
1. Só se concluída com sucesso, executar os passos de importação, que são fechados com `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Criar tarefa para importar e testar imagem base

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Adicione credenciais à tarefa para o registo público simulado:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Conceder à tarefa acesso aos valores de leitura a partir do cofre chave:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Executar a tarefa de importação:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Se a tarefa falhar devido a `./test.sh: Permission denied` , certifique-se de que o script tem permissões de execução, e volte para o repo Git:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>Atualizar `hello-world` imagem para construir a partir de imagem fechada `node`

Crie um [token][acr-tokens] de acesso para aceder ao registo de artefactos base, atento `read` ao `node` repositório. Em seguida, coloque no cofre da chave:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Adicione credenciais à tarefa **hello-world** para o registo de artefactos base:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Atualizar a tarefa para alterar `REGISTRY_FROM_URL` o para usar o `BASE_ARTIFACTS` registo

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Executar a tarefa **hello-world** para mudar a sua dependência de imagem base:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Atualizar a imagem base com uma alteração "válida"

1. Abra o `Dockerfile` `base-image-node` repo.
1. Altere o `BACKGROUND_COLOR` para `Green` simular uma alteração válida.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Comprometa a alteração e monitorize a sequência de atualizações:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Uma vez em funcionamento, digite **Ctrl+C** e monitorize os registos:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Uma vez concluída, monitorize a tarefa **de importação de imagem de base:**

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Uma vez em funcionamento, digite **Ctrl+C** e monitorize os registos:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Uma vez concluída, monitorize a tarefa **hello-world:**

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Uma vez em funcionamento, digite **Ctrl+C** e monitorize os registos:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

Uma vez concluído, obtenha o endereço IP do site que hospeda a `hello-world` imagem atualizada:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

No seu navegador, aceda ao site, que deve ter um fundo verde (válido).

### <a name="view-the-gated-workflow"></a>Ver o fluxo de trabalho fechado

Execute novamente os passos na secção anterior, com uma cor de fundo de vermelho.

1. Abra o `Dockerfile` no `base-image-node` repo
1. Altere o `BACKGROUND_COLOR` para `Red` simular uma alteração inválida.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Comprometa a alteração e monitorize a sequência de atualizações:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Uma vez em funcionamento, digite **Ctrl+C** e monitorize os registos:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

Uma vez concluída, monitorize a tarefa **de importação de imagem de base:**

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

Uma vez em funcionamento, digite **Ctrl+C** e monitorize os registos:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

Neste momento, deverá ver a tarefa **de base-import-node** falhar na validação e parar a sequência para publicar uma `hello-world` atualização. A saída é semelhante a:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Publicar uma atualização para `hello-world`

As alterações na `hello-world` imagem continuarão a utilizar a última imagem validada. `node`

Quaisquer alterações adicionais na imagem base `node` que passam as validações fechadas irão desencadear atualizações de imagem base para a `hello-world` imagem.

## <a name="cleaning-up"></a>Limpeza

Quando já não for necessário, elimine os recursos utilizados neste artigo.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo. utilizou as tarefas ACR para criar um fluxo de trabalho de gating automatizado para introduzir imagens de base atualizadas no seu ambiente. Consulte informações relacionadas para gerir imagens no Registo do Contentor de Azure.


* [Recomendações para a marcação e versão das imagens dos contentores](container-registry-image-tag-version.md)
* [Bloqueie uma imagem de contentor num registo de contentores Azure](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io