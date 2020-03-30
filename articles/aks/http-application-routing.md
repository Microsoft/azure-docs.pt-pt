---
title: Suplemento de encaminhamento de aplicações HTTP no Serviço Azure Kubernetes (AKS)
description: Utilize o complemento de encaminhamento de aplicações HTTP no Serviço Azure Kubernetes (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: 5794e46f10abec1ceef4c79a881f7f353e729634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595693"
---
# <a name="http-application-routing"></a>Encaminhamento de aplicações de HTTP

A solução de encaminhamento de aplicações HTTP facilita o acesso a aplicações que são implantadas para o seu cluster Azure Kubernetes Service (AKS). Quando a solução está ativada, ele confunde um [controlador Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) no seu cluster AKS. À medida que as aplicações são implementadas, a solução também cria nomes dNS acessíveis ao público para pontos finais de aplicação.

Quando o addon está ativado, cria uma Zona DNS na sua subscrição. Para obter mais informações sobre o custo do DNS, consulte [os preços do DNS][dns-pricing].

> [!CAUTION]
> O addon de encaminhamento de aplicações HTTP foi concebido para permitir criar rapidamente um controlador de entrada e aceder às suas aplicações. Este complemento não é recomendado para a utilização da produção. Para implementações de ingressos prontos para a produção que incluam múltiplas réplicas e suporte TLS, consulte [Criar um controlador de entrada HTTPS](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>VISÃO geral da solução de encaminhamento http

O addon implementa dois componentes: um [controlador Kubernetes Ingress][ingress] e um controlador [External-DNS.][external-dns]

- **Controlador de ingressos**: O controlador Ingress é exposto à internet utilizando um serviço Kubernetes do tipo LoadBalancer. O controlador Ingress observa e implementa [os recursos da Kubernetes Ingress,][ingress-resource]que cria rotas para pontos finais de aplicação.
- **Controlador Externo-DNS**: Relógios para os recursos da Kubernetes Ingress e cria registos DNS A na zona DNS específica do cluster.

## <a name="deploy-http-routing-cli"></a>Implementar encaminhamento HTTP: CLI

O complemento de encaminhamento de aplicações HTTP pode ser ativado com o AZURE CLI ao implantar um cluster AKS. Para tal, use as [aks az][az-aks-create] `--enable-addons` criar comando com o argumento.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Se quiser ativar múltiplos addons, forneça-os como uma lista separada da vírvia. Por exemplo, para permitir o encaminhamento e `--enable-addons http_application_routing,monitoring`monitorização da aplicação HTTP, utilize o formato .

Também pode ativar o encaminhamento http num cluster AKS existente utilizando o comando [az aks enable-addons.][az-aks-enable-addons] Para permitir o encaminhamento http num `--addons` cluster existente, adicione o parâmetro e especifique *http_application_routing* conforme mostrado no seguinte exemplo:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Depois de o cluster ser implantado ou atualizado, utilize o comando [az aks para][az-aks-show] recuperar o nome da zona DNS. Este nome é necessário para implementar aplicações para o cluster AKS.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Resultado

9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io


## <a name="deploy-http-routing-portal"></a>Implementar o encaminhamento HTTP: Portal

O add-on de encaminhamento de aplicações HTTP pode ser ativado através do portal Azure ao implantar um cluster AKS.

![Ativar a função de encaminhamento HTTP](media/http-routing/create.png)

Depois de o cluster ser implantado, navegue para o grupo de recursos AKS criado automaticamente e selecione a zona DNS. Tome nota do nome da zona DNS. Este nome é necessário para implementar aplicações para o cluster AKS.

![Obtenha o nome da zona DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Utilizar o encaminhamento HTTP

A solução de encaminhamento de aplicações HTTP só pode ser desencadeada em recursos Ingress que sejam anotados da seguinte forma:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Crie um ficheiro chamado **samples-http-application-routing.yaml** e copie no seguinte YAML. Na linha 43, atualize `<CLUSTER_SPECIFIC_DNS_ZONE>` com o nome de zona DNS recolhido na fase anterior deste artigo.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Use o [kubectl aplicar][kubectl-apply] o comando para criar os recursos.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Utilize cURL ou um navegador para navegar para o nome de anfitrião especificado na secção anfitriã do ficheiro samples-http-application-routing.yaml. A aplicação pode demorar até um minuto antes de estar disponível através da internet.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>Remover o encaminhamento HTTP

A solução de encaminhamento HTTP pode ser removida utilizando o Azure CLI. Para tal, execute o seguinte comando, substituindo o seu cluster AKS e o nome do grupo de recursos.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Quando o complemento de encaminhamento de aplicação HTTP é desativado, alguns recursos kubernetes podem permanecer no cluster. Estes recursos incluem *configMaps* e *segredos*, e são criados no espaço de nome *do sistema kube.* Para manter um cluster limpo, pode querer remover estes recursos.

Procure recursos *adicionais-http-aplicação-routing* utilizando os seguintes [comandos kubectl get:][kubectl-get]

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

A saída de exemplo a seguir mostra configMaps que deve ser eliminado:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Para eliminar recursos, utilize o [comando de eliminação kubectl.][kubectl-delete] Especifique o tipo de recurso, o nome do recurso e o espaço de nome. O exemplo que se segue elimina um dos mapas anteriores:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Repita o `kubectl delete` passo anterior para todos os recursos de *encaminhamento de aplicações addon-http-aplicação* que permaneceram no seu cluster.

## <a name="troubleshoot"></a>Resolução de problemas

Utilize o comando de [registos kubectl][kubectl-logs] para visualizar os registos de aplicação para a aplicação External-DNS. Os registos devem confirmar que um disco DNS A e TXT foi criado com sucesso.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Estes registos também podem ser vistos no recurso da zona DNS no portal Azure.

![Obtenha os registos do DNS](media/http-routing/clippy.png)

Utilize o comando de [registos kubectl][kubectl-logs] para visualizar os registos de aplicação do controlador Nginx Ingress. Os registos devem `CREATE` confirmar o de um recurso Ingress e a recarga do controlador. Toda a atividade http está registada.

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
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Limpeza

Remova os objetos kubernetes associados criados neste artigo.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como instalar um controlador De Entrada protegido em HTTPS no AKS, consulte [HTTPS Ingress no Serviço Azure Kubernetes (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
