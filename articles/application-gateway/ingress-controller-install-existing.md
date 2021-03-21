---
title: Criar um controlador de entrada com um Gateway de aplicação existente
description: Este artigo fornece informações sobre como implementar um Controlador de Entrada de Gateway de Aplicação com um Gateway de aplicação existente.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2d64766c754c0ea104ae83fde799a514e9da6d68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693725"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instale um Controlador de Entrada de Gateway de Aplicação (AGIC) utilizando um Gateway de aplicação existente

O Controlador de Entrada de Gateway de Aplicação (AGIC) é uma cápsula dentro do seu cluster Kubernetes.
A AGIC monitoriza os recursos da Kubernetes [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) e cria e aplica a App Gateway config com base no estado do cluster Kubernetes.

## <a name="outline"></a>Contorno:
- [Pré-requisitos](#prerequisites)
- [Autenticação do Gestor de Recursos Azure (ARM)](#azure-resource-manager-authentication)
    - Opção 1: [Configurar a identidade aad-pod](#set-up-aad-pod-identity) e criar identidade azul nas ARMs
    - Opção 2: [Utilização de um diretor de serviço](#using-a-service-principal)
- [Instale o Controlador ingress usando o leme](#install-ingress-controller-as-a-helm-chart)
- [Gateway multi-cluster / aplicação partilhada](#multi-cluster--shared-application-gateway): Instale o AGIC num ambiente onde o Gateway de Aplicação é partilhado entre um ou mais clusters AKS e/ou outros componentes Azure.

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que já tem as seguintes ferramentas e infraestruturas instaladas:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) com [rede avançada](../aks/configure-azure-cni.md) ativada
- [Application Gateway v2](./tutorial-autoscale-ps.md) na mesma rede virtual que a AKS
- [Identidade do Pod AAD](https://github.com/Azure/aad-pod-identity) instalada no seu cluster AKS
- [Cloud Shell](https://shell.azure.com/) é o ambiente de concha Azure, que tem `az` `kubectl` CLI, e `helm` instalado. Estas ferramentas são necessárias para os comandos abaixo.

Por __favor, faça uma cópia de segurança da configuração do Gateway de Aplicação__ antes de instalar o AGIC:
  1. usando [o portal Azure](https://portal.azure.com/) navegar para o seu `Application Gateway` exemplo
  2. a partir do `Export template` clique `Download`

O ficheiro zip que descarregou terá modelos JSON, bash e scripts PowerShell que você poderia usar para restaurar App Gateway caso isso se torne necessário

## <a name="install-helm"></a>Instalar o Helm
[Helm](../aks/kubernetes-helm.md) é um gestor de pacotes para Kubernetes. Vamos aproveitar para instalar o `application-gateway-kubernetes-ingress` pacote.
Use [a Cloud Shell](https://shell.azure.com/) para instalar o Leme:

1. Instale [o Helm](../aks/kubernetes-helm.md) e execute o seguinte `application-gateway-kubernetes-ingress` pacote de leme:

    - *Kubernetes RBAC habilitado* Aglomerado de AKS

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *Kubernetes RBAC desativado* Aglomerado de AKS

    ```bash
    helm init
    ```

1. Adicione o repositório AGIC Helm:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Autenticação do Gestor de Recursos Azure

A AGIC comunica com o servidor API da Kubernetes e com o Gestor de Recursos Azure. Requer uma identidade para aceder a estas APIs.

## <a name="set-up-aad-pod-identity"></a>Configurar a identidade do pod da AAD

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) é um controlador, semelhante ao AGIC, que também funciona no seu AKS. Liga as identidades do Azure Ative Directory às suas cápsulas Kubernetes. A identidade é necessária para que uma aplicação numa cápsula Kubernetes possa comunicar com outros componentes Azure. No caso em particular, precisamos de autorização para que a cápsula AGIC faça pedidos HTTP à [ARM](../azure-resource-manager/management/overview.md).

Siga as [instruções de instalação da AAD Pod Identity](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) para adicionar este componente à sua AKS.

Em seguida, precisamos criar uma identidade Azure e dar-lhe permissões ARM.
Use [a Cloud Shell](https://shell.azure.com/) para executar todos os seguintes comandos e criar uma identidade:

1. Criar uma identidade Azure **no mesmo grupo de recursos que os nós AKS**. Escolher o grupo de recursos correto é importante. O grupo de recursos exigido no comando abaixo *não* é o referenciado no painel do portal AKS. Este é o grupo de recursos das `aks-agentpool` máquinas virtuais. Normalmente, esse grupo de recursos começa `MC_` com e contém o nome da sua AKS. Por exemplo: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Para os comandos de atribuição de funções abaixo, precisamos obter `principalId` para a identidade recém-criada:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Dê acesso de identidade `Contributor` ao seu Gateway de Aplicação. Para isso precisa da ID do Gateway de Aplicações, que será algo parecido com isto: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Obtenha a lista de IDs do Gateway de Aplicação na sua subscrição com: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Dar acesso de identidade `Reader` ao grupo de recursos Application Gateway. O iD do grupo de recursos seria como: `/subscriptions/A/resourceGroups/B` . Pode obter todos os grupos de recursos com: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Usando um diretor de serviço
Também é possível fornecer acesso AGIC à ARM através de um segredo de Kubernetes.

1. Crie um Ative Directory Service Principal e codificar com base64. A codificação base64 é necessária para que a bolha JSON seja guardada para Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Adicione a bolha JSON codificada de base64 ao `helm-config.yaml` ficheiro. Mais informações estão `helm-config.yaml` na próxima secção.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instale o Controlador Ingress como um gráfico de leme
Nos primeiros passos, instalamos helm's Tiller no seu cluster Kubernetes. Utilize [a Cloud Shell](https://shell.azure.com/) para instalar o pacote AGIC Helm:

1. Adicione o `application-gateway-kubernetes-ingress` repo de leme e execute uma atualização de leme

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Editar helm-config.yaml e preencher os valores para `appgw` e `armAuth` .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > As `<identity-resource-id>` propriedades e são as propriedades da Identidade `<identity-client-id>` AD Azure que configura na secção anterior. Pode obter esta informação executando o seguinte comando: `az identity show -g <resourcegroup> -n <identity-name>` , onde está o grupo de recursos em que o objeto de cluster `<resourcegroup>` AKS de nível superior, o Gateway de Aplicação e a Identificação Gerida são implantados.

1. Instale o gráfico helm `application-gateway-kubernetes-ingress` com a `helm-config.yaml` configuração do passo anterior

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Em alternativa, pode combinar o `helm-config.yaml` comando e o comando Helm num só passo:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Verifique o registo da cápsula recém-criada para verificar se começou corretamente

Consulte [este guia de como](ingress-controller-expose-service-over-http-https.md) saber como expor um serviço AKS através de HTTP ou HTTPS, na internet, utilizando um Gateway de aplicações Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Gateway multi-cluster / Aplicação Partilhada
Por defeito, a AGIC assume a plena propriedade do Gateway de aplicação a que está ligada. Versão AGIC 0.8.0 e mais tarde pode partilhar um único Gateway de Aplicação com outros componentes Azure. Por exemplo, poderíamos usar o mesmo Gateway de aplicações para uma aplicação hospedada em Conjunto de Escala de Máquina Virtual, bem como um cluster AKS.

Faça __uma cópia de segurança da configuração do Gateway de Aplicação__ antes de ativar esta definição:
  1. usando [o portal Azure](https://portal.azure.com/) navegar para o seu `Application Gateway` exemplo
  2. a partir do `Export template` clique `Download`

O ficheiro zip que descarregou terá modelos JSON, bash e scripts PowerShell que você poderia usar para restaurar o Gateway de Aplicações

### <a name="example-scenario"></a>Cenário de Exemplo
Vejamos um Portal de Aplicações imaginário, que gere o tráfego de dois sites:
  - `dev.contoso.com` - hospedado num novo AKS, utilizando o Application Gateway e o AGIC
  - `prod.contoso.com` - hospedado num [conjunto de escala de máquina virtual Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Com as definições predefinidos, a AGIC assume 100% de propriedade do Gateway de aplicação a que é apontada. A AGIC substitui toda a configuração do App Gateway. Se criarmos manualmente um ouvinte `prod.contoso.com` (no Gateway de Aplicação), sem defini-lo no Kubernetes Ingress, a AGIC apagará o `prod.contoso.com` config em segundos.

Para instalar o AGIC e também servir `prod.contoso.com` a partir das nossas máquinas virtual de conjunto de escalas de máquinas, temos de limitar a AGIC a `dev.contoso.com` configurar apenas. Isto é facilitado através da instantaneaização do seguinte [DD:](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

O comando acima cria um `AzureIngressProhibitedTarget` objeto. Isto faz com que a AGIC (versão 0.8.0 e posterior) tenha conhecimento da existência do Gateway de Aplicação `prod.contoso.com` e instrua-a explicitamente para evitar alterar qualquer configuração relacionada com esse nome de hospedeiro.


### <a name="enable-with-new-agic-installation"></a>Ativar com nova instalação AGIC
Limitar o AGIC (versão 0.8.0 e posterior) a um subconjunto da configuração do Gateway de Aplicação modifica o `helm-config.yaml` modelo.
Sob a `appgw:` secção, adicione `shared` a chave e coloque-a em `true` .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Aplicar as alterações ao leme:
  1. Certifique-se de que o `AzureIngressProhibitedTarget` CRD está instalado com:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Atualização Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Como resultado, a sua AKS terá uma nova instância `AzureIngressProhibitedTarget` `prohibit-all-targets` chamada:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

O objeto `prohibit-all-targets` , como o nome indica, proíbe a AGIC de alterar a config para *qualquer* hospedeiro e caminho.
A instalação de leme com `appgw.shared=true` irá implantar a AGIC, mas não fará alterações ao Gateway de Aplicação.


### <a name="broaden-permissions"></a>Alargar permissões
Uma vez que helm com `appgw.shared=true` e o padrão `prohibit-all-targets` bloqueia a AGIC de aplicar qualquer config.

Alargar permissões AGIC com:
1. Crie um novo `AzureIngressProhibitedTarget` com a sua configuração específica:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Só depois de ter criado a sua própria proibição personalizada, pode apagar a padrão, que é demasiado ampla:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Permitir uma instalação AGIC existente
Vamos assumir que já temos uma AKS em funcionamento, gateway de aplicação, e AGIC configurado no nosso cluster. Temos uma Ingress `prod.contoso.com` para e estamos a servir com sucesso o tráfego da AKS. Queremos adicionar `staging.contoso.com` ao nosso Gateway de aplicações existente, mas precisamos acolhê-lo num [VM](https://azure.microsoft.com/services/virtual-machines/). Vamos reutilizar o Gateway de Aplicação existente e configurar manualmente um ouvinte e piscinas de backend para `staging.contoso.com` . Mas ajustar manualmente a aplicação Gateway config (via [portal,](https://portal.azure.com) [APIs ARM](/rest/api/resources/) ou [Terraform)](https://www.terraform.io/)entraria em conflito com os pressupostos da AGIC de plena propriedade. Pouco depois de aplicarmos alterações, a AGIC substituirá ou apagará.

Podemos proibir a AGIC de fazer alterações a um subconjunto de configuração.

1. Criar um `AzureIngressProhibitedTarget` objeto:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Veja o objeto recém-criado:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Modificar o Application Gateway config via portal - adicione ouvintes, regras de encaminhamento, backends, etc. O novo objeto que criamos ( `manually-configured-staging-environment` ) irá proibir a AGIC de sobrepor a configuração do Gateway de aplicação relacionada com `staging.contoso.com` .
