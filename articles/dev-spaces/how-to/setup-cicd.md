---
title: Utilizar CI/CD com o Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Desenvolvimento rápido do Kubernetes com contentores e microsserviços no Azure
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres
ms.openlocfilehash: 525e18cba48756e725cbc7d837c2352b0fec74fe
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280028"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Usar CI/CD com Azure Dev Spaces

Este artigo orienta você pela configuração de CI/CD (integração contínua/implantação contínua) para o AKS (serviço kubernetes do Azure) com espaços de desenvolvimento habilitados. CI/CD para AKS permite que as atualizações de aplicativo sejam implantadas automaticamente sempre que o código confirmado é enviado por push para o repositório de origem. Usar CI/CD em conjunto com um cluster habilitado para espaços de desenvolvimento é útil porque pode manter uma linha de base do aplicativo atualizada para a equipe trabalhar.

![Diagrama de CI/CD de exemplo](../media/common/ci-cd-simple.png)

Embora este artigo o orienta com o Azure DevOps, os mesmos conceitos se aplicariam a sistemas de CI/CD como Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Pré-requisitos
* [Cluster do serviço de kubernetes do Azure (AKS) com Azure Dev Spaces habilitado](../get-started-netcore.md)
* [CLI do Azure Dev Spaces instalada](upgrade-tools.md)
* [Organização do Azure DevOps com um projeto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [ACR (registro de contêiner do Azure)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Detalhes da [conta de administrador](../../container-registry/container-registry-authentication.md#admin-account) do registro de contêiner do Azure disponíveis
* [Autorize o cluster AKS a efetuar pull do seu registro de contêiner do Azure](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Baixar código de exemplo
Por questão de tempo, vamos criar uma bifurcação do nosso repositório GitHub de código de exemplo. Vá para https://github.com/Azure/dev-spaces e selecione **Fork**. Depois que o processo de bifurcação for concluído, **clone** a versão bifurcada do repositório localmente. Por padrão, o check-out da ramificação _mestre_ será feito, mas incluímos algumas alterações que economizam tempo na ramificação _azds_updates_ , que também deve ter sido transferida durante a bifurcação. O _azds_updates_ Branch contém atualizações que solicitamos que você faça manualmente nas seções de tutorial de espaços de desenvolvimento, bem como alguns arquivos YAML e JSON pré-criados para simplificar a implantação do sistema de CI/CD. Você pode usar um comando como `git checkout -b azds_updates origin/azds_updates` para conferir o Branch _azds_updates_ em seu repositório local.

## <a name="dev-spaces-setup"></a>Configuração de espaços de desenvolvimento
Crie um novo espaço chamado _dev_ usando o comando `azds space select`. O espaço de _desenvolvimento_ será usado pelo pipeline de CI/CD para enviar as alterações de código por push. Ele também será usado para criar _espaços filhos_ com base em _desenvolvimento_.

```cmd
azds space select -n dev
```

Quando solicitado a selecionar um espaço de desenvolvimento pai, selecione _\<nenhum\>_ .

Depois que o espaço de desenvolvimento tiver sido criado, você precisará determinar o sufixo do host. Use o comando `azds show-context` para mostrar o sufixo de host do controlador de entrada do Azure Dev Spaces.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

No exemplo acima, o sufixo do host é _fedcba098.eus.azds.Io_. Esse valor será usado mais tarde ao criar a definição de versão.

O espaço de _desenvolvimento_ sempre conterá o estado mais recente do repositório, uma linha de base, para que os desenvolvedores possam criar _espaços filho_ do _dev_ para testar suas alterações isoladas no contexto do aplicativo maior. Esse conceito é discutido mais detalhadamente nos tutoriais dos espaços de desenvolvimento.

## <a name="creating-the-build-definition"></a>Criando a definição de compilação
Abra seu projeto de equipe do Azure DevOps em um navegador da Web e navegue até a seção _pipelines_ . Primeiro, clique em sua foto de perfil no canto superior direito do site do Azure DevOps, abra o painel recursos de visualização e desabilite a _nova experiência de criação de pipeline YAML_:

![Abrindo o painel de recursos de visualização](../media/common/preview-feature-open.png)

A opção para desabilitar:

![Nova opção de experiência de criação de pipeline do YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> O recurso de visualização da experiência de criação de pipeline do Azure DevOps _New YAML_ está em conflito com a criação de pipelines de compilação predefinidos no momento. Você precisa desabilitá-lo por enquanto para implantar nosso pipeline de compilação predefinido.

Na ramificação _azds_updates_ incluímos um YAML de [pipeline do Azure](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) simples que define as etapas de compilação necessárias para *mywebapi* e *WebFrontEnd*.

Dependendo do idioma escolhido, o YAML do pipeline foi verificado em um caminho semelhante a: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para criar um pipeline a partir deste arquivo:
1. Na página principal do projeto DevOps, navegue até pipelines > Builds.
1. Selecione a opção para criar um **novo** pipeline de compilação.
1. Selecione **GitHub** como a origem, autorize com sua conta do GitHub, se necessário, e selecione a ramificação _azds_updates_ da sua versão bifurcada do repositório de aplicativos de exemplo _dev-Spaces_ .
1. Selecione **configuração como código**ou **YAML**, como seu modelo.
1. Agora, você verá uma página de configuração para o pipeline de compilação. Conforme mencionado acima, navegue até o caminho específico do idioma para o **caminho do arquivo YAML** usando o botão **...** . Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Vá para a guia **variáveis** .
1. Adicione manualmente _dockerId_ como uma variável, que é o nome de usuário da sua [conta de administrador de registro de contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado no artigo pré-requisitos)
1. Adicione manualmente _dockerPassword_ como uma variável, que é a senha da sua [conta de administrador do registro de contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de especificar _dockerPassword_ como um segredo (selecionando o ícone de cadeado) para fins de segurança.
1. Selecione **salvar & fila**.

Agora você tem uma solução CI que criará automaticamente o *mywebapi* e o *WebFrontEnd* para qualquer atualização enviada por push para a ramificação _azds_updates_ de sua bifurcação do github. Você pode verificar se as imagens do Docker foram enviadas por push, navegando até a portal do Azure, selecionando o registro de contêiner do Azure e navegando na guia **repositórios** . Pode levar vários minutos para que as imagens sejam compiladas e exibidas no registro de contêiner.

![Repositórios do registro de contêiner do Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criando a definição da versão

1. Na página principal do projeto DevOps, navegue até pipelines > versões
1. Se você estiver trabalhando em um projeto DevOps totalmente novo que ainda não contém uma definição de versão, precisará primeiro criar uma definição de versão vazia antes de continuar. A opção de importação não é exibida na interface do usuário até que você tenha uma definição de versão existente.
1. À esquerda, clique no botão **+ novo** e, em seguida, clique em **importar um pipeline**.
1. Clique em **procurar** e selecione `samples/release.json` em seu projeto.
1. Clique em **OK**. Observe que o painel pipeline foi carregado com a página de edição da definição de versão. Observe também que há alguns ícones de aviso vermelho que indicam detalhes específicos do cluster que ainda devem ser configurados.
1. À esquerda do painel pipeline, clique na bolha **Adicionar um artefato** .
1. Na lista suspensa **origem** , selecione o pipeline de compilação criado anteriormente.
1. Para a **versão padrão**, escolha **mais recente na ramificação padrão do pipeline de compilação com marcas**.
1. Deixe **marcas** vazias.
1. Defina o **alias de origem** como `drop`. O valor do **alias de origem** é usado pelas tarefas de liberação predefinidas para que ele deva ser definido.
1. Clique em **Adicionar**.
1. Agora, clique no ícone de raio na fonte de artefato `drop` recém-criada, conforme mostrado abaixo:

    ![Configuração de implantação contínua do artefato de versão](../media/common/release-artifact-cd-setup.png)
1. Habilite o **gatilho de implantação contínua**.
1. Passe o mouse sobre a guia **tarefas** ao lado de **pipeline** e clique em _desenvolvimento_ para editar as tarefas do estágio de _desenvolvimento_ .
1. Verifique se **Azure Resource Manager** está selecionado em **tipo de conexão.** e você verá os três controles suspensos realçados em vermelho: configuração de definição de versão ![](../media/common/release-setup-tasks.png)
1. Selecione a assinatura do Azure que você está usando com Azure Dev Spaces. Talvez você também precise clicar em **autorizar**.
1. Selecione o grupo de recursos e o cluster que você está usando com Azure Dev Spaces.
1. Clique em **trabalho do agente**.
1. Selecione **Ubuntu 1604 hospedado** no **pool de agentes**.
1. Passe o mouse sobre o seletor de **tarefas** na parte superior, clique em _prod_ para editar as tarefas do estágio de _produção_ .
1. Verifique se **Azure Resource Manager** está selecionado em **tipo de conexão.** e selecione a assinatura do Azure, o grupo de recursos e o cluster que você está usando com Azure Dev Spaces.
1. Clique em **trabalho do agente**.
1. Selecione **Ubuntu 1604 hospedado** no **pool de agentes**.
1. Clique na guia **variáveis** para atualizar as variáveis da sua versão.
1. Atualize o valor de **DevSpacesHostSuffix** de **UPDATE_ME** para seu sufixo de host. O sufixo do host é exibido quando você executou o comando `azds show-context` anteriormente.
1. Clique em **salvar** no canto superior direito e em **OK**.
1. Clique em **+ versão** (ao lado do botão Salvar) e **crie uma versão**.
1. Em **artefatos**, verifique se a compilação mais recente do pipeline de compilação está selecionada.
1. Clique em **Criar**.

Um processo de liberação automatizado agora começará, implantando os gráficos *mywebapi* e *WebFrontEnd* em seu cluster kubernetes no espaço de nível superior do _dev_ . Você pode monitorar o progresso de sua versão no portal da Web do Azure DevOps:

1. Navegue até a seção **versões** em **pipelines**.
1. Clique no pipeline de lançamento para o aplicativo de exemplo.
1. Clique no nome da versão mais recente.
1. Passe o mouse sobre a caixa de **desenvolvimento** em **estágios** e clique em **logs**.

A versão é feita quando todas as tarefas são concluídas.

> [!TIP]
> Se sua versão falhar com uma mensagem de erro, como *falha na atualização: tempo limite excedido ao aguardar a condição*, tente inspecionar o pods no cluster [usando o painel do kubernetes](../../aks/kubernetes-dashboard.md). Se você vir que os pods estão falhando ao iniciar com mensagens de erro, como *falha ao efetuar pull da imagem "azdsexample.azurecr.Io/mywebapi:122": erro de RPC: código = desconhecido desc = resposta de erro do daemon: Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: não autorizado: autenticação necessária*, pode ser porque o cluster não foi autorizado a efetuar pull do seu registro de contêiner do Azure. Certifique-se de ter concluído o [seu cluster autorizar seu AKs para efetuar pull do seu pré-requisito de registro de contêiner do Azure](../../aks/cluster-container-registry-integration.md) .

Agora você tem um pipeline de CI/CD totalmente automatizado para sua bifurcação do GitHub dos aplicativos de exemplo de espaços de desenvolvimento. Sempre que você confirmar e enviar código por push, o pipeline de compilação criará e enviará por push as imagens *mywebapi* e *WebFrontEnd* para sua instância personalizada do ACR. Em seguida, o pipeline de lançamento implantará o gráfico Helm para cada aplicativo no espaço de _desenvolvimento_ em seu cluster habilitado para espaços de desenvolvimento.

## <a name="accessing-your-_dev_-services"></a>Acessando seus serviços de _desenvolvimento_
Após a implantação, a versão de _desenvolvimento_ do *WebFrontEnd* pode ser acessada com uma URL pública como: `http://dev.webfrontend.fedcba098.eus.azds.io`. Você pode encontrar essa URL executando o comando `azds list-uri`: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implantando na produção

Para promover manualmente uma versão específica para _produção_ usando o sistema de CI/CD criado neste tutorial:
1. Navegue até a seção **versões** em **pipelines**.
1. Clique no pipeline de lançamento para o aplicativo de exemplo.
1. Clique no nome da versão mais recente.
1. Passe o mouse sobre a caixa **prod** em **estágios** e clique em **implantar**.
    ![promover para](../media/common/prod-promote.png) de produção
1. Passe o mouse sobre a caixa de **produção** novamente em **estágios** e clique em **logs**.

A versão é feita quando todas as tarefas são concluídas.

O estágio de _produção_ do pipeline de CI/CD usa um balanceador de carga em vez do controlador de entrada de espaços de desenvolvimento para fornecer acesso aos serviços de _produção_ . Os serviços implantados no estágio de _produção_ são acessíveis como endereços IP em vez de nomes DNS. Em um ambiente de produção, você pode optar por criar seu próprio controlador de entrada para hospedar seus serviços com base em sua própria configuração de DNS.

Para determinar o IP do serviço de WebFrontEnd, clique na etapa **Imprimir IP público do WebFrontEnd** para expandir a saída do log. Use o IP exibido na saída de log para acessar o aplicativo de **front-end** .

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentação de espaços de desenvolvimento em produção
Embora a instrumentação de espaços de desenvolvimento tenha sido projetada para _não_ obter a operação normal do seu aplicativo, é recomendável executar suas cargas de trabalho de produção em um namespace kubernetes que não está habilitado com espaços de desenvolvimento. Usar esse tipo de namespace kubernetes significa que você deve criar seu namespace de produção usando o `kubectl` CLI ou permitir que o sistema de CI/CD o crie durante a primeira implantação do Helm. A _seleção_ ou a criação de um espaço usando as ferramentas de espaços de desenvolvimento adicionará a instrumentação de espaços de desenvolvimento a esse namespace.

Aqui está uma estrutura de namespace de exemplo que dá suporte ao desenvolvimento de recursos, ao ambiente de "desenvolvimento" _e_ à produção, tudo em um único cluster kubernetes:

![Estrutura de namespace de exemplo](../media/common/cicd-namespaces.png)

> [!Tip]
> Se você já tiver criado um espaço de `prod` e simplesmente quiser excluí-lo da instrumentação de espaços de desenvolvimento (sem excluir!), poderá fazer isso com o seguinte comando da CLI de espaços de desenvolvimento:
>
> `azds space remove -n prod --no-delete`
>
> Talvez seja necessário excluir todos os pods no namespace `prod` depois de fazer isso para que possam ser recriados sem a instrumentação de espaços de desenvolvimento.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento em equipe usando Azure Dev Spaces](../team-development-netcore.md)