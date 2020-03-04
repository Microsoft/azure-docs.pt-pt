---
title: Tutorial - Criar um controlador de ingresso de gateway de aplicação no Serviço Azure Kubernetes
description: Tutorial ilustrando como criar um Cluster Kubernetes com serviço Azure Kubernetes com Application Gateway como controlador de ingresso
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 14b8f6ba74a06c126da239671cbb2053df19af7d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251766"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Tutorial: Criar um controlador de ingresso de gateway de aplicação no Serviço Azure Kubernetes

[O Serviço Azure Kubernetes (AKS)](/azure/aks/) gere o seu ambiente kubernetes hospedado. A AKS torna-o rápido e fácil de implantar e gerir aplicações contentorizadas sem conhecimentos de orquestração de contentores. A AKS também elimina o fardo de desativar as aplicações para tarefas operacionais e de manutenção. Utilizando o AKS, estas tarefas - incluindo o fornecimento, a atualização e a escala de recursos - podem ser cumpridas a pedido.

Um controlador de ingresso fornece várias funcionalidades para os serviços kubernetes. Estas funcionalidades incluem proxy invertido, encaminhamento de tráfego configurável e rescisão de TLS. Os recursos de ingresso kubernetes são usados para configurar as regras de ingresso para serviços kubernetes individuais. Utilizando um controlador de ingresso e regras de ingresso, um único endereço IP pode encaminhar o tráfego para vários serviços num cluster Kubernetes. Toda esta funcionalidade é fornecida pelo Azure [Application Gateway,](/azure/Application-Gateway/)tornando-se um controlador de Ingress ideal para kubernetes no Azure. 

Neste tutorial, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Crie um cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) usando AKS com Application Gateway como Controlador de Ingress.
> * Utilize o HCL (HashiCorp Language) para definir um cluster Kubernetes.
> * Utilize a Terraform para criar recurso Application Gateway.
> * Use Terraform e AKS para criar um cluster Kubernetes.
> * Utilize a ferramenta kubectl para testar a disponibilidade de um cluster Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configurar o Terraform**: siga as instruções no artigo [Terraform and configure access to Azure](terraform-install-configure.md) (Terraform e configuração do acesso ao Azure)

- Grupo de **recursos Azure**: Se não tiver um grupo de recursos Azure para usar para a demonstração, [crie um grupo](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups)de recursos Azure . Tome nota do nome e localização do grupo de recursos, uma vez que estes valores são utilizados na demonstração.

