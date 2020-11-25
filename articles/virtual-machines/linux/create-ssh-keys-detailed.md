---
title: Passos detalhados para criar um par de chaves SSH
description: Aprenda passos detalhados para criar e gerir um par de chaves SSH público e privado para Os VMs Linux em Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 34a84ed333172ea0931c529d2dbeee1b774ae8c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016391"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Etapas detalhadas: Criar e gerir chaves SSH para autenticação num Linux VM em Azure

Com um par de chaves secure shell (SSH), pode criar uma máquina virtual Linux que utiliza chaves SSH para autenticação. Este artigo mostra-lhe como criar e utilizar um par de ficheiros sSH RSA público-privado para ligações ao cliente SSH.

Se quiser comandos rápidos, consulte [Como criar um par de chaves ssh público-privado para Os VMs Linux em Azure](mac-create-ssh-keys.md).

Para criar teclas SSH e usá-las para ligar a um computador **Windows,** consulte [como utilizar as teclas SSH com o Windows on Azure](ssh-from-windows.md). Também pode utilizar o [portal Azure](../ssh-keys-portal.md) para criar e gerir as teclas SSH para a criação de VMs no portal.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Utilização e vantagens das chaves SSH

Quando cria um Azure VM especificando a chave pública, o Azure copia a chave pública (no `.pub` formato) para a `~/.ssh/authorized_keys` pasta no VM. As teclas SSH `~/.ssh/authorized_keys` são utilizadas para desafiar o cliente a combinar a chave privada correspondente numa ligação SSH. Num Azure Linux VM que utiliza chaves SSH para autenticação, a Azure configura o servidor SSHD para não permitir o acesso à palavra-passe, apenas as teclas SSH. Ao criar um VM Azure Linux com teclas SSH, pode ajudar a proteger a implementação de VM e guardar o passo típico de configuração pós-implantação de palavras-passe desativadas no `sshd_config` ficheiro.

Se não pretender utilizar as teclas SSH, pode configurar o seu VM Linux para utilizar a autenticação de palavra-passe. Se o seu VM não estiver exposto à Internet, a utilização de palavras-passe pode ser suficiente. No entanto, ainda precisa de gerir as suas palavras-passe para cada VM Linux e manter políticas e práticas de senha saudáveis, tais como o comprimento mínimo da palavra-passe e atualizações regulares. 

## <a name="generate-keys-with-ssh-keygen"></a>Gerar chaves com ssh-keygen

Para criar as teclas, um comando preferido é `ssh-keygen` , que está disponível com utilitários OpenSSH no Azure Cloud Shell, um hospedeiro macOS ou Linux, e Windows 10. `ssh-keygen` faz uma série de perguntas e, em seguida, escreve uma chave privada e uma chave pública correspondente. 

Por predefinição, as chaves SSH são mantidas no diretório `~/.ssh`.  Se não tiver um diretório `~/.ssh`, o comando `ssh-keygen` cria-o por si com as permissões corretas.

### <a name="basic-example"></a>Exemplo básico

O `ssh-keygen` seguinte comando gera ficheiros chave ssh RSA públicos e privados de 4096 bits por padrão no `~/.ssh` diretório. Se existir um par de chaves SSH na localização atual, esses ficheiros são substituídos.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Exemplo detalhado
O exemplo a seguir mostra opções de comando adicionais para criar um par de chaves SSH RSA. Se existir um par de chaves SSH na localização atual, esses ficheiros são substituídos. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Comando explicado**

`ssh-keygen` = o programa utilizado para criar as chaves

`-m PEM` = formato da chave como PEM

`-t rsa` = tipo de chave para criar, neste caso no formato RSA

`-b 4096` = o número de bits na chave, neste caso 4096

`-C "azureuser@myserver"` = um comentário acrescentado ao final do ficheiro da chave pública para o identificar facilmente. Normalmente, um endereço de e-mail é usado como comentário, mas use o que funcionar melhor para a sua infraestrutura.

`-f ~/.ssh/mykeys/myprivatekey` = o nome de ficheiro do ficheiro de chave privada, se optar por não utilizar o nome predefinido. Um ficheiro de chave pública correspondente anexado `.pub` é gerado no mesmo diretório. O diretório tem de existir.

`-N mypassphrase` = uma frase-passe adicional utilizada para aceder ao ficheiro de chave privada. 

### <a name="example-of-ssh-keygen"></a>Exemplo de ssh-keygen

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Guarde ficheiros-chave

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

