---
title: Utilizar chaves SSH com Windows para VMs do Linux
description: Aprenda a gerar e a utilizar as teclas SSH num computador Windows para ligar a uma máquina virtual Linux no Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 4fc411d3833fcd47477e7e0fe11c6da9e2114143
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502032"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como utilizar as chaves SSH com Windows on Azure

Este artigo descreve formas de gerar e utilizar chaves *de concha segura* (SSH) num computador Windows para criar e ligar a uma máquina virtual Linux (VM) em Azure. Para utilizar as chaves SSH de um cliente Linux ou macOS, consulte a orientação [rápida](mac-create-ssh-keys.md) ou [detalhada.](create-ssh-keys-detailed.md)

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacotes Windows e clientes SSH
Conecta-se e gere os VMs Linux em Azure utilizando um *cliente SSH*. Os computadores que executam o Linux ou o macOS geralmente têm um conjunto de comandos SSH para gerar e gerir chaves SSH e para fazer ligações SSH. 

Os computadores Windows nem sempre têm comandos SSH comparáveis instalados. Versões recentes do Windows 10 fornecem comandos de [clientes OpenSSH](https://devblogs.microsoft.com/commandline/windows10v1803/) para criar e gerir chaves SSH e fazer ligações SSH a partir de uma solicitação de comando. As versões recentes do Windows 10 também incluem o [Subsistema Windows para o Linux](/windows/wsl/about) executar e aceder a utilitários como um cliente SSH de forma nativa dentro de uma concha bash. 

Outros clientes comuns do Windows SSH que pode instalar localmente estão incluídos nos seguintes pacotes:

* [Rio Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git para janelas](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Rio Cygwin](https://cygwin.com/)

Também pode utilizar os utilitários SSH disponíveis em Bash na [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Aceda à Cloud Shell no seu navegador web [https://shell.azure.com](https://shell.azure.com) ou no [portal Azure.](https://portal.azure.com) 
* Aceder cloud shell como um terminal a partir do Código do Estúdio Visual instalando a [extensão da Conta Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
As secções seguintes descrevem duas opções para criar um par de chaves SSH no Windows. Pode utilizar um comando de concha `ssh-keygen` () ou uma ferramenta GUI (PuTTYgen). Note também que, ao utilizar o Powershell para criar uma chave, carreque a chave pública como formato ssh.com (SECSH). Ao utilizar o CLI, converta a tecla em formato OpenSSH antes de fazer o upload. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Criar teclas SSH com ssh-keygen

Se executar uma concha de comando no Windows que suporta ferramentas de cliente SSH (ou utilizar a Azure Cloud Shell), crie um par de chaves SSH utilizando o `ssh-keygen` comando. Digite o seguinte comando e responda às indicações. Se existir um par de chaves SSH no local escolhido, esses ficheiros são substituídos. 

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações, consulte os passos [rápidos](mac-create-ssh-keys.md) ou [detalhados](create-ssh-keys-detailed.md) para criar teclas SSH utilizando `ssh-keygen` .

### <a name="create-ssh-keys-with-puttygen"></a>Criar teclas SSH com PuTTYgen

Se preferir utilizar uma ferramenta baseada em GUI para criar teclas SSH, pode utilizar o gerador de chave PuTTYgen, incluído com o [pacote de descarregamento PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Para criar um par de chaves SSH RSA com PuTTYgen:

1. Comece puTTYgen.

2. Clique em **Generate** (Gerar). Por defeito, o PuTTYgen gera uma chave RSA SSH-2 de 2048.

3. Mova o rato na área em branco para fornecer aleatoriedade para a chave.

4. Depois de gerada a chave pública, introduza opcionalmente e confirme uma palavra-passe. Será solicitado para a palavra-passe quando autenticar no VM com a sua chave SSH privada. Sem uma palavra-passe, se alguém obtiver a sua chave privada, pode iniciar sação em qualquer VM ou serviço que utilize essa chave. Recomendamos que crie uma palavra-passe. Contudo, caso se esqueça da frase de acesso, não tem como a recuperar.

5. A chave pública é exibida na parte superior da janela. Pode copiar toda esta chave pública e depois colá-la no portal Azure ou num modelo de Gestor de Recursos Azure quando criar um VM Linux. Também pode selecionar Guardar a **chave pública** para guardar uma cópia no seu computador. Note que ao guardar para um ficheiro, a PuTTY converte a chave pública para um formato diferente, [RFC4716](https://tools.ietf.org/html/rfc4716). O formato RFC4716 pode não ser compatível com todas as APIs. Assim, para usar no portal Azure, recomendamos que copie a chave pública que está exposta na janela PuTTY.

    ![Salve o ficheiro chave público putty](./media/ssh-from-windows/save-public-key.png)

6. Opcionalmente, para guardar a chave privada no formato chave privado PuTTy (ficheiro.ppk), **selecione Guardar a tecla privada**. Mais tarde, necessitará do ficheiro .ppk para utilizar o PuTTY para escoar uma ligação SSH ao VM.

    ![Guarde o ficheiro chave privado PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Se pretender guardar a chave privada no formato OpenSSH, o formato chave privado utilizado por muitos clientes SSH, selecione **Conversions**  >  **Export OpenSSH .**

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Forneça uma chave pública SSH ao implementar um VM

Para criar um VM Linux que utilize chaves SSH para autenticação, forneça a sua chave pública SSH ao criar o VM utilizando o portal Azure ou outros métodos.

O exemplo a seguir mostra como copiaria e colaria esta chave pública no portal Azure quando criar um Linux VM. A chave pública é normalmente armazenada no diretório ~/.ssh/authorized_key no seu novo VM.

   ![Use a chave pública quando criar um VM no portal Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ligar à VM

Uma forma de fazer uma ligação SSH ao seu VM Linux do Windows é utilizar um cliente SSH. Este é o método preferido se tiver um cliente SSH instalado no seu sistema Windows, ou se utilizar as ferramentas SSH em Bash em Azure Cloud Shell. Se preferir uma ferramenta baseada em GUI, pode ligar-se ao PuTTY.  

### <a name="use-an-ssh-client"></a>Use um cliente SSH
Com a chave pública implantada no seu Azure VM, e a chave privada no seu sistema local, SSH para o seu VM utilizando o endereço IP ou o nome DNS do seu VM. Substitua *o azureuser* e *o myvm.westus.cloudapp.azure.com* no seguinte comando pelo nome de utilizador do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se configurar uma palavra-passe quando criou o seu par de chaves, introduza a palavra-passe quando solicitada durante o processo de inscrição.

Se o VM estiver a utilizar a política de acesso just-in-time, tem de solicitar acesso antes de poder ligar-se ao VM. Para obter mais informações sobre a política just-in-time, consulte [Gerir o acesso à máquina virtual utilizando a política just in time](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Conecte-se com a PuTTY

Se instalou o [pacote de descarregamento PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) e gerou previamente um ficheiro de chave privada PuTTY (.ppk), pode ligar-se a um Linux VM com PuTTY.

1. Comece o PutTy.

2. Preencha o nome de anfitrião ou endereço IP do seu VM a partir do portal Azure:

    ![Abra nova ligação PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Selecione a categoria **Connection**  >  **SSH**  >  **Auth.** Navegue e selecione a sua chave privada PuTTY (ficheiro.ppk):

    ![Selecione a sua chave privada PuTTY para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)

4. Clique **em Aberto** para ligar ao seu VM.

## <a name="next-steps"></a>Próximos passos

* Para etapas detalhadas, opções e exemplos avançados de trabalhar com chaves SSH, consulte [passos detalhados para criar pares de chaves SSH](create-ssh-keys-detailed.md).

* Também pode utilizar o PowerShell em Azure Cloud Shell para gerar teclas SSH e fazer ligações SSH a VMs Linux. Consulte o [arranque rápido powerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se tiver dificuldade em utilizar o SSH para ligar aos seus VMs Linux, consulte [as ligações SSH de resolução de problemas a um VM Azure Linux](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
