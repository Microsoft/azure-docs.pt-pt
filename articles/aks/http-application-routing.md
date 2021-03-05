---
title: Complemento de encaminhamento de aplicações HTTP no Serviço Azure Kubernetes (AKS)
description: Utilize o addon de encaminhamento de aplicações HTTP para aceder às aplicações implantadas no Serviço Azure Kubernetes (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: 25fc021a48e8936f242df35f7485fc59a93bba13
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172805"
---
# <a name="http-application-routing"></a>Encaminhamento de aplicações de HTTP

A solução de encaminhamento de aplicações HTTP facilita o acesso às aplicações que são implantadas no seu cluster Azure Kubernetes Service (AKS). Quando a solução está ativada, configura um [controlador Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) no seu cluster AKS. À medida que as aplicações são implementadas, a solução também cria nomes DNS acessíveis ao público para os pontos finais de aplicação.

Quando o addon está ativado, cria uma Zona DNS na sua subscrição. Para obter mais informações sobre o custo do DNS, consulte [os preços dns][dns-pricing].

> [!CAUTION]
> O addon de encaminhamento de aplicações HTTP foi concebido para permitir criar rapidamente um controlador de entrada e aceder às suas aplicações. Este complemento não foi atualmente concebido para ser utilizado num ambiente de produção e não é recomendado para utilização de produção. Para implementações de entrada prontas para a produção que incluam múltiplas réplicas e suporte TLS, consulte [Criar um controlador de entrada HTTPS](./ingress-tls.md).

## <a name="http-routing-solution-overview"></a>Visão geral da solução de encaminhamento HTTP

O addon implementa dois componentes: um [controlador Kubernetes Ingress][ingress] e um controlador [external-DNS.][external-dns]

- **Controlador ingress**: O controlador Ingress está exposto à internet utilizando um serviço Kubernetes do tipo LoadBalancer. O controlador Ingress observa e implementa [os recursos da Kubernetes Ingress,][ingress-resource]que cria rotas para os pontos finais de aplicação.
- **Controlador externo-DNS**: Observa os recursos da Kubernetes Ingress e cria registos DNS A na zona de DNS específica do cluster.

## <a name="deploy-http-routing-cli"></a>Implementar encaminhamento HTTP: CLI

O addon de encaminhamento de aplicações HTTP pode ser ativado com o Azure CLI ao implementar um cluster AKS. Para tal, use os [az aks criar][az-aks-create] comando com o `--enable-addons` argumento.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Se pretender ativar vários addons, forneça-os como uma lista separada por vírgula. Por exemplo, para permitir o encaminhamento e monitorização da aplicação HTTP, utilize o formato `--enable-addons http_application_routing,monitoring` .

Também pode ativar o encaminhamento HTTP num cluster AKS existente utilizando o comando [az aks enable-addons.][az-aks-enable-addons] Para ativar o encaminhamento HTTP num cluster existente, adicione o `--addons` parâmetro e especifique *http_application_routing* como mostrado no seguinte exemplo:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Depois de o cluster ser implantado ou atualizado, utilize o comando [de exibição az aks][az-aks-show] para recuperar o nome da zona DENS.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Este nome é necessário para implementar aplicações no cluster AKS e é mostrado na seguinte saída de exemplo:

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Implementar encaminhamento HTTP: Portal

O addon de encaminhamento de aplicações HTTP pode ser ativado através do portal Azure ao implementar um cluster AKS.

![Ativar a função de encaminhamento HTTP](media/http-routing/create.png)

Depois de o cluster ser implantado, navegue pelo grupo de recursos AKS criado automaticamente e selecione a zona DNS. Tome nota do nome da zona DNS. Este nome é necessário para implementar aplicações no cluster AKS.

![Obtenha o nome da zona DNS](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>Ligue-se ao seu cluster AKS

Para ligar ao cluster de Kubernetes a partir do computador local, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes.

Se utilizar o Azure Cloud Shell, o `kubectl` já está instalado. Também pode instalá-lo a nível local com o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][]. O exemplo a seguir obtém credenciais para o cluster AKS chamado *MyAKSCluster* no *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>Utilizar o encaminhamento HTTP

A solução de encaminhamento de aplicações HTTP só pode ser acionada em recursos Ingress que são anotados da seguinte forma:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Crie um ficheiro denominado **samples-http-application-encaminhamento.yaml** e copie no yaML seguinte. Na linha 43, atualização `<CLUSTER_SPECIFIC_DNS_ZONE>` com o nome da zona DNS recolhido no passo anterior deste artigo.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

Utilize o [comando de aplicação de kubectl][kubectl-apply] para criar os recursos.

```bash
kubectl apply -f samples-http-application-routing.yaml
```

O exemplo a seguir mostra os recursos criados:

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Abra um navegador web para *aks-helloworld \<CLUSTER_SPECIFIC_DNS_ZONE\>* *aks-helloworld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io.* O pedido pode demorar alguns minutos a aparecer.

## <a name="remove-http-routing"></a>Remover encaminhamento HTTP

A solução de encaminhamento HTTP pode ser removida utilizando o Azure CLI. Para tal, executar o seguinte comando, substituindo o seu cluster AKS e o nome do grupo de recursos.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Quando o addon de encaminhamento de aplicações HTTP é desativado, alguns recursos kubernetes podem permanecer no cluster. Estes recursos incluem *configMaps* e *segredos*, e são criados no *espaço de nomes do sistema kube.* Para manter um aglomerado limpo, é melhor remover estes recursos.

Procure recursos *de encaminhamento de aplicações addon-http utilizando* os seguintes [comandos kubectl:][kubectl-get]

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

A saída de exemplo a seguir mostra configMaps que devem ser eliminados:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Para eliminar recursos, utilize o comando [de eliminação de kubectl.][kubectl-delete] Especifique o tipo de recurso, o nome do recurso e o espaço de nome. O exemplo a seguir elimina um dos configmaps anteriores:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Repita o passo anterior `kubectl delete` para todos os recursos de *encaminhamento de aplicações addon-http-aplicação* que permaneceram no seu cluster.

## <a name="troubleshoot"></a>Resolução de problemas

Utilize o comando [de registos kubectl][kubectl-logs] para visualizar os registos de aplicação para a aplicação External-DNS. Os registos devem confirmar que um registo de DNS A e TXT foi criado com sucesso.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Estes registos também podem ser vistos no recurso da zona DNS no portal Azure.

![Obtenha os registos DNS](media/http-routing/clippy.png)

Utilize o comando [de registos kubectl][kubectl-logs] para visualizar os registos de aplicação para o comando Nginx Ingress. Os registos devem confirmar o `CREATE` recurso Ingress e a recarga do controlador. Toda a atividade HTTP está registada.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Limpeza

Remova os objetos Kubernetes associados criados neste artigo utilizando `kubectl delete` .

```bash
kubectl delete -f samples-http-application-routing.yaml
```

A saída do exemplo mostra que os objetos Kubernetes foram removidos.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como instalar um controlador Ingress protegido pelo HTTPS em AKS, consulte [HTTPS Ingress no Serviço Azure Kubernetes (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
