---
title: Criar uma máquina virtual Linux com a CLI do Azure no Azure Stack | Documentos da Microsoft
description: Crie uma máquina virtual Linux com a CLI no Azure Stack.
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
ms.openlocfilehash: b3fbd91ddf06d4515ee55d24ba9330d0af6dc124
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540328"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Início rápido: criar uma máquina de virtual de servidor do Linux com a CLI do Azure no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode criar uma máquina virtual do Ubuntu Server 16.04 LTS com a CLI do Azure. Siga os passos neste artigo para criar e utilizar uma máquina virtual. Este artigo também dá-lhe os passos para:

* Ligar à máquina virtual com um cliente remoto.
* Instalar o servidor web NGINX e ver a home page do padrão.
* Limpe os recursos não utilizados.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no mercado do Azure Stack**

   O mercado do Azure Stack não contém uma imagem do Linux por predefinição. Obter o operador do Azure Stack para fornecer a **Ubuntu Server 16.04 LTS** imagem precisa. O operador pode utilizar os passos descritos na [transferir itens do marketplace do Azure para o Azure Stack](../azure-stack-download-azure-marketplace-item.md) artigo.

* O Azure Stack requer uma versão específica da CLI do Azure para criar e gerir os recursos. Se não tiver a CLI do Azure configurado para o Azure Stack, inicie sessão para o [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou um baseado em Windows cliente externo se estiver [ligados através de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e siga os passos para [ instalar e configurar a CLI do Azure](azure-stack-version-profiles-azurecli2.md).

* Uma chave SSH pública com o id_rsa nome guardado no diretório. SSH do seu perfil de utilizador do Windows. Para obter informações detalhadas sobre a criação de chaves SSH, consulte [criar chaves SSH no Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico onde pode implementar e gerir recursos do Azure Stack. Do seu kit de desenvolvimento ou o Azure Stack integrado no sistema, execute o [criar grupo az](/cli/azure/group#az-group-create) comando para criar um grupo de recursos.

>[!NOTE]
 Os valores são atribuídos para todas as variáveis nos exemplos de código. No entanto, pode atribuir novos valores, se quiser.

O exemplo seguinte cria um grupo de recursos com o nome myResourceGroup na localização local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

Criar uma máquina virtual com o [az vm criar](/cli/azure/vm#az-vm-create) comando. O exemplo seguinte cria uma VM com o nome myVM. Este exemplo utiliza Demouser para um nome de utilizador administrativo e Demouser@123 como a palavra-passe do utilizador. Altere estes valores para algo que seja apropriado para seu ambiente.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

O endereço IP público é devolvido na **PublicIpAddress** parâmetro. Anote este endereço, porque irá precisar dele para aceder à máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web

Uma vez que esta máquina virtual vai executar o servidor web IIS, terá de abrir a porta 80 ao tráfego da Internet. Utilize o comando [az vm open-port](/cli/azure/vm) para abrir a porta pretendida.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Utilize SSH para ligar à máquina virtual

Num computador cliente com SSH instalado, ligue à máquina virtual. Se estiver a trabalhar num cliente Windows, utilize [Putty](https://www.putty.org/) para criar a ligação. Para ligar à máquina virtual, utilize o seguinte comando:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalar o servidor web NGINX

Para atualizar recursos de pacote e instalar o pacote NGINX mais recente, execute o seguinte script:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Ver a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 aberta na sua máquina virtual, pode acessar o servidor web usando o endereço IP público da máquina virtual. Abra um browser e navegue para ```http://<public IP address>```.

![Página de boas-vindas do servidor de web do NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que não precisa mais. Pode utilizar o [eliminação do grupo de az](/cli/azure/group#az-group-delete) comando para remover estes recursos. Para eliminar o grupo de recursos e todos os respetivos recursos, execute o seguinte comando:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma máquina de virtual do Linux server básica com um servidor web. Para saber mais sobre as máquinas virtuais do Azure Stack, avance para [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
