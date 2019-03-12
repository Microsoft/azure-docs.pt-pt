---
title: Utilizar o CI/CD com espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.openlocfilehash: 983af0dd75e6ae62630c85d04ac3819c7e260439
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768285"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Utilizar o CI/CD com espaços de desenvolvimento do Azure

Este artigo orienta-o pela configuração de contínua integração/implementação contínua (CI/CD) para Azure Kubernetes Service (AKS) com espaços de desenvolvimento ativada. CI/CD para o AKS permite que as atualizações de aplicações a serem implantados automaticamente sempre que o código confirmado é emitidos via push para o seu repositório de origem. Cluster ativado com CI/CD em conjunto com os espaços de desenvolvimento é útil porque ele pode manter uma linha de base do aplicativo atualizados para a equipa trabalhar com.

![Diagrama de CI/CD de exemplo](../media/common/ci-cd-simple.png)

Embora este artigo orienta-o com o Azure DevOps, os mesmos conceitos aplicaria aos sistemas de CI/CD com Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Pré-requisitos
* [Cluster de Kubernetes Service (AKS) do Azure com os espaços de desenvolvimento do Azure ativado](../get-started-netcore.md)
* [CLI de espaços de desenvolvimento do Azure instalada](upgrade-tools.md)
* [Organização de DevOps do Azure com um projeto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * O Azure Container Registry [conta de administrador](../../container-registry/container-registry-authentication.md#admin-account) detalhes disponíveis
* [Autorizar o seu cluster do AKS para solicitar a partir do seu registo de contentor do Azure](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Baixe o código de exemplo
Para fins de tempo, vamos criar um fork do nosso repositório de GitHub do código de exemplo. Aceda a https://github.com/Azure/dev-spaces e selecione **Fork**. Uma vez concluído, o processo de bifurcação **Clone** sua versão bifurcado do repositório localmente. Por predefinição o _mestre_ ramo irá fazer Check-out, mas a Incluímos algumas alterações de economia de tempo no _azds_updates_ ramo, que também deve ter sido transferido durante o fork. O _azds_updates_ ramo contém atualizações, pedimos-lhe que faça manualmente as secções do tutoriais de espaços de desenvolvimento, bem como alguns ficheiros YAML e JSON criados previamente para simplificar a implementação do sistema de CI/CD. Pode utilizar um comando como `git checkout -b azds_updates origin/azds_updates` Verifique a _azds_updates_ ramo no repositório local.

## <a name="dev-spaces-setup"></a>Configuração de espaços de desenvolvimento
Criar um novo espaço chamado _dev_ usando o `azds space select` comando. O _dev_ espaço será utilizado pelo seu pipeline CI/CD para enviar suas alterações de código. Também vai ser utilizado para criar _espaços de subordinado_ com base nos _dev_.

```cmd
azds space select -n dev
```

Quando lhe for pedido para selecionar um espaço de desenvolvimento principal, selecione  _\<none\>_.

Depois de ter sido criado o seu espaço de desenvolvimento, precisa determinar o sufixo de anfitrião. Utilize o `azds show-context` comando para mostrar o sufixo de anfitrião do controlador de entrada do Azure Dev espaços.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

No exemplo acima, o sufixo de anfitrião for _fedcba098.eus.azds.io_. Este valor é utilizado mais tarde quando criar a sua definição de versão.

O _dev_ espaço sempre irá conter o estado mais recente do repositório, uma linha de base, para que os desenvolvedores podem criar _espaços subordinado_ partir _dev_ para testar as alterações isoladas dentro do contexto da aplicação maior. Esse conceito será discutido mais detalhadamente os tutoriais de espaços de desenvolvimento.

## <a name="creating-the-build-definition"></a>Criar a definição de compilação
Abra o seu projeto de equipa de DevOps do Azure num browser e navegue para o _Pipelines_ secção. Em primeiro lugar, clique em sua fotografia do perfil no canto superior direito do site do Azure DevOps, abrir o painel de funcionalidades de pré-visualização e desativar o _experiência de criação do novo YAML pipeline_:

![Painel de funcionalidades de pré-visualização de abertura](../media/common/preview-feature-open.png)

A opção para desativar:

![Opção de experiência de criação do pipeline YAML novo](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> O Azure DevOps _experiência de criação do novo YAML pipeline_ conflitos de funcionalidade de pré-visualização com a criação de predefinidos Criar pipelines neste momento. Tem de desativá-la por agora para poder implementar nosso pipeline de compilação predefinidas.

Na _azds_updates_ ramo incluímos um simples [YAML de Pipeline do Azure](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) que define os passos de compilação necessários para *mywebapi* e *webfrontend* .

Dependendo da linguagem que escolheu, o pipeline YAML tem sido check-in num caminho semelhante a: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para criar um Pipeline deste ficheiro:
1. DevOps página do projeto principal, navegue para os Pipelines > baseia-se.
1. Selecione a opção para criar uma **New** criar o pipeline.
1. Selecione **GitHub** como a origem, autorizar com a sua conta do GitHub, se necessário e selecione o _azds_updates_ ramo do seu versão bifurcado do repositório de sampleapp dev-espaços.
1. Selecione **configuração como código**, ou **YAML**, como o modelo.
1. Será apresentada uma página de configuração para o seu pipeline de compilação. Conforme mencionado acima, navegue para o caminho de idioma específico para o **caminho do ficheiro YAML** usando o **...**  botão. Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Vá para o **variáveis** separador.
1. Adicionar manualmente _dockerId_ como uma variável, que é o nome de utilizador do seu [conta de administrador do Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado nos pré-requisitos artigo)
1. Adicionar manualmente _dockerPassword_ como uma variável, que é a palavra-passe da sua [conta de administrador do Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de que especificar _dockerPassword_ como um segredo (selecionando o ícone de cadeado) por motivos de segurança.
1. Selecione **guardar e colocar em fila**.

Tem agora uma solução de CI que criará automaticamente *mywebapi* e *webfrontend* para qualquer atualização enviada por push para o _azds_updates_ ramo do fork do GitHub. Pode verificar as imagens do Docker foram enviadas ao navegar para o portal do Azure, selecionar seu registo de contentor do Azure e navegação a **repositórios** separador. Pode demorar vários minutos para as imagens criar e são apresentados no seu registo de contentor.

![Repositórios de registo de contentor do Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criar a definição de versão

1. DevOps página do projeto principal, navegue para os Pipelines > versões
1. Se estiver trabalhando num projeto de DevOps novo em folha que ainda não contém uma definição de versão, terá primeiro de criar uma definição de versão vazia antes de continuar. A opção de importação não exibe na IU até ter uma definição de versão existente.
1. No lado esquerdo, clique nas **+ novo** botão, em seguida, clique em **importar um pipeline**.
1. Clique em **navegue** e selecione `samples/release.json` do seu projeto.
1. Clique em **OK**. Observe que o painel de Pipeline foi carregado com a página de edição da definição de versão. Também observe que há são alguns ícones de aviso vermelho indicando detalhes específicos do cluster que ainda tem de ser configurados.
1. No lado esquerdo do painel de Pipeline, clique nas **adicionar um artefato** bolhas.
1. Na **origem** lista pendente, selecione a compilação de pipeline que criou anteriormente.
1. Para o **versão predefinida**, escolha **mais recentes do ramo de padrão de pipeline de compilação com etiquetas**.
1. Deixe **etiquetas** vazio.
1. Definir o **alias de origem** para `drop`. O **alias de origem** valor é utilizado pelas tarefas de lançamento predefinidos, de modo tem de ser definido.
1. Clique em **Adicionar**.
1. Agora, clique no ícone de relâmpago bolt recentemente criado no `drop` origem de artefacto, conforme mostrado abaixo:

    ![Configuração de implementação contínua do artefacto de versão](../media/common/release-artifact-cd-setup.png)
1. Ativar a **acionador de implementação contínua**.
1. Paire o rato sobre o **tarefas** separador junto a **Pipeline** e clique em _dev_ para editar o _dev_ testar tarefas.
1. Certifique-se **do Azure Resource Manager** está selecionado em **tipo de ligação.** e ver os três controles de lista pendente realçados em vermelho: ![Configuração de definição de versão](../media/common/release-setup-tasks.png)
1. Selecione a subscrição do Azure que estiver a utilizar com espaços de desenvolvimento do Azure. Também poderá ter de clicar em **autorizar**.
1. Selecione o grupo de recursos e o cluster que estiver a utilizar com espaços de desenvolvimento do Azure.
1. Clique em **tarefa de agente**.
1. Selecione **alojada 1604 de Ubuntu** sob **conjunto de agentes**.
1. Paire o rato sobre o **tarefas** Seletor na parte superior, clique em _prod_ para editar o _prod_ testar tarefas.
1. Certifique-se **do Azure Resource Manager** está selecionado em **tipo de ligação.** e selecione a subscrição do Azure, o grupo de recursos e o cluster que estiver a utilizar com espaços de desenvolvimento do Azure.
1. Clique em **tarefa de agente**.
1. Selecione **alojada 1604 de Ubuntu** sob **conjunto de agentes**.
1. Clique nas **variáveis** separador para atualizar as variáveis para a sua versão.
1. Atualize o valor de **DevSpacesHostSuffix** partir **UPDATE_ME** ao seu sufixo de anfitrião. O sufixo de anfitrião é apresentado quando executou o `azds show-context` comando anteriormente.
1. Clique em **salvar** no canto superior direito, e **OK**.
1. Clique em **+ versão** (junto ao botão Save), e **criar uma versão**.
1. Sob **artefactos**, verifique se a compilação mais recente a partir do seu pipeline de compilação está selecionada.
1. Clique em **Criar**.

Um processo de liberação automatizada começará agora, implementar o *mywebapi* e *webfrontend* cluster de gráficos para o Kubernetes no _dev_ espaço de nível superior. Pode monitorizar o progresso da sua liberação no portal web do Azure DevOps:

1. Navegue para o **versões** secção sob **Pipelines**.
1. Clique no pipeline de versões da aplicação de exemplo.
1. Clique no nome da versão mais recente.
1. Coloque o cursor sobre **dev** caixa sob **fases** e clique em **registos**.

A versão é feita quando todas as tarefas estiverem concluídas.

> [!TIP]
> Se a sua versão falhar com uma mensagem de erro, como *ATUALIZAÇÃO falhou: tempo de espera terminou para a condição*, tente inspecionar os pods no seu cluster [usando o dashboard do Kubernetes](../../aks/kubernetes-dashboard.md). Se vir os pods estão a falhar para começar mensagens de erro como *Falha ao solicitar a imagem "azdsexample.azurecr.io/mywebapi:122": erro de rpc: código = desconhecido desc = resposta de erro do daemon: Obtenha https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: não autorizado: autenticação necessária*, poderá ser porque o seu cluster não foi autorizado a tirar do seu registo de contentor do Azure. Certifique-se de que concluiu o [autorizar o seu cluster do AKS para solicitar a partir do seu Azure Container Registry](../../container-registry/container-registry-auth-aks.md) pré-requisito.

Agora tem um pipeline de CI/CD totalmente automatizado para o fork do GitHub de espaços de desenvolvimento de aplicações de exemplo. Cada vez que push e de consolidação de código, o pipeline de compilação irá criar e enviar por push o *mywebapi* e *webfrontend* imagens à sua instância do ACR personalizada. Em seguida, o pipeline de lançamento irá implementar o gráfico Helm para cada aplicação para o _dev_ espaço no seu cluster habilitados para espaços de desenvolvimento.

## <a name="accessing-your-dev-services"></a>Aceder ao seu _dev_ serviços
Após a implementação, o _dev_ versão do *webfrontend* pode ser acessado com um URL público, como: `http://dev.webfrontend.fedcba098.eus.azds.io`. Pode encontrar este URL, executando o `azds list-uri` comando: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implantação em produção

Para promover manualmente uma versão particular _prod_ usando o sistema de CI/CD criado neste tutorial:
1. Navegue para o **versões** secção sob **Pipelines**.
1. Clique no pipeline de versões da aplicação de exemplo.
1. Clique no nome da versão mais recente.
1. Paire o rato sobre o **prod** caixa sob **fases** e clique em **implementar**.
    ![Promover para produção](../media/common/prod-promote.png)
1. Coloque o cursor sobre **prod** caixa novamente sob **fases** e clique em **registos**.

A versão é feita quando todas as tarefas estiverem concluídas.

O _prod_ estágio do pipeline CI/CD utiliza um balanceador de carga em vez do controlador de entradas de espaços de desenvolvimento para fornecer acesso aos _prod_ serviços. Os serviços implementados no _prod_ fase são acessíveis como endereços IP em vez de nomes DNS. Num ambiente de produção, pode optar por criar seu próprio controlador de entradas para hospedar seus serviços com base na sua própria configuração de DNS.

Para determinar o IP do serviço webfrontend, clique nas **imprimir o IP público do webfrontend** passo para expandir a saída de registo. Utilizar o IP apresentado no registo de saída para o acesso a **webfrontend** aplicação.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentação de espaços de desenvolvimento em produção
Embora a instrumentação de espaços de desenvolvimento foi projetada _não_ para representar um impedimento operação normal da sua aplicação, recomendamos a execução suas cargas de trabalho de produção num espaço de nomes do Kubernetes que não está ativado com espaços de desenvolvimento. Usando esse tipo de espaço de nomes do Kubernetes significa que deve cria a seu espaço de nomes de produção com o `kubectl` CLI, ou permitir que o sistema de CI/CD para criá-lo durante a primeira implementação do Helm. _Selecionar_ ou caso contrário, criar um espaço através dos espaços de desenvolvimento as ferramentas irão adicionar instrumentação de espaços de desenvolvimento para esse espaço de nomes.

Aqui está uma estrutura de espaço de nomes de exemplo que oferece suporte ao desenvolvimento de funcionalidade, o ambiente de "dev", _e_ produção, tudo num único cluster de Kubernetes:

![Estrutura de espaço de nomes de exemplo](../media/common/cicd-namespaces.png)

> [!Tip]
> Se já tiver criado uma `prod` espaço, e seria simplesmente como ao excluí-lo a partir da instrumentação de espaços de desenvolvimento (sem eliminá-lo!), pode fazer isso com o seguinte comando da CLI de espaços de desenvolvimento:
>
> `azds space remove -n prod --no-delete`
>
> Poderá ter de eliminar todos os pods no `prod` espaço de nomes depois de o fazer para que possa ser recriadas sem instrumentação de espaços de desenvolvimento.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento em equipe através dos espaços de desenvolvimento do Azure](../team-development-netcore.md)