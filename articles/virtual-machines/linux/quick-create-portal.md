---
title: Início Rápido - Criar uma VM do Linux no portal do Azure | Microsoft Docs
description: Neste início rápido, vai aprender a utilizar o portal do Azure para criar uma máquina virtual do Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 8/16/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 189d6d0030264590986d6fe2af47d35705cfb08b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575800"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Início rápido: Criar uma máquina virtual do Linux no portal do Azure

As máquinas virtuais do Azure (VMs) podem ser criadas através do portal do Azure. O portal do Azure é uma interface de utilizador baseada no browser para criar VMs e os respetivos recursos associados. Este início rápido mostra como utilizar o portal do Azure para implementar uma máquina virtual (VM) do Linux com o Ubuntu 16.04 LTS. Para ver a VM em ação, estabeleça o SSH para a VM e instale o servidor Web NGINX.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Precisa de um par de chaves SSH para concluir este início rápido. Se já tiver um par de chaves SSH, pode ignorar este passo.

Abra uma shell do Bash e utilize [ssh-keygen](https://www.ssh.com/ssh/keygen/) para criar um par de chaves SSH. Um exemplo de comando para fazer isso é listado abaixo. Se não tiver uma shell do Bash no computador local, pode utilizar o [Azure Cloud Shell](https://shell.azure.com/bash).

```bash
ssh-keygen -t rsa -b 2048
```

Você será solicitado a inserir um arquivo no qual salvar o par de chaves. Você pode inserir um local de arquivo específico ou apenas pressionar ' Enter ' para salvar no local padrão indicado entre colchetes. Em seguida, será solicitado que você insira uma frase secreta. Você pode inserir uma frase secreta para sua chave SSH ou pode pressionar ' Enter ' para continuar sem uma frase secreta.

```bash
[root@linuxvm ~]$ ssh-keygen -t rsa -b 2048
Enter the file in which to save the key (home/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your public key has been saved in /home/root/.ssh/id_rsa.pub.
The key fingerprint is: SHA256:kkQS13gbaxevy4ULH0mW6wLIkcFm0twx/rIlSo1fIqU
The key's randomart image is:
+---[RSA 2018]----+
|   +oo=+         |
|  . B=o.+ .      |
|   + o+. + +     |
|    o* o+ = .    |
|   .EoB.S+ =     |
|   .o+.O. * .    |
|    . o. = =     |
|        . *      |
|         .       |
+----[SHA256]-----+
```
O `ssh-keygen` comando gera chaves públicas e privadas com o nome padrão de `id_rsa` no `~/.ssh directory`. O comando devolve o caminho completo para a chave pública. Utilize o caminho para a chave pública para apresentar o respetivo conteúdo com `cat`.

```bash
cat ~/.ssh/id_rsa.pub
```

>[!NOTE]
> Se você optar por salvar a chave SSH em um local diferente do padrão, precisará usar esse local ao executar o`cat`

Guarde o resultado deste comando. Essa é sua chave pública e você precisará dela ao configurar sua conta de administrador para fazer logon em sua VM.

Para obter mais informações sobre o comando ssh-keygen, visite a [página do manual](https://linux.die.net/man/1/ssh-keygen).

Se você estiver usando um computador Windows e quiser saber mais sobre como criar pares de chaves SSH, incluindo o uso de recriação, consulte [como usar chaves SSH com o Windows](ssh-from-windows.md).

Se criar o par de chaves SSH com o Cloud Shell, este será armazenado numa Partilha de Ficheiros do Azure [montado automaticamente pelo Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Não elimine esta partilha de ficheiros ou conta de armazenamento até ter obtido as chaves ou perderá o acesso à VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-virtual-machine"></a>Criar a máquina virtual

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.

1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, pesquise o **Ubuntu Server 18, 4** e selecione a oferta do Ubuntu 18, 4 LTS e, em seguida, escolha **criar**.

1. No separador **Informações básicas**, em **Detalhes do projeto**, certifique-se de que está selecionada a subscrição correta e, em seguida, selecione **Criar novo** em **Grupo de recursos**. No pop-up, escreva *myResourceGroup* no nome do grupo de recursos e, em seguida, selecione **OK**.

    ![Criar um novo grupo de recursos para a VM](./media/quick-create-portal/project-details.png)

1. Em **Detalhes da instância**, escreva *myVM* para o **Nome da máquina virtual** e selecione *E.U.A. Leste* para a **Região**. Mantenha as restantes predefinições inalteradas.

    ![Secção de detalhes da instância](./media/quick-create-portal/instance-details.png)

1. Em **conta de administrador**, selecione **chave pública SSH**, digite seu nome de usuário e cole a chave pública que você salvou anteriormente na caixa de texto. Remova quaisquer espaços em branco à esquerda ou à direita na chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. Em **Regras da porta de entrada** > **Portas de entrada públicas**, selecione **Permitir portas selecionadas** e, em seguida, selecione **SSH (22)** e **HTTP (80)** na lista pendente.

    ![Abrir portas para RDP e HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.

1. Na página **Criar uma máquina virtual**, pode ver os detalhes sobre a VM que está prestes a criar. Quando estiver pronto, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada. Quando a implementação estiver concluída, avance para a secção seguinte.


## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Crie uma ligação SSH à VM.

1. Selecione o botão **Ligar** na página de descrição geral da VM.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. Na página **Ligar à máquina virtual**, mantenha as opções predefinidas para estabelecer ligação por endereço IP através da porta 22. Em **Iniciar sessão com uma conta local da VM**, é apresentado um comando de ligação. Clique no botão para copiar o comando. O exemplo seguinte mostra o aspeto do comando de ligação SSH:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Usando o mesmo shell bash que você usou para criar o par de chaves SSH (como o [Azure cloud Shell](https://shell.azure.com/bash) ou o shell bash local), Cole o comando de conexão SSH no Shell para criar uma sessão SSH.

## <a name="install-web-server"></a>Instalar o servidor Web

Para ver a VM em ação, instale o servidor Web NGINX. Na sessão SSH, atualize as origens do pacote e, em seguida, instale o pacote NGINX mais recente.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Quando terminar, escreva `exit` para deixar a sessão SSH.


## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Utilize um browser à sua escolha para ver a página predefinida de boas-vindas do NGINX. Introduza o endereço IP público da VM como o endereço Web. O endereço IP público pode ser encontrado na página de descrição geral da VM ou como parte da cadeia de ligação SSH que utilizou anteriormente.

![Site predefinido do NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar** e confirme o nome do grupo de recursos a eliminar.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma máquina virtual simples, criou um Grupo de Segurança de Rede e uma regra e instalou um servidor Web básico. Para saber mais sobre as máquinas virtuais do Azure, continue para o tutorial das VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais das máquinas virtuais do Linux do Azure](./tutorial-manage-vm.md)
