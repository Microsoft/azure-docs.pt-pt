---
title: Tutorial – implantar do GitHub para o serviço kubernetes do Azure (AKS) com o Jenkins
description: Configurar o Jenkins para a CI (integração contínua) do GitHub e a implantação contínua (CD) para o serviço kubernetes do Azure (AKS)
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: e46e2c2933ee9afda860b68b10c135ac75a5d247
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263924"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: implantar do GitHub para o serviço kubernetes do Azure (AKS) com integração e implantação contínuas do Jenkins

Este tutorial implanta um aplicativo de exemplo do GitHub em um cluster [do AKS (serviço kubernetes do Azure)](/azure/aks/intro-kubernetes) Configurando a integração contínua (CI) e a implantação contínua (CD) no Jenkins. Dessa forma, quando você atualizar seu aplicativo enviando confirmações por push para o GitHub, o Jenkins executará automaticamente uma nova compilação de contêiner, enviará por push as imagens de contêiner para o ACR (registro de contêiner do Azure) e, em seguida, executará seu aplicativo no AKS. 

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Implante um aplicativo de exemplo do Azure vote em um cluster AKS.
> * Crie um projeto Jenkins básico.
> * Configure as credenciais para Jenkins para interagir com o ACR.
> * Crie um trabalho de Build do Jenkins e o webhook do GitHub para compilações automatizadas.
> * Teste o pipeline de CI/CD para atualizar um aplicativo no AKS com base nas confirmações de código do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará destes itens:

- Noções básicas sobre kubernetes, git, CI/CD e imagens de contêiner

- Um [cluster AKs][aks-quickstart] e `kubectl` configurados com as [credenciais do cluster AKs][aks-credentials]

- Um [registro de ACR (registro de contêiner do Azure)][acr-quickstart], o nome do servidor de logon do ACR e o cluster AKs configurado para [autenticar com o registro do ACR][acr-authentication]

- O CLI do Azure versão 2.0.46 ou posterior instalado e configurado. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

- [Docker instalado][docker-install] em seu sistema de desenvolvimento

- Uma conta do GitHub, o [token de acesso pessoal do GitHub][git-access-token]e o cliente git instalados no seu sistema de desenvolvimento

