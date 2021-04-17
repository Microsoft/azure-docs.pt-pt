---
title: Implementar uma aplicação de origem para Azure Red Hat OpenShift
description: Saiba como implementar uma aplicação para Azure Red Hat OpenShift a partir do código fonte usando a estratégia de construção Source-to-Image (S2I)
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, chapéu vermelho, s2i, fonte para a imagem
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559246"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Implementar uma aplicação de origem para Azure Red Hat OpenShift

Neste artigo, você implementa uma aplicação para um cluster Azure Red Hat OpenShift a partir do código fonte usando uma construção fonte-a-imagem (S2I). [Fonte-a-imagem (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) é um processo de construção para a construção de imagens de contentores reprodutíveis a partir do código fonte. O S2I produz imagens prontas a executar, injetando código fonte numa imagem de recipiente e deixando o recipiente preparar esse código fonte para a execução. Você pode fazer o OpenShift construir uma aplicação de fonte para implantá-lo, para que você não tenha que construir um recipiente à mão com cada mudança. O OpenShift pode então construir e implementar novas versões automaticamente quando notificado das alterações do código fonte.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Criar um projeto

Para criar um novo projeto `demoproject` chamado, executar o comando:

```azurecli-interactive
oc new-project demoproject
```

Deve ver uma saída semelhante a:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Lançar a consola web

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

Mude para a perspetiva *do Desenvolvedor* em vez da perspetiva *do Administrador* no menu do lado esquerdo e selecione na lista `demoproject` de projetos. Você deve então estar na página de *Topologia* para o projeto.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Topologia do projeto Azure Red Hat OpenShift":::

Como o projeto está vazio, não devem ser encontradas cargas de trabalho e ser-lhe-ão apresentadas várias opções para como implementar uma aplicação.

## <a name="deploying-using-the-web-console"></a>Implantação usando a consola web

A partir das opções apresentadas para a implementação de uma aplicação, *selecione From Git*. Isto vai aterrar-te na página *"Import from Git".* Use `https://github.com/sclorg/django-ex.git` como URL Git **Repo**. A aplicação web da amostra é implementada utilizando a linguagem de programação Python.

:::image type="content" source="media/s2i/from-git.png" alt-text="Projeto Azure Red Hat OpenShift de Git":::

> [!NOTE]
> O OpenShift deteta que se trata de um projeto Python e seleciona a imagem de construtor apropriada.

Desloque-se até às *Opções Avançadas* e certifique-se de que a **Criação de uma rota para a aplicação** é verificada. Esta ação criará uma rota OpenShift, uma forma de expor um serviço, dando-lhe um nome de anfitrião acessível externamente.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Projeto Azure Red Hat OpenShift de Git - Configuração de rota":::

Quando estiver pronto, na parte inferior da página clique no **Criar**. Isto criará recursos para gerir a construção e implantação da aplicação. Em seguida, você será redirecionado para a visão geral da topologia para o projeto.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Projeto Azure Red Hat OpenShift de Git - Topologia":::

A visão geral da topologia fornece uma representação visual da aplicação que implementou. Com esta vista, pode ver a estrutura geral da aplicação.

O ícone Git pode ser clicado para levá-lo ao repositório Git a partir do qual o código fonte para a aplicação foi construído. O ícone mostrado na parte inferior esquerda mostra o estado de construção da aplicação. Clicar neste ícone irá levá-lo para a secção de detalhes de construção. Se a aplicação tiver exposto rotas, o ícone no topo direito pode ser clicado para abrir o URL para a rota de aplicação que foi criada.

Enquanto a aplicação está a aumentar ou a descer, a iniciar lançamentos e a recriar cápsulas, a representação da aplicação na vista da topologia será animada para lhe dar uma visão em tempo real do que se está a passar.

Clicar no ícone da aplicação irá trazer mais detalhes como mostrado abaixo.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Projeto Azure Red Hat OpenShift de Git - Detalhes":::

## <a name="viewing-the-builder-logs"></a>Visualização dos registos do construtor

Uma vez iniciada a construção, clique no link *'Ver Registos'* mostrado no painel *Recursos.*

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Projeto Azure Red Hat OpenShift de Git - Construa troncos":::

Isto permitir-lhe-á monitorizar o progresso da construção à medida que funciona. A imagem do construtor, Python neste caso, injetará o código fonte de aplicação na imagem final antes de o empurrar para o registo interno de imagem openShift. A construção terá terminado com sucesso quando vir uma mensagem final de "Push successful".

## <a name="accessing-the-application"></a>Acesso à aplicação

Uma vez concluída a construção da imagem da aplicação, será implantada.

Clique em *Topologia* na barra de menu à esquerda para voltar à vista de topologia do projeto. Quando criou a aplicação utilizando a consola web, uma *Rota* foi criada automaticamente para a aplicação e será exposta fora do cluster. O URL que pode ser usado para aceder à aplicação a partir de um navegador web estava visível no *separador Recursos* para a aplicação que visualizava anteriormente.

A partir da vista topologia, você pode chegar ao URL para a aplicação implementada clicando no ícone superior direito do anel. Quando a implementação estiver concluída, clique no ícone e deverá ver a aplicação que implementou.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Projeto Azure Red Hat OpenShift da Git - Aplicação Browse":::

## <a name="deploying-using-the-command-line"></a>Implantação utilizando a linha de comando

Aprendeu a implementar uma aplicação utilizando a consola web, agora permite implementar a mesma aplicação web, mas desta vez usando a `oc` ferramenta da linha de comando.

Executar o seguinte comando para apagar o projeto e começar de novo:

```azurecli-interactive
oc delete project demoproject
```

Deve receber uma mensagem de confirmação de que foi `demoproject` apagado.

```output
project.project.openshift.io "demoproject" deleted
```

Crie o `demoproject` novo correndo:

```azurecli-interactive
oc new-project demoproject
```

No âmbito do projeto, crie uma nova aplicação a partir da fonte no GitHub, especificando o construtor S2I para a versão mais recente da Python fornecida.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

Isto deve apresentar uma saída semelhante a:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

O OpenShift usará o nome do repositório Git como nome para a aplicação. Reveja o estado da construção e implantação executando:

```azurecli-interactive
oc status
```

Quando a construção e a implantação estiverem concluídas, deverá ver uma saída semelhante.

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Para expor a aplicação fora do cluster OpenShift, terá de criar uma rota executando:

```azurecli-interactive
oc expose service/django-ex
```

Devia ter uma confirmação.

```output
route.route.openshift.io/django-ex exposed
```

Recupere o URL executando:

```azurecli-interactive
oc get route django-ex
```

Deverá recuperar o nome de anfitrião atribuído à rota que pode utilizar para navegar para a aplicação implementada.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Desencadeando uma nova construção binária

Ao trabalhar na aplicação, provavelmente vai querer fazer alterações e vê-las implantadas. Pode configurar facilmente um webhook que irá desencadear uma nova construção e implementação com cada código comprometendo-se. No entanto, isto pode não ser desejável, pois por vezes gostaria de ver as alterações sem ter de empurrar todas as alterações de código para o repositório.

Nos casos em que se está a imerar rapidamente em mudanças, pode usar o que é chamado de construção binária. Para demonstrar este cenário, clone o repositório git para a aplicação localmente, executando:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Isto criará um sub-directório `django-ex` contendo o código fonte para a aplicação:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Mudar para o sub-diretório:

```azurecli-interactive
cd django-ex
```

Abra o editor integrado da Azure Cloud Shell:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Desça e mude a linha que diz `Welcome to your Django application on OpenShift` dizer `Welcome to Azure Red Hat OpenShift` . Guarde o ficheiro e feche o editor através do `...` menu no topo direito.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Projeto Azure Red Hat OpenShift da Git - Aplicação de edição em editor da Azure Cloud Shell":::

Inicie uma nova construção executando o comando:

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

Ao passar a `--from-dir=.` bandeira, a linha de comando OpenShift carregará o código de origem a partir do diretório especificado e iniciará o processo de construção e implantação. Deve obter uma saída semelhante à seguinte, e depois de alguns minutos, a construção deve ser concluída.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Se atualizar o navegador com a aplicação, deverá ver o título atualizado.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Projeto Azure Red Hat OpenShift da Git - Aplicação atualizada da Browse":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar a aplicação, pode executar o seguinte comando para eliminar o projeto:

```azurecli-interactive
oc delete project demoproject
```

Também pode eliminar o cluster seguindo as instruções em [Tutorial: Eliminar um conjunto Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a:
> [!div class="checklist"]
>
> * Criar um projeto
> * Implementar uma aplicação a partir do código fonte usando a consola web
> * Implementar uma aplicação a partir do código fonte utilizando a linha de comando OpenShift
> * Desencadeie uma construção binária utilizando a linha de comando OpenShift

Saiba mais sobre como construir e implementar aplicações utilizando estratégias de origem para imagem e [outras estratégias de construção.](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)
