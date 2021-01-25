---
title: Implementar ações do contentor pela ação do GitHub
description: Configure uma ação GitHub que automatiza passos para construir, empurrar e implantar uma imagem de contentor para instâncias de contentores Azure
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1409d8fc1430cd9bf67bd735d9826a74979d495b
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762957"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Configurar uma ação do GitHub para criar uma instância de contentor

[GitHub Actions](https://docs.github.com/en/actions) é um conjunto de funcionalidades no GitHub para automatizar os fluxos de trabalho de desenvolvimento de software no mesmo local onde armazena código e colabora em pedidos e problemas de puxar.

Utilize a [ação Desação para Instâncias de Contentores Azure](https://github.com/azure/aci-deploy) GitHub para automatizar a colocação de um único contentor para instâncias de contentores Azure. A ação permite definir propriedades para uma instância de contentor semelhante à do [contentor az criar][az-container-create] comando.

Este artigo mostra como configurar um fluxo de trabalho num repo GitHub que executa as seguintes ações:

* Construa uma imagem a partir de um Dockerfile
* Empurre a imagem para um registo de contentores Azure
* Desloque a imagem do recipiente para uma instância do contentor Azure

Este artigo mostra duas formas de configurar o fluxo de trabalho:

* [Configure o fluxo de trabalho GitHub](#configure-github-workflow) - Crie um fluxo de trabalho num repo GitHub utilizando a ação Deploy to Azure Container Instances e outras ações.  
* [Utilizar a extensão CLI](#use-deploy-to-azure-extension) - Utilize o `az container app up` comando na extensão ['Implementar para Azul'](https://github.com/Azure/deploy-to-azure-cli-extension) no CLI Azure. Este comando dinamiza a criação do fluxo de trabalho e das etapas de implantação do GitHub.

> [!IMPORTANT]
> A ação GitHub para instâncias de contentores Azure está atualmente em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

* **Conta GitHub** - Crie uma conta https://github.com se ainda não tiver uma.
* **Azure CLI** - Pode utilizar a Concha da Nuvem Azure ou uma instalação local do CLI Azure para completar os passos do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].
* **Registo do contentor Azure** - Se não tiver um, crie um registo de contentores Azure no nível Básico utilizando o [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), [portal Azure,](../container-registry/container-registry-get-started-portal.md)ou outros métodos. Tome nota do grupo de recursos utilizado para a implantação, que é utilizado para o fluxo de trabalho GitHub.

## <a name="set-up-repo"></a>Configurar repo

* Para os exemplos deste artigo, utilize o GitHub para forrear o seguinte repositório: https://github.com/Azure-Samples/acr-build-helloworld-node

  Este repo contém um Dockerfile e ficheiros de origem para criar uma imagem de contentor de uma pequena aplicação web.

  ![Captura de ecrã do botão Bifurcar (realçado) no GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Certifique-se de que as ações estão ativadas para o seu repositório. Navegue para o seu repositório forcado e selecione **Ações de Definições**  >  . Em **permissões de Ações,** certifique-se de que as **ações locais e de terceiros para este repositório são selecionadas.**

## <a name="configure-github-workflow"></a>Configurar fluxo de trabalho GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Criar o principal de serviço para a autenticação Azure

No fluxo de trabalho GitHub, você precisa fornecer credenciais Azure para autenticar para o Azure CLI. O exemplo a seguir cria um principal de serviço com a função contribuinte no âmbito do grupo de recursos para o seu registo de contentores.

Primeiro, obtenha a identificação de recursos do seu grupo de recursos. Substitua o nome do seu grupo no seguinte comando [de exibição de grupo az:][az-group-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Use [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar o principal serviço:

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

Guarde a saída JSON porque é utilizada num passo posterior. Além disso, tome nota do `clientId` , que precisa atualizar o principal serviço na secção seguinte.

### <a name="update-service-principal-for-registry-authentication"></a>Atualização do serviço principal para autenticação de registo

Atualize as principais credenciais do serviço Azure para permitir empurrar e puxar o acesso ao seu registo de contentores. Este passo permite que o fluxo de trabalho do GitHub utilize o principal de serviço para [autenticar com o seu registo de contentores](../container-registry/container-registry-auth-service-principal.md) e para empurrar e puxar uma imagem do Docker. 

Obtenha a identificação de recursos do seu registo de contentores. Substitua o nome do seu registo no seguinte comando [az acr show:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Use [a az role assignment create][az-role-assignment-create] para atribuir o papel AcrPush, que dá impulso e puxa acesso ao registo. Substitua a ID do cliente do seu principal de serviço:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Guarde credenciais para gitHub repo

1. No GitHub UI, navegue para o seu repositório forcado e selecione **Settings**  >  **Secrets**. 

1. **Selecione Adicionar um novo segredo** para adicionar os seguintes segredos:

|Segredo  |Valor  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Toda a produção JSON do passo principal de criação do serviço |
|`REGISTRY_LOGIN_SERVER`   | O nome do servidor de login do seu registo (todos os minúsculos). Exemplo: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  A `clientId` partir da saída JSON da criação principal de serviço       |
|`REGISTRY_PASSWORD`     |  A `clientSecret` partir da saída JSON da criação principal de serviço |
| `RESOURCE_GROUP` | O nome do grupo de recursos que usou para o âmbito do serviço principal |

### <a name="create-workflow-file"></a>Criar ficheiro de fluxo de trabalho

1. No GitHub UI, selecione **Actions**  >  **New workflow**.
1. Selecione **Configurar um fluxo de trabalho por si mesmo**.
1. No **editar novo ficheiro,** cole os seguintes conteúdos YAML para substituir o código de amostra. Aceite o nome de ficheiro predefinido, `main.yml` ou forneça um nome de ficheiro que escolher.
1. Selecione **Iniciar o compromisso**, opcionalmente forneça descrições curtas e estendidas do seu compromisso e selecione Comprometa novo **ficheiro**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
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

### <a name="validate-workflow"></a>Validar fluxo de trabalho

Depois de estidurá-lo, o fluxo de trabalho é acionado. Para rever o progresso do fluxo de trabalho, navegue para **ações**  >  **fluxos de trabalho.** 

![Ver o progresso do fluxo de trabalho](./media/container-instances-github-action/github-action-progress.png)

Consulte [o histórico de fluxos de trabalho de visualização](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) para obter informações sobre a visualização do estado e resultados de cada passo no seu fluxo de trabalho. Se o fluxo de trabalho não estiver completo, consulte [os registos de visualização para diagnosticar falhas](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures).

Quando o fluxo de trabalho estiver concluído com sucesso, obtenha informações sobre a instância do recipiente denominada *aci-sampleapp* executando o comando de demonstração do [recipiente az.][az-container-show] Substitua o nome do seu grupo de recursos: 

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

Após o fornecimento da instância, navegue para o FQDN do contentor no seu navegador para ver a aplicação web em execução.

![Executando aplicativo web no navegador](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Utilizar implantação para extensão Azure

Em alternativa, utilize [a extensão Deploy to Azure](https://github.com/Azure/deploy-to-azure-cli-extension) no CLI Azure para configurar o fluxo de trabalho. O `az container app up` comando na extensão requer parâmetros de entrada da sua parte para configurar um fluxo de trabalho para implantar em Instâncias de Contentores Azure. 

O fluxo de trabalho criado pelo CLI Azure é semelhante ao fluxo de trabalho que pode [criar manualmente utilizando o GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Pré-requisito adicional

Além dos [pré-requisitos](#prerequisites) e [da configuração de repo](#set-up-repo) para este cenário, é necessário instalar a  **extensão Deploy to Azure** para o CLI Azure.

Executar o comando [de adicionar extensão az][az-extension-add] para instalar a extensão:

```azurecli
az extension add \
  --name deploy-to-azure
```

Para obter informações sobre encontrar, instalar e gerir extensões, consulte [extensões de utilização com Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Executar `az container app up`

Para executar o comando [de aplicação de contentores az,][az-container-app-up] forneça no mínimo:

* O nome do seu registo de contentores Azure, por exemplo, *miogresso*
* O URL para o seu gitHub repo, por exemplo, `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Comando da amostra:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Progresso do comando

* Quando solicitado, forneça as suas credenciais GitHub ou forneça um [token de acesso pessoal GitHub](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) (PAT) que tenha alcances de *repo* e *utilizador* para autenticar com a sua conta GitHub. Se fornecer credenciais do GitHub, o comando cria um PAT para si. Siga as instruções adicionais para configurar o fluxo de trabalho.

* O comando cria segredos de repo para o fluxo de trabalho:

  * Credenciais principais do serviço para o Azure CLI
  * Credenciais para aceder ao registo de contentores Azure

* Depois de o comando entregar o ficheiro de fluxo de trabalho ao seu repo, o fluxo de trabalho é acionado. 

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

Para visualizar o estado de fluxo de trabalho e os resultados de cada passo no GitHub UI, consulte [o histórico de fluxos de trabalho de visualização](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history).

### <a name="validate-workflow"></a>Validar fluxo de trabalho

O fluxo de trabalho implementa uma instância de contentor Azure com o nome base do seu gitHub repo, neste caso, *acr-build-helloworld-node*. Quando o fluxo de trabalho estiver concluído com sucesso, obtenha informações sobre a instância do recipiente denominada *acr-build-helloworld-node,* executando o comando [de show de contentores az.][az-container-show] Substitua o nome do seu grupo de recursos: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A saída é semelhante a:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Após o fornecimento da instância, navegue para o FQDN do contentor no seu navegador para ver a aplicação web em execução.

## <a name="clean-up-resources"></a>Limpar os recursos

Pare a instância de contentor com o comando [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Para eliminar o grupo de recursos e todos os recursos nele, executar o comando [de eliminação do grupo AZ:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Próximos passos

Navegue no [Mercado GitHub](https://github.com/marketplace?type=actions) para mais ações para automatizar o seu fluxo de trabalho de desenvolvimento


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
