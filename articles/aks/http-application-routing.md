---
title: Complemento de roteamento de aplicativo HTTP no serviço kubernetes do Azure (AKS)
description: Use o complemento de roteamento de aplicativo HTTP no serviço de kubernetes do Azure (AKS).
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: f0975d0a60081b66d3d5a513954deb0c4fa1b978
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851540"
---
# <a name="http-application-routing"></a>Encaminhamento de pedido de HTTP

A solução de roteamento de aplicativos HTTP facilita o acesso a aplicativos implantados em seu cluster AKS (serviço kubernetes do Azure). Quando a solução estiver habilitada, ela configurará um [controlador de entrada](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) em seu cluster AKs. À medida que os aplicativos são implantados, a solução também cria nomes DNS acessíveis publicamente para pontos de extremidade de aplicativo.

Quando o complemento está habilitado, ele cria uma zona DNS em sua assinatura. Para obter mais informações sobre o custo DNS, consulte [preços de DNS][dns-pricing].

> [!CAUTION]
> O complemento de roteamento de aplicativos HTTP foi projetado para permitir que você crie rapidamente um controlador de entrada e acesse seus aplicativos. Esse complemento não é recomendado para uso em produção. Para implantações de entrada prontas para produção que incluem várias réplicas e suporte a TLS, consulte [criar um controlador de entrada https](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>Visão geral da solução de roteamento HTTP

O complemento implanta dois componentes: um controlador de [entrada kubernetes][ingress] e um controlador [DNS externo][external-dns] .

- **Controlador de entrada**: O controlador de entrada é exposto à Internet usando um serviço kubernetes do tipo Balancer. O controlador de entrada observa e implementa [recursos de entrada do kubernetes][ingress-resource], que cria rotas para pontos de extremidade do aplicativo.
- **Controlador DNS externo**: Observa os recursos de entrada do kubernetes e cria registros DNS A na zona DNS específica do cluster.

## <a name="deploy-http-routing-cli"></a>Implantar roteamento HTTP: CLI

O complemento de roteamento de aplicativo HTTP pode ser habilitado com o CLI do Azure ao implantar um cluster AKS. Para fazer isso, use o comando [AZ AKs Create][az-aks-create] com o `--enable-addons` argumento.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Se você quiser habilitar vários Complementos, forneça-os como uma lista separada por vírgulas. Por exemplo, para habilitar o monitoramento e o roteamento de aplicativos HTTP, `--enable-addons http_application_routing,monitoring`use o formato.

Você também pode habilitar o roteamento HTTP em um cluster AKS existente usando o comando [AZ AKs Enable-addons][az-aks-enable-addons] . Para habilitar o roteamento http em um cluster existente, adicione `--addons` o parâmetro e especifique *http_application_routing* , conforme mostrado no exemplo a seguir:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Depois que o cluster for implantado ou atualizado, use o comando [AZ AKs show][az-aks-show] para recuperar o nome da zona DNS. Esse nome é necessário para implantar aplicativos no cluster AKS.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table

Result
-----------------------------------------------------
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Implantar roteamento HTTP: Portal

O complemento de roteamento de aplicativo HTTP pode ser habilitado por meio do portal do Azure ao implantar um cluster AKS.

![Habilitar o recurso de roteamento HTTP](media/http-routing/create.png)

Depois que o cluster for implantado, navegue até o grupo de recursos AKS criado automaticamente e selecione a zona DNS. Anote o nome da zona DNS. Esse nome é necessário para implantar aplicativos no cluster AKS.

![Obter o nome da zona DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Usar roteamento HTTP

A solução de roteamento de aplicativos HTTP só pode ser disparada em recursos de entrada que são anotados da seguinte maneira:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Crie um arquivo chamado **Samples-http-Application-Routing. YAML** e copie o YAML a seguir. Na linha 43, atualize `<CLUSTER_SPECIFIC_DNS_ZONE>` com o nome da zona DNS coletado na etapa anterior deste artigo.


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

Use o comando [kubectl Apply][kubectl-apply] para criar os recursos.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Use a rotação ou um navegador para navegar até o nome do host especificado na seção host do arquivo Samples-http-Application-Routing. YAML. O aplicativo pode levar até um minuto antes de estar disponível pela Internet.

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

## <a name="remove-http-routing"></a>Remover roteamento HTTP

A solução de roteamento HTTP pode ser removida usando o CLI do Azure. Para fazer isso, execute o comando a seguir, substituindo o nome do grupo de recursos e do cluster AKS.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Quando o complemento de roteamento de aplicativo HTTP está desabilitado, alguns recursos de kubernetes podem permanecer no cluster. Esses recursos incluem *configMaps* e *segredos*e são criados no namespace *Kube-System* . Para manter um cluster limpo, talvez você queira remover esses recursos.

Procure recursos de *Roteamento de aplicativo e addon-http* usando os seguintes comandos [Get kubectl][kubectl-get] :

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

A saída de exemplo a seguir mostra configMaps que deve ser excluído:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Para excluir recursos, use o comando [kubectl Delete][kubectl-delete] . Especifique o tipo de recurso, o nome do recurso e o namespace. O exemplo a seguir exclui um dos configmaps anteriores:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Repita a etapa `kubectl delete` anterior para todos os recursos de *Roteamento de aplicativo de complemento-http* que permaneceram em seu cluster.

## <a name="troubleshoot"></a>Resolução de problemas

Use o comando [kubectl logs][kubectl-logs] para exibir os logs de aplicativo para o aplicativo DNS externo. Os logs devem confirmar se um registro DNS A e TXT foram criados com êxito.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Esses registros também podem ser vistos no recurso de zona DNS no portal do Azure.

![Obter os registros DNS](media/http-routing/clippy.png)

Use o comando [kubectl logs][kubectl-logs] para exibir os logs de aplicativo para o controlador de entrada do nginx. Os logs devem confirmar o `CREATE` de um recurso de entrada e a recarga do controlador. Todas as atividades HTTP são registradas em log.

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

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como instalar um controlador de entrada protegido por HTTPS no AKS, consulte [entrada HTTPS no AKs (serviço kubernetes do Azure)][ingress-https].

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
