---
title: Criar um controlador de ingresso com um Gateway de Aplicação existente
description: Este artigo fornece informações sobre como implementar um Controlador de Ingress ingresso de Gateway de aplicação com um Gateway de aplicação existente.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 949f1b3ee3db72e1c541c3dd4c5f74f364f1b514
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869887"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Instale um controlador de entrada de gateway de aplicação (AGIC) utilizando um gateway de aplicação existente

O Controlador de Ingress o Gateway (AGIC) é uma cápsula dentro do seu cluster Kubernetes.
A AGIC monitoriza os recursos da Kubernetes [Ingress,](https://kubernetes.io/docs/concepts/services-networking/ingress/) e cria e aplica o config application Gateway com base no estado do cluster Kubernetes.

## <a name="outline"></a>Esboço:
- [Pré-requisitos](#prerequisites)
- [Autenticação do Gestor de Recursos Azure (ARM)](#azure-resource-manager-authentication)
    - Opção 1: Criar identidade [aad-pod](#set-up-aad-pod-identity) e criar identidade Azure em ARMs
    - Opção 2: [Utilização](#using-a-service-principal) de um diretor de serviço
- [Instale o Controlador De Ingress usando o Leme](#install-ingress-controller-as-a-helm-chart)
- [Gateway de aplicação multi-cluster / shared](#multi-cluster--shared-application-gateway): Instale a AGIC num ambiente, onde o Gateway da Aplicação é partilhado entre um ou mais clusters AKS e/ou outros componentes Azure.

## <a name="prerequisites"></a>Pré-requisitos
Este documento assume que já tem as seguintes ferramentas e infraestruturas instaladas:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) com [Rede Avançada](https://docs.microsoft.com/azure/aks/configure-azure-cni) ativada
- [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) na mesma rede virtual que AKS
- Identidade de [Pod AAD](https://github.com/Azure/aad-pod-identity) instalada no seu cluster AKS
- [Cloud Shell](https://shell.azure.com/) é o ambiente de `az` concha `kubectl`Azure, que tem CLI, e `helm` instalado. Estas ferramentas são necessárias para os comandos abaixo.

Por favor, __faça uma cópia de segurança da configuração do Gateway de aplicação__ antes de instalar a AGIC:
  1. usando o [portal Azure](https://portal.azure.com/) navegar para a sua `Application Gateway` instância
  2. de `Export template` clique`Download`

O ficheiro zip que descarregou terá modelos JSON, bata e powerShell scripts que você poderia usar para restaurar App Gateway caso isso se tornasse necessário

## <a name="install-helm"></a>Instalar leme
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) é um gestor de pacotes para kubernetes. Vamos aproveitá-lo `application-gateway-kubernetes-ingress` para instalar o pacote.
Use [cloud shell](https://shell.azure.com/) para instalar o Leme:

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

## <a name="azure-resource-manager-authentication"></a>Autenticação do Gestor de Recursos Azure

A AGIC comunica com o servidor Kubernetes API e com o Gestor de Recursos Azure. Requer uma identidade para aceder a estas APIs.

## <a name="set-up-aad-pod-identity"></a>Configurar identidade de pod AAD

[A AAD Pod Identity](https://github.com/Azure/aad-pod-identity) é um controlador, semelhante ao AGIC, que também funciona no seu AKS. Liga as identidades do Diretório Ativo Azure às suas cápsulas Kubernetes. A identidade é necessária para que uma aplicação numa cápsula Kubernetes possa comunicar com outros componentes Do IA. No caso em particular aqui, precisamos de autorização para que a cápsula AGIC faça pedidos http à [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Siga as instruções de instalação da [Identidade aAD Pod](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) para adicionar este componente ao seu AKS.

Em seguida, precisamos criar uma identidade Azure e dar-lhe permissões ARM.
Use [a Cloud Shell](https://shell.azure.com/) para executar todos os seguintes comandos e criar uma identidade:

1. Crie uma identidade Azure no mesmo grupo de **recursos que os nódosos AKS.** Escolher o grupo de recursos correto é importante. O grupo de recursos exigido no comando abaixo *não* é o referenciado no painel do portal AKS. Este é o grupo `aks-agentpool` de recursos das máquinas virtuais. Normalmente, esse grupo `MC_` de recursos começa com e contém o nome do seu AKS. Por exemplo:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Para os comandos de atribuição `principalId` de funções abaixo precisamos obter para a identidade recém-criada:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Dê acesso `Contributor` de identidade ao seu Gateway de Aplicação. Para isso precisa da identificação do Gateway de Aplicação, que será algo parecido com isto:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Obtenha a lista de IDs de Gateway de Aplicação na sua subscrição com:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Dê acesso `Reader` de identidade ao grupo de recursos Application Gateway. O id do grupo `/subscriptions/A/resourceGroups/B`de recursos seria como: . Você pode obter todos os grupos de recursos com:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Usando um diretor de serviço
Também é possível fornecer acesso AGIC à ARM através de um segredo kubernetes.

1. Crie um Diretor de Serviço de Diretório Ativo e codifique com base64. A codificação base64 é necessária para que a bolha JSON seja guardada para Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Adicione a bolha JSON codificada base64 ao `helm-config.yaml` ficheiro. Mais informações estão `helm-config.yaml` na próxima secção.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Instale o Controlador De Ingress como gráfico de leme
Nos primeiros passos, instalamos o Helm's Tiller no seu cluster Kubernetes. Utilize a [Cloud Shell](https://shell.azure.com/) para instalar o pacote AGIC Helm:

1. Adicione `application-gateway-kubernetes-ingress` o repo do leme e execute uma atualização de leme

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Editar helm-config.yaml e preencher `appgw` os `armAuth`valores para e .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > As `<identity-resource-id>` `<identity-client-id>` propriedades e são as propriedades da Identidade AD Azure que configurana na secção anterior. Pode recuperar esta informação executando o seguinte comando: `az identity show -g <resourcegroup> -n <identity-name>`, onde `<resourcegroup>` está o grupo de recursos em que o objeto de cluster AKS de nível superior, Gateway de Aplicação e Identificação Gerida são implantados.

1. Instale `application-gateway-kubernetes-ingress` gráfico `helm-config.yaml` helm com a configuração do passo anterior

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternativamente, pode `helm-config.yaml` combinar o comando helm e helm em um passo:
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

Consulte [este guia de como entender](ingress-controller-expose-service-over-http-https.md) como pode expor um serviço AKS através de HTTP ou HTTPS, para a internet, utilizando um Portal de Aplicações Azure.



## <a name="multi-cluster--shared-application-gateway"></a>Gateway de aplicação partilhada de vários aglomerados / compartilhados
Por defeito, a AGIC assume a propriedade total do Gateway de Aplicação a que está ligado. A versão AGIC 0.8.0 e mais tarde pode partilhar um único Gateway de aplicação com outros componentes Azure. Por exemplo, poderíamos usar o mesmo Gateway de aplicação para uma aplicação hospedada no Virtual Machine Scale set, bem como um cluster AKS.

Por favor, __faça uma cópia de segurança da configuração do Gateway de Aplicação__ antes de ativar esta definição:
  1. usando o [portal Azure](https://portal.azure.com/) navegar para a sua `Application Gateway` instância
  2. de `Export template` clique`Download`

O ficheiro zip que descarregou terá modelos JSON, bash e powerShell scripts que você poderia usar para restaurar application Gateway

### <a name="example-scenario"></a>Cenário de Exemplo
Vamos ver um Gateway de Aplicação imaginário, que gere o tráfego para dois sites:
  - `dev.contoso.com`- hospedado num novo AKS, utilizando application Gateway e AGIC
  - `prod.contoso.com`- hospedado num conjunto de escala de [máquina virtual Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Com as definições predefinidas, a AGIC assume a propriedade 100% do Gateway de Aplicação a que é apontado. A AGIC substitui toda a configuração do App Gateway. Se criarmos manualmente um ouvinte `prod.contoso.com` para (no Gateway da Aplicação), sem defini-lo na `prod.contoso.com` Entrada Kubernetes, a AGIC eliminará o config em segundos.

Para instalar o AGIC e também servir `prod.contoso.com` a partir das nossas `dev.contoso.com` máquinas de conjunto de escala de máquina virtual, temos de limitar a AGIC a configurar apenas. Isto é facilitado instantaneamente o seguinte [CRD:](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

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

O comando acima `AzureIngressProhibitedTarget` cria um objeto. Isto torna a AGIC (versão 0.8.0 e mais tarde) `prod.contoso.com` consciente da existência de config application Gateway para e instrui-o explicitamente para evitar alterar qualquer configuração relacionada com esse nome de anfitrião.


### <a name="enable-with-new-agic-installation"></a>Ativar com nova instalação AGIC
Para limitar o AGIC (versão 0.8.0 e mais tarde) `helm-config.yaml` a um subconjunto da configuração do Gateway de Aplicação, modifique o modelo.
Por `appgw:` baixo da `shared` secção, adicione `true`a tecla e coloque-a para .

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
  2. Atualizar o Leme:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Como resultado, o seu AKS `AzureIngressProhibitedTarget` terá `prohibit-all-targets`uma nova instância chamada:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

O `prohibit-all-targets`objeto, como o nome indica, proíbe a AGIC de mudar de config para *qualquer* hospedeiro e caminho.
A instalação do leme com `appgw.shared=true` irá implementar a AGIC, mas não fará alterações no Gateway da Aplicação.


### <a name="broaden-permissions"></a>Alargar permissões
Desde helm `appgw.shared=true` com `prohibit-all-targets` e o padrão bloqueia a AGIC de aplicar qualquer config.

Alargar as permissões DaGIC com:
1. Crie `AzureIngressProhibitedTarget` um novo com a sua configuração específica:
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

2. Só depois de ter criado a sua própria proibição personalizada, pode eliminar a deprecinada, que é demasiado ampla:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Ativar uma instalação AGIC existente
Vamos assumir que já temos um AKS em funcionamento, Application Gateway, e AGIC configurado no nosso cluster. Temos um Ingress `prod.contosor.com` para e estamos a servir com sucesso o tráfego da AKS. Queremos adicionar `staging.contoso.com` ao nosso Gateway de Aplicação existente, mas precisamos acolhê-lo num [VM](https://azure.microsoft.com/services/virtual-machines/). Vamos reutilizar o Gateway de Aplicação existente e configurar manualmente um `staging.contoso.com`ouvinte e piscinas de backend para . Mas ajustar manualmente o config application Gateway (via [portal,](https://portal.azure.com) [ARM APIs](https://docs.microsoft.com/rest/api/resources/) ou [Terraform)](https://www.terraform.io/)entraria em conflito com os pressupostos da AGIC de propriedade total. Pouco depois de aplicarmos alterações, a AGIC irá sobrepor-se ou eliminá-las.

Podemos proibir a AGIC de fazer alterações a um subconjunto de configuração.

1. Criar `AzureIngressProhibitedTarget` um objeto:
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

3. Modificar a aplicação Gateway config via portal - adicione ouvintes, regras de encaminhamento, backends etc. O novo objeto`manually-configured-staging-environment`que criamos () proibirá a `staging.contoso.com`AGIC de sobrepor a configuração do Gateway de Aplicação relacionada com .
