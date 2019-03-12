---
title: Criar um cluster do Kubernetes com o Azure Kubernetes Service (AKS) e Terraform
description: Tutorial que demonstra como criar um Cluster do Kubernetes com o Azure Kubernetes Service e Terraform
services: terraform
ms.service: azure
keywords: terraform, devops, virtual machine, azure, kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/04/2018
ms.openlocfilehash: ce16e1fbc112c4bd807c42893685643dc0a13992
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759754"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Criar um cluster do Kubernetes com o Azure Kubernetes Service e Terraform
O [Azure Kubernetes Service (AKS)](/azure/aks/) faz a gestão do seu ambiente alojado do Kubernetes, permitindo implementar e gerir rápida e facilmente aplicações contentorizadas sem que sejam necessários conhecimentos em termos de orquestração de contentores. Também põe fim às tarefas de operações e manutenções contínuas ao aprovisionar, atualizar e dimensionar recursos a pedido, sem que as aplicações sejam colocadas offline.

Neste tutorial, vai aprender a realizar as seguintes tarefas na criação de um cluster do [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) através do [Terraform](http://terraform.io) e do AKS:

> [!div class="checklist"]
> * Utilizar o HCL (HashiCorp Language) para definir um cluster do Kubernetes
> * Utilizar o Terraform e o AKS para criar um cluster do Kubernetes
> * Utilizar a ferramenta kubectl para testar a disponibilidade de um cluster do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configurar o Terraform**: Siga as instruções no artigo, [Terraform e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Principal de serviço do Azure**: Siga as instruções na secção do **criar o principal de serviço** secção no artigo [criar um Azure principal de serviço com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Tome nota dos valores de appId, displayName, password e tenant.

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios
O primeiro passo é criar o diretório que mantenha os seus ficheiros de configuração do Terraform para o exercício.

1. Navegue para o [portal do Azure](http://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se ainda não tiver selecionado um ambiente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Mude para o diretório novo:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Declarar o fornecedor do Azure
Crie o ficheiro de configuração Terraform que declara o fornecedor do Azure.

1. No Cloud Shell, crie um ficheiro com o nome `main.tf`.

    ```bash
    vi main.tf
    ```

1. Selecione a tecla I para entrar no modo de inserção.

1. Cole o seguinte código no editor:

    ```JSON
    provider "azurerm" {
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Saia do modo de inserção ao clicar na tecla **Esc**.

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Definir um cluster do Kubernetes
Crie o ficheiro de configuração Terraform que declare os recursos para o cluster do Kubernetes.

1. No Cloud Shell, crie um ficheiro com o nome `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Selecione a tecla I para entrar no modo de inserção.

1. Cole o seguinte código no editor:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_log_analytics_workspace" "test" {
        name                = "${var.log_analytics_workspace_name}"
        location            = "${var.log_analytics_workspace_location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        sku                 = "${var.log_analytics_workspace_sku}"
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = "${azurerm_log_analytics_workspace.test.location}"
        resource_group_name   = "${azurerm_resource_group.k8s.name}"
        workspace_resource_id = "${azurerm_log_analytics_workspace.test.id}"
        workspace_name        = "${azurerm_log_analytics_workspace.test.name}"

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "agentpool"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS1_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
            }
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    O código anterior define o nome do cluster, a localização e o resource_group_name. Além disso, é definido o valor dns_prefix, que faz parte do nome de domínio completamente qualificado (FQDN) utilizado para aceder ao cluster.

    O registo **linux_profile** permite-lhe configurar as definições que permitem iniciar sessão nos nós de trabalho através do SSH.

    No AKS, paga apenas os nós de trabalho. O registo **agent_pool_profile** configura os detalhes destes nós de trabalho. O **registo agent_pool_profile** inclui o número de nós de trabalho a criar e o tipo de nós de trabalho. Se precisar de aumentar verticalmente ou reduzir verticalmente o cluster futuramente, modifique o valor **count** neste registo.

1. Saia do modo de inserção ao clicar na tecla **Esc**.

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Declarar as variáveis

1. No Cloud Shell, crie um ficheiro com o nome `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Selecione a tecla I para entrar no modo de inserção.

1. Cole o seguinte código no editor:

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Saia do modo de inserção ao clicar na tecla **Esc**.

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Criar um ficheiro de saída do Terraform
Os [ficheiros de saída do Terraform](https://www.terraform.io/docs/configuration/outputs.html) permitem-lhe definir valores que serão realçados para o utilizador quando o Terraform aplicar um plano e podem ser consultados com o comando `terraform output`. Nesta secção, vai criar um ficheiro de saída que permite o acesso ao cluster com [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. No Cloud Shell, crie um ficheiro com o nome `output.tf`.

    ```bash
    vi output.tf
    ```

1. Selecione a tecla I para entrar no modo de inserção.

1. Cole o seguinte código no editor:

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Saia do modo de inserção ao clicar na tecla **Esc**.

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Configurar o armazenamento do Azure para armazenar estado do Terraform
O Terraform monitoriza o estado localmente através do ficheiro `terraform.tfstate`. Este padrão funciona bem num ambiente individual. No entanto, num ambiente mais prático com múltiplas pessoas, precisa de acompanhar o estado no servidor através do [armazenamento do Azure](/azure/storage/). Nesta secção, vai obter as informações necessárias da conta de armazenamento (nome e chave da conta) e criar um contentor de armazenamento no qual serão armazenadas as informações de estado do Terraform.

1. No portal do Azure, selecione **Todos os serviços** no menu à esquerda.

1. Selecione **Contas de armazenamento**.

1. No separador **Contas de armazenamento**, selecione o nome da conta de armazenamento na qual o Terraform deve armazenar o estado. Por exemplo, pode utilizar a conta de armazenamento criada quando abriu o Cloud Shell pela primeira vez.  O nome de conta de armazenamento criado pelo Cloud Shell costuma começar por `cs`, seguido de uma cadeia aleatória de números e letras. **Lembre-se do nome da conta de armazenamento que selecionar, pois será necessário mais tarde.**

1. No separador de conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu de conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Anote o valor **key1** **key**. (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. No Cloud Shell, crie um contentor na sua conta de armazenamento do Azure (substitua os marcadores de posição &lt;YourAzureStorageAccountName> e &lt;YourAzureStorageAccountAccessKey> pelos valores correspondentes na sua conta de armazenamento do Azure).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Criar o cluster do Kubernetes
Nesta secção, pode ver como utilizar o comando `terraform init` para criar os recursos definidos nos ficheiros de configuração que criou nas secções anteriores.

1. No Cloud Shell, inicie o Terraform (substitua os marcadores de posição &lt;YourAzureStorageAccountName> e &lt;YourAzureStorageAccountAccessKey> pelos valores correspondentes na sua conta de armazenamento do Azure).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    O comando `terraform init` mostra o sucesso de inicializar o plug-in de fornecedor e back-end:

    ![Exemplo de resultados "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exporte as credenciais do principal de serviço. Substitua os marcadores de posição &lt;your-client-id> e &lt;your-client-secret> pelos valores **appId** e **password** associados ao seu principal de serviço, respetivamente.

    ```bash
    export TF_VAR_client_id=<your-client-id>
    export TF_VAR_client_secret=<your-client-secret>
    ```

1. Execute o comando `terraform plan` para criar o plano do Terraform que define os elementos de infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` mostra os recursos que serão criados quando executar o comando `terraform apply`:

    ![Exemplo de resultados "terraform plan"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster do Kubernetes pode demorar vários minutos, o que faz com que a sessão do Cloud Shell exceda o tempo limite. Se a sessão do Cloud Shell exceder o tempo limite, pode seguir os passos na secção "Recuperar de um tempo limite do Cloud Shell" para que possa concluir o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` mostra os resultados de criar os recursos definidos nos seus ficheiros de configuração:

    ![Exemplo de resultados "terraform apply"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. No portal do Azure, selecione **todos os serviços** no menu da esquerda para ver os recursos criados para o novo cluster de Kubernetes.

    ![Comando do Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um tempo limite do Cloud Shell
Se o tempo limite de sessão do Cloud Shell for excedido, pode efetuar os seguintes passos para recuperar:

1. Inicie uma sessão do Cloud Shell.

1. Mude para o diretório com os seus ficheiros de configuração do Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Execute o seguinte comando:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Testar o cluster do Kubernetes
As ferramentas do Kubernetes podem ser utilizadas para verificar o cluster acabado de criar.

1. Obtenha a configuração do Kubernetes do estado Terraform e armazene-a num ficheiro que o kubectl possa ler.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Defina uma variável de ambiente para que o kubectl escolha a configuração correta.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Verifique o estado de funcionamento do cluster.

    ```bash
    kubectl get nodes
    ```

    Deverá ver os detalhes dos seus nós de trabalho e estes deverão ter um estado **Ready**, conforme mostrado na seguinte imagem:

    ![A ferramenta kubectl permite-lhe verificar o estado de funcionamento do seu cluster do Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Monitorizar o estado de funcionamento e os registos
Quando o cluster do AKS foi criado, a monitorização foi ativada para capturar métricas de estado de funcionamento dos nós do cluster e dos pods. Estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para obter mais informações sobre a monitorização de estado de funcionamento do contentor, consulte [estado de funcionamento do Monitor do Azure Kubernetes Service](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar o Terraform e AKS para criar o cluster do Kubernetes. Aqui estão alguns recursos adicionais para o ajudar a saber mais acerca do Terraform no Azure: 

 [Hub do Terraform no Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentação do fornecedor do Azure do Terraform](https://aka.ms/terraform)  
 [Origem do fornecedor do Azure do Terraform](https://aka.ms/tfgit)  
 [Módulos do Azure do Terraform](https://aka.ms/tfmodules)
