---
title: Tutorial - Desdobre do GitHub para o Serviço Azure Kubernetes (AKS) com Jenkins
description: Configurar jenkins para integração contínua (CI) do GitHub e implantação contínua (CD) para o Serviço Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: eb48a8558aab6c7a832efe45650686d9df0d7426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624751"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: Desdobre do GitHub para o Serviço Azure Kubernetes (AKS) com integração e implantação contínua seletos jenkins

Este tutorial implementa uma aplicação de amostra do GitHub para um cluster [azure Kubernetes Service (AKS),](/azure/aks/intro-kubernetes) através da criação de integração contínua (CI) e implantação contínua (CD) em Jenkins. Dessa forma, ao atualizar a sua aplicação empurrando-se para o GitHub, jenkins executa automaticamente uma nova construção de contentores, empurra imagens de contentores para o Registo de Contentores Do Azure (ACR), e depois executa a sua app no AKS. 

Neste tutorial, completará estas tarefas:

> [!div class="checklist"]
> * Implante uma aplicação de voto Azure para um cluster AKS.
> * Crie um projeto básico do Jenkins.
> * Crie credenciais para o Jenkins interagir com a ACR.
> * Crie um trabalho de construção jenkins e webhook GitHub para construções automatizadas.
> * Teste o gasoduto CI/CD para atualizar uma aplicação em AKS com base no código GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa destes itens:

- Compreensão básica das imagens kubernetes, Git, CI/CD e contentores

