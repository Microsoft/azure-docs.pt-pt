---
title: Implementar no Azure Kubernetes Service (AKS) com o Jenkins e o padrão de implementação azul/verde
description: Saiba como implementar no Azure Kubernetes Service (AKS) com o Jenkins e o padrão de implementação azul/verde
ms.service: jenkins
keywords: jenkins, azure, devops, kubernetes, k8s, aks, implementação azul verde, entrega contínua, cd
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/11/2018
ms.openlocfilehash: 93f2ac284931ba664e0965e537e515c824e6f7a6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092298"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Implementar no Azure Kubernetes Service (AKS) com o Jenkins e o padrão de implementação azul/verde

O Azure Kubernetes Service (AKS) faz a gestão do seu ambiente alojado do Kubernetes, permitindo implementar e gerir rápida e facilmente aplicações contentorizadas. Não precisa de ter conhecimentos na orquestração de contentores. O AKS também põe fim às tarefas de operações e manutenções contínuas ao aprovisionar, atualizar e dimensionar recursos a pedido. Não precisa de pôr as aplicações offline. Para obter mais informações sobre o AKS, veja a [documentação do AKS](/azure/aks/).

A implementação azul/verde é um padrão de Entrega Contínua de DevOps do Azure que se baseia em manter uma versão já existente ativa (azul) enquanto é implementada uma nova. Regra geral, este padrão aplica o balanceamento de carga para direcionar quantidades crescentes de tráfego para a implementação verde. Se a monitorização detetar um incidente, o tráfego pode ser redirecionado para a implementação azul, que ainda está em execução. Para obter mais informações sobre a Entrega Contínua, veja [What is Continuous Delivery](/azure/devops/what-is-continuous-delivery) (O que é a Entrega Contínua).

Neste tutorial, vai aprender a realizar as seguintes tarefas:

> [!div class="checklist"]
> * Saber mais sobre o padrão de implementação azul/verde
> * Criar um cluster do Kubernetes gerido
> * Executar um script de exemplo para configurar um cluster do Kubernetes
> * Configurar manualmente um cluster do Kubernetes
> * Criar e executar um trabalho do Jenkins

