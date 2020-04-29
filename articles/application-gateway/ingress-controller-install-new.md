---
title: Criar um controlador de ingresso com um novo Gateway de Aplicação
description: Este artigo fornece informações sobre como implementar um Controlador de Ingress ingresso de Gateway de aplicação com um novo Gateway application.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b46c9f8b0cad74f3a4e9be8903270a60993c01f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585889"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Como instalar um controlador de entrada de gateway de aplicação (AGIC) usando um novo gateway de aplicação

As instruções abaixo assumem que o Controlador de Ingresso de Gateway (AGIC) será instalado num ambiente sem componentes pré-existentes.

## <a name="required-command-line-tools"></a>Ferramentas de linha de comando necessárias

Recomendamos a utilização da [Azure Cloud Shell](https://shell.azure.com/) para todas as operações da linha de comando abaixo. Lance a sua concha a partir de shell.azure.com ou clicando no link:

[![Lançamento emcamado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Em alternativa, lance cloud Shell a partir do portal Azure utilizando o seguinte ícone:

![Lançamento do portal](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

A sua [Nuvem Azure](https://shell.azure.com/) Shell já tem todas as ferramentas necessárias. Caso opte por utilizar outro ambiente, certifique-se de que as seguintes ferramentas de linha de comando estão instaladas:

* `az`- Azure CLI: [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Ferramenta de linha de comando Kubernetes: instruções de [instalação](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Gestor de pacotes Kubernetes: [instruções de instalação](https://github.com/helm/helm/releases/latest)
* `jq`- processador JSON de linha de comando: [instruções de instalação](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Criar uma Identidade

Siga os passos abaixo para criar um objeto [principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)de serviço de diretório Ativo Azure (AAD). Por favor, `password`grave `objectId` os `appId`valores e valores que serão utilizados nos seguintes passos.

1. Criar o diretor de serviço aD[(Ler mais sobre rBAC):](https://docs.microsoft.com/azure/role-based-access-control/overview)
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Os `appId` `password` valores e valores da saída JSON serão utilizados nos seguintes passos


1. Utilize `appId` a saída do comando anterior `objectId` para obter o novo diretor de serviço:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    A saída deste `objectId`comando é , que será usada no modelo de Gestor de Recursos Azure abaixo

1. Crie o ficheiro de parâmetro que será utilizado na implantação do modelo do Gestor de Recursos Do Azure mais tarde.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Para implantar um cluster ativado `aksEnabledRBAC` **rBAC,** definir o campo para`true`

## <a name="deploy-components"></a>Componentes de implantação
Este passo irá adicionar os seguintes componentes à sua subscrição:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- Gateway de [aplicação](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) com 2 [subredes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), que será usada pela [Identidade AAD Pod](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Descarregue o modelo do Gestor de Recursos Azure e modifique o modelo conforme necessário.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Implemente o modelo de `az cli`Gestor de Recursos Azure utilizando . Isto pode demorar até 5 minutos.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Uma vez terminada a implementação, `deployment-outputs.json`descarregue a saída de implementação num ficheiro chamado .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Configurar o controlador de ingresso de gateway de aplicação

Com as instruções na secção anterior, criámos e configuramos um novo cluster AKS e um Gateway de Aplicação. Estamos agora prontos para implementar uma aplicação de amostra e um controlador de ingresso para a nossa nova infraestrutura Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Configurar credenciais kubernetes
Para os seguintes passos, precisamos de um comando [kubectl](https://kubectl.docs.kubernetes.io/) de configuração, que usaremos para ligar ao nosso novo cluster Kubernetes. [Cloud](https://shell.azure.com/) Shell `kubectl` já instalou. Usaremos `az` cli para obter credenciais para Kubernetes.

Obtenha credenciais para o seu AKS recém-implantado[(leia mais):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Instalar identidade de pod aad
  A Identidade de Pod de Diretório Ativo Azure fornece acesso baseado em token ao Gestor de [Recursos Azure (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

  [A Identidade de Pod AAD](https://github.com/Azure/aad-pod-identity) adicionará os seguintes componentes ao seu cluster Kubernetes:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
   * [Componente do Controlador de Identidade Gerido (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Componente de Identidade Gerida do [Nó (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Para instalar a Identidade do Pod AAD no seu cluster:

   - *RBAC habilitado* Aglomerado AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC desativado* Aglomerado AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Instalar leme
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) é um gestor de pacotes para kubernetes. Vamos aproveitá-lo `application-gateway-kubernetes-ingress` para instalar o pacote:

1. Instale [o Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) e `application-gateway-kubernetes-ingress` execute o seguinte para adicionar o pacote de leme:

    - *RBAC habilitado* Aglomerado AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC desativado* Aglomerado AKS

        ```bash
        helm init
        ```

1. Adicione o repositório AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Instale gráfico de leme do controlador de entrada

1. Utilize `deployment-outputs.json` o ficheiro acima criado e crie as seguintes variáveis.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Baixe helm-config.yaml, que configurará AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Ou copiar o ficheiro YAML abaixo: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Editar o helm-config.yaml recentemente descarregado e `appgw` `armAuth`preencher as secções e .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Valores:
     - `verbosityLevel`: Define o nível de verbosidade da infraestrutura de exploração madeireira AGIC. Consulte os níveis de exploração de registo para obter possíveis [valores.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)
     - `appgw.subscriptionId`: O ID de subscrição Azure no qual reside o Gateway da Aplicação. Exemplo: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Nome do Grupo de Recursos Azure no qual foi criado o Gateway da Aplicação. Exemplo: `app-gw-resource-group`
     - `appgw.name`: Nome do Gateway da Aplicação. Exemplo: `applicationgatewayd0f0`
     - `appgw.shared`: Esta bandeira booleana `false`deve ser desempregada para . Preparar-se para `true` se precisar de um Gateway de [Aplicação Partilhada](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Especifique o espaço de nome, que a AGIC deve observar. Isto pode ser um único valor de cadeia, ou uma lista separada de espaços de nomes.
    - `armAuth.type`: pode `aadPodIdentity` ser ou`servicePrincipal`
    - `armAuth.identityResourceID`: Identificação de recursos da Identidade Gerida azure
    - `armAuth.identityClientId`: A identificação do cliente da identidade. Veja abaixo mais informações sobre identidade
    - `armAuth.secretJSON`: Só é necessário quando o `armAuth.type` tipo principal `servicePrincipal`de serviço secreto é escolhido (quando tiver sido definido ) 


   > [!NOTE]
   > Os `identityResourceID` `identityClientID` valores e são criados durante os passos dos [Componentes de Implantação,](ingress-controller-install-new.md#deploy-components) e que poderiam ser obtidos novamente utilizando o seguinte comando:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`no comando acima está o grupo de recursos do seu Gateway de Aplicação. `<identity-name>`é o nome da identidade criada. Todas as identidades para uma determinada subscrição podem ser listadas usando:`az identity list`


1. Instale o pacote de controlador de entrada de entrada de gateway de aplicação:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instalar uma app de amostras
Agora que temos application Gateway, AKS e AGIC instalados podemos instalar uma aplicação de amostra através da [Azure Cloud Shell:](https://shell.azure.com/)

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Alternativamente pode:

* Faça o download do ficheiro YAML acima:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Aplicar o ficheiro YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Outros Exemplos
Este [guia de como orientar](ingress-controller-expose-service-over-http-https.md) contém mais exemplos sobre como expor um serviço AKS via HTTP ou HTTPS, à Internet com Application Gateway.