- Se você fornecer sua própria instância de Jenkins em vez desse modo de script de exemplo para implantar o Jenkins, sua instância do Jenkins precisará do [Docker instalado e configurado][docker-install] e [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Preparar seu aplicativo

Neste artigo, você usa um aplicativo de exemplo do Azure vote que contém uma interface da web hospedada em um ou mais pods e um segundo Pod que hospeda o Redis para armazenamento de dados temporário. Antes de integrar o Jenkins e o AKS para implantações automatizadas, primeiro prepare e implante manualmente o aplicativo Azure vote em seu cluster AKS. Essa implantação manual é a versão um do aplicativo e permite que você veja o aplicativo em ação.

> [!NOTE]
> O aplicativo de exemplo do Azure vote usa um pod do Linux que está agendado para ser executado em um nó do Linux. O fluxo descrito neste artigo também funciona para um pod do Windows Server agendado em um nó do Windows Server.

Crie o fork do seguinte repositório do GitHub para o aplicativo de exemplo- [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Para bifurcar o repositório para a sua própria conta do GitHub, selecione o botão **Fork** no canto superior direito.

Clone a bifurcação para seu sistema de desenvolvimento. Certifique-se de usar a URL da bifurcação ao clonar este repositório:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Altere para o diretório de sua bifurcação clonada:

```console
cd azure-voting-app-redis
```

Para criar as imagens de contêiner necessárias para o aplicativo de exemplo, use o arquivo *Docker-Compose. YAML* com `docker-compose`:

```console
docker-compose up -d
```

As imagens base necessárias são puxadas e os contêineres de aplicativo são criados. Em seguida, você pode usar o comando [Docker images][docker-images] para ver a imagem criada. Foram transferidas ou criadas três imagens. A imagem `azure-vote-front` contém a aplicação e utilizar a imagem `nginx-flask` como base. A imagem de `redis` é usada para iniciar uma instância de Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Antes de poder enviar por push a imagem de contêiner *Azure-vote-front* para ACR, obtenha seu servidor de logon do ACR com o comando [AZ ACR List][az-acr-list] . O exemplo a seguir obtém o endereço do servidor de logon do ACR para um registro no grupo de recursos chamado *MyResource*Group:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use o comando [Docker tag][docker-tag] para marcar a imagem com o nome do servidor de logon do ACR e um número de versão de `v1`. Forneça seu próprio nome de `<acrLoginServer>` obtido na etapa anterior:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Por fim, envie por push a imagem *Azure-vote-front* para o registro ACR. Novamente, substitua `<acrLoginServer>` pelo nome do servidor de logon do seu próprio registro ACR, como `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Implantar o aplicativo de exemplo no AKS

Para implantar o aplicativo de exemplo no cluster AKS, você pode usar o arquivo de manifesto kubernetes na raiz do repositório de votos do Azure vote. Abra o arquivo de manifesto *Azure-voto-All-in-One-Redis. YAML* com um editor como `vi`. Substitua `microsoft` pelo nome do servidor de início de sessão do ACR. Esse valor é encontrado na linha **47** do arquivo de manifesto:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Em seguida, use o comando [kubectl Apply][kubectl-apply] para implantar o aplicativo em seu cluster AKs:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Um serviço de balanceador de carga kubernetes é criado para expor o aplicativo à Internet. Este processo pode demorar alguns minutos. Para monitorar o progresso da implantação do balanceador de carga, use o comando [kubectl Get Service][kubectl-get] com o argumento `--watch`. Quando o endereço *EXTERNAL-IP* mudar de *pendente* para *Endereço IP*, utilize `Control + C` para parar o processo de observação do kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Para ver o aplicativo em ação, abra um navegador da Web para o endereço IP externo do seu serviço. O aplicativo de voto do Azure é exibido, conforme mostrado no exemplo a seguir:

![Aplicativo de voto de exemplo do Azure em execução no AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Implantar o Jenkins em uma VM do Azure

Para implantar rapidamente o Jenkins para uso neste artigo, você pode usar o script a seguir para implantar uma máquina virtual do Azure, configurar o acesso à rede e concluir uma instalação básica do Jenkins. Para autenticação entre Jenkins e o cluster AKS, o script copia o arquivo de configuração do kubernetes de seu sistema de desenvolvimento para o sistema Jenkins.

> [!WARNING]
> Este script de exemplo é para fins de demonstração para provisionar rapidamente um ambiente Jenkins executado em uma VM do Azure. Ele usa a extensão de script personalizado do Azure para configurar uma VM e, em seguida, exibir as credenciais necessárias. Seu *~/.Kube/config* é copiado para a VM Jenkins.

Execute os comandos a seguir para baixar e executar o script. Você deve examinar o conteúdo de qualquer script antes de executá-lo- [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Leva alguns minutos para criar a VM e implantar os componentes necessários para o Docker e o Jenkins. Quando o script for concluído, ele produzirá um endereço para o servidor Jenkins e uma chave para desbloquear o painel, conforme mostrado na seguinte saída de exemplo:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Abra um navegador da Web na URL exibida e insira a chave de desbloqueio. Siga os prompts na tela para concluir a configuração do Jenkins:

- Escolha **instalar plug-ins sugeridos**
- Crie o primeiro utilizador administrador. Insira um nome de usuário, como *azureuser*, e forneça sua própria senha segura. Por fim, escreva um nome completo e o endereço de e-mail.
- Selecionar **Guardar e Concluir**
- Assim que o Jenkins estiver pronto, selecione **Começar a utilizar o Jenkins**
    - Se o browser apresentar uma página em branco quando começar a utilizar o Jenkins, reinicie o serviço Jenkins. Para reiniciar o serviço, use o SSH para o endereço IP público de sua instância do Jenkins e digite `sudo service jenkins restart`. Depois que o serviço for reiniciado, atualize o navegador da Web.
- Entre no Jenkins com o nome de usuário e a senha que você criou no processo de instalação.

## <a name="create-a-jenkins-environment-variable"></a>Criar uma variável de ambiente Jenkins

Uma variável de ambiente Jenkins é usada para armazenar o nome do servidor de logon do ACR. Essa variável é referenciada durante o trabalho de Build do Jenkins. Para criar essa variável de ambiente, conclua as seguintes etapas:

- No lado esquerdo do portal do Jenkins, selecione **gerenciar Jenkins** > **Configurar o sistema**
- Em **propriedades globais**, selecione **variáveis de ambiente**. Adicione uma variável com o nome `ACR_LOGINSERVER` e o valor do seu servidor de logon do ACR.

    ![Variáveis de ambiente Jenkins](media/aks-jenkins/env-variables.png)

- Ao concluir, clique em **salvar** na parte inferior da página de configuração do Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Criar uma credencial Jenkins para ACR

Para permitir que o Jenkins crie e envie imagens de contêiner atualizadas para o ACR, você precisa especificar as credenciais para o ACR. Essa autenticação pode usar Azure Active Directory entidades de serviço. Nos pré-requisitos, você configurou a entidade de serviço para o cluster AKS com permissões de *leitor* para o registro ACR. Essas permissões permitem que o cluster AKS *Extraia* imagens do registro ACR. Durante o processo de CI/CD, o Jenkins cria novas imagens de contêiner com base em atualizações de aplicativo e precisa *Enviar* essas imagens para o registro ACR. Para a separação de funções e permissões, agora configure uma entidade de serviço para Jenkins com permissões de *colaborador* para o registro ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Criar uma entidade de serviço para Jenkins usar ACR

Primeiro, crie uma entidade de serviço usando o comando [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] :

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

Anote o *AppID* e a *senha* mostrados na saída. Esses valores são usados nas etapas a seguir para configurar o recurso de credencial no Jenkins.

Obtenha a ID de recurso do seu registro ACR usando o comando [AZ ACR show][az-acr-show] e armazene-o como uma variável. Forneça o nome do grupo de recursos e o nome do ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Agora, crie uma atribuição de função para atribuir os direitos de *colaborador* da entidade de serviço ao registro ACR. No exemplo a seguir, forneça seu próprio *AppID* mostrado na saída de um comando anterior para criar a entidade de serviço:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Criar um recurso de credencial no Jenkins para a entidade de serviço ACR

Com a atribuição de função criada no Azure, agora armazene suas credenciais de ACR em um objeto de credencial Jenkins. Essas credenciais são referenciadas durante o trabalho de Build do Jenkins.

De volta ao lado esquerdo do portal do Jenkins, clique em **credenciais** > **Jenkins** > **credenciais globais (Irrestrito)**  > **Adicionar credenciais**

Verifique se o tipo de credencial é **nome de usuário com senha** e insira os seguintes itens:

- **Nome de usuário** -a *AppID* da entidade de serviço criada para autenticação com o registro ACR.
- **Senha** -a *senha* da entidade de serviço criada para autenticação com o registro ACR.
- **ID** – identificador de credencial, como *ACR-Credentials*

Ao concluir, o formulário de credenciais será semelhante ao exemplo a seguir:

![Criar um objeto de credencial Jenkins com as informações da entidade de serviço](media/aks-jenkins/acr-credentials.png)

Clique em **OK** e retorne ao portal do Jenkins.

## <a name="create-a-jenkins-project"></a>Criar um projeto Jenkins

Na home page do seu portal do Jenkins, selecione **novo item** no lado esquerdo:

1. Insira *Azure-vote* como nome do trabalho. Escolha **projeto Freestyle**e, em seguida, selecione **OK**
1. Na seção **geral** , selecione **projeto do GitHub** e insira a URL do repositório bifurcado, como *https:\//github.com/\<sua conta do GitHub\>/Azure-Voting-app-Redis*
1. Na seção **Gerenciamento de código-fonte** , selecione **git**, insira a URL do repositório bifurcado *. Git* , como *https:\//github.com/\<sua conta do GitHub\>/Azure-Voting-app-Redis.git*

1. Na seção **gatilhos de Build** , selecione **gatilho de gancho do GitHub para sondagem de GITscm**
1. Em **ambiente de compilação**, selecione **usar textos ou arquivos secretos**
1. Em **associações**, selecione **Adicionar** > **nome de usuário e senha (separados)**
   - Insira `ACR_ID` para a **variável de nome de usuário**e `ACR_PASSWORD` para a **variável de senha**

     ![Associações Jenkins](media/aks-jenkins/bindings.png)

1. Escolha Adicionar uma **etapa de compilação** do tipo **executar Shell** e use o texto a seguir. Esse script cria uma nova imagem de contêiner e a envia para o registro do ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Adicione outra **etapa de Build** do tipo **Execute Shell** e use o texto a seguir. Esse script atualiza a implantação do aplicativo no AKS com a nova imagem de contêiner do ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Depois de concluído, clique em **salvar**.

## <a name="test-the-jenkins-build"></a>Testar a compilação do Jenkins

Antes de automatizar o trabalho com base nas confirmações do GitHub, primeiro teste manualmente a compilação Jenkins. Essa compilação manual valida que o trabalho foi configurado corretamente, o arquivo de autenticação kubernetes apropriado está em vigor e que a autenticação com o ACR funciona.

No menu à esquerda do projeto, selecione **Compilar agora**.

![Build de teste do Jenkins](media/aks-jenkins/test-build.png)

A primeira compilação leva um minuto ou dois, uma vez que as camadas de imagem do Docker são puxadas para o servidor Jenkins. As compilações subsequentes podem usar as camadas de imagem em cache para melhorar os tempos de compilação.

Durante o processo de compilação, o repositório GitHub é clonado para o servidor de Build Jenkins. Uma nova imagem de contêiner é criada e enviada por push para o registro ACR. Por fim, o aplicativo de voto do Azure em execução no cluster AKS é atualizado para usar a nova imagem. Como nenhuma alteração foi feita no código do aplicativo, o aplicativo não será alterado se você exibir o aplicativo de exemplo em um navegador da Web.

Quando o trabalho de compilação for concluído, clique em **compilar #1** em histórico de compilação. Selecione **saída do console** e exiba a saída do processo de compilação. A linha final deve indicar uma compilação bem-sucedida.

## <a name="create-a-github-webhook"></a>Criar um webhook do GitHub

Com uma compilação manual bem-sucedida concluída, agora integre o GitHub à compilação Jenkins. Um webhook pode ser usado para executar o trabalho de compilação Jenkins cada vez que uma confirmação de código é feita no GitHub. Para criar o webhook do GitHub, conclua as seguintes etapas:

1. Navegue até o repositório do GitHub bifurcado em um navegador da Web.
1. Selecione **configurações**e, em seguida, selecione **WebHooks** no lado esquerdo.
1. Escolha **Adicionar webhook**. Para a *URL da carga*, insira `http://<publicIp:8080>/github-webhook/`, em que `<publicIp>` é o endereço IP do servidor Jenkins. Certifique-se de incluir o à direita/. Deixe os outros padrões para o tipo de conteúdo e para disparar em eventos de *Push* .
1. Selecione **Adicionar webhook**.

    ![Criar um webhook do GitHub para Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testar o pipeline completo de CI/CD

Agora você pode testar todo o pipeline de CI/CD. Quando você envia por push uma confirmação de código para o GitHub, ocorrem as seguintes etapas:

1. O webhook do GitHub chega ao Jenkins.
1. Jenkins inicia o trabalho de compilação e obtém a confirmação de código mais recente do GitHub.
1. Um Build do Docker é iniciado usando o código atualizado e a nova imagem de contêiner é marcada com o número de Build mais recente.
1. Essa nova imagem de contêiner é enviada por push para o registro de contêiner do Azure.
1. Seu aplicativo implantado nas atualizações do serviço kubernetes do Azure com a imagem de contêiner mais recente do registro de contêiner do Azure.

No computador de desenvolvimento, abra o aplicativo clonado com um editor de código. No diretório */Azure-vote/Azure-vote* , abra o arquivo chamado **CONFIG_FILE. cfg**. Atualize os valores de voto neste arquivo para algo diferente de gatos e cachorros, conforme mostrado no exemplo a seguir:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Quando atualizado, salve o arquivo, confirme as alterações e envie-as por push para a bifurcação do repositório GitHub. O webhook do GitHub dispara um novo trabalho de compilação no Jenkins. No painel da Web do Jenkins, monitore o processo de compilação. Leva alguns segundos para efetuar pull do código mais recente, criar e enviar por push a imagem atualizada e implantar o aplicativo atualizado no AKS.

Quando a compilação for concluída, atualize seu navegador da Web do aplicativo de exemplo do Azure vote. Suas alterações são exibidas, conforme mostrado no exemplo a seguir:

![Exemplo de voto do Azure no AKS atualizado pelo trabalho de Build do Jenkins](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a usar o Jenkins como parte de uma solução de CI/CD. O AKS pode se integrar a outras soluções de CI/CD e ferramentas de automação, como o [projeto DevOps do Azure][azure-devops] ou [criar um cluster AKs com Ansible][aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: cluster-container-registry-integration.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
