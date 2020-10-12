---
title: Criar um controlador de entrada com um novo Gateway de Aplicações
description: Este artigo fornece informações sobre como implementar um Controlador de Entrada de Gateway de Aplicação com um novo Gateway de aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: cbebf430bf44ccdee51bf44b11b8b01f23544dcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807149"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Como instalar um controlador de entrada de gateway de aplicação (AGIC) usando um novo gateway de aplicações

As instruções abaixo assumem que o Controlador de Entrada de Gateway de Aplicação (AGIC) será instalado num ambiente sem componentes pré-existentes.

## <a name="required-command-line-tools"></a>Ferramentas de linha de comando necessárias

Recomendamos a utilização de [Azure Cloud Shell](https://shell.azure.com/) para todas as operações de linha de comando abaixo. Lance a sua concha a partir de shell.azure.com ou clicando no link:

[![Lançamento incorporado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Alternativamente, lançar Cloud Shell a partir do portal Azure utilizando o seguinte ícone:

![Lançamento do Portal](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

O seu [Azure Cloud Shell](https://shell.azure.com/) já tem todas as ferramentas necessárias. Se optar por utilizar outro ambiente, certifique-se de que estão instaladas as seguintes ferramentas de linha de comando:

* `az` - Azure CLI: [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` - Ferramenta de linha de comando Kubernetes: instruções de [instalação](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` - Gestor de pacotes Kubernetes: [instruções de instalação](https://github.com/helm/helm/releases/latest)
* `jq` - processador JSON de linha de comando: instruções de [instalação](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Criar uma Identidade

Siga os passos abaixo para criar um [objeto principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)de serviço Azure Ative (AAD). Por favor, grave os `appId` `password` , e `objectId` valores - estes serão usados nos seguintes passos.

1. Criar diretor de serviço de AD[(Ler mais sobre o RBAC):](https://docs.microsoft.com/azure/role-based-access-control/overview)
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    Os `appId` `password` valores e valores da saída JSON serão utilizados nos seguintes passos


1. Utilize `appId` a saída do comando anterior para obter `objectId` o novo diretor de serviço:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    A saída deste comando é `objectId` , que será usada no modelo Azure Resource Manager abaixo

1. Crie o ficheiro de parâmetros que será usado na implementação do modelo do Gestor de Recursos Azure mais tarde.
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
    Para implementar um cluster ativado pela **RBAC,** desloque o `aksEnableRBAC` campo para `true`

## <a name="deploy-components"></a>Componentes de implantação
Este passo irá adicionar os seguintes componentes à sua subscrição:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) com 2 [sub-redes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), que será usada pela [Identidade do Pod AAD](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Descarregue o modelo do Gestor de Recursos Azure e modifique o modelo conforme necessário.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Implemente o modelo do Gestor de Recursos Azure utilizando `az cli` . Isto pode demorar até 5 minutos.
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

1. Uma vez terminada a implementação, descarregue a saída de implementação num ficheiro denominado `deployment-outputs.json` .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Configurar o Controlador de Entrada de Gateway de Aplicação

Com as instruções na secção anterior, criámos e configuramos um novo cluster AKS e um Gateway de Aplicação. Estamos agora prontos para implementar uma app de amostra e um controlador de entrada para a nossa nova infraestrutura Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Configurar credenciais kubernetes
Para os seguintes passos, precisamos de um comando [kubectl](https://kubectl.docs.kubernetes.io/) de configuração, que usaremos para ligar ao nosso novo cluster Kubernetes. [A Cloud Shell](https://shell.azure.com/) `kubectl` já está instalada. Usaremos `az` o CLI para obter credenciais para Kubernetes.

Obtenha credenciais para o seu AKS recém-implantado[(ler mais):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Instalar identidade do pod do AAD
  A Azure Ative Directory Pod Identity fornece acesso simbólico ao [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) adicionará os seguintes componentes ao seu cluster Kubernetes:
   * [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)kubernetes: `AzureIdentity` , `AzureAssignedIdentity``AzureIdentityBinding`
   * [Componente de Controlador de Identidade Gerido (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * [Componente de identidade gerida no nó (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Para instalar a identidade do pod AAD no seu cluster:

   - *RBAC habilitado* Aglomerado de AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC desativado* Aglomerado de AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Instalar Leme
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) é um gestor de pacotes para Kubernetes. Vamos aproveitar para instalar o `application-gateway-kubernetes-ingress` pacote:

1. Instale [o Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) e execute o seguinte `application-gateway-kubernetes-ingress` pacote de leme:

    - *RBAC habilitado* Aglomerado de AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC desativado* Aglomerado de AKS

        ```bash
        helm init
        ```

1. Adicione o repositório AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Instalar gráfico de leme do controlador ingress

1. Utilize o `deployment-outputs.json` ficheiro acima criado e crie as seguintes variáveis.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Descarregue helm-config.yaml, que configurará a AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Ou copie o ficheiro YAML abaixo: 
    
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

1. Edite o helm-config.yaml recentemente descarregado e preencha as secções `appgw` e `armAuth` .
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
     - `verbosityLevel`: Define o nível de verbosidade da infraestrutura de registo AGIC. Consulte [os Níveis de Registo](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) para obter valores possíveis.
     - `appgw.subscriptionId`: O ID de assinatura Azure no qual o Gateway de aplicação reside. Exemplo: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Nome do Grupo de Recursos Azure em que foi criado o Application Gateway. Exemplo: `app-gw-resource-group`
     - `appgw.name`: Nome do Gateway de Aplicação. Exemplo: `applicationgatewayd0f0`
     - `appgw.shared`: Esta bandeira booleana deve ser infringida a `false` . Configurar para `true` onde necessitar de um Gateway de [aplicação partilhado.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)
     - `kubernetes.watchNamespace`: Especifique o espaço do nome, que a AGIC deve observar. Isto pode ser um único valor de corda, ou uma lista separada por vírgula de espaços de nome.
    - `armAuth.type`: pode ser `aadPodIdentity` ou `servicePrincipal`
    - `armAuth.identityResourceID`: Identificação de recursos da Identidade Gerida do Azure
    - `armAuth.identityClientId`: Identificação do Cliente da Identidade. Veja abaixo mais informações sobre identidade
    - `armAuth.secretJSON`: Só é necessário quando o tipo de Serviço Principal Secreto for escolhido (quando `armAuth.type` tiver sido definido para `servicePrincipal` ) 


   > [!NOTE]
   > Os `identityResourceID` `identityClientID` valores e são criados durante as [etapas de Componentes de Implantação,](ingress-controller-install-new.md#deploy-components) e que poderiam ser obtidos novamente usando o seguinte comando:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` no comando acima está o grupo de recursos do seu Gateway de Aplicação. `<identity-name>` é o nome da identidade criada. Todas as identidades de uma determinada subscrição podem ser listadas utilizando: `az identity list`


1. Instale o pacote do controlador de entrada de entrada de entrada de aplicação:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Instale uma app de amostra
Agora que temos o Application Gateway, AKS e AGIC instalados, podemos instalar uma aplicação de amostra através [do Azure Cloud Shell:](https://shell.azure.com/)

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

* Descarregue o ficheiro YAML acima:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Aplicar o ficheiro YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Outros Exemplos
Este [guia de como fazer](ingress-controller-expose-service-over-http-https.md) contém mais exemplos sobre como expor um serviço AKS via HTTP ou HTTPS, à Internet com o Application Gateway.
