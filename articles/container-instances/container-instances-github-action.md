---
title: Implementar instância de contentores por ação GitHub
description: Configure uma ação GitHub que automatiza passos para construir, empurrar e implementar uma imagem de recipiente para instâncias de contentores Azure
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258044"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Configure uma ação GitHub para criar uma instância de contentores

[GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) é um conjunto de funcionalidades no GitHub para automatizar os seus fluxos de trabalho de desenvolvimento de software no mesmo local onde armazena código e colabora em pedidos e problemas de pull.

Utilize a ação ['Deploy to Azure Container Instances](https://github.com/azure/aci-deploy) GitHub' para automatizar a implantação de um contentor para as instâncias do contentor Azure. A ação permite-lhe definir propriedades para uma instância de contentorsemelhante à do [recipiente az criar][az-container-create] comando.

Este artigo mostra como configurar um fluxo de trabalho num repo GitHub que executa as seguintes ações:

* Construa uma imagem de um Dockerfile
* Empurre a imagem para um registo de contentores Azure
* Desloque a imagem do recipiente para uma instância de contentores Azure

Este artigo mostra duas formas de configurar o fluxo de trabalho:

* Configure um fluxo de trabalho num repo GitHub utilizando a ação De implantação para casos de contentores azure e outras ações.  
* Utilize `az container app up` o comando na extensão [Deploy para Azure](https://github.com/Azure/deploy-to-azure-cli-extension) no Azure CLI. Este comando dinamiza a criação do fluxo de trabalho gitHub e passos de implantação.

> [!IMPORTANT]
> A ação GitHub para as Instâncias de Contentores Azure está atualmente em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

* **Conta GitHub** - Crie uma conta se https://github.com ainda não tiver uma.
* **Azure CLI** - Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI para completar os passos Do CLI Azure. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].
* **Registo de contentores Azure** - Se não tiver um, crie um registo de contentores Azure no nível Básico utilizando o portal [Azure CLI,](../container-registry/container-registry-get-started-azure-cli.md) [Azure,](../container-registry/container-registry-get-started-portal.md)ou outros métodos. Tome nota do grupo de recursos utilizado para a implantação, que é utilizado para o fluxo de trabalho gitHub.

## <a name="set-up-repo"></a>Configurar repo

* Para os exemplos deste artigo, utilize o GitHub para bifurcar o seguinte repositório:https://github.com/Azure-Samples/acr-build-helloworld-node

  Este repo contém um Dockerfile e ficheiros de origem para criar uma imagem de contentor de uma pequena aplicação web.

  ![Captura de ecrã do botão Bifurcar (realçado) no GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Certifique-se de que as ações estão ativadas para o seu repositório. Navegue para o seu repositório bifurcado e selecione **Definições** > **Ações**. Em Autorizações de **Ações,** certifique-se de que é selecionada a Habilitação de **Ações locais e de terceiros para este repositório.**

## <a name="configure-github-workflow"></a>Configure GitHub fluxo de trabalho

### <a name="create-service-principal-for-azure-authentication"></a>Criar o principal de serviço para a autenticação Azure

No fluxo de trabalho gitHub, é necessário fornecer credenciais Azure para autenticar o Azure CLI. O exemplo seguinte cria um diretor de serviço com a função Contributiva ao grupo de recursos para o seu registo de contentores.

Primeiro, obtenha a identificação de recursos do seu grupo de recursos. Substitua o nome do seu grupo no seguinte comando de exibição de [grupo az:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Use [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar o diretor de serviço:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

A saída é semelhante a:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Guarde a saída JSON porque é usada num passo posterior. Além disso, tome nota do `clientId`, que precisa atualizar o diretor de serviço na secção seguinte.

### <a name="update-service-principal-for-registry-authentication"></a>Atual do serviço principal para autenticação de registo

Atualize as principais credenciais do serviço Azure para permitir a pressão e a permissão no registo do seu contentor. Este passo permite ao fluxo de trabalho do GitHub utilizar o comírpito de serviço para [autenticar com o seu registo](../container-registry/container-registry-auth-service-principal.md)de contentores . 

Obtenha a identificação do seu registo de contentores. Substitua o nome do seu registo no seguinte comando [de show az acr:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Use a [atribuição de funções az criar][az-role-assignment-create] para atribuir a função AcrPush, que dá impulso e puxa acesso ao registo. Substitua a identificação do cliente do seu diretor de serviço:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Poupe credenciais para o repo GitHub

1. No GitHub UI, navegue para o seu repositório bifurcado e selecione**Segredos**de **Definições** > . 

1. Selecione **Adicionar um novo segredo** para adicionar os seguintes segredos:

|Segredo  |Valor  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Toda a saída jSON da criação principal do serviço |
|`REGISTRY_LOGIN_SERVER`   | O nome do servidor de login do seu registo (todos os minúsculos). Exemplo: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  A `clientId` saída da JSON da criação principal do serviço       |
|`REGISTRY_PASSWORD`     |  A `clientSecret` saída da JSON da criação principal do serviço |
| `RESOURCE_GROUP` | O nome do grupo de recursos que usou para examinar o diretor de serviço |

### <a name="create-workflow-file"></a>Criar ficheiro de fluxo de trabalho

1. Na UI GitHub, selecione **Ações** > **Novo fluxo de trabalho**.
1. **Selecione configurar um fluxo de trabalho por si mesmo**.
1. Em **Editar novo ficheiro,** colá-se no seguinte conteúdo yAML para substituir o código da amostra. Aceite o nome `main.yml`de ficheiro predefinido, ou forneça um nome de ficheiro que escolha.
1. Selecione **Iniciar comprometer-** opcionalmente fornecer descrições curtas e estendidas do seu compromisso, e selecione **Comprometer novo ficheiro**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Validar o fluxo de trabalho

Depois de cometer o ficheiro de fluxo de trabalho, o fluxo de trabalho é acionado. Para rever o progresso do fluxo de trabalho, navegue para**fluxos**de trabalho de **ações.** >  

![Ver o progresso do fluxo de trabalho](./media/container-instances-github-action/github-action-progress.png)

Consulte [a gestão de um fluxo de trabalho](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) para obter informações sobre a visualização do estado e os resultados de cada passo no seu fluxo de trabalho.

Quando o fluxo de trabalho estiver concluído, obtenha informações sobre a instância do contentor denominada *aci-sampleapp* executando o comando [do contentor az.][az-container-show] Substitua o nome do seu grupo de recursos: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A saída é semelhante a:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Depois de a instância ser disponibilizada, navegue para o FQDN do contentor no seu navegador para ver a aplicação web em execução.

![Executar aplicativo web no navegador](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Utilizar a extensão De implantação para a extensão Azure

Em alternativa, utilize a [extensão Deploy para Azure](https://github.com/Azure/deploy-to-azure-cli-extension) no CLI Azure para configurar o fluxo de trabalho. O `az container app up` comando na extensão retira-lhe parâmetros de entrada para configurar um fluxo de trabalho para implantar em Instâncias de Contentores Azure. 

O fluxo de trabalho criado pelo Azure CLI é semelhante ao fluxo de trabalho que pode criar manualmente utilizando o [GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Pré-requisito adicional

Além dos [pré-requisitos](#prerequisites) e configuração de [repo](#set-up-repo) para este cenário, é necessário instalar a **extensão Deploy para Azure** para o Azure CLI.

Executar a [extensão az adicionar][az-extension-add] comando para instalar a extensão:

```azurecli
az extension add \
  --name deploy-to-azure
```

Para obter informações sobre encontrar, instalar e gerir extensões, consulte [As extensões de utilização com o Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Execute `az container app up`

Para executar o comando do [aplicativo de contentora AZ,][az-container-app-up] forneça no mínimo:

* O nome do seu registo de contentores Azure, por exemplo, *o meu registo*
* O URL do seu repo GitHub, por exemplo,`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Comando da amostra:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Comando progresso

* Quando solicitado, forneça as suas credenciais GitHub ou forneça um token de [acesso pessoal GitHub](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (PAT) que tenha *repo* e âmbitos de *utilizador* para autenticar com o seu registo. Se fornecer credenciais GitHub, o comando cria um PAT para si.

* O comando cria segredos de repo para o fluxo de trabalho:

  * Credenciais principais de serviço para o Azure CLI
  * Credenciais de acesso ao registo de contentores azure

* Depois de o comando comprometer o ficheiro de fluxo de trabalho ao seu repo, o fluxo de trabalho é acionado. 

A saída é semelhante a:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Validar o fluxo de trabalho

O fluxo de trabalho implanta uma instância de contentores Azure com o nome base do seu repo GitHub, neste caso, *acr-build-helloworld-node*. No seu navegador, pode navegar para o link fornecido para visualizar a aplicação web em execução. Se a sua aplicação ouvir numa porta diferente do 8080, especifique-a no URL.

Para ver o estado do fluxo de trabalho e os resultados de cada passo na UI GitHub, consulte [gerir uma execução](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)de fluxo de trabalho .

## <a name="clean-up-resources"></a>Limpar recursos

Pare a instância de contentor com o comando [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Para eliminar o grupo de recursos e todos os recursos nele, executar o [grupo AZ eliminar][az-group-delete] o comando:

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Passos seguintes

Navegue no [GitHub Marketplace](https://github.com/marketplace?type=actions) para mais ações para automatizar o seu fluxo de trabalho de desenvolvimento


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
