---
title: Solução de problemas do controlador de entrada do gateway de aplicativo
description: Este artigo fornece documentação sobre como solucionar problemas e/ou questões comuns com o controlador de entrada do gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/11/2019
ms.author: caya
ms.openlocfilehash: 6ee6239f9b1fbb66bac5d3920a888fb5a288a300
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513331"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Solucionar problemas ou perguntas comuns com controlador de entrada

[Azure cloud Shell](https://shell.azure.com/) é a maneira mais conveniente para solucionar problemas com a instalação do AKs e do AGIC. Inicie o Shell a partir de [shell.Azure.com](https://shell.azure.com/) ou clicando no link:

[![Lançamento de inserção](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Teste com um aplicativo kubernetes simples

As etapas a seguir pressupõem que:
  - Você tem um cluster AKS, com a rede avançada habilitada
  - O AGIC foi instalado no cluster AKS
  - Você já corre um gateway de aplicativo em uma VNET compartilhada com seu cluster AKS

Para verificar se a instalação do gateway de aplicativo + AKS + AGIC está configurada corretamente, implante o aplicativo mais simples possível:

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

Copie e Cole todas as linhas de uma só vez do script acima em um [Azure cloud Shell](https://shell.azure.com/). Verifique se o comando inteiro foi copiado-iniciando com `cat` e incluindo a última `EOF`.

![usar](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Depois que uma implantação bem-sucedida do aplicativo acima do seu cluster AKS terá um novo pod, serviço e uma entrada.

Obtenha a lista de pods com [Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide`.
Esperamos que um pod chamado ' test-agic-app-pod ' tenha sido criado. Ele terá um endereço IP. Esse endereço deve estar dentro da VNET do gateway de aplicativo, que é usada com AKS.

![Pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Obtenha a lista de serviços: `kubectl get services -o wide`. Esperamos ver um serviço chamado ' test-agic-app-Service '.

![Pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Obtenha a lista de insere: `kubectl get ingress`. Esperamos que um recurso de entrada chamado ' test-agic-app-Ingres ' tenha sido criado. O recurso terá um nome de host ' test.agic.contoso.com '.

![Pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Um dos pods será AGIC. `kubectl get pods` mostrará uma lista de pods, uma das quais começará com ' Ingres-Azure '. Obtenha todos os logs desse Pod com `kubectl logs <name-of-ingress-controller-pod>` para verificar se tivemos uma implantação bem-sucedida. Uma implantação bem-sucedida teria adicionado as seguintes linhas ao log:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Como alternativa, de [Cloud Shell](https://shell.azure.com/) podemos recuperar apenas as linhas indicando uma configuração de gateway de aplicativo bem-sucedida com `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`, em que `<ingress-azure....>` deve ser o nome exato do pod AGIC.

O gateway de aplicativo terá a seguinte configuração aplicada:

- Ouvinte: ![ouvinte](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Regra de roteamento: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Pool de back-end:
  - Haverá um endereço IP no pool de endereços de back-end e ele corresponderá ao endereço IP do pod que observamos anteriormente com `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Por fim, podemos usar o comando `cURL` de dentro de [Cloud Shell](https://shell.azure.com/) para estabelecer uma conexão HTTP com o aplicativo implantado recentemente:

1. Use `kubectl get ingress` para obter o endereço IP público do gateway de aplicativo
2. Usar `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Pods](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Um resultado de `HTTP/1.1 200 OK` indica que o sistema do gateway de aplicativo + AKS + AGIC está funcionando conforme o esperado.


## <a name="inspect-kubernetes-installation"></a>Inspecionar a instalação do kubernetes

### <a name="pods-services-ingress"></a>Pods, serviços, entrada
O AGIC (controlador de entrada do gateway de aplicativo) monitora continuamente os seguintes recursos de kubernetes: [implantação](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) ou [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [serviço](https://kubernetes.io/docs/concepts/services-networking/service/), [entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/)


O seguinte deve estar em vigor para que o AGIC funcione conforme o esperado:
  1. AKS deve ter um ou mais **pods**íntegros.
     Verifique isso de [Cloud Shell](https://shell.azure.com/) com `kubectl get pods -o wide --show-labels` se você tiver um pod com uma `apsnetapp`, a saída poderá ser assim:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Um ou mais **Serviços**, fazendo referência aos pods acima por meio de rótulos de `selector` correspondentes.
     Verifique isso em [Cloud Shell](https://shell.azure.com/) com `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Entrada**, anotada com `kubernetes.io/ingress.class: azure/application-gateway`, fazendo referência ao serviço acima, verifique isso de [Cloud Shell](https://shell.azure.com/) com `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Exibir anotações da entrada acima: `kubectl get ingress aspnetapp -o yaml` (substitua `aspnetapp` pelo nome da sua entrada)
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

     O recurso de entrada deve ser anotado com `kubernetes.io/ingress.class: azure/application-gateway`.
 

### <a name="verify-observed-namespace"></a>Verificar o namespace observado

* Obtenha os namespaces existentes no cluster kubernetes. Em que namespace seu aplicativo está sendo executado? AGIC está observando esse namespace? Consulte a documentação de [suporte a vários namespaces](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) sobre como configurar corretamente os namespaces observados.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* O Pod AGIC deve estar no namespace `default` (consulte a coluna `NAMESPACE`). Um pod íntegro teria `Running` na coluna `STATUS`. Deve haver pelo menos um pod AGIC.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Se o Pod AGIC não estiver íntegro (`STATUS` coluna do comando acima não for `Running`):
  - obter logs para entender o porquê: `kubectl logs <pod-name>`
  - para a instância anterior do pod: `kubectl logs <pod-name> --previous`
  - Descreva o pod para obter mais contexto: `kubectl describe pod <pod-name>`


* Você tem um [serviço](https://kubernetes.io/docs/concepts/services-networking/service/) kubernetes e recursos de [entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/) ?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Sua [entrada](https://kubernetes.io/docs/concepts/services-networking/ingress/) está anotada com: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC só observará os recursos de entrada do kubernetes que têm essa anotação.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC emite eventos kubernetes para determinados erros críticos. Você pode exibi-los:
  - no seu terminal via `kubectl get events --sort-by=.metadata.creationTimestamp`
  - em seu navegador usando a [interface do usuário da Web do amKubernetes (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Níveis de log

O AGIC tem 3 níveis de log. O nível 1 é o padrão e mostra o número mínimo de linhas de log.
O nível 5, por outro lado, exibiria todos os logs, incluindo o conteúdo corrigido da configuração aplicada ao ARM.

A Comunidade kubernetes estabeleceu nove níveis de registro em log para a ferramenta [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) . Neste repositório, estamos utilizando três deles, com semântica semelhante:


| Detalhamento | Descrição |
|-----------|-------------|
|  1        | Nível de log padrão; mostra detalhes de inicialização, avisos e erros |
|  3        | Informações estendidas sobre eventos e alterações; listas de objetos criados |
|  5        | Registra objetos empacotados; mostra a configuração JSON corrigida aplicada ao ARM |


Os níveis de detalhamento são ajustáveis por meio da variável `verbosityLevel` no arquivo [Helm-config. YAML](#sample-helm-config-file) . Aumente o nível de detalhes para `5` para obter a configuração JSON expedida para o [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview):
  - Adicionar `verbosityLevel: 5` em uma linha por si só em [Helm-config. YAML](#sample-helm-config-file) e reinstalar
  - obter logs com `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Arquivo de configuração Helm de exemplo
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

