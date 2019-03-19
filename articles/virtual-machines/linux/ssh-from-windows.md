---
title: Utilizar chaves SSH com Windows para VMs do Linux | Documentos da Microsoft
description: Saiba como gerar e utilizar chaves SSH num computador Windows para ligar a uma máquina virtual do Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 0ac62a99f5735647f67917d441645e30444b3818
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005689"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como utilizar chaves SSH com Windows no Azure

Este artigo descreve formas de gerar e utilizar *secure shell* chaves (SSH) num computador Windows para criar e ligar a uma máquina virtual (VM) do Linux no Azure. Para utilizar chaves SSH de um cliente Linux ou macOS, veja a [rápida](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md) orientações.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes SSH
Ligar a e gerir VMs do Linux no Azure com um *cliente SSH*. Computadores que executam o Linux ou macOS normalmente têm um conjunto de comandos SSH para gerar e gerir chaves SSH e fazer ligações SSH. 

Computadores Windows nem sempre dispor comparáveis comandos SSH instalados. Fornecem versões recentes do Windows 10 [comandos de cliente OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) para criar e gerir chaves SSH e fazer conexões de SSH na linha de comandos. As versões recentes do Windows 10 também incluem o [subsistema Windows para Linux](https://docs.microsoft.com/windows/wsl/about) para executar e aceder utilitários como um cliente SSH nativamente dentro de um shell de Bash. 

Outros comuns SSH do Windows clientes que pode instalar localmente estão incluídos nos seguintes pacotes:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git Pro Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Também pode utilizar os utilitários SSH disponíveis no Bash no [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Aceder ao Cloud Shell no seu navegador da web em [ https://shell.azure.com ](https://shell.azure.com) ou no [portal do Azure](https://portal.azure.com). 
* Aceder ao Cloud Shell como um terminal a partir do Visual Studio Code ao instalar o [extensão da conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
As secções seguintes descrevem as duas opções para criar um par de chaves SSH no Windows. Pode utilizar um comando de shell (`ssh-keygen`) ou uma ferramenta de GUI (PuTTYgen).

### <a name="create-ssh-keys-with-ssh-keygen"></a>Criar chaves SSH com ssh-keygen

Se executar um shell de comando no Windows que oferece suporte a ferramentas de cliente SSH (ou utilizar o Azure Cloud Shell), criar um par de chaves de SSH utilizando o `ssh-keygen` comando. Escreva o seguinte comando e responda aos pedidos. Se existir um par de chaves SSH na localização escolhida, esses arquivos são substituídos. 

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações e informações, consulte a [rápida](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md) passos para criar chaves SSH com `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Criar chaves SSH com o PuTTYgen

Se preferir usar uma ferramenta baseada na GUI para criar chaves SSH, pode utilizar o gerador de chaves do PuTTYgen, incluído com o [pacote de transferência PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Para criar um par de chaves SSH RSA com PuTTYgen:

1. Inicie o PuTTYgen.

2. Clique em **gerar**. Por predefinição o PuTTYgen gera uma chave SSH-2 RSA de 2048 bits.

4. Mova o rato na área em branco para fornecer a aleatoriedade para a chave.

5. Depois da chave pública é gerada, opcionalmente, introduza e confirme uma frase de acesso. Será solicitado para a frase de acesso quando se autenticar para a VM com a chave SSH privada. Sem uma frase de acesso, se alguém obtiver a sua chave privada, eles podem iniciar sessão qualquer VM ou serviço que utiliza essa chave. Recomendamos que crie uma frase de acesso. Contudo, caso se esqueça da frase de acesso, não tem como a recuperar.

6. A chave pública é apresentada na parte superior da janela. Pode copiar essa chave pública completa e, em seguida, cole-o num portal do Azure ou um modelo Azure Resource Manager ao criar uma VM do Linux. Também pode selecionar **Guardar chave pública** para guardar uma cópia para o seu computador:

    ![Guarde o ficheiro de chave pública PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Opcionalmente, para guardar a chave privada no formato de chave privada (ficheiro. ppk) PuTTy, selecione **Guardar chave privada**. Terá do ficheiro. ppk posterior para utilizar o PuTTY para efetuar uma ligação SSH à VM.

    ![Guarde o ficheiro de chave privada PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Se pretender guardar a chave privada no formato OpenSSH, o formato de chave privada utilizado por vários clientes SSH, selecione **conversões** > **chave OpenSSH exportar**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornecer uma chave pública SSH ao implementar uma VM

Para criar uma VM do Linux utilizar chaves SSH para autenticação, forneça a chave pública SSH ao criar a VM com o portal do Azure ou outros métodos.

O exemplo seguinte mostra como copiar e colar esta chave pública no portal do Azure quando cria uma VM do Linux. A chave pública, em seguida, é normalmente armazenada no diretório ~/.ssh/authorized_key na sua nova VM.

   ![Utilizar a chave pública quando criar uma VM no portal do Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ligar à VM

Uma forma de fazer uma ligação SSH à sua VM do Linux do Windows é usar um cliente SSH. Este é o método preferencial, se tiver um cliente SSH instalado no seu sistema Windows, ou se usar as ferramentas SSH no Bash no Azure Cloud Shell. Se preferir uma ferramenta baseada na GUI, pode ligar com PuTTY.  

### <a name="use-an-ssh-client"></a>Utilizar um cliente SSH
Com a chave pública implementada na sua VM do Azure e a chave privada no seu sistema local, o SSH para a VM com o endereço IP ou nome DNS da sua VM. Substitua *azureuser* e *myvm.westus.cloudapp.azure.com* no comando seguinte com o nome de utilizador de administrador e o nome de domínio completamente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se tiver configurado uma frase de acesso quando criou o par de chaves, introduza a frase de acesso quando lhe for pedido durante o processo de início de sessão.

Se a VM estiver a utilizar a política de acesso just-in-time, terá de pedir acesso antes de poder ligar à VM. Para obter mais informações sobre a política de acesso just-in-time, consulte [gerir o acesso de máquina virtual utilizando o apenas na política de tempo](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Ligar com PuTTY

Se tiver instalado o [pacote de transferência PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) e gerado anteriormente um ficheiro de chave privada PuTTY (. ppk), pode ligar a uma VM do Linux com PuTTY.

1. Inicie o PuTTy.

2. Preencha o nome de anfitrião ou endereço IP da sua VM a partir do portal do Azure:

    ![Abrir nova ligação PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Selecione o **conexão** > **SSH** > **Auth** categoria. Procure e selecione a sua chave privada PuTTY (ficheiro. ppk):

    ![Selecione a sua chave privada PuTTY para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)

4. Clique em **aberto** para ligar à sua VM.

## <a name="next-steps"></a>Passos Seguintes

* Para obter passos detalhados, opções e exemplos avançados de trabalhar com chaves SSH, veja [pares de chaves de passos detalhados para criar SSH](create-ssh-keys-detailed.md).

* Também pode utilizar o PowerShell no Azure Cloud Shell para gerar chaves SSH e as ligações SSH para VMs do Linux. Consulte a [início rápido do PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se tiver dificuldade em utilizar o SSH para ligar às suas VMs do Linux, consulte [resolver problemas de SSH ligações a uma VM do Linux do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
