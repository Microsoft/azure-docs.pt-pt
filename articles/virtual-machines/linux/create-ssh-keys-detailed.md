---
title: Etapas detalhadas-par de chaves SSH para VMs Linux do Azure
description: Conheça as etapas detalhadas para criar e gerenciar um par de chaves SSH pública e privada para VMs do Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/17/2018
ms.author: cynthn
ms.openlocfilehash: 61f24776bb9ec9443df421dcbcf35dcc83ec2bc9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036511"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Etapas detalhadas: criar e gerenciar chaves SSH para autenticação em uma VM do Linux no Azure 
Com um par de chaves SSH (Secure Shell), você pode criar uma máquina virtual Linux no Azure que usa como padrão as chaves SSH para autenticação, eliminando a necessidade de senhas para entrar. As VMs criadas com o portal do Azure, CLI do Azure, modelos do Resource Manager ou outras ferramentas podem incluir sua chave pública SSH como parte da implantação, que configura a autenticação de chave SSH para conexões SSH. 

Este artigo fornece um plano de fundo detalhado e as etapas para criar e gerenciar um par de arquivos de chave pública-privada do SSH RSA para conexões de cliente SSH. Se você quiser comandos rápidos, consulte [como criar um par de chaves SSH pública-privada para VMs do Linux no Azure](mac-create-ssh-keys.md).

