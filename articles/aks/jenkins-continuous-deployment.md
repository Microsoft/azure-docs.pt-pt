---
title: Tutorial - implementar a partir do GitHub para o serviço Kubernetes do Azure (AKS) com o Jenkins
description: Configurar o Jenkins para integração contínua (CI) partir do GitHub e a implementação contínua (CD) para o Azure Kubernetes Service (AKS)
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: 703aa081c8acf41f9206e2b0ccff45571367d2e8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073788"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: Implementar a partir do GitHub para o Azure Kubernetes Service (AKS) com a integração contínua Jenkins e implementação

Este tutorial implementa uma aplicação de exemplo do GitHub para uma [do Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) cluster ao configurar a integração contínua (CI) e a implementação contínua (CD) no Jenkins. Dessa forma, quando atualizar a sua aplicação ao emitir consolidações para o GitHub, Jenkins automaticamente é executada uma nova compilação de contentor, envia as imagens de contentor para o Azure Container Registry (ACR) e, em seguida, executa a aplicação no AKS. 

Neste tutorial, irá concluir estas tarefas:

> [!div class="checklist"]
> * Implemente uma aplicação de voto do Azure de exemplo para um cluster do AKS.
> * Crie um projeto Jenkins básico.
> * Configure as credenciais do Jenkins interagir com o ACR.
> * Crie uma tarefa de compilação do Jenkins e um webhook do GitHub para compilações automatizadas.
> * Teste o pipeline de CI/CD para atualizar uma aplicação no AKS com base nas consolidações de código do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa destes itens:

- Noções básicas sobre do Kubernetes, o Git, CI/CD e contentor de imagens

- Uma [cluster do AKS] [ aks-quickstart] e `kubectl` configurado com o [as credenciais de cluster do AKS][aks-credentials]

- Uma [registo de Azure Container Registry (ACR)][acr-quickstart], o nome de servidor de início de sessão do ACR e o cluster do AKS configurado para [autenticar com o registo do ACR][acr-authentication]

- A CLI do Azure versão 2.0.46 ou posterior instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

- [Docker instalado] [ docker-install] no sistema de desenvolvimento

- Uma conta do GitHub [token de acesso pessoal do GitHub][git-access-token]e instalado no sistema de desenvolvimento de cliente de Git

