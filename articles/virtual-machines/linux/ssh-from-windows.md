---
title: Utilizar chaves SSH com Windows para VMs do Linux
description: Saiba como gerar e usar chaves SSH em um computador Windows para se conectar a uma máquina virtual Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: e01fb23bbf1720f7d8df9c269373c1b8dc3ec75c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034812"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como usar chaves SSH com o Windows no Azure

Este artigo descreve maneiras de gerar e usar chaves SSH ( *Secure Shell* ) em um computador Windows para criar e conectar-se a uma VM (máquina virtual) do Linux no Azure. Para usar chaves SSH de um cliente Linux ou macOS, consulte as diretrizes [rápidas](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md) .

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes SSH
Você se conecta e gerencia VMs do Linux no Azure usando um *cliente SSH*. Os computadores que executam o Linux ou macOS geralmente têm um conjunto de comandos SSH para gerar e gerenciar chaves SSH e para fazer conexões SSH. 

Computadores com Windows nem sempre têm comandos SSH comparáveis instalados. As versões recentes do Windows 10 fornecem [comandos de cliente OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) para criar e gerenciar chaves SSH e fazer conexões SSH a partir de um prompt de comando. Versões recentes do Windows 10 também incluem o [subsistema do Windows para Linux](https://docs.microsoft.com/windows/wsl/about) para executar e acessar utilitários como um cliente SSH nativamente em um shell bash. 

Outros clientes SSH do Windows comuns que você pode instalar localmente estão incluídos nos seguintes pacotes:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git para Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Você também pode usar os utilitários SSH disponíveis no bash no [Azure cloud Shell](../../cloud-shell/overview.md). 

* Acesse Cloud Shell no seu navegador da Web em [https://shell.azure.com](https://shell.azure.com) ou no [portal do Azure](https://portal.azure.com). 
* Acesse Cloud Shell como um terminal de dentro do Visual Studio Code instalando a [extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
As seções a seguir descrevem duas opções para criar um par de chaves SSH no Windows. Você pode usar um comando do Shell (`ssh-keygen`) ou uma ferramenta GUI (PuTTYgen). Observe também que, ao usar o PowerShell para criar uma chave, carregue a chave pública como o formato SSH. com (SECSH). Ao usar a CLI, converta a chave no formato OpenSSH antes de carregar. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Criar chaves SSH com ssh-keygen

Se você executar um shell de comando no Windows que dá suporte a ferramentas de cliente SSH (ou usar Azure Cloud Shell), crie um par de chaves SSH usando o comando `ssh-keygen`. Digite o comando a seguir e responda aos prompts. Se um par de chaves SSH existir no local escolhido, esses arquivos serão substituídos. 

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações, consulte as etapas [rápidas](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md) para criar chaves SSH usando `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Criar chaves SSH com PuTTYgen

Se preferir usar uma ferramenta baseada em GUI para criar chaves SSH, você poderá usar o gerador de chave PuTTYgen, incluído no pacote de [Download](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)de descriação. 

Para criar um par de chaves SSH RSA com PuTTYgen:

1. Inicie o PuTTYgen.

2. Clique em **gerar**. Por padrão, o PuTTYgen gera uma chave RSA de 2048 bits SSH-2.

4. Mova o mouse sobre a área em branco para fornecer aleatoriedade à chave.

5. Depois que a chave pública é gerada, opcionalmente, insira e confirme uma frase secreta. A senha será solicitada quando você se autenticar na VM com sua chave privada SSH. Sem uma frase secreta, se alguém obtiver sua chave privada, ele poderá entrar em qualquer VM ou serviço que use essa chave. Recomendamos que você crie uma frase secreta. Contudo, caso se esqueça da frase de acesso, não tem como a recuperar.

6. A chave pública é exibida na parte superior da janela. Você pode copiar essa chave pública inteira e, em seguida, colá-la no portal do Azure ou em um modelo de Azure Resource Manager ao criar uma VM do Linux. Você também pode selecionar **salvar chave pública** para salvar uma cópia em seu computador:

    ![Salvar arquivo de chave pública de saída](./media/ssh-from-windows/save-public-key.png)

7. Opcionalmente, para salvar a chave privada no formato de chave privada de saída (arquivo. PPK), selecione **salvar chave privada**. Você precisará do arquivo. PPK mais tarde para usar a reversão para fazer uma conexão SSH com a VM.

    ![Salvar arquivo de chave privada de saída](./media/ssh-from-windows/save-ppk-file.png)

    Se você quiser salvar a chave privada no formato OpenSSH, o formato de chave privada usado por muitos clientes SSH, selecione **conversões** > **chave OpenSSH de exportação**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornecer uma chave pública SSH ao implantar uma VM

Para criar uma VM do Linux que usa chaves SSH para autenticação, forneça sua chave pública SSH ao criar a VM usando o portal do Azure ou outros métodos.

O exemplo a seguir mostra como você copiaria e colaria essa chave pública no portal do Azure ao criar uma VM do Linux. Em geral, a chave pública é armazenada no diretório ~/.ssh/authorized_key em sua nova VM.

   ![Use a chave pública ao criar uma VM no portal do Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ligar à VM

Uma maneira de fazer uma conexão SSH com sua VM Linux do Windows é usar um cliente SSH. Esse é o método preferencial se você tiver um cliente SSH instalado no seu sistema Windows ou se usar as ferramentas SSH no bash no Azure Cloud Shell. Se preferir uma ferramenta baseada em GUI, você poderá se conectar com a saída.  

### <a name="use-an-ssh-client"></a>Usar um cliente SSH
Com a chave pública implantada em sua VM do Azure e a chave privada no sistema local, use SSH para sua VM usando o endereço IP ou o nome DNS da sua VM. Substitua *azureuser* e *MyVM.westus.cloudapp.Azure.com* no comando a seguir pelo nome de usuário do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você tiver configurado uma senha quando criou o par de chaves, insira a senha quando solicitado durante o processo de entrada.

Se a VM estiver usando a política de acesso just-in-time, você precisará solicitar acesso antes de poder se conectar à VM. Para obter mais informações sobre a política just-in-time, consulte [gerenciar o acesso à máquina virtual usando a política just in time](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Conectar-se com o inacabamento

Se você instalou o [pacote de download](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) de desinstalação e gerou anteriormente um arquivo de chave privada de geração (. PPK), você pode se conectar a uma VM do Linux com o reemitinte.

1. Iniciar a reinicialização.

2. Preencha o nome do host ou o endereço IP da sua VM no portal do Azure:

    ![Abrir nova conexão de saída](./media/ssh-from-windows/putty-new-connection.png)

3. Selecione a > **conexão** > a categoria de **autenticação** **SSH** . Navegue até e selecione sua chave privada de saída (arquivo. PPK):

    ![Selecione sua chave privada de saída para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)

4. Clique em **abrir** para se conectar à sua VM.

## <a name="next-steps"></a>Passos seguintes

* Para obter etapas detalhadas, opções e exemplos avançados de como trabalhar com chaves SSH, consulte [etapas detalhadas para criar pares de chaves SSH](create-ssh-keys-detailed.md).

* Você também pode usar o PowerShell no Azure Cloud Shell para gerar chaves SSH e fazer conexões SSH com VMs Linux. Consulte o [início rápido do PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se você tiver dificuldade para usar o SSH para se conectar às suas VMs do Linux, consulte [solucionar problemas de conexões SSH para uma VM Linux do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
