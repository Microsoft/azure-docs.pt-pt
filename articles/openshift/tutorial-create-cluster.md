---
title: Tutorial - Criar um cluster OpenShift do chapéu vermelho azure
description: Saiba como criar um cluster OpenShift do Microsoft Azure Red Hat usando o Azure CLI
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 58fc695707995aafe4d804ffab8beee7c52b4320
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79455303"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Tutorial: Criar um cluster OpenShift de chapéu vermelho azul

Este tutorial é a primeira parte de uma série. Você vai aprender a criar um cluster OpenShift do Microsoft Azure Red Hat usando o Azure CLI, escaloná-lo e depois apagá-lo para limpar recursos.

Na primeira parte da série, aprenderás a:

> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift
> * [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Este tutorial requer a versão 2.0.65 do Azure CLI.

Antes de começar este tutorial:

Certifique-se de que criou o seu ambiente de [desenvolvimento,](howto-setup-environment.md)que inclui:
- Instalação do último CLI (versão 2.0.65 ou superior)
- Criar um inquilino se ainda não tiver um
- Criar um objeto de aplicação Azure se ainda não tiver um
- Criação de um grupo de segurança
- Criar um utilizador de Diretório Ativo para iniciar sessão no cluster.

## <a name="step-1-sign-in-to-azure"></a>Passo 1: Iniciar sessão em Azure

Se estiver a executar o Azure CLI localmente, `az login` abra uma concha de comando Bash e corra para iniciar sessão no Azure.

```azurecli
az login
```

 Se tiver acesso a várias `az account set -s {subscription ID}` subscrições, faça a substituição `{subscription ID}` pela subscrição que pretende utilizar.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Passo 2: Criar um cluster OpenShift do chapéu vermelho azure

Numa janela de comando Bash, descoloque as seguintes variáveis:

> [!IMPORTANT]
> Escolha um nome para o seu cluster que seja único e toda a criação de minúsculas ou clusters falhará.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Escolha um local para criar o seu cluster. Para uma lista de regiões azure que suporta o OpenShift em Azure, consulte [Regiões Apoiadas](supported-resources.md#azure-regions). Por exemplo: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Definir `APPID` o valor que guardou no passo 5 da [Create a Azure AD app registration](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Detete 'GROUPID' para o valor que guardou no passo 10 da [Create a D.C.](howto-aad-app-configuration.md#create-an-azure-ad-security-group)

```bash
GROUPID=<group ID value>
```

Definir `SECRET` o valor que guardou no passo 8 de Criar um segredo de [cliente.](howto-aad-app-configuration.md#create-a-client-secret)

```bash
SECRET=<secret value>
```

Detete `TENANT` teo valor de ID do inquilino que guardou no passo 7 da [Create um novo inquilino](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Crie o grupo de recursos para o cluster. Execute o seguinte comando da mesma concha bash que usou para definir as variáveis acima:

```azurecli
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcional: Ligar a rede virtual do cluster a uma rede virtual existente

Se não necessitar de ligar a rede virtual (VNET) do cluster que cria a um VNET existente através de um epeering, ignore este passo.

Se espreitar uma rede fora da subscrição predefinida, nessa subscrição, também terá de registar o fornecedor Microsoft.ContainerService. Para isso, execute o comando abaixo nessa subscrição. Caso contrário, se o VNET que está a espreitar estiver localizado na mesma subscrição, pode saltar o passo de registo.

`az provider register -n Microsoft.ContainerService --wait`

Primeiro, pegue o identificador do VNET existente. O identificador será da `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`forma: .

Se não sabe o nome da rede ou o grupo de recursos a que o VNET existente pertence, vá à lâmina das [redes Virtuais](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) e clique na sua rede virtual. A página de rede Virtual aparece e listará o nome da rede e do grupo de recursos a que pertence.

Defina uma variável VNET_ID utilizando o seguinte comando CLI numa concha BASH:

```azurecli
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Por exemplo: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Opcional: Ligar o cluster à Monitorização Azure

Primeiro, obtenha o identificador do espaço de trabalho de log-analytics **existente.** O identificador será da forma:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Se não conhece o nome do espaço de trabalho de análise de log-analytics ou o grupo de recursos a que o espaço de trabalho existente em log-analytics pertence, vá ao Espaço de [Trabalho log-Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) e clique nos seus espaços de trabalho de análise de log-analytics. A página do espaço de trabalho de análise de log-analytics aparece e listará o nome do espaço de trabalho e do grupo de recursos a que pertence.

_Para criar um espaço de trabalho de análise de log-analytics, consulte [Criar espaço de trabalho de análise de log-analytics](../azure-monitor/learn/quick-create-workspace-cli.md)_

Defina uma variável WORKSPACE_ID utilizando o seguinte comando CLI numa concha BASH:

```azurecli
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Criar o cluster

Agora estás pronto para criar um aglomerado. O seguinte criará o cluster no específico inquilino da AD Azure, especificará o objeto da aplicação Azure AD e o segredo para usar como diretor de segurança, e o grupo de segurança que contém os membros que têm acesso administrativo ao cluster.

> [!IMPORTANT]
> Certifique-se de que adicionou corretamente as permissões apropriadas para a aplicação Azure AD, conforme [detalhado aqui](howto-aad-app-configuration.md#add-api-permissions) antes de criar o cluster

Se **não** estiver a espreitar o seu cluster para uma rede virtual ou **não** quiser monitorização Azure, utilize o seguinte comando:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Se **estiver** a espreitar o seu cluster para uma rede `--vnet-peer` virtual, utilize o seguinte comando que adiciona a bandeira:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Se **pretender** monitorização Azure com o seu cluster, utilize o seguinte comando que adiciona a `--workspace-id` bandeira:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Se tiver um erro de que o nome do anfitrião não esteja disponível, pode ser porque o nome do cluster não é único. Tente eliminar o registo original da aplicação e refazer os passos com um nome de cluster diferente na Create um novo registo de [aplicações,](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)omitindo o passo de criar um novo grupo de utilizadores e segurança.




Depois de alguns minutos, `az openshift create` estará completo.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Obtenha o sinal em URL para o seu cluster

Faça com que o URL instete no seu cluster executando o seguinte comando:

```azurecli
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Procure a `publicHostName` saída, por exemplo:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

O sinal em URL para `https://` o `publicHostName` seu cluster será seguido pelo valor.  Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Utilizará este URI no próximo passo como parte do redirecionamento uri do registo da aplicação.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Passo 3: Atualizar o redirecionamento do registo da sua aplicação URI

Agora que tem o sinal em URL para o cluster, delineie o redirecionamento do registo da aplicação UI:

1. Abra a lâmina de [registos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)da App .
2. Clique no objeto de registo da sua aplicação.
3. Clique em **Adicionar um URI redirecionamento**.
4. Certifique-se de que **o TYPE** é **Web** e desloque o **REDIRECT URI** utilizando o seguinte padrão: `https://<public host name>/oauth2callback/Azure%20AD`. Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Clique em **Guardar**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Passo 4: Inscreva-se na consola OpenShift

Está agora pronto para assinar a consola OpenShift para o seu novo cluster. A [Consola Web OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) permite-lhe visualizar, navegar e gerir os conteúdos dos seus projetos OpenShift.

Você precisará de uma nova instância de navegador que não tenha cache a identidade que você normalmente usa para iniciar sessão no portal Azure.

1. Abra uma janela *incógnita* (Chrome) ou *InPrivate* (Microsoft Edge).
2. Navegue para o URL de inscrição que obteve acima, por exemplo:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Inscreva-se utilizando o nome de utilizador que criou no passo 3 da [Create um novo utilizador do Diretório Ativo Azure](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Aparecerá um diálogo solicitado por **permissões.** Clique em **Consentimento em nome da sua organização** e, em seguida, clique em **Aceitar**.

Está agora ligado à consola de cluster.

![Screenshot da consola de cluster OpenShift](./media/aro-console.png)

 Saiba mais sobre [a utilização da consola OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) para criar e construir imagens na documentação OpenShift do [Chapéu Vermelho.](https://docs.openshift.com/aro/welcome/index.html)

## <a name="step-5-install-the-openshift-cli"></a>Passo 5: Instalar o CLI OpenShift

O [CLI OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (ou *Ferramentas OC)* fornece comandos para gerir as suas aplicações e utilitários de nível inferior para interagir com os vários componentes do seu cluster OpenShift.

Na consola OpenShift, clique no ponto de interrogação no canto superior direito pelo seu nome de sinal e selecione **Ferramentas de Linha de Comando**.  Siga o link **Delançamento mais recente** para descarregar e instalar o oc CLI suportado para Linux, MacOS ou Windows.

> [!NOTE]
> Se não vir o ícone do ponto de interrogação no canto superior direito, selecione Catálogo de *Serviços* ou Consola de *aplicação* a partir da parte superior da esquerda.
>
> Alternadamente, pode [baixar o cli oc](https://www.okd.io/download.html) diretamente.

A página **Ferramentas** da Linha de `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`Comando fornece um comando do formulário .  Clique na *Cópia para botão de clipboard* para copiar este comando.  Numa janela terminal, [delicie o seu caminho](https://docs.okd.io/latest/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) para incluir a instalação local das ferramentas oc. Em seguida, inscreva-se no cluster utilizando o comando oc CLI que copiou.

Se não conseguir obter o valor simbólico usando os passos acima, obtenha o valor simbólico de: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
