---
title: Utilize os seus certificados TLS para entrada
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de entrada NGINX que utiliza os seus próprios certificados num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: ee196bd749ad5821a1855d4549b22698c724b3f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104931"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Criar um controlador de entrada HTTPS e utilizar os seus próprios certificados TLS no Azure Kubernetes Service (AKS)

Um controlador de entrada é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para serviços Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras e rotas de entrada para serviços individuais de Kubernetes. Utilizando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para encaminhar o tráfego para vários serviços num cluster kubernetes.

Este artigo mostra-lhe como implantar o [controlador de entrada NGINX][nginx-ingress] num cluster Azure Kubernetes Service (AKS). Você gera os seus próprios certificados, e cria um segredo kubernetes para uso com a rota de entrada. Finalmente, duas aplicações são executadas no cluster AKS, cada uma das quais é acessível sobre um único endereço IP.

Também pode:

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- Crie um controlador ingress que utilize o Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza [o Helm 3][helm] para instalar o controlador de entrada NGINX. Certifique-se de que está a utilizar a mais recente versão do Helm e tenha acesso ao repositório de Helm *ingresss-nginx.* Para obter instruções de atualização, consulte os [docs de instalação helm][helm-install]. Para obter mais informações sobre a configuração e utilização do Helm, consulte [instalar aplicações com Helm in Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, utilize `Helm` para instalar a entrada de *nginx*. Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para beneficiar totalmente da execução de réplicas do controlador de entrada, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo a seguir cria um espaço de nome Kubernetes para os recursos *ingressos denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário. Se o seu cluster AKS não estiver ativado por Kubernetes RBAC, adicione `--set rbac.create=false` aos comandos Helm.

> [!TIP]
> Se pretender permitir a [preservação ip da fonte do cliente][client-source-ip] para pedidos a contentores no seu cluster, adicione ao comando de `--set controller.service.externalTrafficPolicy=Local` instalação Helm. A FONTE DO CLIENTE IP é armazenada no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de entrada com a preservação IP de fonte do cliente ativada, o passe do TLS não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante a instalação, é criado um endereço IP público Azure para o controlador de entrada. Este endereço IP público é estático para o tempo de vida do controlador de entrada. Se eliminar o controlador de entrada, perde-se a atribuição do endereço IP público. Se então criar um controlador adicional de entrada, será atribuído um novo endereço IP público. Se desejar reter a utilização do endereço IP público, pode, em vez disso, [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use o `kubectl get service` comando.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Demora alguns minutos para que o endereço IP seja atribuído ao serviço.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Tome nota deste endereço IP público, uma vez que é usado no último passo para testar a implementação.

Ainda não foram criadas regras ingressas. Se navegar no endereço IP público, a página padrão 404 do controlador NGINX é apresentada.

## <a name="generate-tls-certificates"></a>Gerar certificados TLS

Para este artigo, vamos gerar um certificado auto-assinado com `openssl` . Para uso de produção, deverá solicitar um certificado de confiança assinado através de um fornecedor ou da sua própria autoridade de certificados (CA). No passo seguinte, gera um Kubernetes *Secret* utilizando o certificado TLS e a chave privada gerada pela OpenSSL.

O exemplo a seguir gera um certificado RSA X509 de 2048 válido por 365 dias denominado *aks-ingress-tls.crt*. O ficheiro de chave privada *chama-se aks-ingress-tls.key*. Um segredo do Kubernetes TLS requer ambos estes ficheiros.

Este artigo funciona com o *demo.azure.com* nome comum e não precisa de ser alterado. Para uso de produção, especifique os seus próprios valores organizacionais para o `-subj` parâmetro:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Criar segredo Kubernetes para o certificado TLS

Para permitir que kubernetes utilize o certificado TLS e a chave privada para o controlador de entrada, você cria e usa um Segredo. O segredo é definido uma vez, e usa o certificado e o ficheiro chave criado no passo anterior. Em seguida, referencia este segredo quando define rotas de entrada.

O exemplo a seguir cria um nome secreto *aks-ingress-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Um controlador de entrada e um segredo com o seu certificado foram configurados. Agora vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, Helm é usado para implementar dois casos de uma simples aplicação "Olá mundo".

Para ver o controlador de entrada em ação, execute duas aplicações de demonstração no seu cluster AKS. Neste exemplo, você usa `kubectl apply` para implementar dois casos de uma simples aplicação *Hello world.*

Crie um ficheiro *aks-helloworld.yaml* e copie no seguinte exemplo YAML:

```yml
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
```

Crie um ficheiro *ingress-demo.yaml* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Executar as duas aplicações de demonstração `kubectl apply` utilizando:

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Ambas as aplicações estão agora a ser executadas no seu cluster Kubernetes, no entanto estão configuradas com um serviço de tipo `ClusterIP` . Como tal, as aplicações não são acessíveis a partir da internet. Para torná-los disponíveis ao público, crie um recurso de entrada kubernetes. O recurso ingressa configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para o endereço `https://demo.azure.com/` é encaminhado para o serviço denominado `aks-helloworld` . O tráfego para o endereço `https://demo.azure.com/hello-world-two` é encaminhado para o `ingress-demo` serviço. Para este artigo, não precisas de mudar os nomes dos anfitriões da demonstração. Para utilização na produção, forneça os nomes especificados como parte do pedido de certificado e processo de geração.

> [!TIP]
> Se o nome do anfitrião especificado durante o processo de pedido de certificado, o nome CN, não corresponder ao anfitrião definido na sua rota de entrada, o controlador de entrada apresenta um aviso *de Certificado Falso do Controlador De Entrada Kubernetes.* Certifique-se de que o seu certificado e os nomes dos anfitriões da rota de entrada correspondem.

A secção *tls* diz à rota de entrada para usar o Segredo chamado *aks-ingress-tls* para o anfitrião *demo.azure.com*. Mais uma vez, para uso de produção, especifique o seu próprio endereço de anfitrião.

Crie um ficheiro nomeado `hello-world-ingress.yaml` e copie no seguinte exemplo YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Crie o recurso de entrada utilizando o `kubectl apply -f hello-world-ingress.yaml` comando.

```console
kubectl apply -f hello-world-ingress.yaml
```

A saída de exemplo mostra que o recurso de entrada é criado.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Para testar os certificados com o nosso anfitrião *de demo.azure.com* falso, use `curl` e especifique o parâmetro *de resolução.* Este parâmetro permite mapear o *nome demo.azure.com* para o endereço IP público do seu controlador de entrada. Especifique o endereço IP público do seu próprio controlador de entrada, como mostra o seguinte exemplo:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Não foi fornecido nenhum caminho adicional com o endereço, pelo que o controlador de entrada não tem padrão para a */* rota. A primeira aplicação de demonstração é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

O parâmetro *-v* no nosso `curl` comando produz informações verbosas, incluindo o certificado TLS recebido. A meio da sua saída de caracóis, pode verificar se o seu próprio certificado TLS foi usado. O parâmetro *-k* continua a carregar a página, mesmo estando a usar um certificado auto-assinado. O exemplo a seguir mostra que o *emitente: CN=demo.azure.com; Foi utilizado o certificado O=aks-ingress-tls:*

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Adicione *agora /olá-mundo-dois* caminho ao endereço, como `https://demo.azure.com/hello-world-two` . A segunda aplicação de demonstração com o título personalizado é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Limpar os recursos

Este artigo usou o Helm para instalar os componentes de entrada e as aplicações de amostra. Quando se implementa um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço de nome da amostra, use o `kubectl delete` comando e especifique o nome do seu nome de espaço. Todos os recursos no espaço de nomes são eliminados.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular consiste em eliminar os recursos individuais criados. Lista os lançamentos do Leme com o `helm list` comando. 

```console
helm list --namespace ingress-basic
```

Procure um gráfico nomeado *nginx-ingress* como mostrado na saída do exemplo seguinte:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Desinstale os desbloqueios com o `helm uninstall` comando. 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

O exemplo a seguir desinstala a implantação de entrada NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Em seguida, remova as duas aplicações da amostra:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Remova a rota de entrada que direcionou o tráfego para as aplicações da amostra:

```console
kubectl delete -f hello-world-ingress.yaml
```

Eliminar o certificado Secret:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Finalmente, pode apagar o espaço de nome em si. Utilize o `kubectl delete` comando e especifique o nome do seu nome de espaço de nome:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos seguintes

Este artigo incluiu alguns componentes externos para a AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de entrada NGINX][nginx-ingress]

Também pode:

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- Crie um controlador ingress que utilize o Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
