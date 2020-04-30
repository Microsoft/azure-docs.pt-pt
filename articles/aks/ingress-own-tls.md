---
title: Utilize os seus certificados TLS para ingresso
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de ingresso NGINX que utilize os seus próprios certificados num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: cce92f59e9a90c2993df964fa834e98cc837a397
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207382"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Criar um controlador de entrada HTTPS e utilizar os seus próprios certificados TLS no Azure Kubernetes Service (AKS)

Um controlador de ingresso é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para os serviços kubernetes. Os recursos de ingresso kubernetes são usados para configurar as regras e rotas de ingresso para serviços kubernetes individuais. Utilizando um controlador de ingresso e regras de ingresso, um único endereço IP pode ser usado para direcionar o tráfego para vários serviços em um cluster Kubernetes.

Este artigo mostra-lhe como implantar o [controlador de ingresso NGINX][nginx-ingress] num cluster do Serviço Azure Kubernetes (AKS). Você gera seus próprios certificados, e cria um segredo Kubernetes para uso com a rota de ingresso. Finalmente, duas aplicações são executadas no cluster AKS, cada uma das quais é acessível num único endereço IP.

Também pode:

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza o [Helm 3][helm] para instalar o controlador de entrada NGINX e uma aplicação web de amostra. Precisa de ter o Helm inicializado dentro do seu cluster AKS e usar uma conta de serviço para a Tiller. Certifique-se de que está a usar o último lançamento do Helm. Para obter instruções de atualização, consulte o [Helm instalar docs][helm-install]. Para obter mais informações sobre configurar e utilizar o Helm, consulte [Instalar aplicações com o Helm no Serviço Azure Kubernetes (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de ingresso

Para criar o controlador `Helm` de ingresso, utilize para instalar *nginx-ingress.* Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de ingresso, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo seguinte cria um espaço de nome Kubernetes para os recursos de ingresso *denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário. Se o seu cluster AKS não `--set rbac.create=false` estiver ativado por RBAC, adicione aos comandos Helm.

> [!TIP]
> Se quiser ativar a [preservação ip][client-source-ip] de origem do cliente `--set controller.service.externalTrafficPolicy=Local` para pedidos de contentores no seu cluster, adicione ao comando de instalação helm. O IP de origem do cliente é armazenado no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de ingresso com a preservação IP de origem do cliente ativada, a passagem do TLS não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante a instalação, é criado um endereço IP público Azure para o controlador de ingresso. Este endereço IP público é estático para o tempo de vida do controlador de ingresso. Se eliminar o controlador de ingresso, a atribuição de endereçoip público perde-se. Se criar um controlador adicional de ingresso, é atribuído um novo endereço IP público. Se desejar manter a utilização do endereço IP público, pode, em vez disso, criar um controlador de [ingresso com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP `kubectl get service` público, use o comando. Leva alguns minutos para que o endereço IP seja atribuído ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Tome nota deste endereço IP público, uma vez que é usado no último passo para testar a implementação.

Ainda não foram criadas regras de ingresso. Se navegar no endereço IP público, é apresentada a página padrão 404 do controlador de entrada NGINX.

## <a name="generate-tls-certificates"></a>Gerar certificados TLS

Para este artigo, vamos gerar um certificado `openssl`auto-assinado com . Para utilização da produção, deve solicitar um certificado assinado de confiança através de um fornecedor ou da sua própria autoridade de certificados (CA). No passo seguinte, gera um Kubernetes *Secret* usando o certificado TLS e chave privada gerada pela OpenSSL.

O exemplo seguinte gera um certificado RSA X509 de 2048 válido por 365 dias chamado *aks-ingress-tls.crt*. O ficheiro chave privado é chamado *aks-ingress-tls.key*. Um segredo Kubernetes TLS requer ambos os ficheiros.

Este artigo trabalha com o *demo.azure.com* tema comum e não precisa de ser alterado. Para utilização da produção, especifique os seus próprios valores organizacionais para o `-subj` parâmetro:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Criar segredo Kubernetes para o certificado TLS

Para permitir que a Kubernetes utilize o certificado TLS e a chave privada para o controlador de ingresso, cria e utiliza um Segredo. O segredo é definido uma vez, e utiliza o certificado e o ficheiro chave criado si no passo anterior. Referencia-se então este segredo quando define rotas de ingresso.

O exemplo seguinte cria um nome secreto *aks-ingress-tls:*

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Um controlador de ingresso e um Segredo com o seu certificado foram configurados. Agora vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, helm é usado para implementar dois casos de uma simples aplicação "Olá mundo".

Antes de poder instalar as tabelas Helm da amostra, adicione o repositório de amostras Azure ao seu ambiente Helm da seguinte forma:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie a primeira aplicação de demonstração a partir de um gráfico Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Agora instale uma segunda instância da aplicação de demonstração. Em segundo lugar, especifice um novo título para que as duas aplicações sejam visualmente distintas. Também especifica um nome de serviço único:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de ingresso

Ambas as aplicações estão agora a funcionar no seu cluster Kubernetes, no entanto estão configuradas com um serviço de tipo. `ClusterIP` Como tal, as aplicações não são acessíveis a partir da internet. Para torná-los disponíveis publicamente, crie um recurso de entrada Kubernetes. O recurso de ingresso configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o `https://demo.azure.com/` tráfego para o endereço `aks-helloworld`é encaminhado para o serviço denominado . O tráfego `https://demo.azure.com/hello-world-two` para o endereço `ingress-demo` é encaminhado para o serviço. Para este artigo, não precisas de mudar os nomes dos anfitriões da demonstração. Para utilização da produção, forneça os nomes especificados como parte do pedido de certificado e do processo de geração.

> [!TIP]
> Se o nome do anfitrião especificado durante o processo de pedido de certificado, o nome NC, não corresponder ao anfitrião definido na sua rota de ingresso, o controlador de entrada apresenta um aviso de Certificado falso do *Controlador de Entrada Kubernetes.* Certifique-se de que os nomes dos anfitriões do certificado e da rota de ingresso correspondem.

A secção *tls* diz a rota de ingresso para usar o Segredo chamado *aks-ingress-tls* para o hospedeiro *demo.azure.com*. Mais uma vez, para utilização da produção, especifique o seu próprio endereço de anfitrião.

Crie um `hello-world-ingress.yaml` ficheiro nomeado e copie no seguinte exemplo YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
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
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Crie o recurso `kubectl apply -f hello-world-ingress.yaml` de entrada utilizando o comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração da entrada

Para testar os certificados com o `curl` nosso anfitrião de *demo.azure.com* falso, use e especifique o parâmetro de *resolução.* Este parâmetro permite-lhe mapear o nome *demo.azure.com* para o endereço IP público do seu controlador de ingresso. Especifique o endereço IP público do seu próprio controlador de ingresso, como mostra o seguinte exemplo:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Não foi fornecido nenhum caminho adicional com o endereço, */* pelo que o controlador de entrada falha na rota. A primeira aplicação de demonstração é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

O parâmetro *-v* `curl` nas nossas saídas de comando verbosa informação, incluindo o certificado TLS recebido. A meio da sua saída de caracóis, pode verificar se o seu próprio certificado TLS foi usado. O parâmetro *-k* continua a carregar a página, mesmo estando a usar um certificado auto-assinado. O exemplo que se segue mostra que o *emitente: CN=demo.azure.com; Foi utilizado o certificado O=aks-ingress-tls:*

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

Agora adicione */olá-mundo-dois* caminho para `https://demo.azure.com/hello-world-two`o endereço, tais como . A segunda aplicação de demonstração com o título personalizado é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de ingresso e aplicações de amostragem. Quando se implanta um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço `kubectl delete` de nome da amostra, utilize o comando e especifique o nome do seu espaço de nome. Todos os recursos no espaço de nome são eliminados.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repo Helm para a app aks olá mundo:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais criados. Lista o Helm liberta `helm list` com o comando. Procure gráficos *chamados nginx-ingress* e *aks-helloworld,* como mostra a seguinte saída exemplo:

```
$ helm list

NAME               REVISION    UPDATED                     STATUS      CHART                   APP VERSION    NAMESPACE
virulent-seal      1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0         kube-system
billowing-guppy    1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                   default
listless-quokka    1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                   default
```

Elimine os lançamentos com o `helm delete` comando. O exemplo seguinte elimina a implementação de ingressos NGINX e as duas aplicações aks olá world.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Em seguida, remova o repo Helm para a app aks olá world:

```console
helm repo remove azure-samples
```

Remova a rota de ingresso que direcionou o tráfego para as aplicações da amostra:

```console
kubectl delete -f hello-world-ingress.yaml
```

Eliminar o certificado Segredo:

```console
kubectl delete secret aks-ingress-tls
```

Finalmente, pode eliminar o espaço de nome si mesmo. Utilize `kubectl delete` o comando e especifique o nome do seu espaço de nome:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos seguintes

Este artigo incluía alguns componentes externos para a AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de ingresso NGINX][nginx-ingress]

Também pode:

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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
