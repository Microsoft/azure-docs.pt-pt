---
title: Utilizar CI/CD com o Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Saiba como configurar a integração contínua/implantação contínua utilizando O Azure DevOps com espaços Azure Dev
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contentores Azure, contentores
ms.openlocfilehash: f2eb9449518b32ab74f2dbbca6b5489aed325db7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685624"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Utilizar CI/CD com o Azure Dev Spaces

Este artigo guia-o através da criação de uma integração contínua/implantação contínua (CI/CD) para o Serviço Azure Kubernetes (AKS) com espaços Dev ativados. O CI/CD para o AKS permite que as atualizações das aplicações sejam automaticamente implementadas sempre que o código comprometido for empurrado para o seu repositório de origem. A utilização de CI/CD em conjunto com um cluster ativado por Espaços Dev é útil porque pode manter uma linha de base da aplicação atualizada para a equipa trabalhar com.

![Diagrama de CI/CD de exemplo](../media/common/ci-cd-simple.png)

Embora este artigo o guie com O Azure DevOps, os mesmos conceitos aplicar-se-iam a sistemas CI/CD como Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Pré-requisitos
* [Cluster azure Kubernetes Service (AKS) com espaços Azure Dev habilitados](../get-started-netcore.md)
* [Azure Dev Spaces CLI instalado](upgrade-tools.md)
* [Organização Azure DevOps com um projeto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Registo de Contentores Azure (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Dados da conta do [administrador](../../container-registry/container-registry-authentication.md#admin-account) do Registo de Contentores do Azure disponíveis
* [Autorize o seu cluster AKS a retirar do seu Registo de Contentores Azure](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Descarregue o código da amostra
Por uma questão de tempo, vamos criar uma bifurcação do nosso código de amostra GitHub repositório. Vá https://github.com/Azure/dev-spaces e selecione **Fork**. Uma vez concluído o processo do garfo, **clone** a sua versão bifurcada do repositório localmente. Por defeito, o ramo _principal_ será verificado, mas incluímos algumas mudanças de poupança de tempo na filial _azds_updates,_ que também deveria ter sido transferida durante o seu garfo. O ramo _azds_updates_ contém atualizações que pedimos para fazer manualmente nas secções tutoriais de Dev Spaces, bem como alguns ficheiros YAML e JSON pré-fabricados para simplificar a implementação do sistema CI/CD. Pode usar um `git checkout -b azds_updates origin/azds_updates` comando como verificar a _filial azds_updates_ no seu repositório local.

## <a name="dev-spaces-setup"></a>Configuração de Espaços Dev
Crie um novo espaço `azds space select` chamado _dev_ usando o comando. O espaço _de v_ será utilizado pelo seu oleoduto CI/CD para empurrar as alterações de código. Também será usado para criar _espaços infantis baseados_ em _dev_.

```cmd
azds space select -n dev
```

Quando for solicitado para selecionar um _ \<espaço\>_ de dev dos pais, selecione nenhum .

Depois do seu espaço de dev ter sido criado, você precisa determinar o sufixo do hospedeiro. Utilize `azds show-context` o comando para mostrar o sufixo anfitrião do Controlador de Ingress os Espaços Azure Dev.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

No exemplo acima, o sufixo hospedeiro é _fedcba098.eus.azds.io_. Este valor é usado mais tarde ao criar a sua definição de lançamento.

O espaço _de v_ conterá sempre o estado mais recente do repositório, uma linha de base, para que os desenvolvedores possam criar _espaços infantis_ a partir de _dev_ para testar as suas mudanças isoladas no contexto da app maior. Este conceito é discutido mais detalhadamente nos tutoriais de Dev Spaces.

## <a name="creating-the-build-definition"></a>Criando a definição de construção
Abra o seu projeto de equipa Azure DevOps num navegador web e navegue para a secção _Pipelines._ Primeiro, clique na sua foto de perfil na parte superior direita do site Azure DevOps, abra o painel de funcionalidades de pré-visualização e desative a experiência de criação do _novo gasoduto YAML:_

![Painel de pré-visualização de abertura](../media/common/preview-feature-open.png)

A opção de desativar:

![Nova opção de experiência de criação de gasodutoYL](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> A experiência de pré-visualização da nova experiência de criação de gasodutos Azure DevOps _Novo YAML_ apresenta conflitos com a criação de oleodutos de construção pré-definidos neste momento. Tens de o desativar por enquanto para implantar o nosso oleoduto de construção pré-definido.

No ramo _azds_updates_ incluímos um simples [IAML de Pipeline Azure](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) que define os passos de construção necessários para *mywebapi* e *webfrontend*.

Dependendo da linguagem que escolheu, o pipeline YAML foi verificado num caminho semelhante ao:`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para criar um Pipeline a partir deste ficheiro:
1. Na página principal do projeto DevOps, navegue para Pipelines > Builds.
1. Selecione a opção de criar **um** novo oleoduto de construção.
1. Selecione **gitHub** como fonte, autorize com a sua conta GitHub se necessário e selecione _o_ azds_updates filial da sua versão bifurcada do repositório de aplicações de amostra de _dev-spaces._
1. **Selecione a configuração como código,** ou **YAML,** como modelo.
1. Agora é-lhe apresentada uma página de configuração para o seu pipeline de construção. Como mencionado acima, navegue para o caminho específico da linguagem para o caminho de **ficheiro YAML** usando o **...** botão. Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Vá ao separador **Variáveis.**
1. Adicione manualmente _o dockerId_ como uma variável, que é o nome de utilizador da sua conta de administrador de registo de [contentores Azure](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado sem acordo no artigo requisitos)
1. Adicione manualmente _dockerPassword_ como uma variável, que é a palavra-passe da sua conta de administrador de registo de [contentores Azure](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de especificar _dockerPassword_ como um Segredo (selecionando o ícone de bloqueio) para fins de segurança.
1. Selecione **Guardar & fila**.

Tem agora uma solução CI que irá automaticamente construir *mywebapi* e *webfrontend* para qualquer atualização empurrada para o ramo _azds_updates_ do seu garfo GitHub. Pode verificar se as imagens do Docker foram empurradas navegando para o portal Azure, selecionando o registo de contentores Azure e navegando no separador **Repositórios.** Pode levar vários minutos para as imagens construirem e aparecerem no registo do seu contentor.

![Repositórios de registo de contentores de Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criando a definição de lançamento

1. Na página principal do projeto DevOps, navegue para Pipelines > Releases
1. Se estiver a trabalhar num novo Projeto DevOps que ainda não contenha uma definição de lançamento, terá primeiro de criar uma definição de lançamento vazia antes de prosseguir. A opção Import não se exibe na UI até ter uma definição de lançamento existente.
1. À esquerda, clique no botão **+ Novo** e, em seguida, clique em Importar **um pipeline**.
1. Clique **Browse** em Navegar `samples/release.json` e selecione a partir do seu projeto.
1. Clique em **OK**. Note que o painel do Pipeline carregou com a página de edição da definição de lançamento. Note também que existem alguns ícones de aviso vermelho que indicam detalhes específicos do cluster que ainda devem ser configurados.
1. À esquerda do painel do gasoduto, clique na bolha de **artefacto.**
1. No dropdown **Source,** selecione o pipeline de construção que criou anteriormente.
1. Para a **versão Padrão,** escolha **o Mais Recente do ramo predefinido do pipeline de construção com etiquetas**.
1. Deixe **etiquetas** vazias.
1. Desloque o `drop`pseudónimo **Fonte** para . O valor do **pseudónimo Fonte** é utilizado pelas tarefas de libertação predefinidas, pelo que deve ser definido.
1. Clique em **Adicionar**.
1. Agora clique no ícone do `drop` relâmpago na fonte de artefacto recém-criada, como mostrado abaixo:

    ![Desbloqueie a configuração contínua de implantação do artefacto](../media/common/release-artifact-cd-setup.png)
1. Ativar o **gatilho de implantação contínua**.
1. Passe sobre o separador **Tasks** ao lado **do Pipeline** e clique em _dev_ para editar as tarefas de fase _de dev._
1. Verifique se **o Gestor de Recursos Azure** é selecionado no âmbito do Tipo de **Ligação.** e vê os três controlos de ![dropdown realçados a vermelho: Configuração da definição de lançamento](../media/common/release-setup-tasks.png)
1. Selecione a subscrição Azure que está a usar com o Azure Dev Spaces. Também pode ter de clicar em **Autorizar**.
1. Selecione o grupo de recursos e o cluster que está a usar com o Azure Dev Spaces.
1. Clique no **trabalho do Agente.**
1. Selecione **Ubuntu 1604 hospedado** sob **a piscina do Agente**.
1. Paire sobre o seletor de **Tarefas** na parte superior, clique em _prod_ para editar as tarefas de estágio _prod._
1. Verifique se **o Gestor de Recursos Azure** é selecionado no âmbito do Tipo de **Ligação.** e selecione a subscrição Azure, grupo de recursos e cluster que está a usar com o Azure Dev Spaces.
1. Clique no **trabalho do Agente.**
1. Selecione **Ubuntu 1604 hospedado** sob **a piscina do Agente**.
1. Clique no separador **Variáveis** para atualizar as variáveis para a sua libertação.
1. Atualize o valor do **DevSpacesHostSufixo** de **UPDATE_ME** para o seu sufixo anfitrião. O sufixo hospedeiro é exibido `azds show-context` quando dirigiu o comando mais cedo.
1. Clique em **Guardar** na parte superior direita e **OK**.
1. Clique **+ Desbloqueie** (ao lado do botão Guardar) e **crie um desbloqueio**.
1. Em **Artefactos,** verifique se a última construção do seu oleoduto de construção está selecionada.
1. Clique em **Criar**.

Um processo de libertação automatizado começará agora, implantando as tabelas *mywebapi* e *webfrontend* para o seu cluster Kubernetes no espaço de topo _de nível de v._ Pode monitorizar o progresso da sua libertação no portal web Azure DevOps:

1. Navegue para a secção **de lançamentos** em **Pipelines**.
1. Clique no gasoduto de libertação para a aplicação da amostra.
1. Clique no nome do último lançamento.
1. Passe sobre a caixa **de dev** em **Palcos** e clique em **Registos**.

A libertação é feita quando todas as tarefas estão completas.

> [!TIP]
> Se a sua libertação falhar com uma mensagem de erro como *upgrade FAILEd: tempo para fora à espera da condição,* tente inspecionar as cápsulas do seu cluster [utilizando o painel kubernetes](../../aks/kubernetes-dashboard.md). Se vir que as cápsulas estão a falhar em começar com mensagens de erro como falhou em *puxar a imagem\/"azdsexample.azurecr.io/mywebapi:122": erro rpc: código = Desc = Resposta de erro desconhecida do daemon: Get https: /azdsexample.azurecr.io/v2/mywebapi/manifests/122: não autorizado: autenticação necessária,* pode ser porque o seu cluster não foi autorizado a retirar do registo do seu contentor Azure. Certifique-se de que completou o cluster DE Autorização do seu conjunto AKS para retirar do seu pré-requisito do Registo de [Contentores Azure.](../../aks/cluster-container-registry-integration.md)

Tem agora um pipeline CI/CD totalmente automatizado para o seu garfo GitHub das aplicações de amostra de Dev Spaces. Cada vez que cometer e empurrar código, o pipeline de construção irá construir e empurrar as imagens *mywebapi* e *webfrontend* para a sua instância ACR personalizada. Em seguida, o gasoduto de lançamento irá implantar o gráfico Helm para cada aplicação no espaço _de v_ no seu cluster ativado por Dev Spaces.

## <a name="accessing-your-_dev_-services"></a>Acesso aos seus serviços _de dev_
Após a implementação, a versão _dev_ do *webfrontend* `http://dev.webfrontend.fedcba098.eus.azds.io`pode ser acedida com um URL público como: . Pode encontrar este URL `azds list-uri` executando o comando: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implantação para produção

Promover manualmente uma determinada libertação para _lançar_ utilizando o sistema CI/CD criado neste tutorial:
1. Navegue para a secção **de lançamentos** em **Pipelines**.
1. Clique no gasoduto de libertação para a aplicação da amostra.
1. Clique no nome do último lançamento.
1. Pairar sobre a caixa de **prostituto** em **Palcos** e clicar em **Implementar**.
    ![Promover a produção](../media/common/prod-promote.png)
1. Passe sobre a caixa de **proa** novamente em **Palcos** e clique em **Registos**.

A libertação é feita quando todas as tarefas estão completas.

A fase _de proa_ do gasoduto CI/CD utiliza um equilibrador de carga em vez do controlador Dev Spaces Ingress para fornecer acesso aos serviços _de prod._ Os serviços implantados na fase _de proa_ são acessíveis como endereços IP em vez de nomes DNS. Num ambiente de produção, poderá optar por criar o seu próprio controlador Ingress para hospedar os seus serviços com base na sua própria configuração DNS.

Para determinar o IP do serviço webfrontend, clique no passo **IP público de impressão webfrontend** para expandir a saída de log. Utilize o IP apresentado na saída de registo para aceder à aplicação **webfrontend.**

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentação de Espaços Dev na produção
Embora a instrumentação Dev Spaces tenha sido concebida para _não_ se intentar no funcionamento normal da sua aplicação, recomendamos que as suas cargas de trabalho de produção num espaço de nome Kubernetes não esteja ativado com espaços Dev. Utilizar este tipo de espaço de nome kubernetes significa `kubectl` que deve criar o seu espaço de nome de produção utilizando o CLI, ou permitir que o seu sistema CI/CD o crie durante a primeira implementação do Helm. _Selecionando_ ou criando um espaço utilizando ferramentas Dev Spaces adicionará instrumentação Dev Spaces a esse espaço de nome.

Aqui está uma estrutura de espaço de nome de exemplo que suporta o desenvolvimento de recursos, o ambiente 'dev' _e_ a produção, tudo num único cluster kubernetes:

![Estrutura de espaço de nome de exemplo](../media/common/cicd-namespaces.png)

> [!Tip]
> Se já criou um `prod` espaço, e simplesmente gostaria de excluí-lo da instrumentação de Dev Spaces (sem o apagar!), pode fazê-lo com o seguinte comando CLI espaços de V:
>
> `azds space remove -n prod --no-delete`
>
> Pode ser necessário eliminar todas `prod` as cápsulas no espaço de nome depois de o fazer para que possam ser recriadas sem a instrumentação de Dev Spaces.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça o desenvolvimento da equipa utilizando espaços Azure Dev](../team-development-netcore.md)