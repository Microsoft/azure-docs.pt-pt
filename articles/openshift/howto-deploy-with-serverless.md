---
title: Implementar uma aplicação para Azure Red Hat OpenShift usando o OpenShift Serverless
description: Saiba como implementar uma aplicação para Azure Red Hat OpenShift usando o OpenShift Serverless
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, chapéu vermelho, sem servidor
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532815"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Implementar uma aplicação para Azure Red Hat OpenShift usando o OpenShift Serverless

Neste artigo, implementa uma aplicação para um cluster Azure Red Hat OpenShift utilizando [o OpenShift Serverless](https://www.openshift.com/learn/topics/serverless). O OpenShift Serverless ajuda os desenvolvedores a implementar e executar aplicações que irão aumentar ou escalar para zero a pedido, eliminando o consumo de recursos quando não estão a ser utilizados.

O código de aplicação pode ser embalado num recipiente juntamente com os tempos de funcionamento apropriados, e a funcionalidade sem servidor iniciará os recipientes de aplicação quando são desencadeados por um evento. As aplicações podem ser desencadeadas por várias fontes de eventos, tais como eventos a partir das suas próprias aplicações, serviços na nuvem de vários fornecedores, sistemas de Software como serviço (SaaS) e outros serviços.

A gestão de todos os aspetos da implantação de qualquer recipiente de forma sem servidor é incorporada diretamente na interface OpenShift. Os desenvolvedores podem identificar visualmente quais os eventos que estão a impulsionar o lançamento das suas aplicações contentorizadas, com várias formas de modificar os parâmetros do evento. As aplicações OpenShift Serverless podem ser integradas com outros serviços OpenShift, tais como Pipelines OpenShift, Rede de Serviço e Monitorização, proporcionando uma experiência completa de desenvolvimento e implementação de aplicações sem servidor.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Instale a ferramenta knative command-line (kn)

Pode baixar a versão mais recente do CLI apropriada para a sua máquina a partir de <https://github.com/knative/client/releases/>

Se estiver a executar os comandos na Azure Cloud Shell, descarregue o mais recente CLI Knative para Linux.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Lançar a consola web

Descubra o URL da consola web de cluster executando:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Devias arranjar uma URL semelhante a esta.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Lance o URL da consola num browser e faça login usando as `kubeadmin` credenciais.

:::image type="content" source="media/login.png" alt-text="Ecrã de login Azure Red Hat OpenShift":::

## <a name="install-the-openshift-serverless-operator"></a>Instale o operador OpenShift Serverless

Assim que iniciar sessão na consola web OpenShift, certifique-se de que está na perspetiva do *Administrador.* Abra o hub do *operador* e procure o operador **OpenShift Serverless** e selecione-o.

![Procure o operador openshift serverless](media/serverless/serverless-operatorhub.png)

Em seguida, abra a página de instalação do operador clicando na **Instalação**.

![Clique em Instalar para instalar o operador](media/serverless/serverless-clickinstall.png)

Escolha o *Canal de Atualização* apropriado para a versão de cluster do Azure Red Hat OpenShift e instale o operador no `openshift-serverless` espaço de nomes. Desloque-se para baixo e clique **em Instalar**.

![Página de instalação do operador](media/serverless/serverless-installpage.png)

Em poucos minutos, a página de estado deve refletir que o operador está instalado e está pronto a ser utilizado. Clique no botão **'Visualizar o operador'** para proceder.

![O operador está instalado e está pronto para ser utilizado](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Instalar serviço knativo

A capacidade de executar qualquer recipiente de forma sem servidor em OpenShift Serverless é possível utilizando o Knative a montante. A Knative estende a Kubernetes para fornecer um conjunto de componentes para implantação, execução e gestão de aplicações modernas usando a metodologia sem servidor.

### <a name="create-an-instance-of-the-knative-serving"></a>Criar um exemplo do Serviço Knative

Mude para o espaço de `knative-serving` nome clicando na lista de drop-down do projeto no canto superior esquerdo e, em seguida, em *APIs fornecidas,* clique em **Criar Instância** no cartão *Servir Knative.*

![Clique para criar a instância do Serviço Knative](media/serverless/serverless-createknativeserving.png)

Mantenha as predefinições e desloque-se para baixo na página *'Criar Servir Knative'* para clicar no botão **Criar.**

![Mantenha os predefinidos e clique em Criar](media/serverless/serverless-createknativeserving2.png)

Aguarde até que a coluna *Status* apareça **Pronta** e, em seguida, o OpenShift Serverless deve ser instalado e está pronto para criar um projeto OpenShift Serverless.

![Serviço Knative pronto](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Criar um projeto sem servidor

Para criar um novo projeto `demoserverless` chamado, executar o comando:

```azurecli-interactive
oc new-project demoserverless
```

Deve ver uma saída semelhante a:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Mude para a perspetiva *do Desenvolvedor* em vez da perspetiva *do Administrador* no menu do lado esquerdo e selecione na lista `demoserverless` de projetos. Você deve então estar na página de *Topologia* para o projeto.

![Topologia do projeto Azure Red Hat OpenShift](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Implantação usando a consola web

A partir das opções apresentadas para a implementação de uma aplicação, *selecione From Git*. Isto vai aterrar-te na página *"Import from Git".* Use `https://github.com/sclorg/django-ex.git` como URL Git **Repo**. A aplicação web da amostra é implementada utilizando a linguagem de programação Python.

![Projeto Azure Red Hat OpenShift de Git](media/serverless/serverless-from-git.png)

> [!NOTE]
> O OpenShift deteta que se trata de um projeto Python e seleciona a imagem de construtor apropriada.

Percorra os *Recursos* e certifique-se de que o **Serviço Knative** é selecionado como o tipo de recurso a gerar. Esta ação criará um Serviço Knative, um tipo de implementação que permite a escala sem servidor aberto para zero quando está inativo.

![Projeto Azure Red Hat OpenShift - Knative](media/serverless/serverless-knative.png)


Quando estiver pronto, na parte inferior da página clique no **Criar**. Isto criará recursos para gerir a construção e implantação da aplicação. Em seguida, você será redirecionado para a visão geral da topologia para o projeto.

A visão geral da topologia fornece uma representação visual da aplicação que implementou. Com esta vista, pode ver a estrutura geral da aplicação.

Espere que a construção termine. Esta operação poderá demorar alguns minutos. Após a conclusão da construção, aparece uma marca de verificação verde no canto inferior esquerdo do serviço.

![Topologia do projeto Azure Red Hat OpenShift - Construa com sucesso](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Consulte a sua escala de candidaturas

Na lista *de Opções de Exibição* no topo da vista Topology, clique em *Pod Count*. Aguarde que a contagem de pods reduza para zero Pods. A escalada pode levar alguns minutos.

![Topologia do projeto Azure Red Hat OpenShift - Dimensionada para zero](media/serverless/serverless-scaledtozero.png)

Clique no ícone *URL aberto* no canto superior direito do painel de Serviço Knative. A aplicação abre-se num novo separador. Feche o novo separador do navegador e volte à vista Topology. Na vista Topologia, pode ver que a sua aplicação aumentou para um Pod para acomodar o seu pedido. Após alguns minutos, a sua aplicação volta a zero Pods.

![Topologia do projeto Azure Red Hat OpenShift - Dimensionada para um](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Forçar uma nova revisão e definir a distribuição do tráfego

Com cada atualização para a configuração de um serviço, é criada uma nova revisão para o serviço. A rota de serviço aponta todo o tráfego para a última revisão pronta por defeito. Pode alterar este comportamento definindo qual a revisão que obtém uma parte do tráfego. Os serviços knativos permitem o mapeamento do tráfego, o que significa que as revisões de um serviço podem ser mapeadas para uma parte do tráfego atribuída. O mapeamento de tráfego também oferece uma opção para criar URLs únicos para revisões particulares.

Na *Topologia,* clique na revisão dentro do seu serviço para ver os seus detalhes. As insígnias sob o anel pod e no topo do painel de detalhes devem estar `(REV)` . No painel lateral, dentro do separador *Recursos,* desloque-se para baixo e clique na configuração associada ao seu serviço.

![Topologia do projeto Azure Red Hat OpenShift - Revisão](media/serverless/serverless-revdetails.png)

Force uma atualização de configuração mudando para o separador *YAML* e deslocando-se para baixo para editar o valor de `timeoutSeconds` ser `301` . Clique em **Guardar** para guardar a configuração atualizada. Num cenário real, esta atualização de configuração também pode ser desencadeada através da atualização da etiqueta de imagem do contentor.

![Force uma nova revisão atualizando a configuração](media/serverless/serverless-confupdate.png)

Volta para a vista da *Topologia,* verás que foi implementada uma nova revisão. Clique no serviço terminando com o crachá `(KSVC)` e clique no botão De Distribuição de **Tráfego Definido,** agora deverá ser capaz de dividir o tráfego entre as diferentes revisões do serviço.

![Definir distribuição de tráfego](media/serverless/serverless-trafficdist.png)

A vista *Topology* irá agora mostrar-lhe como o tráfego é distribuído entre as duas revisões.

![Rever a distribuição de tráfego](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Utilizando a ferramenta knative command-line (kn)

Nos passos anteriores, utilizou a consola web OpenShift para criar e implementar uma aplicação para OpenShift Serverless. Uma vez que o OpenShift Serverless está a executar o Knative por baixo, também pode utilizar a ferramenta de linha de comando Knative (kn) para criar serviços Knative.

> [!NOTE]
> Se ainda não instalou o `kn` CLI, certifique-se de seguir os passos na secção de pré-requisitos deste artigo. Certifique-se também de que fez login utilizando a ferramenta da linha de comando OpenShift `oc` .

Vamos usar uma imagem de contentor que já foi construída `quay.io/rhdevelopers/knative-tutorial-greeter` em.

### <a name="deploy-a-service"></a>Implementar um serviço

Para implementar o serviço, executar o seguinte comando:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Verá uma saída semelhante à seguinte.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Pode obter uma lista de rotas do projeto executando:

```azurecli-interactive
kn route list
```

Receberá uma lista de rotas no espaço de nomes. Abra o URL num browser para ver o serviço implantado.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Implementar uma nova versão do serviço

Implementar uma nova versão da aplicação executando o comando abaixo, passando a `:latest` etiqueta de imagem e uma variável `MESSAGE_PREFIX` ambiental:

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

Receberá a confirmação de que uma nova revisão `greeter-v2` foi implementada.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Para visualizar uma lista de todas as revisões e a sua distribuição de tráfego, executar o seguinte comando:

```azurecli-interactive
kn revision list
```

Terá uma lista semelhante à abaixo. Note-se que a nova revisão está a receber 100% do tráfego.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Implantações azuis/verdes e canárias

Por defeito, quando uma nova revisão é implementada, recebe 100% do tráfego. Digamos que pretende implementar uma estratégia de implementação azul/verde onde pode rapidamente reverter para a versão mais antiga da aplicação. Knative facilita as coisas.

Atualizar o serviço para criar três tags de tráfego ao enviar 100% do tráfego para o

- **corrente**: pontos na versão atual implantada
- **prev**: pontos na versão anterior
- **mais recente:** sempre aponta para a versão mais recente

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Terá uma confirmação semelhante à seguinte.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Listar as rotas utilizando o comando abaixo:

```azurecli-interactive
kn route describe greeter
```

Você terá uma saída mostrando os URLs para cada uma das etiquetas juntamente com a distribuição de tráfego.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Digamos que deseja reverter rapidamente para a versão anterior, pode atualizar a distribuição de tráfego para enviar 100% do tráfego para a etiqueta anterior:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Verifique novamente listando as rotas utilizando o comando abaixo:

```azurecli-interactive
kn route describe greeter
```

Receberá uma saída que mostra que 100% da distribuição de tráfego vai para a versão anterior.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Brinque com a distribuição de tráfego enquanto refresca a rota principal `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` (neste caso) no seu navegador.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar a aplicação, pode executar o seguinte comando para eliminar o projeto:

```azurecli-interactive
oc delete project demoserverless
```

Também pode eliminar o cluster seguindo as instruções em [Tutorial: Eliminar um conjunto Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a:
> [!div class="checklist"]
>
> * Instale o operador openshift serverless e a servir knative
> * Implementar um projeto sem servidor usando a consola web
> * Implementar um projeto sem servidor utilizando o Knative CLI (kn)
> * Configure as implantações azuis/verdes e as implantações canárias utilizando o CLI Knative (kn)

Saiba mais sobre como construir e implementar aplicações sem servidor, orientadas para eventos no Azure Red Hat OpenShift usando [OpenShift Serverless](https://www.openshift.com/learn/topics/serverless), siga o Início com documentação [openshift serverless](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) e a documentação [de aplicações sem servidor e criação e gestão de aplicações sem servidor.](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html)
