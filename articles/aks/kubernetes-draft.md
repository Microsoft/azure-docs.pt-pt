---
title: Utilizar o Draft com o AKS e registo de contentor do Azure
description: Utilizar o Draft com o AKS e registo de contentor do Azure
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: 5169b8856155df5e62b1e85d291ce6c1b54c8a87
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011944"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Utilizar o Draft com o serviço Kubernetes do Azure (AKS)

Rascunho é uma ferramenta open-source que ajuda o pacote e implementa contentores da aplicação num cluster do Kubernetes, deixando-o gratuitamente para se concentrar no ciclo de desenvolvimento - o "loop interno" de desenvolvimento concentrarem. O draft funciona, como o código está a ser desenvolvido, mas antes de consolidar ao controle de versão. Com o Draft, pode rapidamente voltar a implementar uma aplicação no Kubernetes à medida que ocorrem alterações de código. Para obter mais informações sobre o rascunho, consulte a [rascunho da documentação no GitHub][draft-documentation].

Este artigo mostra-lhe como utilizar o Draft com um cluster de Kubernetes no AKS.

## <a name="prerequisites"></a>Pré-requisitos

Os passos detalhados neste artigo partem do princípio de que já criou um cluster do AKS e estabeleceu uma `kubectl` ligação com o cluster. Se precisar destes itens, consulte a [início rápido do AKS][aks-quickstart].

Precisa de um registo privado do Docker no Azure Container Registry (ACR). Para obter passos sobre como criar uma instância do ACR, consulte a [início rápido do Azure Container Registry][acr-quickstart].

Helm também tem de estar instalado no seu cluster do AKS. Para obter mais informações sobre como instalar e configurar o Helm, consulte [Helm de utilização com o Azure Kubernetes Service (AKS)][aks-helm].

