---
title: Resolução de problemas do controlador de entrada de entrada de aplicação
description: Este artigo fornece documentação sobre como resolver problemas e/ou problemas com o Controlador de Entradas de Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: f2b9f79f0914e645c736f8a577c46baa42587332
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874615"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Resolução de problemas comuns ou problemas com o Controlador Ingress

[A Azure Cloud Shell](https://shell.azure.com/) é a forma mais conveniente de resolver problemas com a instalação AKS e AGIC. Lance a sua concha a partir de [shell.azure.com](https://shell.azure.com/) ou clicando no link:

[![Lançamento incorporado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Teste com uma aplicação simples de Kubernetes

Os passos abaixo assumem:
  - Você tem um cluster AKS, com rede avançada ativada
  - A AGIC foi instalada no cluster AKS
  - Você já tem um Gateway de aplicação em um VNET compartilhado com o seu cluster AKS

Para verificar se a instalação do Gateway + AKS + AGIC está configurada corretamente, implemente a aplicação mais simples possível:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
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
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Copie e cole todas as linhas de uma só vez do script acima para uma [Azure Cloud Shell](https://shell.azure.com/). Certifique-se de que todo o comando é copiado - começando `cat` com e incluindo o último `EOF` .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Depois de uma implementação bem sucedida da aplicação acima do seu cluster AKS terá um novo Pod, Serviço e um Ingress.

Obtenha a lista de cápsulas com [Cloud Shell:](https://shell.azure.com/) `kubectl get pods -o wide` .
Esperamos que tenha sido criado um pod chamado "test-agic-app-pod". Terá um endereço IP. Este endereço deve estar dentro do VNET do Gateway de aplicação, que é utilizado com AKS.

![Screenshot da janela Bash em Azure Cloud Shell mostrando uma lista de cápsulas que inclui test-agic-app-pod na lista.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Obtenha a lista de serviços: `kubectl get services -o wide` . Esperamos ver um serviço chamado "test-agic-app-service".

![Screenshot da janela Bash em Azure Cloud Shell mostrando uma lista de serviços que inclui test-agic-app-pod na lista.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Obtenha a lista dos ingressess: `kubectl get ingress` . Esperamos que tenha sido criado um recurso Ingress chamado "test-agic-app-ingress". O recurso terá um nome de anfitrião 'test.agic.contoso.com'.

![Screenshot da janela Bash em Azure Cloud Shell mostrando uma lista de ingresses que inclui test-agic-app-ingresss na lista.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Uma das cápsulas será a AGIC. `kubectl get pods` apresentará uma lista de cápsulas, uma das quais começará com "ingresss-azure". Obtenha todos os registos da cápsula `kubectl logs <name-of-ingress-controller-pod>` para verificar se tivemos uma implementação bem sucedida. Uma implementação bem sucedida teria adicionado as seguintes linhas ao registo:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Em alternativa, a partir da [Cloud Shell](https://shell.azure.com/) só podemos recuperar as linhas que indicam uma configuração bem sucedida do Application Gateway com , onde deve ser o `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` nome `<ingress-azure....>` exato da cápsula AGIC.

O Application Gateway terá a seguinte configuração aplicada:

- Ouvinte: ![ ouvinte](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Regra de encaminhamento: ![ routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Piscina de backend:
  - Haverá um endereço IP no pool de endereços de backend e corresponderá ao endereço IP do Pod que observamos anteriormente com `kubectl get pods -o wide` 
 ![ backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Finalmente podemos usar o `cURL` comando de dentro da Cloud [Shell](https://shell.azure.com/) para estabelecer uma ligação HTTP à aplicação recém-implantada:

1. Utilize `kubectl get ingress` para obter o endereço IP público do Gateway de aplicações
2. Utilizar o comando `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Screenshot da janela Bash em Azure Cloud Shell mostrando um comando cURL com sucesso estabelecendo uma ligação HTTP à aplicação de teste.](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Um resultado `HTTP/1.1 200 OK` indica que o sistema Application Gateway + AKS + AGIC está a funcionar como esperado.


## <a name="inspect-kubernetes-installation"></a>Inspecione a instalação de Kubernetes

### <a name="pods-services-ingress"></a>Pods, Serviços, Ingress
O Controlador de Entradas de Gateway de Aplicação (AGIC) monitoriza continuamente os seguintes recursos Kubernetes: [Implantação](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) ou [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Serviço](https://kubernetes.io/docs/concepts/services-networking/service/), [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Devem estar em vigor para que a AGIC funcione como esperado:
  1. AKS deve ter uma ou mais **cápsulas** saudáveis.
     Verifique isto na [Cloud Shell](https://shell.azure.com/) com Se tiver um Pod `kubectl get pods -o wide --show-labels` com `apsnetapp` um, a sua saída pode ficar assim:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Um ou mais **serviços,** referindo-se às cápsulas acima através de `selector` etiquetas correspondentes.
     Verifique isto da [Cloud Shell](https://shell.azure.com/) com `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, anotado `kubernetes.io/ingress.class: azure/application-gateway` com, referenciando o serviço acima Verifique isto da [Cloud Shell](https://shell.azure.com/) com `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Ver anotações da entrada acima: `kubectl get ingress aspnetapp -o yaml` (substituir `aspnetapp` com o nome da sua entrada)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     O recurso de entrada deve ser anotado com `kubernetes.io/ingress.class: azure/application-gateway` .
 

### <a name="verify-observed-namespace"></a>Verifique o espaço de nome observado

* Obtenha os espaços de nome existentes no cluster Kubernetes. Em que espaço está a sua aplicação? A AGIC está a ver o espaço de nomes? Consulte a documentação de suporte de [múltiplos nomes](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) sobre como configurar adequadamente os espaços de nome observados.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* A cápsula AGIC deve estar no espaço de `default` nomes (ver `NAMESPACE` coluna). Uma cápsula saudável teria `Running` na `STATUS` coluna. Deve haver pelo menos uma cápsula AGIC.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Se a cápsula AGIC não for saudável `STATUS` (coluna do comando acima não `Running` é):
  - obter registos para entender o porquê: `kubectl logs <pod-name>`
  - para a instância anterior da cápsula: `kubectl logs <pod-name> --previous`
  - descrever a cápsula para obter mais contexto: `kubectl describe pod <pod-name>`


* Tem um [serviço](https://kubernetes.io/docs/concepts/services-networking/service/) Kubernetes e recursos [ingress?](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* A sua [Entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/) é anotada `kubernetes.io/ingress.class: azure/application-gateway` com: A AGIC só vai estar atenta aos recursos da Kubernetes Ingress que têm esta anotação.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* A AGIC emite eventos kubernetes para certos erros críticos. Pode ver estes:
  - no seu terminal via `kubectl get events --sort-by=.metadata.creationTimestamp`
  - no seu navegador utilizando o [UI Web Kubernetes (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Níveis de Registo

A AGIC tem 3 níveis de exploração madeireira. O nível 1 é o predefinido e mostra o número mínimo de linhas de registo.
O nível 5, por outro lado, exibiria todos os registos, incluindo o conteúdo higienizado de config aplicado à ARM.

A comunidade Kubernetes estabeleceu 9 níveis de registo para a ferramenta [kubectl.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) Neste repositório estamos a utilizar 3 destes, com semântica semelhante:


| Verbosidade | Descrição |
|-----------|-------------|
|  1        | Nível de registo predefinido; mostra detalhes de arranque, avisos e erros |
|  3        | Informação alargada sobre eventos e alterações; listas de objetos criados |
|  5        | Troncos de objetos de marechal; mostra json config higienizado aplicado à ARM |


Os níveis de verbosidade são ajustáveis através da `verbosityLevel` variável no ficheiro [helm-config.yaml.](#sample-helm-config-file) Aumente o nível de verbosidade `5` para que o config JSON despachasse para a [ARM:](../azure-resource-manager/management/overview.md)
  - adicionar `verbosityLevel: 5` em uma linha por si só em [helm-config.yaml](#sample-helm-config-file) e reinstalar
  - obter troncos com `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Arquivo config de amostra helm
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
# Specify if the cluster is Kubernetes RBAC enabled or not
rbac:
    enabled: false # true/false

# Specify aks cluster related information. THIS IS BEING DEPRECATED.
aksClusterConfiguration:
    apiServerAddress: <aks-api-server-address>
```