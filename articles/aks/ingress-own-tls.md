---
title: Usar seus próprios certificados TLS para entrada com o cluster do serviço de kubernetes do Azure (AKS)
description: Saiba como instalar e configurar um controlador de entrada do NGINX que usa seus próprios certificados em um cluster do AKS (serviço de kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 2b30ade9971ede6f9544b618504033553392e9bd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615442"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Criar um controlador de entrada HTTPS e usar seus próprios certificados TLS no serviço kubernetes do Azure (AKS)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação de TLS para serviços Kubernetess. Os recursos de entrada do kubernetes são usados para configurar as regras de entrada e rotas para serviços individuais do kubernetes. Usando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para rotear o tráfego para vários serviços em um cluster kubernetes.

Este artigo mostra como implantar o controlador de [entrada Nginx][nginx-ingress] em um cluster do AKS (serviço kubernetes do Azure). Você gera seus próprios certificados e cria um segredo kubernetes para uso com a rota de entrada. Por fim, dois aplicativos são executados no cluster AKS, cada um deles acessível por um único endereço IP.

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
- Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa Helm para instalar o controlador de entrada do NGINX e um aplicativo Web de exemplo. Você precisa ter o Helm inicializado em seu cluster AKS e usar uma conta de serviço para o gaveta. Verifique se você está usando a versão mais recente do Helm. Para obter instruções de atualização, consulte o [Helm instalar documentos][helm-install]. Para obter mais informações sobre como configurar e usar o Helm, consulte [instalar aplicativos com Helm no serviço kubernetes do Azure (AKs)][use-helm].

Este artigo também requer que você esteja executando o CLI do Azure versão 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, use `Helm` para instalar o *Nginx-Ingres*. Para redundância adicional, duas réplicas dos controladores de entrada do Nginx são implantadas com `--set controller.replicaCount` o parâmetro. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó no cluster AKS.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server (atualmente em visualização no AKS) não devem executar o controlador de entrada. Um seletor de nó é especificado `--set nodeSelector` usando o parâmetro para instruir o Agendador kubernetes a executar o controlador de entrada Nginx em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamados *ingress-Basic*. Especifique um namespace para seu próprio ambiente, conforme necessário. Se o cluster AKs não estiver habilitado para RBAC, `--set rbac.create=false` adicione aos comandos Helm.

> [!TIP]
> Se você quiser habilitar a [preservação de IP de origem do cliente][client-source-ip] para solicitações a contêineres em seu `--set controller.service.externalTrafficPolicy=Local` cluster, adicione ao comando Helm install. O IP de origem do cliente é armazenado no cabeçalho da solicitação em *X-forwardd-for*. Ao usar um controlador de entrada com preservação de IP de origem do cliente habilitada, a passagem SSL não funcionará.

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

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Esse endereço IP público é estático para o período de vida útil do controlador de entrada. Se você excluir o controlador de entrada, a atribuição de endereço IP público será perdida. Se você criar um controlador de entrada adicional, um novo endereço IP público será atribuído. Se você quiser manter o uso do endereço IP público, poderá [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use o `kubectl get service` comando. Leva alguns minutos para que o endereço IP seja atribuído ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Anote esse endereço IP público, pois ele é usado na última etapa para testar a implantação.

Nenhuma regra de entrada foi criada ainda. Se você navegar até o endereço IP público, a página padrão 404 do controlador de entrada do NGINX será exibida.

## <a name="generate-tls-certificates"></a>Gerar certificados TLS

Para este artigo, vamos gerar um certificado autoassinado com `openssl`. Para uso em produção, você deve solicitar um certificado assinado e confiável por meio de um provedor ou sua própria autoridade de certificação (CA). Na próxima etapa, você gerará um *segredo* kubernetes usando o certificado TLS e a chave privada gerada pelo OpenSSL.

O exemplo a seguir gera um certificado X509 RSA de 2048 bits válido para 365 dias denominados *AKs-ingress-TLS. CRT*. O arquivo de chave privada é denominado *AKs-ingress-TLS. Key*. Um segredo do kubernetes TLS requer esses dois arquivos.

Este artigo funciona com o nome comum da entidade *demo.Azure.com* e não precisa ser alterado. Para uso em produção, especifique seus próprios valores organizacionais `-subj` para o parâmetro:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Criar o segredo kubernetes para o certificado TLS

Para permitir que o kubernetes use o certificado TLS e a chave privada para o controlador de entrada, você cria e usa um segredo. O segredo é definido uma vez e usa o certificado e o arquivo de chave criados na etapa anterior. Em seguida, você faz referência a esse segredo ao definir rotas de entrada.

O exemplo a seguir cria um nome secreto *AKs-ingress-TLS*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Um controlador de entrada e um segredo com seu certificado foram configurados. Agora, vamos executar dois aplicativos de demonstração em seu cluster AKS. Neste exemplo, Helm é usado para implantar duas instâncias de um aplicativo "Hello World" simples.

Antes de instalar os gráficos de exemplo do Helm, adicione o repositório de exemplos do Azure ao seu ambiente Helm da seguinte maneira:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie o primeiro aplicativo de demonstração de um gráfico do Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Agora, instale uma segunda instância do aplicativo de demonstração. Para a segunda instância, você especifica um novo título para que os dois aplicativos sejam visualmente distintos. Você também especifica um nome de serviço exclusivo:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Os dois aplicativos agora estão em execução no cluster kubernetes, no entanto, eles são configurados com um serviço do tipo `ClusterIP`. Assim, os aplicativos não são acessíveis pela Internet. Para torná-los publicamente disponíveis, crie um recurso de entrada do kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o `https://demo.azure.com/` endereço é roteado para o `aks-helloworld`serviço chamado. O tráfego para o `https://demo.azure.com/hello-world-two` endereço é roteado `ingress-demo` para o serviço. Para este artigo, você não precisa alterar os nomes de host de demonstração. Para uso em produção, forneça os nomes especificados como parte do processo de solicitação e geração de certificado.

> [!TIP]
> Se o nome do host especificado durante o processo de solicitação de certificado, o nome CN não corresponder ao host definido em sua rota de entrada, o controlador de entrada exibirá um *certificado falso do controlador de entrada do kubernetes*. Verifique se os nomes de host do certificado e da rota de entrada correspondem.

A seção *TLS* informa a rota de entrada para usar o segredo chamado *AKs-ingress-TLS* para o host *demo.Azure.com*. Novamente, para uso em produção, especifique seu próprio endereço de host.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie no exemplo a seguir YAML.

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

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Para testar os certificados com nosso host *demo.Azure.com* falso, use `curl` e especifique o parâmetro *--resolve* . Esse parâmetro permite mapear o nome do *demo.Azure.com* para o endereço IP público do controlador de entrada. Especifique o endereço IP público de seu próprio controlador de entrada, conforme mostrado no exemplo a seguir:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nenhum caminho adicional foi fornecido com o endereço, portanto, o controlador de entrada usa como padrão */* a rota. O primeiro aplicativo de demonstração é retornado, conforme mostrado na seguinte saída de exemplo condensada:

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

O parâmetro *-v* em nosso `curl` comando gera informações detalhadas, incluindo o certificado TLS recebido. Meio-caminho por meio de sua saída de ondulação, você pode verificar se o seu próprio certificado TLS foi usado. O parâmetro *-k* continua carregando a página, embora estejamos usando um certificado autoassinado. O exemplo a seguir mostra que *o emissor: CN = demo. Azure. com; O = AKs-ingresso do certificado TLS* foi usado:

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

Agora, adicione o caminho */Hello-World-Two* ao endereço, como `https://demo.azure.com/hello-world-two`. O segundo aplicativo de demonstração com o título personalizado é retornado, conforme mostrado na seguinte saída de exemplo condensada:

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

Este artigo usou o Helm para instalar os componentes de entrada e aplicativos de exemplo. Quando você implanta um gráfico do Helm, vários recursos do kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir o namespace de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Excluir o namespace de exemplo e todos os recursos

Para excluir o namespace de exemplo inteiro, use `kubectl delete` o comando e especifique o nome do namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Como alternativa, uma abordagem mais granular é excluir os recursos individuais criados. Liste as versões Helm com `helm list` o comando. Procure gráficos chamados *Nginx-ingress* e *AKs-HelloWorld*, conforme mostrado na seguinte saída de exemplo:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Exclua as versões com `helm delete` o comando. O exemplo a seguir exclui a implantação de entrada do NGINX e os dois aplicativos de exemplo do AKS Hello World.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que direcionou o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Exclua o segredo do certificado:

```console
kubectl delete secret aks-ingress-tls
```

Por fim, você pode excluir o namespace em si. Use o `kubectl delete` comando e especifique o nome do namespace:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo incluía alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada do NGINX][nginx-ingress]

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
- Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

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
[client-source-ip]: concepts-network.md#ingress-controllers