- Se fornecer seus próprios Jenkins de instância em vez de maneira de implementar o Jenkins com script de neste exemplo, os Jenkins instância necessidades [Docker instalado e configurado] [ docker-install] e [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Preparar a sua aplicação

Neste artigo, vai utilizar um exemplo de aplicação de voto do Azure que contém uma interface da web hospedada em pods de um ou mais e um segundo pod Redis de alojamento para o armazenamento de dados temporário. Antes de integrar o Jenkins e o AKS para Implantações automatizadas, primeiro manualmente preparar e implementar a aplicação de voto do Azure no seu cluster do AKS. Esta implementação manual é a versão da aplicação e permite-lhe ver a aplicação em ação.

Bifurcar o repositório de GitHub seguinte para o aplicativo de exemplo - [ https://github.com/Azure-Samples/azure-voting-app-redis ](https://github.com/Azure-Samples/azure-voting-app-redis). Para bifurcar o repositório para a sua própria conta do GitHub, selecione o botão **Fork** no canto superior direito.

Clone a bifurcação no sistema de desenvolvimento. Certifique-se de que utilizar o URL do seu fork quando este repositório de clonagem:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Mude para o diretório do seu fork clonado:

```console
cd azure-voting-app-redis
```

Para criar as imagens de contentor necessárias para a aplicação de exemplo, utilize o *docker-yaml* de ficheiros com `docker-compose`:

```console
docker-compose up -d
```

As imagens bases necessárias são retiradas e os contentores de aplicação incorporada. Em seguida, pode utilizar o [imagens do docker] [ docker-images] comando para ver a imagem criada. Foram transferidas ou criadas três imagens. A imagem `azure-vote-front` contém a aplicação e utilizar a imagem `nginx-flask` como base. O `redis` imagem é utilizada para iniciar uma instância de Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Antes de poder enviar o *azure-vote-front* imagem de contentor para o ACR, obter o seu servidor de início de sessão ACR com o [lista do az acr] [ az-acr-list] comando. O exemplo seguinte obtém o endereço do servidor de início de sessão ACR para um registo no grupo de recursos chamado *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize o [etiqueta do docker] [ docker-tag] comando para marcar a imagem com o nome de servidor de início de sessão do ACR e um número de versão de `v1`. Fornecer seu próprio `<acrLoginServer>` nome obtido no passo anterior:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Por fim, enviar por push o *azure-vote-front* imagem para o seu registo ACR. Mais uma vez, substitua `<acrLoginServer>` com o nome do servidor de início de sessão do seu próprio registo do ACR, tais como `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Implementar a aplicação de exemplo no AKS

Para implementar a aplicação de exemplo ao seu cluster do AKS, pode usar o arquivo de manifesto do Kubernetes na raiz do repositório de repositório do Azure vote. Abra o *azure-vote-all-in-one-redis.yaml* como o arquivo com um editor de manifesto `vi`. Substitua `microsoft` pelo nome do servidor de início de sessão do ACR. Este valor foi encontrado na linha **47** do ficheiro de manifesto:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Em seguida, utilize o [aplicam-se de kubectl] [ kubectl-apply] comando para implementar a aplicação no seu cluster do AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Um serviço de Balanceador de carga do Kubernetes é criado para expor a aplicação na internet. Este processo pode demorar alguns minutos. Para monitorizar o progresso da implementação do Balanceador de carga, utilize o [kubectl obter serviço] [ kubectl-get] comando com o `--watch` argumento. Quando o endereço *EXTERNAL-IP* mudar de *pendente* para *Endereço IP*, utilize `Control + C` para parar o processo de observação do kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Para ver a aplicação em ação, abra um navegador da web para o endereço IP externo do seu serviço. A aplicação Azure vote é apresentada, conforme mostrado no exemplo a seguir:

![Aplicação de voto de exemplo do Azure em execução no AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Implementar o Jenkins para uma VM do Azure

Para implementar rapidamente o Jenkins para utilização neste artigo, pode utilizar o seguinte script para implementar uma máquina virtual do Azure, configurar o acesso de rede e concluir uma instalação básica do Jenkins. Para a autenticação entre o Jenkins e o cluster do AKS, o script copia o ficheiro de configuração de Kubernetes do seu sistema de desenvolvimento para o sistema do Jenkins.

> [!WARNING]
> Este script de exemplo é para fins de demonstração aprovisionar rapidamente um ambiente Jenkins, que é executado numa VM do Azure. Utiliza a extensão de script personalizado do Azure para configurar uma VM e, em seguida, apresentar as credenciais necessárias. Sua *~/.kube/config* é copiado para a VM do Jenkins.

Execute os seguintes comandos para transferir e executar o script. Deve rever o conteúdo de qualquer script antes de executá-lo- [ https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh ](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Demora alguns minutos para criar a VM e implementar os componentes necessários para o Docker e o Jenkins. Quando o script tiver concluído, produz um endereço para o servidor Jenkins e uma chave para desbloquear o dashboard, conforme mostrado no seguinte exemplo:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Abra um navegador da web para o URL apresentado e introduza a chave de desbloqueio. Siga as mensagens no ecrã para concluir a configuração do Jenkins:

- Escolha **instale os plugins sugeridos**
- Crie o primeiro utilizador administrador. Introduza um nome de utilizador, tal como *azureuser*, em seguida, forneça a sua própria palavra-passe segura. Por fim, escreva um nome completo e o endereço de e-mail.
- Selecionar **Guardar e Concluir**
- Assim que o Jenkins estiver pronto, selecione **Começar a utilizar o Jenkins**
    - Se o browser apresentar uma página em branco quando começar a utilizar o Jenkins, reinicie o serviço Jenkins. Para reiniciar o serviço, o SSH para o endereço IP público da sua instância do Jenkins e o tipo `sudo service jenkins restart`. Depois do serviço foi reiniciado, atualize, navegador da web.
- Inicie sessão no Jenkins com o nome de utilizador e palavra-passe que criou no processo de instalação.

## <a name="create-a-jenkins-environment-variable"></a>Criar uma variável de ambiente Jenkins

Uma variável de ambiente Jenkins é utilizada para conter o nome de servidor de início de sessão do ACR. Esta variável é referenciada durante a tarefa de compilação do Jenkins. Para criar esta variável de ambiente, conclua os seguintes passos:

- No lado esquerdo do portal do Jenkins, selecione **gerir Jenkins** > **configurar o sistema**
- Sob **propriedades globais**, selecione **variáveis de ambiente**. Adicione uma variável com o nome `ACR_LOGINSERVER` e o valor do seu servidor de início de sessão do ACR.

    ![Variáveis de ambiente Jenkins](media/aks-jenkins/env-variables.png)

- Quando terminar, clique em **guardar** na parte inferior da página de configuração do Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Criar uma credencial do Jenkins para o ACR

Para permitir que o Jenkins criar e, em seguida, enviar imagens de contentor atualizada para o ACR, terá de especificar credenciais para o ACR. Esta autenticação pode utilizar principais de serviço do Azure Active Directory. Pré-requisitos, configurou o principal de serviço para os AKS cluster com *leitor* permissões para o seu registo ACR. Estas permissões permitem ao cluster AKS *pull* imagens do registo ACR. Durante o processo de CI/CD, o Jenkins compila novas imagens de contentor com base nas atualizações da aplicação e, tem de, em seguida, *push* essas imagens para o registo do ACR. Para a separação de funções e permissões, agora configurar um principal de serviço para o Jenkins com *contribuinte* permissões para o seu registo ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Criar um principal de serviço para o Jenkins utilizar o ACR

Primeiro, crie um principal de serviço através da [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] comando:

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

Anote o *appId* e *palavra-passe* mostrado no resultado. Estes valores são utilizados nos seguintes passos para configurar o recurso de credencial no Jenkins.

Obter o ID de recurso do seu registo do ACR com o [show do az acr] [ az-acr-show] de comandos e armazená-lo como uma variável. Forneça o nome do grupo de recursos e o nome ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Agora, crie uma atribuição de função para atribuir o principal de serviço *contribuinte* direitos para o registo do ACR. No exemplo a seguir, fornecer seu próprio *appId* mostrado no resultado do comando anterior para criar o principal de serviço:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Criar um recurso de credencial no Jenkins para o serviço ACR principal

Com a atribuição de função criada no Azure, agora armazene as suas credenciais ACR num objeto de credencial do Jenkins. Estas credenciais são referenciadas durante a tarefa de compilação do Jenkins.

Novamente no lado esquerdo do portal do Jenkins, clique em **credenciais** > **Jenkins** > **Global de credenciais (ilimitado)**  >  **Adicionar credenciais**

Certifique-se de que é o tipo de credencial **nome de utilizador com palavra-passe** e introduza os seguintes itens:

- **Nome de utilizador** – a *appId* do principal de serviço criado para a autenticação com o registo do ACR.
- **Palavra-passe** – a *palavra-passe* do principal de serviço criado para a autenticação com o registo do ACR.
- **ID** -como o identificador de credencial *as credenciais do acr*

Quando terminar, o formulário de credenciais terá um aspeto semelhante ao seguinte exemplo:

![Criar um objeto de credencial do Jenkins com as informações do principal de serviço](media/aks-jenkins/acr-credentials.png)

Clique em **OK** e regresse ao portal do Jenkins.

## <a name="create-a-jenkins-project"></a>Criar um projeto Jenkins

Na home page do portal do Jenkins, selecione **novo item** no lado esquerdo:

1. Introduza *azure-vote* nome da tarefa. Escolher **projeto Freestyle**, em seguida, selecione **OK**
1. Sob o **gerais** secção, selecione **projeto GitHub** e introduza o URL do repositório bifurcado, como *https:\//github.com/\<your-conta github\>/azure-voting-app-redis*
1. Sob o **gestão de código fonte** secção, selecione **Git**, introduza o seu repositório bifurcado *projeto* URL, como *https:\//github.com/\<sua conta github\>/azure-voting-app-redis.git*

1. Sob o **criar Acionadores** secção, selecione **acionador de hook do GitHub para consulta GITscm**
1. Sob **ambiente de compilação**, selecione **utilizar textos secretos ou ficheiros**
1. Sob **enlaces**, selecione **Add** > **nome de utilizador e palavra-passe (separados)**
   - Introduza `ACR_ID` para o **variável nome de utilizador**, e `ACR_PASSWORD` para o **variável de palavra-passe**

     ![Enlaces do Jenkins](media/aks-jenkins/bindings.png)

1. Optar por adicionar um **passo de compilação** do tipo **executar shell** e utilize o seguinte texto. Este script cria uma nova imagem de contentor e envia-o para o seu registo ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Adicione outro **passo de compilação** do tipo **executar shell** e utilize o seguinte texto. Este script atualiza a implementação da aplicação no AKS com a nova imagem de contentor do ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Depois de concluído, clique em **guardar**.

## <a name="test-the-jenkins-build"></a>Testar a compilação do Jenkins

Antes de automatizar a tarefa com base nas consolidações do GitHub, primeiro teste manualmente a compilação do Jenkins. Este manual compilação valida se a tarefa foi configurada corretamente, o ficheiro de autenticação adequado do Kubernetes está no local e que funciona a autenticação com o ACR.

No menu do lado esquerdo do projeto, selecione **compilar agora**.

![Compilação de teste do Jenkins](media/aks-jenkins/test-build.png)

A primeira compilação leva um minuto ou dois como as camadas de imagem do Docker são baixados para o servidor Jenkins. Compilações subseqüentes podem utilizar as camadas de imagem em cache para melhorar os tempos de compilação.

Durante o processo de compilação, o repositório do GitHub for clonado para o servidor de compilação do Jenkins. Uma nova imagem de contentor é criada e enviada para o registo do ACR. Por fim, a aplicação de voto do Azure em execução no cluster do AKS é atualizada para utilizar a nova imagem. Uma vez que não foram efetuadas alterações ao código do aplicativo, o aplicativo não é alterado se visualizar a aplicação de exemplo num navegador da web.

Quando a tarefa de compilação estiver concluída, clique em **criar #1** em histórico de compilações. Selecione **saída da consola** e ver o resultado do processo de compilação. A linha final deve indicar uma compilação com êxito.

## <a name="create-a-github-webhook"></a>Criar um webhook do GitHub

Com uma compilação com êxito manual completa, agora a integrar o GitHub para a compilação do Jenkins. Um webhook pode ser utilizado para executar a tarefa de compilação do Jenkins sempre que uma consolidação de código é efetuada no GitHub. Para criar o webhook do GitHub, conclua os seguintes passos:

1. Navegue para o seu repositório bifurcado do GitHub num navegador da web.
1. Selecione **configurações**, em seguida, selecione **Webhooks** no lado esquerdo.
1. Optar por **adicionar webhook**. Para o *URL de Payload*, introduza `http://<publicIp:8080>/github-webhook/`, onde `<publicIp>` é o endereço IP do servidor Jenkins. Certifique-se incluir à direita /. Manter os padrões para o tipo de conteúdo e para disparar *push* eventos.
1. Selecione **adicionar webhook**.

    ![Criar um webhook do GitHub para o Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testar o pipeline de CI/CD completo

Agora pode testar todo o pipeline de CI/CD. Quando emitir uma consolidação de código para o GitHub, acontecem os seguintes passos:

1. O GitHub webhook acede ao Jenkins.
1. Jenkins inicia a tarefa de compilação e obtém a consolidação de código mais recente a partir do GitHub.
1. Uma compilação do Docker é iniciada com o código atualizado e a nova imagem de contentor é marcada com o número de compilação mais recente.
1. Esta nova imagem de contentor é emitidos via push para o Azure Container Registry.
1. A aplicação implementada para atualizações de serviço Kubernetes do Azure com a imagem de contentor mais recente a partir do registo do Azure Container Registry.

No computador de desenvolvimento, abra a aplicação clonada com um editor de código. Sob o */azure-vote/azure-vote* directory, abra o ficheiro denominado **config_file.cfg**. Atualize os valores de voto neste ficheiro para algo que não sejam gatos e cães, conforme mostrado no exemplo a seguir:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Quando atualizado, salvar o arquivo consolidar as alterações e distribuí-los para o fork do repositório do GitHub. O webhook do GitHub aciona uma nova tarefa de compilação no Jenkins. No dashboard do Jenkins web, monitorize o processo de compilação. Demora alguns segundos para extrair o código mais recente, criar e enviar a imagem atualizada e implementar a aplicação atualizada no AKS.

Assim que a compilação estiver concluída, atualize o seu navegador da web de exemplo de aplicação de voto do Azure. As alterações são exibidas, conforme mostrado no exemplo a seguir:

![Vote de exemplo, o Azure no AKS atualizada pela tarefa de compilação Jenkins](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a utilizar o Jenkins como parte de uma solução de CI/CD. AKS pode integrar com outras soluções de CI/CD e ferramentas de automatização, tais como o [projeto de DevOps do Azure] [ azure-devops] ou [criação de um cluster do AKS com o Ansible] [ aks-ansible].

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
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
