---
title: Utilize os seus próprios certificados TLS de entrada com o cluster do Azure Kubernetes Service (AKS)
description: Saiba como instalar e configurar um controlador de entradas NGINX que utiliza os seus próprios certificados num cluster do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 4ba38ee0a4c26a99b7cbddc46eef35cfc39a511d
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392557"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Criar um controlador de entradas HTTPS e utilizar seus próprios certificados TLS no Azure Kubernetes Service (AKS)

Um controlador de entrada é uma parte do software que fornece o proxy inverso, encaminhamento de tráfego configurável e terminação de TLS para serviços do Kubernetes. Recursos de entrada do Kubernetes são utilizados para configurar as regras de entrada e as rotas para serviços individuais do Kubernetes. Utilizar um controlador de entrada e de regras de entrada, um único endereço IP pode servir-se para encaminhar o tráfego para vários serviços num cluster do Kubernetes.

Este artigo mostra-lhe como implementar o [controlador de entradas NGINX] [ nginx-ingress] num cluster do Azure Kubernetes Service (AKS). Gerar seus próprios certificados e crie um Kubernetes secreta para utilização com a rota de entrada. Por fim, duas aplicações são executadas no cluster do AKS, cada um deles está acessível através de um único endereço IP.

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Ativar o suplemento de encaminhamento de aplicação de HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
- Criar um controlador de entrada que utiliza a encriptar vamos para gerar automaticamente certificados TLS [com um endereço IP público dinâmico] [ aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza o Helm para instalar o controlador de entrada do NGINX e um exemplo de aplicação web. Tem de ter o Helm inicializado no seu cluster do AKS e utilizar uma conta de serviço para Tiller. Certifique-se de que está a utilizar a última versão do Helm. Para obter instruções de atualização, consulte a [Helm instalar docs][helm-install]. Para obter mais informações sobre como configurar e utilizar o Helm, consulte [instalar aplicações com Helm no Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer a execução da versão 2.0.64 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, utilize `Helm` para instalar *entrada de nginx*. Para maior redundância, as duas réplicas dos controladores de entrada de NGINX são implementadas com o `--set controller.replicaCount` parâmetro. Para beneficiar totalmente a execução de réplicas do controlador de entrada, certificar-se de que há mais de um nó no cluster do AKS.

O controlador de entrada também tem de ser agendado num nó de Linux. Nós do Windows Server (atualmente em pré-visualização no AKS) não deve ser executado o controlador de entrada. Um Seletor de nó é especificado com o `--set nodeSelector` parâmetro para informar o agendador de Kubernetes para executar o controlador de entrada do NGINX num nó baseado em Linux.

> [!TIP]
> O exemplo seguinte cria um espaço de nomes do Kubernetes para os recursos de entrada com o nome *básico de entrada*. Especifique um espaço de nomes para o seu próprio ambiente, conforme necessário. Se o cluster do AKS não RBAC ativado, adicione `--set rbac.create=false` para os comandos do Helm.

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

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Este endereço IP público é estático para o tempo de vida do controlador de entrada. Se eliminar o controlador de entrada, a atribuição de endereços IP pública é perdida. Se, em seguida, criar um controlador de entrada adicionais, é atribuído um novo endereço IP público. Se pretender manter a utilização do endereço IP público, em vez disso, pode [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, utilize o `kubectl get service` comando. Demora alguns minutos para que o endereço IP a ser atribuída ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Tome nota deste endereço IP público, como é usado no último passo para testar a implementação.

Não existem regras de entrada foram criadas ainda. Se navegar até o endereço IP público, é apresentada a página 404 do padrão do controlador de entrada NGINX.

## <a name="generate-tls-certificates"></a>Gerar certificados TLS

Neste artigo, vamos gerar um certificado autoassinado com `openssl`. Para utilização em produção, deve pedir um certificado fidedigno e assinado, por meio de um fornecedor ou a sua própria autoridade de certificação (AC). O próximo passo, vai gerar um Kubernetes *segredo* usando o certificado TLS e a chave privada gerada pelo OpenSSL.

O exemplo a seguir gera um RSA de 2048 bits X 509 certificado válido para 365 dias com o nome *aks-entrada-tls.crt*. É com o nome do ficheiro de chave privada *aks-entrada-tls.key*. Um segredo do Kubernetes TLS requer ambos os arquivos.

Este artigo funciona com o *demo.azure.com* nome comum da entidade e não precisam de ser alteradas. Para utilização em produção, especificar seus próprios valores organizacionais para o `-subj` parâmetro:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Criar segredo para o certificado TLS do Kubernetes

Para permitir que o Kubernetes utilizar o certificado TLS e a chave privada para o controlador de entrada, pode criar e utiliza um segredo. O segredo é definido uma vez e utiliza o certificado e o ficheiro de chave criada no passo anterior. Em seguida, referenciar este segredo ao definir rotas de entrada.

O exemplo seguinte cria um nome secreto *aks-entrada-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Um controlador de entrada e um segredo com o certificado foram configuradas. Agora vamos executadas dois demonstrar aplicativos no seu cluster do AKS. Neste exemplo, o Helm é utilizada para implementar duas instâncias de um aplicativo "Hello world" simples.

Antes de instalar os gráficos do Helm de exemplo, adicione o repositório de exemplos do Azure para o seu ambiente do Helm da seguinte forma:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie a primeira aplicação de demonstração de um gráfico Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Agora a instalar uma segunda instância do aplicativo de demonstração. Para a segunda instância, especifique um novo título para que as duas aplicações são visualmente distintas. Também especificar um nome de serviço único:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Ambos os aplicativos agora estão em execução no seu cluster do Kubernetes, no entanto, estes são configurados com um serviço do tipo `ClusterIP`. Como tal, os aplicativos não estão acessíveis a partir da internet. Para que fiquem disponíveis publicamente, crie um recurso de entrada do Kubernetes. O recurso de entrada configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo a seguir, o tráfego para o endereço `https://demo.azure.com/` é encaminhado para o serviço com o nome `aks-helloworld`. O tráfego para o endereço `https://demo.azure.com/hello-world-two` é encaminhado para o `ingress-demo` serviço. Neste artigo, não precisa alterar esses nomes de anfitrião de demonstração. Para utilização em produção, forneça os nomes especificados como parte do processo de pedido e a geração do certificado.

> [!TIP]
> Se o nome de anfitrião especificado durante o processo de pedido de certificado, o nome de NC, não corresponde ao anfitrião definido na sua rota de entrada, entrada controlador apresenta um *enganar o certificado do controlador de entrada Kubernetes*. Certifique-se a rota de certificado e a entrada de correspondência de nomes de anfitrião.

O *tls* secção informa a rota de entrada para utilizar o segredo com o nome *aks-entrada-tls* para o anfitrião *demo.azure.com*. Novamente, para utilização em produção, especifique o seu endereço de anfitrião.

Crie um ficheiro denominado `hello-world-ingress.yaml` e copie o exemplo seguinte YAML.

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

Crie o recurso de entrada com o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Para testar os certificados com o nosso falsificação *demo.azure.com* anfitrião, utilize `curl` e especifique o *– resolver* parâmetro. Este parâmetro permite que mapeie a *demo.azure.com* nome para o endereço IP público do seu controlador de entrada. Especifique o endereço IP público do seu próprio controlador de entrada, conforme mostrado no exemplo seguinte:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nenhum caminho adicional foi fornecido com o endereço, portanto, o controlador de entrada é predefinido para o */* rota. A primeira aplicação de demonstração é retornada, conforme mostrado no seguinte exemplo condensado:

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

O *- v* parâmetro na nossa `curl` comando produz informações verbosas, incluindo o certificado TLS recebido. Meio-maneira por meio de sua saída curl, pode verificar que o seu próprio certificado TLS foi utilizado. O *-k* parâmetro continua a carregar a página mesmo que estamos a utilizar um certificado autoassinado. O exemplo seguinte mostra que o *emissor: CN=demo.Azure.com; S = aks-entrada-tls* certificado foi usado:

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

Agora, adicione */hello-world-two* caminho para o endereço, tais como `https://demo.azure.com/hello-world-two`. O segundo aplicativo de demonstração com o título personalizado for retornado, conforme mostrado no seguinte exemplo condensado:

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

Este artigo usou o Helm para instalar os componentes de entrada e aplicações de exemplo. Quando implementa um gráfico Helm, um número de recursos do Kubernetes é criado. Esses recursos incluem pods, implementações e serviços. Para limpar estes recursos, pode optar por eliminar o espaço de nomes de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nomes de exemplo e todos os recursos

Para eliminar o espaço de nomes de exemplo completo, utilize o `kubectl delete` de comando e especifique o nome do espaço de nomes. Todos os recursos no espaço de nomes são eliminados.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repositório Helm para a aplicação hello world do AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais que criou. Lista o Helm versões com a `helm list` comando. Procure gráficos chamados *entrada de nginx* e *aks-helloworld*, conforme mostrado no seguinte exemplo:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Eliminar as versões com a `helm delete` comando. O exemplo seguinte elimina a implementação de entrada do NGINX e as dois exemplos AKS Olá mundo de aplicações.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Em seguida, remova o repositório Helm para a aplicação hello world do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que eram direcionadas para o tráfego para as aplicações de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Elimine o certificado segredo:

```console
kubectl delete secret aks-ingress-tls
```

Por fim, pode eliminar o próprio espaço de nomes. Utilize o `kubectl delete` de comando e especifique o nome do espaço de nomes:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo inclui alguns componentes externos ao AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de entradas do NGINX][nginx-ingress]

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Ativar o suplemento de encaminhamento de aplicação de HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
- Criar um controlador de entrada que utiliza a encriptar vamos para gerar automaticamente certificados TLS [com um endereço IP público dinâmico] [ aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
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