- Um [cluster AKS](../aks/kubernetes-walkthrough.md) e `kubectl` configurado com as credenciais de cluster [AKS.](/cli/azure/aks#az-aks-get-credentials)

- Um registo de registo de [contentores Azure (ACR),](../container-registry/container-registry-get-started-azure-cli.md)o nome do servidor de login ACR e o cluster AKS configurado para [autenticar com o registo ACR](../aks/cluster-container-registry-integration.md).

- A versão Azure CLI 2.0.46 ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

- [Docker instalado](https://docs.docker.com/install/) no seu sistema de desenvolvimento

- Uma conta GitHub, ficha de [acesso pessoal GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)e cliente Git instalado no seu sistema de desenvolvimento

- Se fornecer esporão jenkins em vez desta amostra escrita para implementar jenkins, a sua instância Jenkins precisa do [Docker instalado e configurado](https://docs.docker.com/install/) e [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Prepare a sua aplicação

Neste artigo, você usa uma aplicação de voto Azure de amostra que contém uma interface web hospedada em uma ou mais cápsulas, e um segundo pod hospedando Redis para armazenamento temporário de dados. Antes de integrar jenkins e AKS para implementações automatizadas, prepare e implemente manualmente a aplicação de voto Azure para o seu cluster AKS. Esta implementação manual é a versão uma da aplicação, e permite-lhe ver a aplicação em ação.

> [!NOTE]
> A amostra de aplicação de voto Azure usa uma cápsula Linux que está programada para funcionar num nó Linux. O fluxo descrito neste artigo também funciona para um pod do Windows Server agendado num nó do Windows Server.

Garfo o seguinte repositório GitHub [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)para a aplicação da amostra - . Para bifurcar o repositório para a sua própria conta do GitHub, selecione o botão **Fork** no canto superior direito.

Clone o garfo para o seu sistema de desenvolvimento. Certifique-se de que utiliza o URL do seu garfo ao clonar este repo:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Mude para o diretório do seu garfo clonado:

```console
cd azure-voting-app-redis
```

Para criar as imagens do recipiente necessárias para a aplicação `docker-compose`da amostra, utilize o ficheiro *docker-compose.yaml* com:

```console
docker-compose up -d
```

As imagens de base necessárias são puxadas e os recipientes de aplicação construídos. Em seguida, pode usar o comando de imagens de [estivador](https://docs.docker.com/engine/reference/commandline/images/) para ver a imagem criada. Foram transferidas ou criadas três imagens. A imagem `azure-vote-front` contém a aplicação e utilizar a imagem `nginx-flask` como base. A `redis` imagem é usada para iniciar uma instância redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Antes de poder empurrar a imagem do recipiente *frontal de voto azul* para ACR, obtenha o seu servidor de login ACR com o comando da lista [az acr.](/cli/azure/acr#az-acr-list) O exemplo seguinte obtém o endereço do servidor de login ACR para um registo no grupo de recursos chamado *myResourceGroup:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize o comando de marcação de [estivadores](https://docs.docker.com/engine/reference/commandline/tag/) para marcar a `v1`imagem com o nome do servidor de login ACR e um número de versão de . Forneça o `<acrLoginServer>` seu próprio nome obtido no passo anterior:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Finalmente, empurre a imagem *azure-vote-front* para o seu registo ACR. Mais uma `<acrLoginServer>` vez, substitua-o pelo nome do servidor `myacrregistry.azurecr.io`de login do seu próprio registo ACR, tais como:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Implementar a aplicação da amostra para AKS

Para implementar a aplicação da amostra para o seu cluster AKS, pode utilizar o ficheiro manifesto Kubernetes na raiz do repositório de voto Azure. Abra o ficheiro de manifesto *azure-vote-all-in-one-redis.yaml* com um editor como `vi`. Substitua `microsoft` pelo nome do servidor de início de sessão do ACR. Este valor encontra-se na linha **47** do ficheiro manifesto:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Em seguida, utilize o comando [de aplicação kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) para implantar a aplicação no seu cluster AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Um serviço de balanceadores de carga Kubernetes é criado para expor a aplicação à internet. Este processo pode demorar alguns minutos. Para monitorizar o progresso da implantação do equilíbrio de carga, `--watch` utilize o [kubectl obter](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) comando de serviço com o argumento. Uma vez que o endereço *EXTERNO-IP* tenha `Control + C` mudado de *pendente* para um endereço *IP,* utilize para parar o processo de observação kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Para ver a aplicação em ação, abra um navegador web para o endereço IP externo do seu serviço. A aplicação de voto Azure é apresentada, como mostra o seguinte exemplo:

![Candidatura de voto de amostra azure em execução no AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Desloque Jenkins a um VM Azure

Para implementar rapidamente o Jenkins para ser usado neste artigo, pode utilizar o seguinte script para implementar uma máquina virtual Azure, configurar o acesso à rede e completar uma instalação básica de Jenkins. Para autenticação entre jenkins e o cluster AKS, o script copia o seu ficheiro de configuração Kubernetes do seu sistema de desenvolvimento para o sistema Jenkins.

> [!WARNING]
> Este script de amostra é para fins de demonstração para fornecer rapidamente um ambiente Jenkins que funciona em um VM Azure. Utiliza a extensão de script personalizada Azure para configurar um VM e, em seguida, exibir as credenciais necessárias. O seu *~/.kube/config* é copiado para o Jenkins VM.

Executar os seguintes comandos para descarregar e executar o script. Deve rever o conteúdo de qualquer guião antes de executá-lo . [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Leva alguns minutos para criar o VM e implementar os componentes necessários para Docker e Jenkins. Quando o script estiver concluído, produz um endereço para o servidor Jenkins e uma chave para desbloquear o painel de instrumentos, como mostra a seguinte saída de exemplo:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Abra um navegador web para o URL apresentado e introduza a chave de desbloqueio. Siga as instruções no ecrã para completar a configuração de Jenkins:

- Escolha **Instalar plugins sugeridos**
- Crie o primeiro utilizador administrador. Introduza um nome de utilizador, como *o azureuser,* e depois forneça a sua própria senha segura. Por fim, escreva um nome completo e o endereço de e-mail.
- Selecionar **Guardar e Concluir**
- Assim que o Jenkins estiver pronto, selecione **Começar a utilizar o Jenkins**
    - Se o browser apresentar uma página em branco quando começar a utilizar o Jenkins, reinicie o serviço Jenkins. Para reiniciar o serviço, SSH para o endereço `sudo service jenkins restart`IP público da sua instância jenkins e tipo . Uma vez reiniciado o serviço, refresque-o no navegador web.
- Inscreva-se no Jenkins com o nome de utilizador e palavra-passe que criou no processo de instalação.

## <a name="create-a-jenkins-environment-variable"></a>Criar uma variável ambiental Jenkins

Uma variável ambiental Jenkins é usada para conter o nome do servidor de login ACR. Esta variável é referenciada durante o trabalho de construção de Jenkins. Para criar esta variável ambiental, complete os seguintes passos:

- No lado esquerdo do portal Jenkins, selecione **Manage Jenkins** > **Configure System**
- Em **Propriedades Globais,** selecione **variáveis ambientais.** Adicione uma variável `ACR_LOGINSERVER` com o nome e o valor do seu servidor de login ACR.

    ![Variáveis ambientais jenkins](media/jenkins-continuous-deployment/env-variables.png)

- Quando estiver completo, clique em **Guardar** na parte inferior da página de configuração do Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Criar uma credencial Jenkins para a ACR

Para permitir que o Jenkins construa e, em seguida, empurre imagens de contentores atualizadas para ACR, precisa especificar credenciais para ACR. Esta autenticação pode utilizar os diretores de serviço sinuosos do Azure Ative Diretório. Nos pré-requisitos, configurou o diretor de serviço para o seu cluster AKS com permissões *do Leitor* para o seu registo ACR. Estas permissões permitem ao cluster AKS *retirar* imagens do registo ACR. Durante o processo CI/CD, jenkins constrói novas imagens de contentores com base em atualizações de aplicações, e precisa então *de empurrar* essas imagens para o registo ACR. Para separação de funções e permissões, configure agora um diretor de serviço para jenkins com permissões *do Colaborador* para o seu registo ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Crie um diretor de serviço para jenkins usar ACR

Primeiro, crie um diretor de serviço utilizando o comando [az ad sp create-for-rbac:](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Tome nota da *appId* e *palavra-passe* mostrada na sua saída. Estes valores são usados em passos seguintes para configurar o recurso credencial em Jenkins.

Obtenha a identificação de recursos do seu registo ACR usando o comando do [az acr show,](/cli/azure/acr#az-acr-show) e guarde-o como uma variável. Forneça o nome do seu grupo de recursos e o nome ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Crie agora uma atribuição de funções para atribuir os direitos principais de *contribuidores* de serviço ao registo ACR. No exemplo seguinte, forneça o seu próprio *appId* mostrado na saída um comando anterior para criar o principal de serviço:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Criar um recurso credencial em Jenkins para o diretor de serviço da ACR

Com a atribuição de funções criada em Azure, armazene agora as suas credenciais ACR num objeto credencial Jenkins. Estas credenciais são referenciadas durante o trabalho de construção de Jenkins.

De volta ao lado esquerdo do portal Jenkins, clique em **credenciais** > **Jenkins** > **Global credenciais (sem restrições)** > **Adicionar credenciais**

Certifique-se de que o tipo credencial é **username com senha** e introduza os seguintes itens:

- **Nome de utilizador** - A *aplicação Do* principal de serviço criado para autenticação com o seu registo ACR.
- **Palavra-passe** - A *palavra-passe* do diretor de serviço criado para autenticação com o seu registo ACR.
- **ID** - Identificador credencial, como *credenciais acr*

Quando completa, o formulário de credenciais parece o seguinte exemplo:

![Crie um objeto credencial Jenkins com a informação principal do serviço](media/jenkins-continuous-deployment/acr-credentials.png)

Clique **em OK** e volte ao portal Jenkins.

## <a name="create-a-jenkins-project"></a>Criar um projeto Jenkins

A partir da página inicial do seu portal Jenkins, selecione **Novo item** no lado esquerdo:

1. Insira *o voto azure* como nome de trabalho. Escolha **o projeto Freestyle**e, em seguida, selecione **OK**
1. Na secção **Geral,** selecione **o projeto GitHub** e introduza o seu URL de repo bifurcado, como *https:\//github.com/\<sua conta\>github/azure-voting-app-redis*
1. Na secção de gestão do **código fonte,** selecione **Git**, introduza o seu repo bifurcado *.git* URL, tais como *https:\/\</github.com/ sua conta\>github/azure-voting-app-redis.git*

1. Sob a secção **'Gatilhos de Construção',** selecione o gatilho do **gancho GitHub para a sondagem do GITscm**
1. Under **Build Environment**, selecione Use **textos ou ficheiros secretos**
1. Em **Encadernações,** selecione **Adicionar** > **nome de utilizador e palavra-passe (separado)**
   - Introduza `ACR_ID` para a **Variável nome**de utilizador , e `ACR_PASSWORD` para a **Variável Palavra-passe**

     ![Encadernações jenkins](media/jenkins-continuous-deployment/bindings.png)

1. Opte por adicionar uma camada de **construção** do tipo **Executar a concha** e utilize o seguinte texto. Este script constrói uma nova imagem de recipiente e empurra-a para o seu registo ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Adicione mais um passo de **construção** do tipo **Executar concha** e use o seguinte texto. Este script atualiza a implementação da aplicação em AKS com a nova imagem de contentor da ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Uma vez concluído, clique em **Guardar**.

## <a name="test-the-jenkins-build"></a>Teste a construção de Jenkins

Antes de automatizar o trabalho baseado no Compromisso GitHub, primeiro teste manualmente a construção de Jenkins. Esta construção manual valida que o trabalho foi corretamente configurado, o ficheiro de autenticação Kubernetes adequado está em vigor e que a autenticação com ACR funciona.

No menu à esquerda do projeto, selecione **Build Now**.

![Construção de teste jenkins](media/jenkins-continuous-deployment/test-build.png)

A primeira construção leva um minuto ou dois à medida que as camadas de imagem do Docker são puxadas para o servidor Jenkins. As construções subsequentes podem usar as camadas de imagem em cache para melhorar os tempos de construção.

Durante o processo de construção, o repositório GitHub é clonado para o servidor de construção Jenkins. Uma nova imagem de recipiente é construída e empurrada para o registo DaCR. Finalmente, a aplicação de voto Azure em execução no cluster AKS é atualizada para usar a nova imagem. Como não foram feitas alterações ao código de aplicação, a aplicação não é alterada se vir a aplicação de amostra num navegador web.

Uma vez que o trabalho de construção esteja concluído, clique em **construir #1** sob a história da construção. Selecione **saída de consola** e veja a saída a partir do processo de construção. A linha final deve indicar uma construção bem sucedida.

## <a name="create-a-github-webhook"></a>Criar um webhook GitHub

Com uma construção manual bem sucedida completa, agora integre o GitHub na construção jenkins. Um webhook pode ser usado para executar o trabalho de construção jenkins cada vez que um compromisso de código é feito no GitHub. Para criar o webhook GitHub, complete os seguintes passos:

1. Navegue no seu repositório GitHub bifurcado num navegador web.
1. Selecione **Definições**e, em seguida, selecione **Webhooks** no lado esquerdo.
1. Escolha **adicionar webhook**. Para o URL de `http://<publicIp:8080>/github-webhook/`Carga `<publicIp>` *Útil,* introduza, onde está o endereço IP do servidor Jenkins. Certifique-se de incluir o rasto /. Deixe as outras predefinições para o tipo de conteúdo e para desencadear em eventos *de impulso.*
1. **Selecione Adicionar webhook**.

    ![Crie um webhook GitHub para Jenkins](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testar o gasoduto CI/CD completo

Agora pode testar todo o oleoduto CI/CD. Quando se pressiona um código compromete-se com o GitHub, os seguintes passos acontecem:

1. O webhook gitHub estende a mão ao Jenkins.
1. Jenkins começa o trabalho de construção e retira o último código do GitHub.
1. Uma construção do Docker é iniciada usando o código atualizado, e a nova imagem do recipiente é marcada com o mais recente número de construção.
1. Esta nova imagem do recipiente é empurrada para o Registo de Contentores Azure.
1. A sua aplicação foi implementada para atualizações do Serviço Azure Kubernetes com a mais recente imagem de contentor do registo do registo de contentores Azure.

Na sua máquina de desenvolvimento, abra a aplicação clonada com um editor de código. Sob o diretório */azure-vote/azure-vote,* abra o ficheiro denominado **config_file.cfg**. Atualize os valores de voto neste ficheiro para algo diferente de gatos e cães, como mostra o seguinte exemplo:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Quando atualizado, guarde o ficheiro, comprometa as alterações e empurre-as para o seu garfo do repositório GitHub. O webhook gitHub despoleta um novo trabalho de construção em Jenkins. No painel de instrumentos da Web Jenkins, monitorize o processo de construção. Demora alguns segundos a puxar o último código, criar e empurrar a imagem atualizada e implementar a aplicação atualizada no AKS.

Uma vez concluída a construção, refresque o seu navegador web da aplicação de voto Azure da amostra. As suas alterações são apresentadas, como mostra o seguinte exemplo:

![Voto da amostra Azure na AKS atualizado pelo trabalho de construção de Jenkins](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Jenkins em Azure](/azure/jenkins)