Para obter outras maneiras de gerar e usar chaves SSH em um computador Windows, consulte [como usar chaves SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Frase secreta da chave privada
A chave privada SSH deve ter uma senha muito segura para protegê-la. Essa frase secreta é apenas para acessar o arquivo de chave SSH privada e *não é* a senha da conta de usuário. Quando você adiciona uma frase secreta à chave SSH, ela criptografa a chave privada usando o AES de 128 bits, para que a chave privada seja inútil sem a frase secreta para descriptografá-la. Se um invasor roubasse sua chave privada e essa chave não tiver uma frase secreta, ele poderá usar essa chave privada para entrar em qualquer servidor que tenha a chave pública correspondente. Se uma chave privada for protegida por uma frase secreta, ela não poderá ser usada por esse invasor, fornecendo uma camada adicional de segurança para sua infraestrutura no Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Utilização e vantagens das chaves SSH

Quando você cria uma VM do Azure especificando a chave pública, o Azure copia a chave pública (no formato `.pub`) para a pasta `~/.ssh/authorized_keys` na VM. As chaves SSH no `~/.ssh/authorized_keys` são usadas para desafiar o cliente para corresponder à chave privada correspondente em uma conexão SSH. Em uma VM Linux do Azure que usa chaves SSH para autenticação, o Azure configura o servidor SSHD para não permitir a entrada de senha, somente chaves SSH. Portanto, ao criar uma VM Linux do Azure com chaves SSH, você pode ajudar a proteger a implantação da VM e salvar a etapa de configuração pós-implantação típica de desabilitar as senhas no arquivo de `sshd_config`.

Se você não quiser usar chaves SSH, poderá configurar sua VM Linux para usar a autenticação de senha. Se sua VM não estiver exposta à Internet, usar senhas poderá ser suficiente. No entanto, você ainda precisa gerenciar suas senhas para cada VM do Linux e manter políticas e práticas de senha íntegras, como o tamanho mínimo da senha e atualizações regulares. O uso de chaves SSH reduz a complexidade do gerenciamento de credenciais individuais em várias VMs.

## <a name="generate-keys-with-ssh-keygen"></a>Gerar chaves com ssh-keygen

Para criar as chaves, um comando preferencial é `ssh-keygen`, que está disponível com utilitários OpenSSH na Azure Cloud Shell, um host macOS ou Linux, o [subsistema do Windows para Linux](https://docs.microsoft.com/windows/wsl/about)e outras ferramentas. `ssh-keygen` faz uma série de perguntas e, em seguida, grava uma chave privada e uma chave pública correspondente. 

Por predefinição, as chaves SSH são mantidas no diretório `~/.ssh`.  Se não tiver um diretório `~/.ssh`, o comando `ssh-keygen` cria-o por si com as permissões corretas.

### <a name="basic-example"></a>Exemplo básico

O comando a seguir `ssh-keygen` gera arquivos de chave pública e privada RSA do SSH de 2048 bits por padrão no diretório `~/.ssh`. Se um par de chaves SSH existir no local atual, esses arquivos serão substituídos.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Exemplo detalhado
O exemplo a seguir mostra opções de comando adicionais para criar um par de chaves SSH RSA. Se um par de chaves SSH existir no local atual, esses arquivos serão substituídos. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Comando explicado**

`ssh-keygen` = o programa utilizado para criar as chaves

`-t rsa` = tipo de chave para criar, nesse caso, no formato RSA

`-b 4096` = o número de bits na chave, neste caso, 4096

`-C "azureuser@myserver"` = um comentário acrescentado ao final do ficheiro da chave pública para o identificar facilmente. Normalmente, um endereço de email é usado como comentário, mas use o que for melhor para sua infraestrutura.

`-f ~/.ssh/mykeys/myprivatekey` = o nome do arquivo de chave privada, se você optar por não usar o nome padrão. Um arquivo de chave pública correspondente anexado com `.pub` é gerado no mesmo diretório. O diretório deve existir.

`-N mypassphrase` = uma senha adicional usada para acessar o arquivo de chave privada. 

### <a name="example-of-ssh-keygen"></a>Exemplo de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>Arquivos de chave salvos

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

O nome do par de chaves para este artigo. Ter um par de chaves chamado `id_rsa` é o padrão; algumas ferramentas podem esperar o `id_rsa` nome do arquivo de chave privada, portanto, ter uma é uma boa ideia. O diretório `~/.ssh/` é a localização predefinida de todos os pares de chaves SSH e do ficheiro de configuração SSH. Se não for especificado com um caminho completo, a `ssh-keygen` cria as chaves no atual diretório de trabalho, não a predefinição `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista do diretório `~/.ssh`

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Frase secreta da chave

`Enter passphrase (empty for no passphrase):`

É *altamente* recomendável adicionar uma senha à sua chave privada. Sem uma frase secreta para proteger o arquivo de chave, qualquer pessoa com o arquivo pode usá-lo para entrar em qualquer servidor que tenha a chave pública correspondente. Adicionar uma senha oferece mais proteção no caso de alguém ser capaz de obter acesso ao seu arquivo de chave privada, dando tempo para alterar as chaves.

## <a name="generate-keys-automatically-during-deployment"></a>Gerar chaves automaticamente durante a implantação

Se você usar o [CLI do Azure](/cli/azure) para criar sua VM, você pode, opcionalmente, gerar arquivos de chave SSH pública e privada executando o comando [AZ VM Create](/cli/azure/vm) com a opção `--generate-ssh-keys`. As chaves são armazenadas no diretório ~/.ssh Observe que essa opção de comando não substituirá as chaves se elas já existirem nesse local.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Fornecer chave pública SSH ao implantar uma VM

Para criar uma VM do Linux que usa chaves SSH para autenticação, forneça sua chave pública SSH ao criar a VM usando o portal do Azure, a CLI, os modelos do Resource Manager ou outros métodos. Ao usar o portal, você insere a chave pública em si. Se você usar o [CLI do Azure](/cli/azure) para criar sua VM com uma chave pública existente, especifique o valor ou o local dessa chave pública executando o comando [AZ VM Create](/cli/azure/vm) com a opção `--ssh-key-value`. 

Se você não estiver familiarizado com o formato de uma chave pública SSH, poderá ver sua chave pública executando `cat` da seguinte maneira, substituindo `~/.ssh/id_rsa.pub` por seu próprio local de arquivo de chave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

A saída é semelhante à seguinte (aqui redação):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Se você copiar e colar o conteúdo do arquivo de chave pública no portal do Azure ou em um modelo do Resource Manager, certifique-se de não copiar nenhum espaço em branco adicional nem introduzir quebras de linha adicionais. Por exemplo, se você usar o macOS, poderá canalizar o arquivo de chave pública (por padrão `~/.ssh/id_rsa.pub`) para **pbcopy** para copiar o conteúdo (há outros programas do Linux que fazem a mesma coisa, como `xclip`).

Se preferir usar uma chave pública que esteja em um formato de várias linhas, você poderá gerar uma chave formatada como RFC4716 em um contêiner PEM a partir da chave pública que você criou anteriormente.

Para criar uma chave formatada RFC4716 a partir de uma chave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH para sua VM com um cliente SSH
Com a chave pública implantada em sua VM do Azure e a chave privada no sistema local, use SSH para sua VM usando o endereço IP ou o nome DNS da sua VM. Substitua *azureuser* e *MyVM.westus.cloudapp.Azure.com* no comando a seguir pelo nome de usuário do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você forneceu uma senha quando criou o par de chaves, insira a senha quando solicitado durante o processo de entrada. (O servidor é adicionado à pasta `~/.ssh/known_hosts` e não lhe será pedido para ligar novamente até a chave pública na VM do Azure ser alterada ou o nome do servidor ser removido do `~/.ssh/known_hosts`.)

Se a VM estiver usando a política de acesso just-in-time, você precisará solicitar acesso antes de poder se conectar à VM. Para obter mais informações sobre a política just-in-time, consulte [gerenciar o acesso à máquina virtual usando a política just in time](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Usar SSH-Agent para armazenar sua frase secreta de chave privada

Para evitar digitar a frase secreta do arquivo de chave privada com cada entrada SSH, você pode usar `ssh-agent` para armazenar em cache a frase secreta do arquivo de chave privada. Se você estiver usando um Mac, o conjunto de chaves macOS armazena com segurança a frase secreta da chave privada quando você invoca `ssh-agent`.

Verifique e use `ssh-agent` e `ssh-add` para informar o sistema SSH sobre os arquivos de chave para que você não precise usar a senha interativamente.

```bash
eval "$(ssh-agent -s)"
```

Em seguida, adicione a chave privada a `ssh-agent` utilizando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

A frase secreta da chave privada agora é armazenada no `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Use ssh-copy-id para copiar a chave para uma VM existente
Se você já tiver criado uma VM, poderá instalar a nova chave pública SSH em sua VM Linux com um comando semelhante ao seguinte:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um ficheiro de configuração SSH

Você pode criar e configurar um arquivo de configuração SSH (`~/.ssh/config`) para acelerar os logons e otimizar o comportamento do cliente SSH. 

O exemplo a seguir mostra uma configuração simples que você pode usar para entrar rapidamente como um usuário em uma VM específica usando a chave privada SSH padrão. 

### <a name="create-the-file"></a>Criar o ficheiro

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Editar o arquivo para adicionar a nova configuração de SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Configuração de exemplo

Adicione as definições de configuração apropriadas para a VM do host.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Você pode adicionar configurações para hosts adicionais para permitir que cada um use seu próprio par de chaves dedicado. Consulte [arquivo de configuração SSH](https://www.ssh.com/ssh/config/) para obter opções de configuração mais avançadas.

Agora que você tem um par de chaves SSH e um arquivo de configuração SSH configurado, é possível entrar em sua VM Linux de forma rápida e segura. Quando você executa o comando a seguir, o SSH localiza e carrega as configurações do bloco de `Host myvm` no arquivo de configuração do SSH.

```bash
ssh myvm
```

Na primeira vez que você entrar em um servidor usando uma chave SSH, o comando solicitará a senha para esse arquivo de chave.

## <a name="next-steps"></a>Passos seguintes

O passo seguinte consiste na criação de VMs com Linux do Azure utilizando a nova chave pública SSH. As VMs do Azure criadas com uma chave pública SSH como a entrada são mais seguras do que as VMs criadas com o método de entrada padrão, senhas.

* [Criar uma máquina virtual Linux com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com o CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux usando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
