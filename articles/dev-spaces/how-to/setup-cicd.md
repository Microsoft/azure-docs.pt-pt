---
title: Utilizar o CI/CD com espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.openlocfilehash: b614a517874363be95ff17d802995a927a15af2f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194635"
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
1. DevOps página do projeto principal, navegue para os Pipelines > baseia-se
1. Selecione a opção para criar uma **New** criar pipeline
1. Selecione **GitHub** como a origem, autorizar com a sua conta do GitHub, se necessário e selecione o _azds_updates_ ramo do seu versão bifurcado do repositório de sampleapp dev-espaços
1. Selecione **configuração como código**, ou **YAML**, como o modelo
1. Será apresentada uma página de configuração para o seu pipeline de compilação. Conforme mencionado acima, introduza o caminho de idioma específico para o **caminho do ficheiro YAML**. Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`
1. Vá para o separador de variáveis
1. Adicionar manualmente _dockerId_ como uma variável, que é o nome de utilizador do seu [conta de administrador do Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado nos pré-requisitos artigo)
1. Adicionar manualmente _dockerPassword_ como uma variável, que é a palavra-passe da sua [conta de administrador do Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de que especificar _dockerPassword_ como um segredo (selecionando o ícone de cadeado) por motivos de segurança.
1. Selecione **guardar e colocar em fila**

Tem agora uma solução de CI que criará automaticamente *mywebapi* e *webfrontend* para qualquer atualização enviada por push para o _azds_updates_ ramo do fork do GitHub. Pode verificar as imagens do Docker foram enviadas ao navegar para o portal do Azure, selecionar seu registo de contentor do Azure e navegação a _repositórios_ separador:

![Repositórios de registo de contentor do Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criar a definição de versão

1. DevOps página do projeto principal, navegue para os Pipelines > versões
1. Se estiver trabalhando num projeto de DevOps novo em folha que ainda não contém uma definição de versão, terá primeiro de criar uma definição de versão vazia antes de continuar. A opção de importação não exibe na IU até ter uma definição de versão existente.
1. No lado esquerdo, clique nas **+ novo** botão, em seguida, clique em **importar um pipeline**
1. Selecione o ficheiro. JSON na `samples/release.json`
1. Clique em Ok. Observe que o painel de Pipeline foi carregado com a página de edição da definição de versão. Também observe que há são alguns ícones de aviso vermelho indicando detalhes específicos do cluster que ainda tem de ser configurados.
1. No lado esquerdo do painel de Pipeline, clique nas **adicionar um artefato** bolhas.
1. Na **origem** lista pendente, selecione o pipeline de compilação que criou um pouco mais cedo neste documento.
1. Para o **versão predefinida**, recomendamos que escolha **mais recentes do ramo de padrão de pipeline de compilação**. Não é necessário especificar quaisquer etiquetas.
1. Definir o **alias de origem** para `drop`. A versão predefinida tarefas uso **alias de origem** , de modo tem de ser definido.
1. Clique em **Adicionar**.
1. Agora, clique no ícone de relâmpago bolt recentemente criado no `drop` origem de artefacto, conforme mostrado abaixo:

    ![Configuração de implementação contínua do artefacto de versão](../media/common/release-artifact-cd-setup.png)
1. Ativar o **acionador de implementação contínua**
1. Agora, vá para o painel de tarefas. O _dev_ estágio deve ser selecionado e que seja apresentado com três controles de lista pendente de vermelho, como abaixo:

    ![Configuração de definição de versão](../media/common/release-setup-tasks.png)
1. Especifique a subscrição do Azure, o grupo de recursos e o cluster que estiver a utilizar com espaços de desenvolvimento do Azure.
1. Deve haver apenas mais uma secção mostrar neste momento; vermelho o **tarefa de agente** secção. Vá lá e especifique **alojada 1604 de Ubuntu** como o conjunto de agentes neste estágio.
1. Coloque o cursor sobre o Seletor de tarefas na parte superior, selecione _prod_.
1. Especifique a subscrição do Azure, o grupo de recursos e o cluster que estiver a utilizar com espaços de desenvolvimento do Azure.
1. Sob **tarefa do agente**, configurar **alojados 1604 de Ubuntu** como o conjunto de agentes.
1. Clique em **salvar** no canto superior direito, e **OK**.
1. Clique em **+ versão** (junto ao botão Save), e **criar uma versão**.
1. Verifique se a compilação mais recente a partir do seu pipeline de compilação está selecionada na secção de artefactos e de acessos **criar**.

Um processo de liberação automatizada começará agora, implementar o *mywebapi* e *webfrontend* cluster de gráficos para o Kubernetes no _dev_ espaço de nível superior. Pode monitorizar o progresso da sua liberação no portal web do Azure DevOps.

> [!TIP]
> Se a sua versão falhar com uma mensagem de erro, como *ATUALIZAÇÃO falhou: tempo de espera terminou para a condição*, tente inspecionar os pods no seu cluster [usando o dashboard do Kubernetes](../../aks/kubernetes-dashboard.md). Se vir os pods estão a falhar para começar mensagens de erro como *Falha ao solicitar a imagem "azdsexample.azurecr.io/mywebapi:122": erro de rpc: código = desconhecido desc = resposta de erro do daemon: Obtenha https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: não autorizado: autenticação necessária*, poderá ser porque o seu cluster não foi autorizado a tirar do seu registo de contentor do Azure. Certifique-se de que concluiu o [autorizar o seu cluster do AKS para solicitar a partir do seu Azure Container Registry](../../container-registry/container-registry-auth-aks.md) pré-requisito.

Agora tem um pipeline de CI/CD totalmente automatizado para o fork do GitHub de espaços de desenvolvimento de aplicações de exemplo. Cada vez que push e de consolidação de código, o pipeline de compilação irá criar e enviar por push o *mywebapi* e *webfrontend* imagens à sua instância do ACR personalizada. Em seguida, o pipeline de lançamento irá implementar o gráfico Helm para cada aplicação para o _dev_ espaço no seu cluster habilitados para espaços de desenvolvimento.

## <a name="accessing-your-dev-services"></a>Aceder ao seu _dev_ serviços
Após a implementação, o _dev_ versão do *webfrontend* pode ser acessado com um URL público, como: `http://dev.webfrontend.<hash>.<region>.aksapp.io`.

Pode encontrar este URL a utilizar o *kubectl* CLI:
```cmd
kubectl get ingress -n dev webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="deploying-to-production"></a>Implantação em produção
Clique em **edite** em sua definição de versão e observe que há um _prod_ estágio definido:

![Fase de produção](../media/common/prod-env.png)

Para promover manualmente uma versão particular _prod_ usando o sistema de CI/CD criado neste tutorial:
1. Abra uma versão anteriormente bem-sucedida no portal do DevOps
1. Coloque o cursor sobre a fase 'prod'
1. Selecione implementar

![Promover para produção](../media/common/prod-promote.png)

Nosso exemplo de pipeline de CI/CD faz uso de variáveis para alterar o prefixo DNS para *webfrontend* dependendo que ambiente está a ser implementado. Portanto, para aceder aos seus serviços "prod", pode usar uma URL como: `http://prod.webfrontend.<hash>.<region>.aksapp.io`.

Após a implementação, pode encontrar este URL a utilizar o *kubectl* CLI: <!-- TODO update below to use list-uris when the product has been updated to list non-azds ingresses #769297 -->

```cmd
kubectl get ingress -n prod webfrontend -o=jsonpath="{.spec.rules[0].host}"
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