## <a name="prerequisites"></a>Pré-requisitos
- [Conta do GitHub](https://github.com) : Precisa de uma conta do GitHub para clonar o repositório de exemplo.
- [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) : Utilizar a CLI 2.0 do Azure para criar o cluster de Kubernetes.
- [Chocolatey](https://chocolatey.org): Um Gestor de pacotes que utilizar para instalar o kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Uma interface de linha de comandos que utiliza para executar comandos em relação a clusters do Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): Um processador JSON simples e de linha de comandos.

## <a name="clone-the-sample-app-from-github"></a>Clonar a aplicação de exemplo a partir do GitHub

No repositório da Microsoft no GitHub, pode encontrar uma aplicação de exemplo que ilustra como implementar no AKS com o Jenkins e o padrão azul/verde. Nesta secção, vai criar um fork desse repositório no seu GitHub e clonar a aplicação para o seu sistema local.

1. Navegue para o repositório do GitHub da aplicação de exemplo [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Captura de ecrã da aplicação de exemplo no repositório da Microsoft no GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Crie um fork do repositório ao selecionar **Fork**, no canto superior direito da página, e siga as instruções para criar o fork do repositório na sua conta do GitHub.

    ![Captura de ecrã da opção do GitHub para criar o fork](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Depois de criar o fork do repositório, verá que o nome da conta muda para o nome da sua conta e a origem do fork do repositório (Microsoft) é indicada numa nota.

    ![Captura de ecrã do nome da conta do GitHub e da nota](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Selecione **Clone or download** (Clonar ou transferir).

    ![Captura de ecrã da opção do GitHub para clonar ou transferir um repositório](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Na janela **Clone with HTTPS** (Clonar com HTTPS), selecione o ícone **copy** (copiar).

    ![Captura de ecrã da opção do GitHub para copiar o URL do clone para a área de transferência](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Abra um terminal ou uma janela de Bash do Git.

1. Mude o diretório para a localização na qual pretende armazenar a cópia local (clone) do repositório.

1. Com o comando `git clone`, clone o URL que copiou anteriormente.

    ![Captura de ecrã do comando de clonagem do git do Bash do Git](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Prima a tecla Enter para iniciar o processo de clonagem.

    ![Captura dos resultados do comando de clonagem do git do Bash do Git](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Mude o diretório para o diretório criado recentemente que contém o clone da origem da aplicação.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Criar e configurar um cluster do Kubernetes gerido

Neste tutorial, vai executar os seguintes passos:

- Utilize a CLI 2.0 do Azure para criar um cluster do Kubernetes gerido.
- Saiba como configurar um cluster, seja com o script de configuração ou manualmente.
- Crie uma instância do serviço Azure Container Registry.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Utilizar a CLI 2.0 do Azure para criar um cluster do Kubernetes gerido
Para criar um cluster do Kubernetes gerido com a [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), confirme que está a utilizar a versão 2.0.25 ou posterior da CLI do Azure.

1. Inicie sessão na sua conta do Azure. Depois de introduzir o seguinte comando, receberá instruções que explicam como concluir o início de sessão. 
    
    ```bash
    az login
    ```

1. Quando executa o comando `az login` no passo anterior, é apresentada uma lista com todas as suas subscrições do Azure (juntamente com os respetivos IDs). Neste passo, vai definir a subscrição do Azure predefinida. Substitua o marcador de posição &lt;your-subscription-id> pelo ID da subscrição pretendida. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Crie um grupo de recursos. Substitua o marcador de posição &lt;your-resource-group-name> pelo nome do seu grupo de recursos novo e substitua o marcador de posição &lt;your-location> pela localização. O comando `az account list-locations` mostra todas as localizações do Azure. Durante a pré-visualização do AKS, nem todas as regiões estão disponíveis. Se introduzir uma região que não for válida nesta altura, a mensagem de erro mostra todas as localizações disponíveis.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Crie o cluster do Kubernetes. Substitua &lt;your-resource-group-name> pelo nome do grupo de recursos criado no passo anterior e substitua &lt;your-kubernetes-cluster-name> pelo nome do cluster novo. (Este processo pode demorar vários minutos a ser concluído).

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Configurar o cluster do Kubernetes

Pode configurar uma implementação azul/verde no AKS manualmente ou utilizar um script de configuração no exemplo clonado anteriormente. Nesta secção, vai aprender ambos os métodos.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Configurar o cluster do Kubernetes através do script de configuração de exemplo
1. Edite o ficheiro **deploy/aks/setup/setup.sh**, substituindo os seguintes marcadores de posição pelos valores adequados para o seu ambiente: 

   - **&lt;your-resource-group-name>**
   - **&lt;your-kubernetes-cluster-name>**
   - **&lt;your-location>**
   - **&lt;your-dns-name-suffix>**

     ![Captura de ecrã do script setup.sh no bash, com vários marcadores de posição realçados](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Execute o script de configuração.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Configurar um cluster do Kubernetes manualmente 
1. Transfira a configuração do Kubernetes para a sua pasta de perfil.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Mude o diretório para o diretório **deploy/aks/setup**. 

1. Execute os seguintes comandos **kubectl** para configurar os serviços para o ponto final público e para os dois pontos finais de teste.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Atualize o nome DNS dos pontos finais públicos e de teste. Quando cria um cluster do Kubernetes, também cria um [grupo de recursos adicional](https://github.com/Azure/AKS/issues/3), com o padrão de nomenclatura **MC_&lt;your-resource-group-name>_&lt;your-kubernetes-cluster-name>_&lt;your-location>**.

    Localize os IPs públicos no grupo de recursos.

    ![Captura de ecrã dos IPs públicos no grupo de recursos](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Para cada um dos serviços, execute o seguinte comando para localizar o endereço IP externo:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Atualize o nome DNS para o endereço IP correspondente com o comando abaixo:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Repita a chamada para `todoapp-test-blue` e `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    O nome DNS tem de ser exclusivo na sua subscrição. Para garantir a exclusividade, pode utilizar `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Criar uma instância do Container Registry

1. Execute o comando `az acr create` para criar uma instância do Container Registry. Na próxima secção, pode utilizar `login server` como o URL de registo do Docker.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Execute o comando `az acr credential` para mostrar as suas credenciais do Container Registry. Tome nota do nome de utilizador e da palavra-passe do registo do Docker, uma vez que vai precisar dessas informações na próxima secção.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Preparar o servidor Jenkins

Nesta secção, vai aprender a preparar o servidor Jenkins para executar uma compilação, que é adequada para testes. Contudo, deve utilizar um [agente de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) ou um [agente do Azure Container](https://plugins.jenkins.io/azure-container-agents) para criar um agente no Azure, o qual irá executar as suas compilações. Para obter mais informações, veja o artigo do Jenkins sobre as [implicações em termos de segurança da compilação no servidor Mestre](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Implemente um [servidor Mestre do Jenkins no Azure](https://aka.ms/jenkins-on-azure).

1. Ligue ao servidor através de SSH e instale as ferramentas de compilação no servidor em que vai executar a compilação.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Instale o Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Confirme que o utilizador `jenkins` tem permissão para executar os comandos `docker`.

1. [Instale o kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Transfira o jq](https://stedolan.github.io/jq/download/).

1. Instale o jq com o seguinte comando:

   ```bash
   sudo apt-get install jq
   ```
   
1. Realize os passos abaixo no dashboard do Jenkins para instalar os plug-ins no Jenkins:

    1. Selecione **Manage Jenkins > Manage Plugins > Available** (Gerir o Jenkins > Gerir plug-ins > Disponíveis).
    1. Procure e instale o plug-in do Azure Container Service.

1. Adicione credenciais para gerir recursos no Azure. Se ainda não o tiver, instale o plug-in **Azure Credential**.

1. Adicione a credencial do Principal de Serviço do Azure como o tipo **Microsoft Azure Service Principal** (Principal de Serviço do Microsoft Azure).

1. Adicione o nome de utilizador e a palavra-passe do registo do Docker (obtidos na secção “Criar uma instância do Container Registry”) como o tipo **Username with password** (Nome de utilizador com palavra-passe).

## <a name="edit-the-jenkinsfile"></a>Editar o Jenkinsfile

1. No seu próprio repositório, aceda a `/deploy/aks/` e abra `Jenkinsfile`.

2. Atualize o ficheiro da seguinte forma:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Atualize o ID da credencial do Container Registry:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Criar o trabalho
1. Adicione um trabalho novo do tipo **Pipeline**.

1. Selecione **Pipeline** > **Definition** > **Pipeline script from SCM** (Pipeline > Definição > Script de pipeline de SCM).

1. Introduza o URL do repositório de SCM com &lt;your-forked-repo>.

1. Introduza o caminho do script como `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Executar a tarefa

1. Confirme que consegue executar o projeto no seu ambiente local. Eis como: [Execute o projeto no computador local](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Execute o trabalho do Jenkins. Na primeira vez que executar o trabalho, o Jenkins implementa a aplicação todo no ambiente azul, que é o ambiente inativo predefinido. 

1. Para verificar se o trabalho foi executado, aceda a estes URLs:
    - Ponto final público: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Ponto final azul - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Ponto final verde - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Os pontos finais público e de teste azul têm a mesma atualização, ao passo que o ponto final verde mostra a imagem tomcat predefinida. 

Se executar a compilação mais de uma vez, a mesma é transmitida pelas implementações azul e verde. Por outras palavras, se o ambiente atual for azul, o trabalho implementa e testa no ambiente verde. Depois, se os testes forem bem-sucedidos, o trabalho atualiza o ponto final público da aplicação para encaminhar o tráfego para o ambiente verde.

## <a name="additional-information"></a>Informações adicionais

Para saber mais sobre a implementação sem tempo de inatividade, veja este [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que crio neste tutorial, pode eliminá-los.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Resolução de problemas

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a implementar no AKS com o Jenkins e o padrão de implementação azul/verde. Para saber mais sobre o fornecedor de Jenkins do Azure, veja o site Jenkins no Azure.

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/jenkins/)