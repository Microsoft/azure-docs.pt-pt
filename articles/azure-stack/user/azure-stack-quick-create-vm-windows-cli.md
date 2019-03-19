---
title: Criar uma máquina virtual do Windows no Azure Stack com a CLI do Azure | Documentos da Microsoft
description: Saiba como criar uma VM do Windows no Azure Stack com a CLI do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 15dd878b7ff322ac8243b37649f16fed4e068af4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091720"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Início rápido: criar uma máquina virtual do Windows Server com a CLI do Azure no Azure Stack

‎*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode criar uma máquina virtual do Windows Server 2016 com a CLI do Azure. Siga os passos neste artigo para criar e utilizar uma máquina virtual. Este artigo também dá-lhe os passos seguintes:

* Ligar à máquina virtual com um cliente remoto.
* Instalar o servidor web IIS e ver a home page do padrão.
* Limpe seus recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que o operador do Azure Stack é adicionado a **Windows Server 2016** imagem para o mercado do Azure Stack.

* O Azure Stack requer uma versão específica da CLI do Azure para criar e gerir os recursos. Se não tiver a CLI do Azure configurado para o Azure Stack, siga os passos para [instalar e configurar a CLI do Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico onde pode implementar e gerir recursos do Azure Stack. A partir do seu ambiente do Azure Stack, execute o [criar grupo az](/cli/azure/group#az-group-create) comando para criar um grupo de recursos.

> [!NOTE]
>  Os valores são atribuídos para todas as variáveis nos exemplos de código. No entanto, pode atribuir novos valores, se quiser.

O exemplo seguinte cria um grupo de recursos com o nome myResourceGroup na localização local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma máquina virtual (VM) com o [az vm criar](/cli/azure/vm#az-vm-create) comando. O exemplo seguinte cria uma VM com o nome myVM. Este exemplo utiliza Demouser para um nome de utilizador administrativo e Demouser@123 como a palavra-passe do utilizador. Altere estes valores para algo que seja apropriado para seu ambiente.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Quando a VM é criada, o **PublicIPAddress** parâmetro na saída contém o endereço IP público para a máquina virtual. Anote este endereço, porque irá precisar dele para aceder à máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web

Uma vez que esta VM vai executar o servidor web IIS, terá de abrir a porta 80 ao tráfego da Internet.

Utilize o [az vm open-port](/cli/azure/vm) comando para abrir a porta 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Utilize o comando seguinte para criar uma ligação de ambiente de trabalho remoto à máquina virtual. Substitua o endereço IP da sua máquina virtual "Endereço IP público". Quando lhe for pedido, introduza o nome de utilizador e palavra-passe que utilizou para a máquina virtual.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalar o IIS com o PowerShell

Agora que já iniciou sessão na máquina virtual, pode utilizar o PowerShell para instalar o IIS. Inicie o PowerShell na máquina virtual e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Ver a página de boas-vindas do IIS

Pode utilizar um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Utilize o endereço IP público documentado na secção anterior para visitar a página predefinida.

![Site predefinido do IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que não precisa mais. Utilize o [eliminação do grupo de az](/cli/azure/group#az-group-delete) de comando para remover o grupo de recursos, a máquina virtual, e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma máquina de virtual básica do Windows Server. Para saber mais sobre as máquinas virtuais do Azure Stack, avance para [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
