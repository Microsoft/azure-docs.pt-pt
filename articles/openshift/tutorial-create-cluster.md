---
title: Tutorial - criar um cluster do Azure Red Hat OpenShift | Documentos da Microsoft
description: Saiba como criar um cluster do Microsoft Azure Red Hat OpenShift com a CLI do Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 5bc71a2d0f29fed163fb5c93ebd27df7f66a1325
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080759"
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

Antes de começar este tutorial:

Certifique-se de que [configurar o ambiente de desenvolvimento](howto-setup-environment.md), que inclui:
- Instalar a CLI mais recente
- Criação de um inquilino
- Criação de um objeto de aplicativo do Azure
- Criar um utilizador do Active Directory utilizado para iniciar sessão em aplicações em execução no cluster.

## <a name="step-1-sign-in-to-azure"></a>Passo 1: Iniciar sessão no Azure

Se estiver a executar localmente a CLI do Azure, abra um comando do Bash shell e execute `az login` para iniciar sessão no Azure.

```bash
az login
```

 Se tiver acesso a várias subscrições, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` com a subscrição que pretende utilizar.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Passo 2: Criar um cluster do Azure Red Hat OpenShift

Na janela de comando Bash, defina as seguintes variáveis:

> [!IMPORTANT]
> O nome do cluster tem de estar todo em minúsculo ou a criação do cluster irá falhar.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Utilize o mesmo nome para o cluster que escolheu no passo 6 de [criar novo registo de aplicação](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Escolha uma localização para criar o cluster. Para obter uma lista de regiões do azure que suporta o OpenShift no Azure, consulte [regiões suportadas](supported-resources.md#azure-regions). Por exemplo: `LOCATION=eastus`.

Definir `FQDN` para o nome completamente qualificado do seu cluster. Este nome é composto pelo nome do cluster, a localização, e `.cloudapp.azure.com` acrescentado ao fim. Este é o mesmo que o URL de início de sessão que criou no passo 6 de [criar novo registo de aplicação](howto-aad-app-configuration.md#create-a-new-app-registration). Por exemplo:  

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Definir `APPID` para o valor que guardou no passo 9 [criar um novo registo de aplicação](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Definir `SECRET` para o valor que guardou no passo 6 de [criar um segredo de cliente](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Definir `TENANT` para o valor de ID de inquilino que guardou no passo 7 da [criar um novo inquilino](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Crie o grupo de recursos para o cluster. Execute o seguinte comando a partir da shell de Bash que utilizou para definir as variáveis acima:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcional: Ligar uma rede virtual do cluster para uma rede virtual existente

Se não precisar de ligar a rede virtual (VNET) no cluster que cria uma VNET já existente, ignore este passo.

Em primeiro lugar, obtenha o identificador da VNET existente. O será do formulário: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Se não souber o nome de rede ou o grupo de recursos a que pertence a VNET existente, vá para o [painel redes virtuais](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) e clique em sua rede virtual. A página de rede Virtual é apresentada e irá listar o nome da rede e o grupo de recursos a que pertence.

Defina uma variável VNET_ID usando o seguinte comando da CLI numa BASH shell:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Por exemplo: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Criar o cluster

Agora, está pronto para criar um cluster.

 Se a rede virtual do cluster não estiver a ligar a uma rede virtual existente, omita à direita `--vnet-peer-id $VNET_ID` parâmetro no exemplo a seguir.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Após alguns minutos, `az openshift create` será concluído com êxito e devolver uma resposta JSON que contém os detalhes do seu cluster.

> [!NOTE]
> Se obtiver um erro que o nome do anfitrião não está disponível, poderá ser porque o nome do cluster não é exclusivo. Tente eliminar o registo de aplicação original e refazer os passos em [criar um novo registo de aplicações] (howto-aad-app-configuration.md#create-a-new-app-registration) (omitindo o último passo da criação de um novo utilizador, desde que já criou um) com um nome do cluster diferente.

## <a name="step-3-sign-in-to-the-openshift-console"></a>Passo 3: Inicie sessão consola do OpenShift

Agora, está pronto para iniciar sessão na consola do OpenShift para o novo cluster. O [consola de Web do OpenShift](https://docs.openshift.com/dedicated/architecture/infrastructure_components/web_console.html) permite-lhe visualizar, navegar e gerenciar o conteúdo dos seus projetos do OpenShift.

Vai iniciar sessão como o [novo utilizador do Azure AD](howto-aad-app-configuration.md#create-a-new-active-directory-user) criadas para testar. Para tal, terá uma instância do navegador nova que ainda não armazenados em cache a identidade que normalmente utiliza para iniciar sessão no portal do Azure.

1. Abra um *incognito* janela (cromado) ou *InPrivate* janela (Microsoft Edge).
2. Navegue para o URL de início de sessão que criou no passo 6 de [criar um novo registo de aplicação](howto-aad-app-configuration.md#create-a-new-app-registration). Por exemplo, https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> A consola do OpenShift utiliza um certificado autoassinado.
> Quando lhe for pedido no seu browser, ignore o aviso e aceitar o certificado "não confiável".

Inicie sessão com o utilizador e palavra-passe que criou no [criar um novo utilizador do Active Directory](howto-aad-app-configuration.md#create-a-new-active-directory-user) quando o **permissões solicitadas** for apresentada a caixa de diálogo, selecione **consentir em nome da sua organização**  e, em seguida **aceitar**.

Agora são registadas para a consola de cluster.

[Captura de ecrã da consola de cluster do OpenShift](./media/aro-console.png)

 Pode saber mais sobre [utilizando a consola do OpenShift](https://docs.openshift.com/dedicated/getting_started/developers_console.html) para criar e incorporado imagens a [Red Hat OpenShift](https://docs.openshift.com/dedicated/welcome/index.html) documentação.

## <a name="step-4-install-the-openshift-cli"></a>Passo 4: Instalar a CLI do OpenShift

O [OpenShift CLI](https://docs.openshift.com/dedicated/cli_reference/get_started_cli.html) (ou *ferramentas de OC*) fornecer comandos para gestão de aplicações e os utilitários de nível inferior para interagir com os vários componentes do seu cluster do OpenShift.

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