- **Principal de serviço do Azure**: siga as instruções na secção **Criar o principal de serviço** no artigo [Criar um principal de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Tome nota dos valores para o appId, displayName e password.

- **Obtenha o ID principal do objeto de serviço**: Executar o seguinte comando na Cloud Shell: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

O primeiro passo é criar o diretório que mantenha os seus ficheiros de configuração do Terraform para o exercício.

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `terraform-aks-appgw-ingress`.

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
    code main.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Guarde o ficheiro **(&lt;Ctrl>S)** e saia do editor **(&lt;Ctrl>Q).**

## <a name="define-input-variables"></a>Definir variáveis de entrada

Crie o ficheiro de configuração Terraform que lista todas as variáveis necessárias para esta implementação.

1. No Cloud Shell, crie um ficheiro com o nome `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o seguinte código no editor:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
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
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
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

1. Guarde o ficheiro **(&lt;Ctrl>S)** e saia do editor **(&lt;Ctrl>Q).**

## <a name="define-the-resources"></a>Definir os recursos 
Crie o ficheiro de configuração Terraform que cria todos os recursos. 

1. No Cloud Shell, crie um ficheiro com o nome `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Colar o seguinte bloco de código para criar um bloco local para variáveis computadas para reutilizar:

    ```hcl
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

1. Colar o seguinte bloco de código para criar uma fonte de dados para o grupo Derecursos, nova identidade do Utilizador:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Colar o seguinte bloco de código para criar recursos de rede base:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Colar o seguinte bloco de código para criar recurso Application Gateway:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Colar o seguinte bloco de código para criar atribuições de funções:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Colar o seguinte bloco de código para criar o cluster Kubernetes:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      agent_pool_profile {
        name            = "agentpool"
        count           = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_type         = "Linux"
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Guarde o ficheiro **(&lt;Ctrl>S)** e saia do editor **(&lt;Ctrl>Q).**

O código apresentado nesta secção define o nome do cluster, localização e resource_group_name. O valor `dns_prefix` - que faz parte do nome de domínio totalmente qualificado (FQDN) utilizado para aceder ao cluster - é definido.

O registo `linux_profile` permite configurar as definições que permitem a sessão nos nódosos do trabalhador utilizando o SSH.

No AKS, paga apenas os nós de trabalho. O registo `agent_pool_profile` configura os detalhes destes nódosos operários. O `agent_pool_profile record` inclui o número de nós dos trabalhadores para criar e o tipo de nós dos trabalhadores. Se precisar de escalar ou escalar o cluster no futuro, modifica o valor `count` neste registo.

## <a name="create-a-terraform-output-file"></a>Criar um ficheiro de saída do Terraform

As [saídas terraformes](https://www.terraform.io/docs/configuration/outputs.html) permitem definir valores que são realçados ao utilizador quando a Terraform aplica um plano, e podem ser consultadas usando o comando `terraform output`. Nesta secção, vai criar um ficheiro de saída que permite o acesso ao cluster com [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. No Cloud Shell, crie um ficheiro com o nome `output.tf`.

    ```bash
    code output.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Guarde o ficheiro **(&lt;Ctrl>S)** e saia do editor **(&lt;Ctrl>Q).**

## <a name="configure-azure-storage-to-store-terraform-state"></a>Configure armazenamento Azure para armazenar estado terraforme

O Terraform monitoriza o estado localmente através do ficheiro `terraform.tfstate`. Este padrão funciona bem num ambiente individual. No entanto, num ambiente multipessoal mais prático, é necessário rastrear o estado no servidor utilizando o [armazenamento Azure](/azure/storage/). Nesta secção, aprende-se a recuperar as informações necessárias da conta de armazenamento e a criar um recipiente de armazenamento. As informações do estado terraforme são então armazenadas naquele contentor.

1. No portal Azure, sob **os serviços Azure,** selecione contas de **Armazenamento.** (Se a opção **de contas de armazenamento** não estiver visível na página principal, selecione Mais **serviços** e, em seguida, localize e selecione.)

1. Na página de **contas de Armazenamento,** selecione o nome da conta de armazenamento na qual a Terraform deve armazenar o estado. Por exemplo, pode utilizar a conta de armazenamento criada quando abriu o Cloud Shell pela primeira vez.  O nome de conta de armazenamento criado pelo Cloud Shell costuma começar por `cs`, seguido de uma cadeia aleatória de números e letras. 

    Tome nota da conta de armazenamento que selecionar, pois precisa dela mais tarde.

1. Na página da conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu de conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Tome nota do **valor-chave1.** (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Na Cloud Shell, crie um recipiente na sua conta de armazenamento Azure. Substitua os espaços reservados pelos valores apropriados para a sua conta de armazenamento Azure.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Criar o cluster do Kubernetes
Nesta secção, pode ver como utilizar o comando `terraform init` para criar os recursos definidos nos ficheiros de configuração que criou nas secções anteriores.

1. Em Cloud Shell, inicialize terraforma. Substitua os espaços reservados pelos valores apropriados para a sua conta de armazenamento Azure.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    O comando `terraform init` mostra o sucesso de inicializar o backend e o plug-in do fornecedor:

    ![Exemplo de resultados "terraform init"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Na Cloud Shell, crie um ficheiro chamado `terraform.tfvars`:

    ```bash
    code terraform.tfvars
    ```

1. Colar as seguintes variáveis criadas anteriormente no editor. Para obter o valor de localização para o seu ambiente, use `az account list-locations`.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Guarde o ficheiro **(&lt;Ctrl>S)** e saia do editor **(&lt;Ctrl>Q).**

1. Execute o comando `terraform plan` para criar o plano do Terraform que define os elementos de infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` exibe os recursos que são criados quando executa o comando `terraform apply`:

    ![Exemplo de resultados "terraform plan"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster Kubernetes pode demorar vários minutos, resultando na sessão cloud Shell. Se a sessão cloud Shell passar, pode seguir os passos na secção "Recuperar de um intervalo de tempo da Cloud Shell" para lhe permitir completar o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` mostra os resultados de criar os recursos definidos nos seus ficheiros de configuração:

    ![Exemplo de resultados "terraform apply"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. No portal Azure, selecione **Grupos de Recursos** no menu esquerdo para ver os recursos criados para o seu novo cluster Kubernetes no grupo de recursos selecionados.

    ![Comandos do Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um tempo limite do Cloud Shell

Se a sessão Cloud Shell se esgotar, pode utilizar os seguintes passos para recuperar:

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

## <a name="install-azure-ad-pod-identity"></a>Instale identidade de pod azure

A Identidade de Pod de Diretório Ativo Azure proporciona acesso baseado em token ao Gestor de [Recursos Azure.](/azure/azure-resource-manager/resource-group-overview)

Identidade de [Pod Azure AD](https://github.com/Azure/aad-pod-identity) adiciona os seguintes componentes ao seu cluster Kubernetes:

  - Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - [Componente do Controlador de Identidade Gerido (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Componente de Identidade Gerida do [Nó (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Se o RBAC estiver **ativado,** execute o seguinte comando para instalar a Identidade do Pod Azure AD no seu cluster:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Se o RBAC estiver **desativado,** execute o seguinte comando para instalar a Identidade do Pod Azure AD no seu cluster:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Instalar leme

O código desta secção utiliza [o Helm](/azure/aks/kubernetes-helm) - gestor de pacotes Kubernetes - para instalar o pacote `application-gateway-kubernetes-ingress`:

1. Se o RBAC estiver **ativado,** execute o seguinte conjunto de comandos para instalar e configurar o Leme:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Se o RBAC estiver **desativado,** execute o seguinte comando para instalar e configurar o Leme:

    ```bash
    helm init
    ```

1. Adicione o repositório AGIC Helm:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Instale gráfico de leme do controlador de entrada

1. Descarregue `helm-config.yaml` para configurar a AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Editar a `helm-config.yaml` e introduzir valores adequados para as secções `appgw` e `armAuth`.

    ```bash
    code helm-config.yaml
    ```

    Os valores são descritos da seguinte forma:

    - `verbosityLevel`: Define o nível de verbosidade da infraestrutura de exploração madeireira AGIC. Consulte os níveis de exploração de registo para obter possíveis [valores.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)
    - `appgw.subscriptionId`: O ID de subscrição azure para o Gateway da aplicação. Exemplo: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: Nome do Grupo de Recursos Azure no qual a App Gateway foi criada. 
    - `appgw.name`: Nome do Gateway da Aplicação. Exemplo: `applicationgateway1`.
    - `appgw.shared`: Esta bandeira booleana deve ser desempregada para `false`. Adere a `true` caso necessite de um Gateway de [Aplicações Partilhadas](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: Especifique o espaço de nome, que a AGIC deve observar. O espaço de nome pode ser um único valor de cadeia, ou uma lista separada de espaços de nomes. Deixando esta variável comentada, ou definindo-a em branco ou em resultados de cordas vazias em Controlador De Ingress observando todos os espaços de nome acessíveis.
    - `armAuth.type`: Um valor de `aadPodIdentity` ou `servicePrincipal`.
    - `armAuth.identityResourceID`: Identificação de recursos da identidade gerida.
    - `armAuth.identityClientId`: A Identificação do Cliente da Identidade.
    - `armAuth.secretJSON`: Só é necessário quando for escolhido o tipo principal de serviço secreto (quando `armAuth.type` tiver sido definido para `servicePrincipal`).

    Notas-chave:
    - O valor `identityResourceID` é criado no roteiro terraforme e pode ser encontrado executando: `echo "$(terraform output identity_resource_id)"`.
    - O valor `identityClientID` é criado no roteiro terraforme e pode ser encontrado executando: `echo "$(terraform output identity_client_id)"`.
    - O valor `<resource-group>` é o grupo de recursos da sua App Gateway.
    - O valor `<identity-name>` é o nome da identidade criada.
    - Todas as identidades para uma determinada subscrição podem ser listadas usando: `az identity list`.

1. Instale o pacote de controlador de entrada de entrada de gateway de aplicação:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Instalar uma aplicação de amostra

Assim que tiver instalado o Portal da Aplicação, AKS e AGIC, pode instalar uma aplicação de amostra através da [Azure Cloud Shell:](https://shell.azure.com/)

1. Utilize o comando de caracóis para descarregar o ficheiro YAML:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Aplicar o ficheiro YAML:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados neste artigo.  

Substitua o espaço reservado pelo valor adequado. Todos os recursos dentro do grupo de recursos especificado serão eliminados.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Controlador de entrada de gateway de aplicação](https://azure.github.io/application-gateway-kubernetes-ingress/)
