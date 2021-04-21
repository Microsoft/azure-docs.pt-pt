---
title: Utilize chaves SSH para ligar aos VMs Linux
description: Aprenda a gerar e a utilizar as teclas SSH a partir de um computador Windows para ligar a uma máquina virtual Linux no Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 0199a47b2306d7d461ba61057c7ab1015015df08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835565"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como utilizar as chaves SSH com Windows on Azure

Este artigo destina-se a utilizadores do Windows que pretendam [criar](#create-an-ssh-key-pair) e utilizar chaves *de conchas seguras* (SSH) para [ligar](#connect-to-your-vm) às máquinas virtuais Linux (VMs) em Azure. Também pode [gerar e armazenar chaves SSH no portal Azure](../ssh-keys-portal.md) para utilizar ao criar VMs no portal.


Para utilizar as teclas SSH de um cliente Linux ou macOS, consulte os [passos rápidos](mac-create-ssh-keys.md). Para uma visão geral mais detalhada do SSH, consulte [passos detalhados: Criar e gerir as chaves SSH para autenticação num Linux VM em Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e chaves

[SSH](https://www.ssh.com/ssh/) é um protocolo de ligação encriptado que permite iniciar s-ins seguros sobre ligações não seguras. SSH é o protocolo de ligação padrão para Os VMs Linux hospedados em Azure. Embora o próprio SSH forneça uma ligação encriptada, usar palavras-passe com SSH ainda deixa o VM vulnerável a ataques de força bruta. Recomendamos a ligação a um VM sobre SSH utilizando um par de chaves público-privado, também conhecido como *teclas SSH*. 

O par de chaves público-privado é como a fechadura da sua porta da frente. A fechadura está exposta ao **público,** qualquer pessoa com a chave certa pode abrir a porta. A chave é **privada**, e só é dada a pessoas em quem confia porque pode ser usada para destrancar a porta. 

- A *chave pública* é colocada no seu VM Linux quando cria o VM. 

- A *chave privada* permanece no seu sistema local. Proteja esta chave privada. Não a partilhe.

Quando se liga ao seu VM Linux, o VM testa o cliente SSH para se certificar de que tem a chave privada correta. Se o cliente tem a chave privada, tem acesso ao VM. 

Dependendo das políticas de segurança da sua organização, pode reutilizar um único par de chaves para aceder a vários VMs e serviços Azure. Não precisa de um par de chaves separado para cada VM. 

A sua chave pública pode ser partilhada com qualquer pessoa, mas apenas você (ou a sua infraestrutura de segurança local) deve ter acesso à sua chave privada.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>Clientes SSH

Versões recentes do Windows 10 incluem [comandos de clientes OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) para criar e utilizar chaves SSH e fazer ligações SSH a partir de PowerShell ou uma solicitação de comando. Esta é a forma mais fácil de criar uma ligação SSH ao seu VM Linux, a partir de um computador Windows. 

Também pode utilizar Bash na [Azure Cloud Shell](../../cloud-shell/overview.md) para ligar ao seu VM. Pode utilizar o Cloud Shell num [navegador web](https://shell.azure.com/bash), a partir do [portal Azure,](https://portal.azure.com)ou como um terminal em Código de Estúdio Visual utilizando a [extensão da Conta Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

Também pode instalar o [Subsistema Windows para o Linux](/windows/wsl/about) ligar-se ao seu VM sobre SSH e utilizar outras ferramentas Linux nativas dentro de uma concha Bash.

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Crie um par de chaves SSH utilizando o `ssh-keygen` comando. Introduza um nome de ficheiro ou use o padrão mostrado na parêntese (por `C:\Users\username/.ssh/id_rsa` exemplo).  Introduza uma palavra-passe para o ficheiro ou deixe a palavra-passe em branco se não quiser utilizar uma palavra-passe. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Crie um VM utilizando a sua chave

Para criar um Linux VM que utilize chaves SSH para autenticação, forneça a sua chave pública SSH ao criar o VM.

Utilizando o CLI Azure, especifique o caminho e o nome de ficheiro para a chave pública utilizando `az vm create` e o `--ssh-key-value` parâmetro.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Com o PowerShell, utilize `New-AzVM` e adicione a tecla SSH à configuração VM utilizando". Por exemplo, consulte [Quickstart: Crie uma máquina virtual Linux em Azure com PowerShell](quick-create-powershell.md).

Se fizer muitas implementações utilizando o portal, é possível que queira enviar a sua chave pública para o Azure, onde pode ser facilmente selecionada ao criar um VM a partir do portal. Para obter mais informações, consulte [a chave SSH](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Ligar à VM

Com a chave pública implantada no seu Azure VM, e a chave privada no seu sistema local, SSH para o seu VM utilizando o endereço IP ou o nome DNS do seu VM. Substitua *o azureuser* e *o 10.111.12.123* no seguinte comando com o nome de utilizador do administrador, o endereço IP (ou nome de domínio totalmente qualificado) e o caminho para a sua chave privada:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Se configurar uma palavra-passe quando criou o seu par de chaves, introduza a palavra-passe quando solicitado.

Se o VM estiver a utilizar a política de acesso just-in-time, tem de solicitar acesso antes de poder ligar-se ao VM. Para obter mais informações sobre a política just-in-time, consulte [Gerir o acesso à máquina virtual utilizando a política just in time](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre as teclas SSH no portal Azure, consulte [as teclas SSH do Portal Azure](../ssh-keys-portal.md) para utilizar ao criar VMs no portal.

- Para etapas detalhadas, opções e exemplos avançados de trabalhar com chaves SSH, consulte [passos detalhados para criar pares de chaves SSH](create-ssh-keys-detailed.md).

- Também pode utilizar o PowerShell em Azure Cloud Shell para gerar teclas SSH e fazer ligações SSH a VMs Linux. Consulte o [arranque rápido powerShell](../../cloud-shell/quickstart-powershell.md#ssh).

- Se tiver dificuldade em utilizar o SSH para ligar aos seus VMs Linux, consulte [as ligações SSH de resolução de problemas a um VM Azure Linux](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
