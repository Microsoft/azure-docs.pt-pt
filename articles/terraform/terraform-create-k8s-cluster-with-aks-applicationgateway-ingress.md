---
title: Criar um cluster de Kubernetes com o Gateway de aplicação como controlador de entrada com o Azure Kubernetes Service (AKS)
description: Tutorial que ilustra como criar um Cluster do Kubernetes com o serviço de Kubernetes do Azure com o Gateway de aplicação como controlador de entrada
services: terraform
ms.service: azure
keywords: terraform, devops, máquina virtual, do azure, kubernetes, entrada, o gateway de aplicação
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 1/10/2019
ms.openlocfilehash: 477b2ec1af4c52f51c3ab20ac2ddf7ef043dfcc7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994356"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>Criar um cluster de Kubernetes com o controlador de entrada do Gateway de aplicação com o serviço Kubernetes do Azure e o Terraform
[O Azure Kubernetes Service (AKS)](/azure/aks/) gere o seu ambiente alojado do Kubernetes. AKS torna rápido e fácil de implementar e gerir aplicações em contentores sem conhecimentos de orquestração de contentores. Também põe fim às tarefas de operações e manutenções contínuas ao aprovisionar, atualizar e dimensionar recursos a pedido, sem que as aplicações sejam colocadas offline.

Um controlador de entrada é uma parte do software que fornece o proxy inverso, encaminhamento de tráfego configurável e terminação de TLS para serviços do Kubernetes. Recursos de entrada do Kubernetes são utilizados para configurar as regras de entrada e as rotas para serviços individuais do Kubernetes. Utilizar um controlador de entrada e de regras de entrada, um único endereço IP pode servir-se para encaminhar o tráfego para vários serviços num cluster do Kubernetes. Todas as funcionalidades acima são fornecidas pelo Azure [Gateway de aplicação](/azure/Application-Gateway/), que faz com que seja um controlador de entrada ideal para Kubernetes no Azure. 

Neste tutorial, irá aprender a realizar as seguintes tarefas na criação de um [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) cluster com o AKS com o Gateway de aplicação como controlador de entrada:

> [!div class="checklist"]
> * Utilizar o HCL (HashiCorp Language) para definir um cluster do Kubernetes
> * Utilize o Terraform para criar o recurso de Gateway de aplicação
> * Utilizar o Terraform e o AKS para criar um cluster do Kubernetes
> * Utilizar a ferramenta kubectl para testar a disponibilidade de um cluster do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configurar o Terraform**: Siga as instruções no artigo, [Terraform e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Principal de serviço do Azure**: Siga as instruções na secção do **criar o principal de serviço** secção no artigo [criar um Azure principal de serviço com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Tome nota dos valores para o appId, displayName e palavra-passe.
  - Tenha em atenção o ID de objeto do Principal de serviço, executando o seguinte comando

    ```bash
    az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios
O primeiro passo é criar o diretório que mantenha os seus ficheiros de configuração do Terraform para o exercício.

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se ainda não tiver selecionado um ambiente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Mude para o diretório novo:

    ```bash
    cd terraform-aks-appgw-ingress
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
        version = "~>1.18"
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
   ## <a name="define-input-variables"></a>Definir variáveis de entrada
   Criar o arquivo de configuração do Terraform que apresenta uma lista de todas as variáveis necessárias para esta implementação
1. No Cloud Shell, crie um ficheiro denominado `variables.tf`
    ```bash
    vi variables.tf
    ```
1. Prima a tecla I para entrar no modo de inserção.

2. Cole o seguinte código no editor:
    
    ```JSON
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>Definir os recursos 
Crie ficheiro de configuração do Terraform que cria todos os recursos. 

1. No Cloud Shell, crie um ficheiro com o nome `resources.tf`.

    ```bash
    vi resources.tf
    ```

1. Prima a tecla I para entrar no modo de inserção.

1. Cole os seguintes blocos de código no editor:

    a. Criar um bloco de locais para variáveis calculadas reutilizar

    ```JSON
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```
    b. Criar uma origem de dados para o grupo de recursos, nova identidade de utilizador
    ```JSON
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```
    c. Criar recursos de rede bases
   ```JSON
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```
    d. Criar recurso do Gateway de aplicação
    ```JSON
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```
    e. Criar atribuições de funções
    ```JSON
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```
    f. Criar o cluster do Kubernetes
    ```JSON
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
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
O Terraform monitoriza o estado localmente através do ficheiro `terraform.tfstate`. Este padrão funciona bem num ambiente individual. No entanto, num ambiente de várias pessoa mais prático, precisa controlar o estado no servidor com [armazenamento do Azure](/azure/storage/). Nesta secção, vai obter as informações necessárias da conta de armazenamento (nome e chave da conta) e criar um contentor de armazenamento no qual serão armazenadas as informações de estado do Terraform.

1. No portal do Azure, selecione **Todos os serviços** no menu à esquerda.

1. Selecione **Contas de armazenamento**.

1. No separador **Contas de armazenamento**, selecione o nome da conta de armazenamento na qual o Terraform deve armazenar o estado. Por exemplo, pode utilizar a conta de armazenamento criada quando abriu o Cloud Shell pela primeira vez.  O nome de conta de armazenamento criado pelo Cloud Shell costuma começar por `cs`, seguido de uma cadeia aleatória de números e letras. **Tome nota do nome da conta de armazenamento que selecionar, porque temos-lo mais tarde.**

1. No separador de conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu de conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Anote o valor **key1** **key**. (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

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

    ![Exemplo de resultados "terraform init"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Crie um ficheiro de variáveis para fornecer valores de entrada no Cloud Shell, crie um ficheiro denominado `main.tf`.

    ```bash
    vi terraform.tfvars
    ```

1. Prima a tecla I para entrar no modo de inserção.

1. Cole as seguintes variáveis que criou anteriormente no editor:

    ```JSON
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Saia do modo de inserção ao clicar na tecla **Esc**.

1. Guarde o ficheiro e saia do editor vi ao inserir o seguinte comando:

    ```bash
    :wq
    ```

1. Execute o comando `terraform plan` para criar o plano do Terraform que define os elementos de infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` mostra os recursos que serão criados quando executar o comando `terraform apply`:

    ![Exemplo de resultados "terraform plan"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster do Kubernetes pode demorar vários minutos, o que faz com que a sessão do Cloud Shell exceda o tempo limite. Se a sessão do Cloud Shell exceder o tempo limite, pode seguir os passos na secção "Recuperar de um tempo limite do Cloud Shell" para que possa concluir o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` mostra os resultados de criar os recursos definidos nos seus ficheiros de configuração:

    ![Exemplo de resultados "terraform apply"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. No portal do Azure, selecione **grupos de recursos** no menu da esquerda para ver os recursos criados para o novo cluster de Kubernetes no grupo de recursos selecionado.

    ![Comando do Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um tempo limite do Cloud Shell
Se a sessão do Cloud Shell exceder o tempo limite, pode utilizar os seguintes passos para recuperar:

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

    ![A ferramenta kubectl permite-lhe verificar o estado de funcionamento do seu cluster do Kubernetes](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o Terraform e AKS para criar o cluster do Kubernetes. Aqui estão alguns recursos adicionais para o ajudar a saber mais acerca do Terraform no Azure.
 
 > [!div class="nextstepaction"] 
 > [Hub do Terraform no Microsoft.com](https://docs.microsoft.com/azure/terraform/)
 
