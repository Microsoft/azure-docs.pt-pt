---
title: Tutorial - criar um cluster do Azure Red Hat OpenShift | Documentos da Microsoft
description: Saiba como criar um cluster do Microsoft Azure Red Hat OpenShift com a CLI do Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/14/2019
ms.openlocfilehash: d8d767b97e335feeb31851c89a9b21eddf7157ea
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962222"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Tutorial: Criar um cluster do Azure Red Hat OpenShift

Este tutorial é a primeira parte de uma série. Irá aprender a criar um cluster do Microsoft Azure Red Hat OpenShift com a CLI do Azure, dimensioná-lo, em seguida, elimine-o para limpar os recursos.

Parte da série, ficará a saber como:

> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift
> * [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Este tutorial requer a versão 2.0.65 da CLI do Azure.
>    
> Antes de poder utilizar o Azure Red Hat OpenShift, terá de comprar um mínimo de 4 nós de aplicação do Azure Red Hat OpenShift reservado, conforme descrito em [configurar o ambiente de desenvolvimento do Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Antes de começar este tutorial:

Certifique-se de que [configurar o ambiente de desenvolvimento](howto-setup-environment.md), que inclui:
- Instalar a CLI mais recente (versão 2.0.65 ou superior)
- Criação de um inquilino se ainda não tiver uma
- Criar um objeto de aplicativo do Azure, se ainda não tiver uma
- Criar um grupo de segurança
- Criar um utilizador do Active Directory para iniciar sessão no cluster.

## <a name="step-1-sign-in-to-azure"></a>Passo 1: Inicie sessão no  Azure

Se estiver a executar localmente a CLI do Azure, abra um comando do Bash shell e execute `az login` para iniciar sessão no Azure.

```bash
az login
```

 Se tiver acesso a várias subscrições, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` com a subscrição que pretende utilizar.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Passo 2: Criar um cluster do Azure Red Hat OpenShift

Na janela de comando Bash, defina as seguintes variáveis:

> [!IMPORTANT]
> Escolha um nome para cluster que é exclusivo e a criação de cluster ou em minúsculas todos os irá falhar.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Escolha uma localização para criar o cluster. Para obter uma lista de regiões do azure que suporta o OpenShift no Azure, consulte [regiões suportadas](supported-resources.md#azure-regions). Por exemplo: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Definir `APPID` para o valor que guardou no passo 5 da [criar um registo de aplicação do Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Definir 'GROUPID' para o valor que guardou no passo 10 da [criar um grupo de segurança do Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Definir `SECRET` para o valor que guardou no passo 8 [criar um segredo de cliente](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Definir `TENANT` para o valor de ID de inquilino que guardou no passo 7 da [criar um novo inquilino](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Crie o grupo de recursos para o cluster. Execute o seguinte comando a partir da shell de Bash mesmo que utilizou para definir as variáveis acima:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcional: Ligar uma rede virtual do cluster para uma rede virtual existente

Se não precisar de ligar a rede virtual (VNET) do cluster que cria para uma VNET existente por meio de peering, ignore este passo.

Em primeiro lugar, obtenha o identificador da VNET existente. O será do formulário: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Se não souber o nome de rede ou o grupo de recursos a que pertence a VNET existente, vá para o [painel redes virtuais](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) e clique em sua rede virtual. A página de rede Virtual é apresentada e irá listar o nome da rede e o grupo de recursos a que pertence.

Defina uma variável VNET_ID usando o seguinte comando da CLI numa BASH shell:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Por exemplo: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Criar o cluster

Agora, está pronto para criar um cluster. O seguinte irá criar o cluster no Azure AD especificado de inquilino, especifique o objeto de aplicação do Azure AD e o segredo para utilizar como uma entidade de segurança e o grupo de segurança que contém os membros que tenham acesso de administrador para o cluster.

Se estiver **não** peering seu cluster a uma rede virtual, utilize o seguinte comando:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Se **estão** peering seu cluster a uma rede virtual, utilize o seguinte comando que adiciona o `--vnet-peer` sinalizador:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Se obtiver um erro que o nome do anfitrião não está disponível, poderá ser porque o nome do cluster não é exclusivo. Tente eliminar o registo de aplicação original e refazer as etapas com outro nome de cluster no [criar um novo registo de aplicações] (howto-aad-app-configuration.md#create-a-new-app-registration), omitindo o passo de criação de um novo grupo de segurança e utilizadores.

Após alguns minutos, `az openshift create` será concluída.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Obter o início de sessão no URL do seu cluster

Obter o URL para iniciar sessão no seu cluster, executando o seguinte comando:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Procure o `publicHostName` na saída, por exemplo: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

URL de início de sessão para o seu cluster estará `https://` seguido do `publicHostName` valor.  Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Irá utilizar este URI no próximo passo como parte do URI de redirecionamento do registo da aplicação.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Passo 3: Atualizar o seu URI de redirecionamento registo de aplicação

Agora que tem o início de sessão no URL para o cluster, defina o redirecionamento de registo de aplicação da interface do Usuário:

1. Abra o [painel de registos de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Clique no seu objeto de registo de aplicação.
3. Clique em **adicionar um URI de redirecionamento**.
4. Certifique-se de que **tipo** é **Web** e defina o **URI de REDIRECIONAMENTO** usando o seguinte padrão: `https://<public host name>/oauth2callback/Azure%20AD`. Por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Clique em **Guardar**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Passo 4: Inicie sessão consola do OpenShift

Agora, está pronto para iniciar sessão na consola do OpenShift para o novo cluster. O [consola de Web do OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) permite-lhe visualizar, navegar e gerenciar o conteúdo dos seus projetos do OpenShift.

Precisará de uma instância do navegador nova que ainda não armazenados em cache a identidade que normalmente utiliza para iniciar sessão no portal do Azure.

1. Abra um *incognito* janela (cromado) ou *InPrivate* janela (Microsoft Edge).
2. Navegue para o URL de início de sessão que obteve anteriormente, por exemplo: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Inicie sessão com o nome de utilizador que criou no passo 3 [criar um novo utilizador do Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

R **permissões solicitadas** será apresentada a caixa de diálogo. Clique em **consentir em nome da sua organização** e, em seguida, clique em **Accept**.

Agora são registadas para a consola de cluster.

![Captura de ecrã da consola de cluster do OpenShift](./media/aro-console.png)

 Saiba mais sobre [utilizando a consola do OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) para criar e incorporado imagens a [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) documentação.

## <a name="step-5-install-the-openshift-cli"></a>Passo 5: Instalar a CLI do OpenShift

O [OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (ou *ferramentas de OC*) fornecer comandos para gestão de aplicações e os utilitários de nível inferior para interagir com os vários componentes do seu cluster do OpenShift.

Na consola do OpenShift, clique com o ponto de interrogação no canto superior direito por seu nome de início de sessão e selecione **ferramentas de linha de comandos**.  Siga os **versão mais recente** link para baixar e instalar a CLI de oc suportado para o Linux, MacOS ou Windows.

> [!NOTE]
> Se não vir o ícone de ponto de interrogação no canto superior direito, selecione *catálogo de serviços* ou *consola do Application* o canto superior esquerdo lista pendente.
>
> Em alternativa, pode [transferir o CLI de oc](https://www.okd.io/download.html) diretamente.

O **ferramentas de linha de comandos** página fornece um comando do formulário `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Clique nas *copiar para área de transferência* botão Copiar esse comando.  Na janela de terminal [definir seu caminho](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) para incluir a sua instalação local das ferramentas de oc. Em seguida, inicie sessão para o cluster utilizando o comando da CLI de oc que copiou.

Se não foi possível obter o valor do token através dos passos acima, obter o valor do token do: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um cluster do Azure Red Hat OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Dimensionar um cluster do Azure Red Hat OpenShift](tutorial-scale-cluster.md)
