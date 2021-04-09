---
title: Utilizar CI/CD com o Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Saiba como configurar a integração contínua/implementação contínua utilizando Azure DevOps com Espaços Azure Dev
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.openlocfilehash: 142f01a04066b919a81e1ad1ea95efa31f51f6ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548738"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Utilizar CI/CD com o Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Este artigo guia-o através da configuração da integração contínua/implementação contínua (CI/CD) ao Serviço Azure Kubernetes (AKS) com espaços Dev ativados. O CI/CD para a AKS permite que as atualizações de aplicações sejam automaticamente implementadas sempre que o código comprometido é empurrado para o seu repositório de origem. A utilização de CI/CD em conjunto com um cluster habilitado para os Dev Spaces é útil porque pode manter uma linha de base da aplicação atualizada para a equipa trabalhar.

![Diagrama de EXEMPLO CI/CD](../media/common/ci-cd-simple.png)

Embora este artigo o guie com Azure DevOps, os mesmos conceitos aplicar-se-iam a sistemas de CI/CD como Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Pré-requisitos
* Cluster de Serviço Azure Kubernetes (AKS) com espaços Azure Dev habilitados
* [Azure Dev Spaces CLI instalado](upgrade-tools.md)
* [Azure DevOps organização com um projeto](/azure/devops/user-guide/sign-up-invite-teammates)
* [Registo do Contentor de Azure (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Detalhes da conta [do administrador do](../../container-registry/container-registry-authentication.md#admin-account) registo do contentor Azure disponíveis
* [Autorize o seu cluster AKS para retirar do seu Registo de Contentores Azure](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Baixar código de amostra
Por uma questão de tempo, vamos criar um garfo do nosso repositório de código de amostra GitHub. Vá https://github.com/Azure/dev-spaces e selecione **Fork**. Assim que o processo do garfo estiver concluído, **clone** a sua versão forquilhada do repositório localmente. Por defeito, o ramo _principal_ será verificado, mas incluímos algumas alterações na _azds_updates_ filial, que também deveria ter sido transferida durante o seu garfo. O _azds_updates_ ramo contém atualizações que pedimos para fazer manualmente nas secções tutoriais de Dev Spaces, bem como alguns ficheiros YAML e JSON pré-fabricados para agilizar a implementação do sistema CI/CD. Você pode usar um comando como `git checkout -b azds_updates origin/azds_updates` para verificar a _azds_updates_ filial no seu repositório local.

## <a name="dev-spaces-setup"></a>Configuração de Dev Spaces
Crie um novo espaço chamado _dev_ usando o `azds space select` comando. O _espaço dev_ será utilizado pelo seu pipeline CI/CD para empurrar as alterações de código. Também será usado para criar _espaços para crianças_ com base no _dev._

```cmd
azds space select -n dev
```

Quando solicitado para selecionar um espaço dev dos pais, selecione _\<none\>_ .

Depois de o seu espaço dev ter sido criado, precisa determinar o sufixo do hospedeiro. Utilize o `azds show-context` comando para mostrar o sufixo do controlador Azure Dev Spaces Ingress.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

No exemplo acima, o sufixo do hospedeiro é _fedcba098.eus.azds.io_. Este valor é usado mais tarde ao criar a definição de libertação.

O espaço _dev_ conterá sempre o estado mais recente do repositório, uma linha de base, para que os desenvolvedores possam criar _espaços infantis_ a partir de _dev_ para testar as suas mudanças isoladas no contexto da app maior. Este conceito é discutido mais detalhadamente nos tutoriais de Dev Spaces.

## <a name="creating-the-build-definition"></a>Criar a definição de construção
Abra o seu projeto de equipa Azure DevOps num navegador web e navegue para a secção _Pipelines._ Primeiro, clique na sua foto de perfil no canto superior direito do site Azure DevOps, abra o painel de funcionalidades de pré-visualização e desative a experiência de criação do _novo gasoduto YAML_:

![Painel de pré-visualização de abertura](../media/common/preview-feature-open.png)

A opção de desativar:

![Nova opção de experiência de criação de gasoduto YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> A experiência de criação de gasodutoS Azure DevOps _New YAML_ apresenta conflitos com a criação de oleodutos de construção pré-definidos neste momento. Precisa desativá-lo por enquanto para implementar o nosso oleoduto de construção pré-definido.

No _ramo azds_updates_ incluímos um simples [Azure Pipeline YAML](/azure/devops/pipelines/yaml-schema?tabs=schema) que define os passos de construção necessários para *o mywebapi* e *webfrontend*.

Dependendo do idioma que escolheu, o pipeline YAML foi verificado num caminho semelhante ao de: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para criar um Pipeline a partir deste ficheiro:
1. Na sua página principal do projeto DevOps, navegue até Pipelines > Builds.
1. Selecione a opção para criar um **novo** oleoduto de construção.
1. Selecione **o GitHub** como fonte, autorize com a sua conta GitHub se necessário e selecione o ramo _azds_updates_ a partir da sua versão forçada do repositório de aplicação de amostra _de dev-spaces._
1. Selecione **Configuração como código**, ou **YAML,** como o seu modelo.
1. É agora apresentada uma página de configuração para o seu pipeline de construção. Como mencionado acima, navegue para o caminho específico da linguagem para o caminho do **ficheiro YAML** usando o **botão ...** Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Vá ao separador **Variáveis.**
1. Adicione manualmente _o DockerId_ como variável, que é o nome de utilizador da sua conta de [administrador do Registo de Contentores Azure](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado no artigo pré-requisitos)
1. Adicione manualmente _dockerPassword_ como uma variável, que é a palavra-passe da sua conta de [administrador do Registo de Contentores Azure](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de especificar _o dockerPassword_ como um Segredo (selecionando o ícone de bloqueio) para fins de segurança.
1. **Selecione Guardar & fila**.

Agora tem uma solução de CI que irá automaticamente construir *mywebapi* e *webfrontend* para qualquer atualização empurrada para o ramo _azds_updates_ do seu garfo GitHub. Pode verificar se as imagens do Docker foram empurradas navegando para o portal Azure, selecionando o seu Registo de Contentores Azure e navegando no **separador Repositórios.** Pode levar vários minutos para que as imagens construam e apareçam no registo do seu contentor.

![Repositórios do Registo de Contentores de Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criar a definição de libertação

1. Na sua página principal do projeto DevOps, navegue para Pipelines > Releases
1. Se estiver a trabalhar num novo DevOps Project que ainda não contenha uma definição de lançamento, terá primeiro de criar uma definição de libertação vazia antes de prosseguir. A opção Import não é exibida na UI até ter uma definição de libertação existente.
1. À esquerda, clique no botão **+ Novo** e, em seguida, clique em Importar **um pipeline**.
1. Clique **em Procurar** e selecione do `samples/release.json` seu projeto.
1. Clique em **OK**. Note que o painel pipeline carregou com a página de edição de definição de lançamento. Note também que existem alguns ícones de aviso vermelho que indicam detalhes específicos do cluster que ainda devem ser configurados.
1. À esquerda do painel de gasoduto, clique na bolha de **artefacto Adicionar.**
1. No dropdown **Source,** selecione o gasoduto de construção que criou anteriormente.
1. Para a **versão Predefinição,** escolha **o mais recente do ramo padrão do gasoduto de construção com etiquetas**.
1. Deixe **as etiquetas vazias.**
1. Desa estalades de **Origem** `drop` . O valor **do pseudónimo 'Fonte'** é utilizado pelas tarefas de desbloqueio predefinidas, pelo que deve ser definido.
1. Clique em **Adicionar**.
1. Clique agora no ícone do relâmpago na fonte de artefacto recém-criada, `drop` como mostra abaixo:

    ![Desbloqueie a configuração contínua da implantação do artefacto](../media/common/release-artifact-cd-setup.png)
1. Ativar o **gatilho de implantação contínua**.
1. Passe sobre o separador **Tarefas** ao lado **do Pipeline** e clique em _dev_ para editar as tarefas de fase _dev._
1. Verifique se **o Gestor de Recursos Azure** está selecionado sob o Tipo de **Ligação.** e você vê os três controlos de dropdown realçados no vermelho: ![ Configuração de definição de lançamento](../media/common/release-setup-tasks.png)
1. Selecione a subscrição Azure que está a usar com Azure Dev Spaces. Também poderá ter de clicar **em Autorização**.
1. Selecione o grupo de recursos e o cluster que está a utilizar com espaços Azure Dev.
1. Clique no **trabalho do Agente.**
1. **Selecione Hosted Ubuntu 1604** **embaixo da piscina do agente**.
1. Passe sobre o seletor **tasks** no topo, clique em _prod_ para editar as tarefas de estágio de _prod._
1. Verifique se **o Gestor de Recursos Azure** está selecionado sob o Tipo de **Ligação.** e selecione a subscrição Azure, grupo de recursos e cluster que está a usar com Azure Dev Spaces.
1. Clique no **trabalho do Agente.**
1. **Selecione Hosted Ubuntu 1604** **embaixo da piscina do agente**.
1. Clique no separador **Variáveis** para atualizar as variáveis para a sua libertação.
1. Atualize o valor do **DevSpacesHostS sufixo** de **UPDATE_ME** para o sufixo do anfitrião. O sufixo do anfitrião é exibido quando executou o `azds show-context` comando mais cedo.
1. Clique em **Guardar** no canto superior direito e **OK**.
1. Clique **+ Desbloqueie** (ao lado do botão Guardar) e **Crie um desbloqueio**.
1. Sob **artefactos,** verifique se a mais recente construção do seu gasoduto de construção está selecionada.
1. Clique em **Criar**.

Um processo de lançamento automatizado irá agora começar, implantando as tabelas *mywebapi* e *webfrontend* para o seu cluster Kubernetes no espaço de nível superior _dev._ Pode monitorizar o progresso da sua versão no portal web Azure DevOps:

1. Navegue para a secção **lançamentos** sob **pipelines**.
1. Clique no gasoduto de libertação para a aplicação da amostra.
1. Clique no nome da última versão.
1. Passe sobre a caixa **dev** sob **Estágios** e clique em **Logs**.

O desbloqueio é feito quando todas as tarefas estão completas.

> [!TIP]
> Se o seu desbloqueio falhar com uma mensagem de erro como *O UPGRADE FALHADO: cronometrado à espera da condição,* tente inspecionar as cápsulas do seu cluster [utilizando o painel kubernetes](../../aks/kubernetes-dashboard.md). Se vir que as cápsulas não começam com mensagens de erro como *"azdsexample.azurecr.io/mywebapi:122": erro do rpc: código = Desc / Desc / Resposta de erro da daemon: Obtenha https: \/ /azdsexample.azurecr.io/v2/mywebapi/manifests/122: não autorizada: autenticação necessária,* pode ser porque o seu cluster não foi autorizado a retirar do seu Registo de Contentores Azure. Certifique-se de que completou o conjunto De Autorização do seu AKS para retirar do seu pré-requisito [do Registo do Contentor Azure.](../../aks/cluster-container-registry-integration.md)

Tem agora um oleoduto CI/CD totalmente automatizado para o seu garfo GitHub das aplicações de amostras Dev Spaces. Cada vez que você cometer e empurrar código, o pipeline de construção irá construir e empurrar as imagens *mywebapi* e *webfrontend* para a sua instância ACR personalizada. Em seguida, o oleoduto de lançamento implantará o gráfico Helm para cada aplicação no espaço _dev_ no seu cluster habilitado para o Dev Spaces.

## <a name="accessing-your-_dev_-services"></a>Acede aos seus _serviços dev_
Após a implementação, a versão _dev_ da *webfrontend* pode ser acedida com um URL público como: `http://dev.webfrontend.fedcba098.eus.azds.io` . Pode encontrar este URL executando o `azds list-uri` comando: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implantação na Produção

Promover manualmente uma versão específica para _o prod_ utilizando o sistema CI/CD criado neste tutorial:
1. Navegue para a secção **lançamentos** sob **pipelines**.
1. Clique no gasoduto de libertação para a aplicação da amostra.
1. Clique no nome da última versão.
1. Passe sobre a caixa **de prod** em **fases** e clique em **Implementar**.
    ![Promover a produção](../media/common/prod-promote.png)
1. Volte a pairar sobre a caixa **de prod** sob **Estágios** e clique em **Logs**.

O desbloqueio é feito quando todas as tarefas estão completas.

A fase _de prod_ do gasoduto CI/CD utiliza um equilibrador de carga em vez do controlador Dev Spaces Ingress para fornecer acesso a serviços _de prod._ Os serviços implantados na fase _prod_ são acessíveis como endereços IP em vez de nomes DNS. Num ambiente de produção, poderá optar por criar o seu próprio controlador Ingress para hospedar os seus serviços com base na sua própria configuração de DNS.

Para determinar o IP do serviço de webfrontend, clique no passo IP público print  **webfrontend** para expandir a saída de registo. Utilize o IP apresentado na saída de registo para aceder à aplicação **webfrontend.**

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentação de Dev Spaces em produção
Embora a instrumentação de Dev Spaces tenha sido projetada para _não_ atrapalhar o funcionamento normal da sua aplicação, recomendamos executar as suas cargas de trabalho de produção num espaço de nome Kubernetes que não esteja habilitado com dev Spaces. A utilização deste tipo de espaço de nomes Kubernetes significa que deve criar o seu espaço de nome de produção utilizando o `kubectl` CLI, ou permitir que o seu sistema CI/CD o crie durante a primeira implementação do Helm. _Selecionar_ ou criar um espaço utilizando a ferramenta Dev Spaces irá adicionar a instrumentação Dev Spaces a esse espaço de nome.

Aqui está um exemplo de estrutura de espaço de nome que suporta o desenvolvimento de recursos, o ambiente 'dev' _e_ a produção, tudo num único cluster Kubernetes:

![Estrutura de espaço de nome exemplo](../media/common/cicd-namespaces.png)

> [!Tip]
> Se já criou um `prod` espaço, e simplesmente gostaria de excluí-lo da instrumentação Dev Spaces (sem o eliminar!), pode fazê-lo com o seguinte comando Dev Spaces CLI:
>
> `azds space remove -n prod --no-delete`
>
> Pode ser necessário eliminar todas as cápsulas no espaço de `prod` nomes depois de o fazer para que possam ser recriadas sem instrumentação de Dev Spaces.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](../how-dev-spaces-works.md)