Por fim, tem de instalar [Docker](https://www.docker.com).

## <a name="install-draft"></a>Instalar o Draft

A CLI de rascunho é um cliente que é executado no sistema de desenvolvimento e permite-lhe implementar código num cluster do Kubernetes. Para instalar a CLI de rascunho num Mac, utilize `brew`. Para opções de instalação adicionais, consulte a [guia de instalação do Draft][draft-documentation].

> [!NOTE]
> Se tiver instalado o rascunho anteriores à versão 0,12, eliminar primeiro rascunho da sua utilização do cluster `helm delete --purge draft` e, em seguida, remover a sua configuração local executando `rm -rf ~/.draft`. Se estiver em MacOS, em seguida, execute `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

Agora, inicialize o Draft com o `draft init` comando:

```console
draft init
```

## <a name="configure-draft"></a>Configurar o Draft

Rascunho baseia-se as imagens de contentor localmente e, em seguida, qualquer um implementa-los do Registro local (por exemplo, com Minikube), ou utilizar um registo de imagem que especificar. Este artigo utiliza o Azure Container Registry (ACR), pelo que tem de estabelecer uma relação de confiança entre o seu cluster do AKS e registo ACR, em seguida, configure o Draft para enviar suas imagens de contentor para o ACR.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Criar fidedignidade entre o cluster do AKS e ACR

Para estabelecer confiança entre um cluster do AKS e registo do ACR, conceda permissões para o principal de serviço do Azure Active Directory utilizadas pelo cluster do AKS para aceder ao registo do ACR. Os seguintes comandos, fornecer seu próprio `<resourceGroupName>`, substitua `<aksName>` com o nome do seu cluster do AKS e substitua `<acrName>` com o nome do seu registo ACR:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Para obter mais informações sobre estes passos para aceder a ACR, consulte [autenticar com ACR](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configurar o Draft para enviar para e implementar a partir de ACR

Agora que existe uma relação de confiança entre o ACR e o AKS, ative a utilização do ACR do seu cluster do AKS.

1. Definir a configuração de rascunho *Registro* valor. Nos seguintes comandos, substitua `<acrName>` com o nome do seu registo ACR:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Inicie sessão no registo do ACR com [início de sessão az acr][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

Como foi criada uma confiança entre o ACR e o AKS, sem senhas ou segredos são necessários para push ou pull do registo ACR. A autenticação ocorre ao nível do Gestor de recursos do Azure, com o Azure Active Directory.

## <a name="run-an-application"></a>Executar uma aplicação

Para ver o rascunho em ação, vamos implementar uma aplicação de exemplo a partir da [repositório do Draft][draft-repo]. Em primeiro lugar, clone o repositório:

```console
git clone https://github.com/Azure/draft
```

Mude para o diretório de exemplos de Java:

```console
cd draft/examples/example-java/
```

Utilize o `draft create` comando para iniciar o processo. Este comando cria os artefatos que são utilizados para executar a aplicação num cluster do Kubernetes. Esses itens incluem um Dockerfile, um gráfico Helm e um *draft.toml* arquivo, que é o ficheiro de configuração de rascunho.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Para executar o aplicativo de exemplo no cluster do AKS, utilize o `draft up` comando. Este comando cria o Dockerfile para criar uma imagem de contentor, envia a imagem para o ACR e, finalmente, instala o gráfico Helm para iniciar o aplicativo no AKS.

Na primeira vez que este comando é executado, emitir e solicitar a imagem de contentor podem demorar algum tempo. Depois das camadas de bases são colocadas em cache, o tempo necessário para implementar a aplicação é drasticamente reduzido.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Se tiver problemas ao enviar a imagem do Docker, certifique-se de que tiver iniciado sessão com êxito para o seu registo do ACR com [início de sessão az acr][az-acr-login], em seguida, tente o `draft up` novamente o comando.

## <a name="test-the-application-locally"></a>Testar a aplicação localmente

Para testar a aplicação, utilize o `draft connect` comando. Este comando proxies de uma ligação segura para o pod de Kubernetes. Quando terminar, o aplicativo pode ser acessado no URL fornecido.

> [!NOTE]
> Pode demorar alguns minutos para que a imagem de contentor a ser baixado e a aplicação para iniciar. Se receber um erro ao aceder à aplicação, tente novamente a ligação.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Para aceder à sua aplicação, abra um navegador da web para o endereço e porta especificada na `draft connect` , tais como de saída `http://localhost:49804`. 

![Aplicação de Java de exemplo em execução com o Draft](media/kubernetes-draft/sample-app.png)

Utilize `Control+C` para parar a ligação de proxy.

> [!NOTE]
> Também pode utilizar o `draft up --auto-connect` comando para criar e implementar a sua aplicação, em seguida, ligue-se imediatamente para o primeiro contentor em execução.

## <a name="access-the-application-on-the-internet"></a>Aceder à aplicação na internet

O passo anterior, criado uma ligação de proxy para o pod da aplicação no cluster do AKS. À medida que desenvolve e testar a sua aplicação, pode querer disponibilizar a aplicação na internet. Para expor um aplicativo na internet, vai criar um serviço de Kubernetes com um tipo de [LoadBalancer][kubernetes-service-loadbalancer], ou criar um [controlador de entradas] [ kubernetes-ingress]. Vamos criar um *LoadBalancer* serviço.

Em primeiro lugar, atualize o *Values* rascunho pack para especificar que um serviço com um tipo *LoadBalancer* devem ser criados:

```console
vi charts/java/values.yaml
```

Localize a *service.type* propriedade e atualize o valor de *ClusterIP* para *LoadBalancer*, conforme mostrado no seguinte exemplo condensado:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Guarde e feche o ficheiro, em seguida, utilize `draft up` voltar a executar o aplicativo:

```console
draft up
```

Demora alguns minutos para que o serviço retornar um endereço IP público. Para monitorizar o progresso, utilize o `kubectl get service` comando com o *watch* parâmetro:

```console
kubectl get service --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço é apresentado como *pendente*:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Quando o endereço EXTERNAL-IP mudar de *pendente* para um endereço IP, utilize `Control+C` para parar o `kubectl` veja processo:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Para ver a aplicação, navegue para o endereço IP externo do seu Balanceador de carga com `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Iterar sobre a aplicação

Agora que foi configurado rascunho e a aplicação está em execução no Kubernetes, estão definidas para a iteração de código. Código de cada vez que deseja testar atualizada, execute o `draft up` comando para atualizar a aplicação em execução.

Neste exemplo, atualize a aplicação de exemplo de Java para alterar o texto de exibição. Abra o *Hello.java* ficheiro:

```console
vi src/main/java/helloworld/Hello.java
```

Atualizar o texto de saída para apresentar, *Hello World, eu sou o Java no AKS!*:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Execute o `draft up` comando para voltar a implementar a aplicação:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Para ver a aplicação atualizada, curl novamente o endereço IP do seu Balanceador de carga:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como utilizar o Draft, consulte a documentação de rascunho no GitHub.

> [!div class="nextstepaction"]
> [Documentação de rascunho][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