O nome do par de chaves para este artigo. Ter um par de chaves nomeado `id_rsa` é o padrão; algumas ferramentas podem esperar o `id_rsa` nome do ficheiro de chave privada, por isso ter um é uma boa ideia. O diretório `~/.ssh/` é a localização predefinida de todos os pares de chaves SSH e do ficheiro de configuração SSH. Se não for especificado com um caminho completo, a `ssh-keygen` cria as chaves no atual diretório de trabalho, não a predefinição `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista do `~/.ssh` diretório

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Palavra-chave

`Enter passphrase (empty for no passphrase):`

Recomenda-se *vivamente* adicionar uma palavra-passe à sua chave privada. Sem uma palavra-passe para proteger o ficheiro chave, qualquer pessoa com o ficheiro pode usá-lo para iniciar sômperse em qualquer servidor que tenha a chave pública correspondente. A adição de uma palavra-passe oferece mais proteção no caso de alguém ter acesso ao seu ficheiro de chave privada, dando-lhe tempo para mudar as teclas.

## <a name="generate-keys-automatically-during-deployment"></a>Gerar teclas automaticamente durante a implantação

Se utilizar o [CLI Azure](/cli/azure) para criar o seu VM, pode opcionalmente gerar ficheiros de chaves públicas e privadas SSH executando o comando [az vm criar](/cli/azure/vm) com a `--generate-ssh-keys` opção. As chaves são armazenadas no diretório ~/ssh. Note que esta opção de comando não substitui as teclas se já existirem nesse local.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Fornecer chave pública SSH ao implementar um VM

Para criar um VM Linux que utilize chaves SSH para autenticação, forneça a sua chave pública SSH ao criar o VM utilizando o portal Azure, CLI, modelos de Gestor de Recursos ou outros métodos. Ao utilizar o portal, introduza a chave pública em si. Se utilizar o [CLI Azure](/cli/azure) para criar o seu VM com uma chave pública existente, especifique o valor ou a localização desta chave pública executando o comando [az vm criar](/cli/azure/vm) comando com a `--ssh-key-value` opção. 

Se não estiver familiarizado com o formato de uma chave pública SSH, pode ver a sua chave pública executando `cat` da seguinte forma, substituindo `~/.ssh/id_rsa.pub` a sua própria localização de ficheiro de chave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

A saída é semelhante à seguinte (aqui redigida):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Se copiar e colar o conteúdo do ficheiro de chave pública no portal Azure ou num modelo de Gestor de Recursos, certifique-se de que não copia nenhum espaço em branco adicional ou introduz quebras de linha adicionais. Por exemplo, se utilizar o macOS, pode canalizar o ficheiro da chave pública (por defeito, `~/.ssh/id_rsa.pub` ) para **copiar** o conteúdo (existem outros programas Linux que fazem a mesma coisa, `xclip` como).

Se preferir utilizar uma chave pública que esteja num formato multiline, pode gerar uma chave formatada RFC4716 num recipiente pem a partir da chave pública que criou anteriormente.

Para criar uma chave formatada RFC4716 a partir de uma chave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH para o seu VM com um cliente SSH
Com a chave pública implantada no seu Azure VM, e a chave privada no seu sistema local, SSH para o seu VM utilizando o endereço IP ou o nome DNS do seu VM. Substitua *o azureuser* e *o myvm.westus.cloudapp.azure.com* no seguinte comando pelo nome de utilizador do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se tiver fornecido uma palavra-passe quando criou o seu par de chaves, introduza a palavra-passe quando solicitada durante o processo de inscrição. (O servidor é adicionado à pasta `~/.ssh/known_hosts` e não lhe será pedido para ligar novamente até a chave pública na VM do Azure ser alterada ou o nome do servidor ser removido do `~/.ssh/known_hosts`.)

Se o VM estiver a utilizar a política de acesso just-in-time, tem de solicitar acesso antes de poder ligar-se ao VM. Para obter mais informações sobre a política just-in-time, consulte [Gerir o acesso à máquina virtual utilizando a política just in time](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Use o agente ssh para armazenar a sua palavra-chave privada

Para evitar escrever a sua palavra-passe de ficheiro de chave privada com cada sSH, pode usar `ssh-agent` para cache a sua palavra-passe de ficheiro de chave privada. Se estiver a utilizar um Mac, o macOS Keychain armazena de forma segura a palavra-passe privada quando invoca `ssh-agent` .

Verifique e utilize `ssh-agent` e `ssh-add` informe o sistema SSH sobre os ficheiros-chave para que não seja necessário utilizar a palavra-passe interativamente.

```bash
eval "$(ssh-agent -s)"
```

Em seguida, adicione a chave privada a `ssh-agent` utilizando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

A palavra-chave privada está agora armazenada em `ssh-agent` .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Use ssh-copy-id para copiar a chave para um VM existente

Se já criou um VM, pode adicionar uma nova chave pública SSH ao seu VM Linux utilizando `ssh-copy-id` .

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um ficheiro de configuração SSH

Pode criar e configurar um ficheiro SSH config `~/.ssh/config` () para acelerar os logins e otimizar o comportamento do seu cliente SSH. 

O exemplo a seguir mostra uma configuração simples que pode utilizar para iniciar súmin como utilizador num VM específico utilizando a chave privada SSH predefinido. 

Crie o ficheiro.

```bash
touch ~/.ssh/config
```

Editar o ficheiro para adicionar a nova configuração SSH

```bash
vim ~/.ssh/config
```

Adicione as definições de configuração apropriadas para o VM do anfitrião. Neste exemplo, o nome VM é *myvm* e o nome da conta é *azureuser*.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Pode adicionar configurações para anfitriões adicionais para permitir que cada um use o seu próprio par de chaves dedicado. Consulte [o ficheiro SSH config](https://www.ssh.com/ssh/config/) para obter opções de configuração mais avançadas.

Agora que tem um par de chaves SSH e um ficheiro configurado de config SSH, pode iniciar sômposições no seu VM Linux de forma rápida e segura. Quando executam o seguinte comando, o SSH localiza e carrega quaisquer definições do `Host myvm` bloco no ficheiro SSH config.

```bash
ssh myvm
```

A primeira vez que iniciar súm na sômência de um servidor utilizando uma chave SSH, o comando indica-lhe a palavra-passe para o ficheiro de teclas.

## <a name="next-steps"></a>Passos seguintes

O passo seguinte consiste na criação de VMs com Linux do Azure utilizando a nova chave pública SSH. Os VMs Azure que são criados com uma chave pública SSH, uma vez que o sign-in é melhor seguro do que os VMs criados com o método de entrada padrão, palavras-passe.

* [Criar uma máquina virtual do Linux com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com o Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux utilizando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
