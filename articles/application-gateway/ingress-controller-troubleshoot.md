---
title: Resolução de problemas do Controlador de Ingress ingresso de Gateway gateway
description: Este artigo fornece documentação sobre como resolver questões comuns e/ou problemas com o Controlador de Entrada de Entrada de Aplicações.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795503"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Problemas de resolução de problemas ou problemas comuns com o Controlador de Ingress

[A Azure Cloud Shell](https://shell.azure.com/) é a forma mais conveniente de resolver qualquer problema com a sua instalação AKS e AGIC. Lance a sua concha a partir de [shell.azure.com](https://shell.azure.com/) ou clicando no link:

[![Lançamento emcamado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Teste com uma simples app Kubernetes

Os passos abaixo assumem:
  - Você tem um cluster AKS, com Rede Avançada ativada
  - AGIC foi instalada no cluster AKS
  - Você já tem um Gateway de Aplicação em um VNET partilhado com o seu cluster AKS

Para verificar se a instalação Gateway + AKS + AGIC está corretamente configurada, implemente a aplicação mais simples possível:

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

Copie e cole todas as linhas ao mesmo tempo do guião acima para uma [Casca de Nuvem Azure](https://shell.azure.com/). Certifique-se de que todo o `cat` comando é `EOF`copiado - começando com e incluindo o último .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Depois de uma implementação bem sucedida da app acima do seu cluster AKS terá um novo Pod, Serviço e um Ingress.

Obtenha a lista de cápsulas `kubectl get pods -o wide`com Cloud [Shell](https://shell.azure.com/): .
Esperamos que tenha sido criado um casulo chamado "test-agic-app-pod". Terá um endereço IP. Este endereço deve estar dentro do VNET do Gateway de Aplicação, que é utilizado com AKS.

![pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Obtenha a lista `kubectl get services -o wide`de serviços: . Esperamos ver um serviço chamado "test-agic-app-service".

![pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Obtenha a lista dos `kubectl get ingress`ingressos: . Esperamos que tenha sido criado um recurso Ingress chamado "test-agic-app-ingress". O recurso terá um nome de anfitrião 'test.agic.contoso.com'.

![pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Uma das cápsulas será a AGIC. `kubectl get pods`mostrará uma lista de cápsulas, uma das quais começará com "ingress-azure". Obtenha todos os registos `kubectl logs <name-of-ingress-controller-pod>` dessa cápsula para verificar se tivemos uma implementação bem sucedida. Uma implantação bem sucedida teria adicionado as seguintes linhas ao registo:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternativamente, a partir da [Cloud Shell](https://shell.azure.com/) só podemos `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`recuperar `<ingress-azure....>` as linhas indicando uma configuração de Gateway de aplicação bem sucedida com, onde deve ser o nome exato da cápsula AGIC.

O Gateway de aplicação terá a seguinte configuração aplicada:

- Ouvinte: ![ouvinte](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Regra de ![encaminhamento: routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Piscina de backend:
  - Haverá um endereço IP na piscina de endereços de backend e corresponderá `kubectl get pods -o wide` 
 ![ao endereço IP do Pod que observamos anteriormente com backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Finalmente podemos `cURL` usar o comando de dentro da [Cloud Shell](https://shell.azure.com/) para estabelecer uma ligação HTTP à aplicação recém-implantada:

1. Utilizar `kubectl get ingress` para obter o endereço IP público do Gateway de Aplicação
2. Utilizar `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Um resultado `HTTP/1.1 200 OK` indica que o sistema Application Gateway + AKS + AGIC está a funcionar como esperado.


## <a name="inspect-kubernetes-installation"></a>Inspecione a instalação de Kubernetes

### <a name="pods-services-ingress"></a>Pods, Serviços, Ingress
O Controlador de Ingress (AGIC) gateway gateway implementa continuamente os seguintes recursos Kubernetes: [Implantação](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) ou [Pod,](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod) [Serviço,](https://kubernetes.io/docs/concepts/services-networking/service/) [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Devem estar em vigor para que a AGIC funcione como esperado:
  1. AkS deve ter uma ou mais **cápsulas**saudáveis.
     Verifique isto a partir da [Cloud Shell](https://shell.azure.com/) com `kubectl get pods -o wide --show-labels` Se tiver um Pod com um, `apsnetapp`a sua saída pode parecer assim:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Um ou mais **serviços,** fazendo `selector` referência às cápsulas acima através de etiquetas correspondentes.
     Verifique isto a partir de [Cloud Shell](https://shell.azure.com/) com`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress,** anotado com `kubernetes.io/ingress.class: azure/application-gateway`, referindo o serviço acima Verifique isto a partir da [Cloud Shell](https://shell.azure.com/) com`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Ver anotações da entrada `kubectl get ingress aspnetapp -o yaml` acima: `aspnetapp` (substituto do nome da sua entrada)
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

     O recurso de entrada deve `kubernetes.io/ingress.class: azure/application-gateway`ser anotado com .
 

### <a name="verify-observed-namespace"></a>Verificar espaço de nome observado

* Obtenha os espaços de nome existentes no aglomerado de Kubernetes. Qual é o espaço de nome da sua aplicação? A AGIC está a ver aquele espaço de nome? Consulte a documentação de suporte de [espaço de nome múltiplos](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) sobre como configurar corretamente os espaços de nome observados.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* A cápsula AGIC deve `default` estar no `NAMESPACE`espaço de nome (ver coluna). Uma cápsula saudável `Running` teria `STATUS` na coluna. Deve haver pelo menos uma cápsula AGIC.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Se a cápsula AGIC`STATUS` não for saudável (coluna do comando acima não `Running`é):
  - obter registos para entender o porquê:`kubectl logs <pod-name>`
  - para a instância anterior da cápsula:`kubectl logs <pod-name> --previous`
  - descrever a cápsula para obter mais contexto:`kubectl describe pod <pod-name>`


* Tem um [Serviço](https://kubernetes.io/docs/concepts/services-networking/service/) Kubernetes e recursos [Ingress?](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* O seu [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) está anotado com: `kubernetes.io/ingress.class: azure/application-gateway`? A AGIC só vai vigiar os recursos da Kubernetes Ingress que têm esta anotação.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* A AGIC emite eventos de Kubernetes para certos erros críticos. Pode ver isto:
  - no seu terminal via`kubectl get events --sort-by=.metadata.creationTimestamp`
  - no seu navegador utilizando o [Kubernetes Web UI (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Níveis de Registo

A AGIC tem 3 níveis de exploração madeireira. O nível 1 é o padrão e mostra um número mínimo de linhas de registo.
O nível 5, por outro lado, exibiria todos os troncos, incluindo o conteúdo de config sanitizado aplicado à ARM.

A comunidade Kubernetes estabeleceu 9 níveis de exploração madeireira para a ferramenta [kubectl.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) Neste repositório estamos a utilizar 3 destes, com semântica semelhante:


| Verbosidade | Descrição |
|-----------|-------------|
|  1        | Nível de registo padrão; mostra detalhes, avisos e erros de arranque |
|  3        | Informação alargada sobre eventos e alterações; listas de objetos criados |
|  5        | Regista objetos maqueados; mostra config JSON sanitizado aplicado à ARM |


Os níveis de verbosidade são `verbosityLevel` ajustáveis através da variável no ficheiro [helm-config.yaml.](#sample-helm-config-file) Aumente o nível `5` de verbosidade para que o config JSON seja enviado para a [ARM:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
  - adicionar `verbosityLevel: 5` em uma linha por si só em [helm-config.yaml](#sample-helm-config-file) e reinstalar
  - obter registos com`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Arquivo de config de leme de amostra
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

