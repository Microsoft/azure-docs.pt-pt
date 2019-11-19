---
title: Tutorial-criar um controlador de entrada do gateway de aplicativo no serviço kubernetes do Azure
description: Tutorial ilustrando como criar um cluster kubernetes com o serviço kubernetes do Azure com o gateway de aplicativo como controlador de entrada
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 6d07fc6becf76453de792c69b25aea49c39775ae
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159092"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Tutorial: criar um controlador de entrada do gateway de aplicativo no serviço kubernetes do Azure

O [AKs (serviço kubernetes do Azure)](/azure/aks/) gerencia seu ambiente kubernetes hospedado. O AKS torna rápido e fácil implantar e gerenciar aplicativos em contêineres sem conhecimento de orquestração de contêiner. O AKS também elimina a carga de colocar aplicativos offline para tarefas operacionais e de manutenção. Usando o AKS, essas tarefas, incluindo provisionamento, atualização e dimensionamento de recursos, podem ser realizadas sob demanda.

Um controlador de entrada fornece vários recursos para serviços Kubernetess. Esses recursos incluem proxy reverso, roteamento de tráfego configurável e terminação de TLS. Os recursos de entrada do kubernetes são usados para configurar as regras de entrada para serviços individuais do kubernetes. Usando um controlador de entrada e regras de entrada, um único endereço IP pode rotear o tráfego para vários serviços em um cluster kubernetes. Toda essa funcionalidade é fornecida pelo [Gateway de aplicativo](/azure/Application-Gateway/)do Azure, tornando-a um controlador de entrada ideal para kubernetes no Azure. 

Neste tutorial, você aprenderá a executar as seguintes tarefas:

> [!div class="checklist"]
> * Crie um cluster [kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) usando o AKs com o gateway de aplicativo como controlador de entrada.
> * Use a HCL (linguagem HashiCorp) para definir um cluster kubernetes.
> * Use Terraform para criar o recurso de gateway de aplicativo.
> * Use Terraform e AKS para criar um cluster kubernetes.
> * Use a ferramenta kubectl para testar a disponibilidade de um cluster kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configurar o Terraform**: Siga as instruções no artigo [Terraform e configuração do acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Grupo de recursos do Azure**: se você não tiver um grupo de recursos do Azure para usar para a demonstração, [crie um grupo de recursos do Azure](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Anote o nome e o local do grupo de recursos, pois esses valores são usados na demonstração.

- **Principal de serviço do Azure**: siga as instruções na secção **Criar o principal de serviço** no artigo [Criar um principal de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Anote os valores para appId, displayName e password.

- **Obter a ID de objeto da entidade de serviço**: execute o seguinte comando no Cloud Shell: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

O primeiro passo é criar o diretório que mantenha os seus ficheiros de configuração do Terraform para o exercício.

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório denominado `terraform-aks-k8s`.

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

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

## <a name="define-input-variables"></a>Definir variáveis de entrada

Crie o arquivo de configuração Terraform que lista todas as variáveis necessárias para essa implantação.

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

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

## <a name="define-the-resources"></a>Definir os recursos 
Crie um arquivo de configuração Terraform que cria todos os recursos. 

1. No Cloud Shell, crie um ficheiro com o nome `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Cole o bloco de código a seguir para criar um bloco de locais para as variáveis computadas a serem reutilizadas:

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

1. Cole o bloco de código a seguir para criar uma fonte de dados para o grupo de recursos, nova identidade de usuário:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Cole o seguinte bloco de código para criar recursos de rede base:

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

1. Cole o seguinte bloco de código para criar o recurso de gateway de aplicativo:

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

1. Cole o seguinte bloco de código para criar atribuições de função:

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

1. Cole o bloco de código a seguir para criar o cluster kubernetes:

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

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

O código apresentado nesta seção define o nome do cluster, o local e o resource_group_name. O valor `dns_prefix`-que faz parte do FQDN (nome de domínio totalmente qualificado) usado para acessar o cluster – está definido.

O registro de `linux_profile` permite que você defina as configurações que permitem a entrada nos nós de trabalho usando o SSH.

No AKS, paga apenas os nós de trabalho. O registro de `agent_pool_profile` configura os detalhes para esses nós de trabalho. O `agent_pool_profile record` inclui o número de nós de trabalho a serem criados e o tipo de nós de trabalho. Se você precisar escalar ou reduzir verticalmente o cluster no futuro, modifique o valor `count` nesse registro.

## <a name="create-a-terraform-output-file"></a>Criar um ficheiro de saída do Terraform

As [saídas do Terraform](https://www.terraform.io/docs/configuration/outputs.html) permitem que você defina valores que são realçados para o usuário quando o Terraform aplica um plano e pode ser consultado usando o comando `terraform output`. Nesta secção, vai criar um ficheiro de saída que permite o acesso ao cluster com [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

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

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Configurar o armazenamento do Azure para armazenar o estado do Terraform

O Terraform monitoriza o estado localmente através do ficheiro `terraform.tfstate`. Este padrão funciona bem num ambiente individual. No entanto, em um ambiente de várias pessoas mais prática, você precisa controlar o estado no servidor usando o [armazenamento do Azure](/azure/storage/). Nesta seção, você aprenderá a recuperar as informações de conta de armazenamento necessárias e criar um contêiner de armazenamento. As informações de estado Terraform são armazenadas nesse contêiner.

1. No portal do Azure, em **Serviços do Azure**, selecione **contas de armazenamento**. (Se a opção **contas de armazenamento** não estiver visível na página principal, selecione **mais serviços** e localize e selecione-o.)

1. Na página **contas de armazenamento** , selecione o nome da conta de armazenamento na qual o Terraform deve armazenar o estado. Por exemplo, pode utilizar a conta de armazenamento criada quando abriu o Cloud Shell pela primeira vez.  O nome de conta de armazenamento criado pelo Cloud Shell costuma começar por `cs`, seguido de uma cadeia aleatória de números e letras. 

    Anote a conta de armazenamento que você selecionar, pois você precisará dela mais tarde.

1. Na página da conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu de conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Anote o valor **key1** **key**. (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Em Cloud Shell, crie um contêiner em sua conta de armazenamento do Azure. Substitua os espaços reservados pelos valores apropriados para sua conta de armazenamento do Azure.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Criar o cluster do Kubernetes
Nesta secção, pode ver como utilizar o comando `terraform init` para criar os recursos definidos nos ficheiros de configuração que criou nas secções anteriores.

1. Em Cloud Shell, inicialize Terraform. Substitua os espaços reservados pelos valores apropriados para sua conta de armazenamento do Azure.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    O comando `terraform init` exibe o sucesso da inicialização do plug-in de back-end e do provedor:

    ![Exemplo de resultados "terraform init"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Em Cloud Shell, crie um arquivo chamado `terraform.tfvars`:

    ```bash
    code terraform.tfvars
    ```

1. Cole as seguintes variáveis criadas anteriormente no editor. Para obter o valor de local para o seu ambiente, use `az account list-locations`.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

1. Execute o comando `terraform plan` para criar o plano do Terraform que define os elementos de infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` exibe os recursos que são criados quando você executa o comando `terraform apply`:

    ![Exemplo de resultados "terraform plan"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster kubernetes pode levar vários minutos, resultando na Cloud Shell tempo limite da sessão. Se a sessão de Cloud Shell atingir o tempo limite, você poderá seguir as etapas na seção "recuperar de um tempo limite de Cloud Shell" para permitir que você conclua o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` mostra os resultados de criar os recursos definidos nos seus ficheiros de configuração:

    ![Exemplo de resultados "terraform apply"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. No portal do Azure, selecione **grupos de recursos** no menu à esquerda para ver os recursos criados para o novo cluster kubernetes no grupo de recursos selecionado.

    ![Comandos do Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um tempo limite do Cloud Shell

Se a sessão de Cloud Shell atingir o tempo limite, você poderá usar as seguintes etapas para recuperar:

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

## <a name="install-azure-ad-pod-identity"></a>Instalar a identidade do Pod do Azure AD

Azure Active Directory identidade Pod fornece acesso baseado em token ao [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

A [identidade do Pod do Azure ad](https://github.com/Azure/aad-pod-identity) adiciona os seguintes componentes ao cluster kubernetes:

  - Kubernetes [crds](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
  - Componente [do controlador de identidade gerenciada (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Componente [de identidade gerenciada de nó (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Se o RBAC estiver **habilitado**, execute o seguinte comando para instalar a identidade do Pod do Azure AD em seu cluster:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Se o RBAC estiver **desabilitado**, execute o seguinte comando para instalar a identidade do Pod do Azure AD em seu cluster:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Instalar o Helm

O código nesta seção usa o Gerenciador de pacotes [Helm](/azure/aks/kubernetes-helm) -kubernetes-para instalar o pacote de `application-gateway-kubernetes-ingress`:

1. Se o RBAC estiver **habilitado**, execute o seguinte conjunto de comandos para instalar e configurar o Helm:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Se o RBAC estiver **desabilitado**, execute o seguinte comando para instalar e configurar o Helm:

    ```bash
    helm init
    ```

1. Adicione o repositório AGIC Helm:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Instalar o gráfico do controlador de entrada Helm

1. Baixar `helm-config.yaml` para configurar o AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Edite o `helm-config.yaml` e insira os valores apropriados para `appgw` e `armAuth` seções.

    ```bash
    code helm-config.yaml
    ```

    Os valores são descritos da seguinte maneira:

    - `verbosityLevel`: define o nível de detalhamento da infraestrutura de log AGIC. Consulte [níveis de log](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) para obter os valores possíveis.
    - `appgw.subscriptionId`: a ID de assinatura do Azure para o gateway de aplicativo. Exemplo: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: o nome do grupo de recursos do Azure no qual o gateway de aplicativo foi criado. 
    - `appgw.name`: nome do gateway de aplicativo. Exemplo: `applicationgateway1`.
    - `appgw.shared`: esse sinalizador booliano deve ser padronizado para `false`. Defina como `true` caso você precise de um [Gateway de aplicativo compartilhado](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: especifique o espaço de nome, que o AGIC deve observar. O namespace pode ser um único valor de cadeia de caracteres ou uma lista separada por vírgulas de namespaces. Deixar essa variável comentada ou defini-la como um resultado de cadeia de caracteres vazia ou vazio faz com que o controlador de entrada Observe todos os namespaces acessíveis.
    - `armAuth.type`: um valor de `aadPodIdentity` ou `servicePrincipal`.
    - `armAuth.identityResourceID`: ID de recurso da identidade gerenciada.
    - `armAuth.identityClientId`: a ID do cliente da identidade.
    - `armAuth.secretJSON`: necessário somente quando o tipo de segredo da entidade de serviço é escolhido (quando `armAuth.type` foi definido como `servicePrincipal`).

    Observações-chave:
    - O valor de `identityResourceID` é criado no script Terraform e pode ser encontrado executando: `echo "$(terraform output identity_client_id)"`.
    - O valor de `identityClientID` é criado no script Terraform e pode ser encontrado executando: `echo "$(terraform output identity_resource_id)"`.
    - O valor de `<resource-group>` é o grupo de recursos do seu gateway de aplicativo.
    - O valor de `<identity-name>` é o nome da identidade criada.
    - Todas as identidades de uma determinada assinatura podem ser listadas usando: `az identity list`.

1. Instale o pacote do controlador de entrada do gateway de aplicativo:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Instalar um aplicativo de exemplo

Depois de ter o gateway de aplicativo, o AKS e o AGIC instalados, você pode instalar um aplicativo de exemplo por meio do [Azure cloud Shell](https://shell.azure.com/):

1. Use o comando de ondulação para baixar o arquivo YAML:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Aplique o arquivo YAML:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo.  

Substitua o espaço reservado pelo valor apropriado. Todos os recursos dentro do grupo de recursos especificado serão excluídos.

```bash
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Controlador de entrada do gateway de aplicativo](https://azure.github.io/application-gateway-kubernetes-ingress/)