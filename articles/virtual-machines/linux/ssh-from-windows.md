---
title: Utilizar chaves SSH com Windows para VMs do Linux
description: Aprenda a gerar e utilizar chaves SSH num computador Windows para se ligar a uma máquina virtual Linux no Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: cdf901ca56c150cfed6ba3d462ce493d40bd2488
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757987"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como utilizar as teclas SSH com Windows no Azure

Este artigo descreve formas de gerar e usar chaves seguras de *concha* (SSH) num computador Windows para criar e ligar a uma máquina virtual Linux (VM) em Azure. Para utilizar as chaves SSH de um cliente Linux ou macOS, consulte a orientação [rápida](mac-create-ssh-keys.md) ou [detalhada.](create-ssh-keys-detailed.md)

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacotes Windows e clientes SSH
Liga-se e gere os VMs Linux em Azure utilizando um *cliente SSH*. Os computadores que executam o Linux ou o macOS têm normalmente um conjunto de comandos SSH para gerar e gerir as teclas SSH e fazer ligações SSH. 

Os computadores Windows nem sempre têm comandos SSH comparáveis instalados. As versões recentes do Windows 10 fornecem comandos de [clientes OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) para criar e gerir chaves SSH e fazer ligações SSH a partir de um pedido de comando. As versões recentes do Windows 10 também incluem o [Subsistema Windows para](https://docs.microsoft.com/windows/wsl/about) o Linux executar e aceder a utilitários como um cliente SSH de forma nativa dentro de uma concha bash. 

Outros clientes comuns do Windows SSH que pode instalar localmente estão incluídos nos seguintes pacotes:

* [Rio Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git Para Janelas](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cigwin](https://cygwin.com/)

Também pode utilizar os utilitários SSH disponíveis em Bash in the [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Aceda à Cloud Shell [https://shell.azure.com](https://shell.azure.com) no seu navegador web no [portal Azure.](https://portal.azure.com) 
* Aceda à Cloud Shell como terminal a partir do Código do Estúdio Visual, instalando a [extensão da Conta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
As seguintes secções descrevem duas opções para criar um par de chaves SSH no Windows. Pode utilizar um comando`ssh-keygen`de concha () ou uma ferramenta GUI (PuTTYgen). Note também, ao utilizar o Powershell para criar uma chave, carregue a chave pública como formato ssh.com (SECSH). Ao utilizar o CLI, converta a chave em formato OpenSSH antes de fazer o upload. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Crie chaves SSH com ssh-keygen

Se executar uma concha de comando no Windows que suporta as ferramentas do cliente SSH (ou utilizar a Azure Cloud Shell), crie um par de chaves SSH utilizando o `ssh-keygen` comando. Digite o seguinte comando e responda às instruções. Se existir um par de chaves SSH no local escolhido, esses ficheiros são substituídos. 

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações sobre o fundo e informações, consulte os passos [rápidos](mac-create-ssh-keys.md) ou [detalhados](create-ssh-keys-detailed.md) para criar teclas SSH utilizando `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Crie chaves SSH com PuTTYgen

Se preferir utilizar uma ferramenta baseada em GUI para criar chaves SSH, pode utilizar o gerador de chaves PuTTYgen, incluído com o pacote de [descarregamento PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Para criar um par de chaves SSH RSA com PuTTYgen:

1. Começa o PuTTYgen.

2. Clique em **Generate** (Gerar). Por padrão, puTTYgen gera uma chave RSA SSH-2 de 2048.

4. Mova o rato na área em branco para fornecer aleatoriedade para a chave.

5. Depois de gerada a chave pública, insira opcionalmente e confirme uma frase-passe. Será solicitado para a frase-passe quando autenticar o VM com a sua chave SSH privada. Sem uma palavra-passe, se alguém obtiver a sua chave privada, pode iniciar sessão em qualquer VM ou serviço que utilize essa chave. Recomendamos que crie uma frase de passe. Contudo, caso se esqueça da frase de acesso, não tem como a recuperar.

6. A chave pública é exibida no topo da janela. Você pode copiar toda esta chave pública e, em seguida, colá-la no portal Azure ou um modelo de Gestor de Recursos Azure quando você criar um VM Linux. Também pode selecionar a **chave Guardar para** guardar uma cópia para o seu computador:

    ![Salvar o ficheiro de chave pública PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Opcionalmente, para guardar a chave privada no formato chave privado PuTTy (ficheiro.ppk), selecione Salvar a **tecla privada**. Necessitará do ficheiro .ppk mais tarde para utilizar o PuTTY para fazer uma ligação SSH ao VM.

    ![Salvar o ficheiro de chaves privadas PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Se quiser guardar a chave privada no formato OpenSSH, o formato de chave privada utilizado por muitos clientes SSH, selecione **Conversãos** > **Export OpenSSH .**

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Forneça uma chave pública SSH ao implementar um VM

Para criar um VM Linux que utilize chaves SSH para autenticação, forneça a sua chave pública SSH ao criar o VM utilizando o portal Azure ou outros métodos.

O exemplo que se segue mostra como copiaria e colaria esta chave pública no portal Azure quando cria um VM Linux. A chave pública é normalmente armazenada no diretório ~/ssh/authorized_key no seu novo VM.

   ![Utilize a chave pública quando criar um VM no portal Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ligar à VM

Uma forma de fazer uma ligação SSH ao seu VM Linux a partir do Windows é usar um cliente SSH. Este é o método preferido se tiver um cliente SSH instalado no seu sistema Windows, ou se utilizar as ferramentas SSH em Bash em Azure Cloud Shell. Se preferir uma ferramenta baseada em GUI, pode ligar-se à PuTTY.  

### <a name="use-an-ssh-client"></a>Use um cliente SSH
Com a chave pública implantada no seu VM Azure e a chave privada no seu sistema local, SSH para o seu VM utilizando o endereço IP ou o nome DNS do seu VM. Substitua *o azureuser* e *myvm.westus.cloudapp.azure.com* no seguinte comando pelo nome de utilizador do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se configurar uma frase-passe quando criou o seu par de chaves, introduza a palavra-passe quando solicitada durante o processo de entrada.

Se o VM estiver a utilizar a política de acesso just-in-time, precisa de solicitar acesso antes de se ligar ao VM. Para obter mais informações sobre a política just-in-time, consulte Gerir o acesso virtual à [máquina utilizando a política de tempo justo.](../../security-center/security-center-just-in-time.md)

### <a name="connect-with-putty"></a>Conecte-se com putty

Se instalou o pacote de [descarregamento PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) e gerou anteriormente um ficheiro de chave privada PuTTY (.ppk), pode ligar-se a um Linux VM com PuTTY.

1. Começa a Putty.

2. Preencha o nome do anfitrião ou endereço IP do seu VM do portal Azure:

    ![Abrir nova ligação PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Selecione a categoria **Ligação** > **SSH** > **Auth.** Navegue e selecione a sua chave privada PuTTY (ficheiro.ppk):

    ![Selecione a sua chave privada PuTTY para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)

4. Clique **em Open** para ligar ao seu VM.

## <a name="next-steps"></a>Passos seguintes

* Para etapas detalhadas, opções e exemplos avançados de trabalhar com teclas SSH, consulte [passos detalhados para criar pares de chaves SSH](create-ssh-keys-detailed.md).

* Também pode utilizar o PowerShell em Azure Cloud Shell para gerar chaves SSH e fazer ligações SSH a VMs Linux. Consulte o arranque rápido da [PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se tiver dificuldade em utilizar o SSH para se ligar aos seus VMs Linux, consulte [as ligações SSH de Troubleshoot a um VM Azure Linux](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
