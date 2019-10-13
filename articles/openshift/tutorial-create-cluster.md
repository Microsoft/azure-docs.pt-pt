---
title: Tutorial – criar um cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Saiba como criar um cluster Microsoft Azure Red Hat OpenShift usando o CLI do Azure
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 01319de8fd72875ca35bb7a869a6eaedee62f2a7
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285532"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Tutorial: criar um cluster do Azure Red Hat OpenShift

Este tutorial é a primeira parte de uma série. Você aprenderá como criar um cluster Microsoft Azure Red Hat OpenShift usando o CLI do Azure, dimensioná-lo e, em seguida, excluí-lo para limpar os recursos.

Na parte um da série, você aprenderá a:

> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift
> * [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Este tutorial requer a versão 2.0.65 do CLI do Azure.
>    
> Antes de usar o Azure Red Hat OpenShift, você precisará comprar um mínimo de 4 nós de aplicativos reservados do Red Hat OpenShift do Azure, conforme descrito em [configurar seu ambiente de desenvolvimento do Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Antes de começar este tutorial:

Certifique-se de que você [configurou seu ambiente de desenvolvimento](howto-setup-environment.md), que inclui:
- Instalando a CLI mais recente (versão 2.0.65 ou superior)
- Criar um locatário se você ainda não tiver um
- Criar um objeto Aplicativo Azure se você ainda não tiver um
- Criando um grupo de segurança
- Criar um usuário Active Directory para entrar no cluster.

## <a name="step-1-sign-in-to-azure"></a>Etapa 1: entrar no Azure

Se você estiver executando o CLI do Azure localmente, abra um shell de comando bash e execute `az login` para entrar no Azure.

```bash
az login
```

 Se você tiver acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Etapa 2: criar um cluster do Azure Red Hat OpenShift

Em uma janela de comando do bash, defina as seguintes variáveis:

> [!IMPORTANT]
> Escolha um nome para o cluster que seja exclusivo e toda a criação de cluster ou em minúsculas falhará.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Escolha um local para criar o cluster. Para obter uma lista de regiões do Azure que dão suporte a OpenShift no Azure, consulte [regiões com suporte](supported-resources.md#azure-regions). Por exemplo: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Defina `APPID` para o valor que você salvou na etapa 5 de [criar um registro de aplicativo do Azure ad](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Defina ' GROUPid ' como o valor que você salvou na etapa 10 de [criar um grupo de segurança do Azure ad](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Defina `SECRET` para o valor que você salvou na etapa 8 de [criar um segredo do cliente](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Defina `TENANT` para o valor da ID de locatário que você salvou na etapa 7 de [criar um novo locatário](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Crie o grupo de recursos para o cluster. Execute o seguinte comando do mesmo shell bash que você usou para definir as variáveis acima:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcional: conectar a rede virtual do cluster a uma rede virtual existente

Se você não precisar conectar a rede virtual (VNET) do cluster que você cria para uma VNET existente por meio de emparelhamento, ignore esta etapa.

Se o emparelhamento para uma rede fora da assinatura padrão nessa assinatura, também será necessário registrar o provedor Microsoft. ContainerService. Para fazer isso, execute o comando abaixo nessa assinatura. Caso contrário, se a VNET que você está emparelhando estiver localizada na mesma assinatura, você poderá ignorar a etapa de registro. 

`az provider register -n Microsoft.ContainerService --wait`

Primeiro, obtenha o identificador da VNET existente. O identificador estará no formato: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Se você não souber o nome da rede ou o grupo de recursos ao qual a VNET existente pertence, vá para a [folha redes virtuais](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) e clique em sua rede virtual. A página rede virtual é exibida e lista o nome da rede e o grupo de recursos ao qual ele pertence.

Defina uma variável VNET_ID usando o seguinte comando da CLI em um shell BASH:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Por exemplo: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Criar o cluster

Agora você está pronto para criar um cluster. O seguinte criará o cluster no locatário do Azure AD especificado, especificará o objeto de aplicativo do Azure AD e o segredo a ser usado como uma entidade de segurança e o grupo de segurança que contém os membros que têm acesso de administrador ao cluster.

> [!IMPORTANT]
> Verifique se você adicionou corretamente as permissões apropriadas para o aplicativo do Azure AD, conforme [detalhado aqui](howto-aad-app-configuration.md#add-api-permissions) antes de criar o cluster

Se você **não** estiver emparelhando o cluster para uma rede virtual, use o seguinte comando:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Se você **estiver** emparelhando o cluster para uma rede virtual, use o seguinte comando que adiciona o sinalizador `--vnet-peer`:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Se você receber um erro informando que o nome do host não está disponível, pode ser porque o nome do cluster não é exclusivo. Tente excluir o registro do aplicativo original e refazer as etapas com um nome de cluster diferente em [criar um novo registro de aplicativo](howto-aad-app-configuration.md#create-an-azure-ad-app-registration), omitindo a etapa de criação de um novo usuário e grupo de segurança.

Após alguns minutos, `az openshift create` será concluído.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Obter a URL de entrada para o cluster

Obtenha a URL para entrar no cluster executando o seguinte comando:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Procure o `publicHostName` na saída, por exemplo: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

A URL de entrada para seu cluster será `https://` seguido pelo valor de `publicHostName`.  Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Você usará esse URI na próxima etapa como parte do URI de redirecionamento de registro do aplicativo.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Etapa 3: atualizar o URI de redirecionamento de registro do aplicativo

Agora que você tem a URL de entrada para o cluster, defina a interface do usuário de redirecionamento de registro do aplicativo:

1. Abra a [folha registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Clique no seu objeto de registro de aplicativo.
3. Clique em **Adicionar um URI de redirecionamento**.
4. Verifique se o **tipo** é **Web** e defina o **URI de redirecionamento** usando o seguinte padrão: `https://<public host name>/oauth2callback/Azure%20AD`. Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Clicar em **Guardar**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Etapa 4: entrar no console do OpenShift

Agora você está pronto para entrar no console do OpenShift para o novo cluster. O [console Web do OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) permite que você visualize, procure e gerencie o conteúdo de seus projetos do OpenShift.

Você precisará de uma nova instância do navegador que não armazenou em cache a identidade que você normalmente usa para entrar no portal do Azure.

1. Abra uma janela do *Incognito* (Chrome) ou uma janela não *privada* (Microsoft Edge).
2. Navegue até a URL de logon que você obteve acima, por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Entre usando o nome de usuário que você criou na etapa 3 de [criar um novo usuário Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Uma caixa de diálogo **permissões solicitadas** será exibida. Clique em **consentimento em nome de sua organização** e, em seguida, clique em **aceitar**.

Agora você está conectado ao console do cluster.

![Captura de tela do console de cluster OpenShift](./media/aro-console.png)

 Saiba mais sobre como [usar o console do OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) para criar e compilar imagens na documentação do [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) .

## <a name="step-5-install-the-openshift-cli"></a>Etapa 5: instalar a CLI do OpenShift

A [CLI do OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (ou as *Ferramentas do OC*) fornece comandos para gerenciar seus aplicativos e utilitários de nível inferior para interagir com os vários componentes do cluster do OpenShift.

No console do OpenShift, clique no ponto de interrogação no canto superior direito pelo nome de entrada e selecione **ferramentas de linha de comando**.  Siga o link de **versão mais recente** para baixar e instalar a CLI do OC com suporte para Linux, MacOS ou Windows.

> [!NOTE]
> Se você não vir o ícone de ponto de interrogação no canto superior direito, selecione *Catálogo de serviços* ou console de *aplicativo* no menu suspenso superior esquerdo.
>
> Como alternativa, você pode [baixar a CLI do OC](https://www.okd.io/download.html) diretamente.

A página de **ferramentas de linha de comando** fornece um comando do formulário `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Clique no botão *copiar para a área de transferência* para copiar este comando.  Em uma janela de terminal, [defina o caminho](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) para incluir a instalação local das ferramentas do OC. Em seguida, entre no cluster usando o comando da CLI do OC que você copiou.

Se você não pôde obter o valor do token usando as etapas acima, obtenha o valor do token de: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
