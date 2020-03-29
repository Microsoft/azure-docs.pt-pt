---
title: Passos detalhados para criar um par de chaves SSH
description: Aprenda passos detalhados para criar e gerir um par de chaves públicas e privadas SSH para VMs Linux em Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969545"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Passos detalhados: Criar e gerir chaves SSH para autenticação a um VM Linux em Azure 
Com um par de chaves de concha segura (SSH), pode criar uma máquina virtual Linux no Azure que não utiliza as teclas SSH para autenticação, eliminando a necessidade de as palavras-passe iniciarem o seu início. Os VMs criados com o portal Azure, modelos Azure CLI, Resource Manager ou outras ferramentas podem incluir a sua chave pública SSH como parte da implementação, que configura a autenticação da chave SSH para ligações SSH. 

Este artigo fornece antecedentes e passos detalhados para criar e gerir um par de ficheiros público-privado SSH RSA para ligações ao cliente SSH. Se quiser comandos rápidos, consulte como criar um par de [chaves público-privada SSH para VMs Linux em Azure](mac-create-ssh-keys.md).

Para obter formas adicionais de gerar e utilizar as teclas SSH num computador Windows, consulte [como utilizar as teclas SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Frase de acesso de chave privada
A chave privada SSH deve ter uma palavra-passe muito segura para a salvaguardar. Esta palavra-passe é apenas para aceder ao ficheiro de chave SSH privado e *não é* a palavra-passe da conta de utilizador. Quando adiciona uma frase de acesso à chave SSH, esta encripta a chave privada para que a chave privada com AES de 128 bits não seja utilizável sem a frase de acesso para a desencriptar. Se um intruso roubasse a sua chave privada e essa chave não tivesse uma palavra-passe, seria capaz de usar essa chave privada para iniciar sessão em quaisquer servidores que tenham a chave pública correspondente. Se uma chave privada for protegida por uma frase-passe, não pode ser usada por esse intruso, fornecendo uma camada adicional de segurança para a sua infraestrutura em Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Utilização e vantagens das chaves SSH

Quando cria um VM Azure especificando a chave pública, o `.pub` Azure copia a chave pública (no formato) para a `~/.ssh/authorized_keys` pasta no VM. As teclas `~/.ssh/authorized_keys` SSH são usadas para desafiar o cliente a combinar a chave privada correspondente numa ligação SSH. Num VM Azure Linux que utiliza chaves SSH para autenticação, o Azure confunde o servidor SSHD para não permitir o início de sessão de senha, apenas as teclas SSH. Por isso, ao criar um VM Azure Linux com teclas SSH, pode ajudar a proteger a implementação do `sshd_config` VM e a guardar o passo típico de configuração pós-implementação de palavras-passe incapacitantes no ficheiro.

Se não desejar utilizar as teclas SSH, pode configurar o seu VM Linux para utilizar a autenticação de senha. Se o seu VM não estiver exposto à Internet, a utilização de palavras-passe pode ser suficiente. No entanto, ainda precisa de gerir as suas palavras-passe para cada VM Linux e manter políticas e práticas saudáveis de senha, tais como o comprimento mínimo da palavra-passe e atualizações regulares. A utilização de teclas SSH reduz a complexidade da gestão de credenciais individuais em vários VMs.

## <a name="generate-keys-with-ssh-keygen"></a>Gerar chaves com ssh-keygen

Para criar as teclas, `ssh-keygen`um comando preferido é , que está disponível com utilitários OpenSSH na Casca de Nuvem Azure, um anfitrião macOS ou Linux, o [Subsistema Windows para Linux](https://docs.microsoft.com/windows/wsl/about), e outras ferramentas. `ssh-keygen`faz uma série de perguntas e, em seguida, escreve uma chave privada e uma chave pública correspondente. 

Por predefinição, as chaves SSH são mantidas no diretório `~/.ssh`.  Se não tiver um diretório `~/.ssh`, o comando `ssh-keygen` cria-o por si com as permissões corretas.

### <a name="basic-example"></a>Exemplo básico

O `ssh-keygen` comando seguinte gera ficheiros de chave públicos e privados sSH `~/.ssh` RSA de 2048 por padrão no diretório. Se existir um par de chaves SSH na localização atual, esses ficheiros são substituídos.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Exemplo detalhado
O exemplo seguinte mostra opções de comando adicionais para criar um par de teclas SSH RSA. Se existir um par de chaves SSH na localização atual, esses ficheiros são substituídos. 

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

`-m PEM`= formate a chave como PEM

`-t rsa`= tipo de chave para criar, neste caso no formato RSA

`-b 4096`= o número de bits na chave, neste caso 4096

`-C "azureuser@myserver"` = um comentário acrescentado ao final do ficheiro da chave pública para o identificar facilmente. Normalmente, um endereço de e-mail é usado como comentário, mas use o que funciona melhor para a sua infraestrutura.

`-f ~/.ssh/mykeys/myprivatekey`= o nome de ficheiro do ficheiro chave privado, se optar por não utilizar o nome predefinido. Um ficheiro de chave `.pub` pública correspondente anexado é gerado no mesmo diretório. O diretório tem de existir.

`-N mypassphrase`= uma palavra-passe adicional usada para aceder ao ficheiro chave privado. 

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

#### <a name="saved-key-files"></a>Ficheiros de chaves guardados

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

O nome do par de chaves para este artigo. Ter um par `id_rsa` de chaves nomeado é o padrão; algumas ferramentas `id_rsa` podem esperar o nome do ficheiro chave privado, então ter um é uma boa ideia. O diretório `~/.ssh/` é a localização predefinida de todos os pares de chaves SSH e do ficheiro de configuração SSH. Se não for especificado com um caminho completo, a `ssh-keygen` cria as chaves no atual diretório de trabalho, não a predefinição `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista do `~/.ssh` diretório

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Palavra-chave

`Enter passphrase (empty for no passphrase):`

É *fortemente* recomendado adicionar uma palavra-passe à sua chave privada. Sem uma palavra-passe para proteger o ficheiro chave, qualquer pessoa com o ficheiro pode usá-lo para iniciar sessão em qualquer servidor que tenha a chave pública correspondente. Adicionar uma frase de passe oferece mais proteção no caso de alguém ser capaz de ter acesso ao seu ficheiro de chave privada, dando-lhe tempo para mudar as teclas.

## <a name="generate-keys-automatically-during-deployment"></a>Gerar chaves automaticamente durante a implementação

Se utilizar o [Azure CLI](/cli/azure) para criar o seu VM, pode opcionalmente gerar ficheiros de chaves `--generate-ssh-keys` públicas e privadas SSH executando o comando [az vM criar](/cli/azure/vm) comando com a opção. As chaves são armazenadas no diretório ~/.ssh. Note que esta opção de comando não substitui as chaves se já existirem nesse local.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Fornecer a chave pública SSH ao implementar um VM

Para criar um VM Linux que utilize chaves SSH para autenticação, forneça a sua chave pública SSH ao criar o VM utilizando os modelos do portal Azure, CLI, Gestor de Recursos ou outros métodos. Ao utilizar o portal, introduza a própria chave pública. Se utilizar o [Azure CLI](/cli/azure) para criar o seu VM com uma chave pública existente, especifique o `--ssh-key-value` valor ou localização desta chave pública executando o comando [az vM criar](/cli/azure/vm) comando com a opção. 

Se não estiver familiarizado com o formato de uma chave pública SSH, pode ver a sua chave pública executando `cat` da seguinte forma, substituindo `~/.ssh/id_rsa.pub` pela sua própria localização de ficheiros de chave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

A saída é semelhante à seguinte (aqui redigida):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Se copiar e colar o conteúdo do ficheiro chave público no portal Azure ou num modelo de Gestor de Recursos, certifique-se de que não copia qualquer espaço branco adicional ou introduz quebras de linha adicionais. Por exemplo, se utilizar o macOS, pode canalizar o `~/.ssh/id_rsa.pub`ficheiro de chaves públicas (por padrão, ) para **a cópia** para `xclip`copiar o conteúdo (existem outros programas Linux que fazem a mesma coisa, como).

Se preferir utilizar uma chave pública que se encontra num formato multi-line, pode gerar uma chave formada RFC4716 num recipiente pem a partir da chave pública que criou anteriormente.

Para criar uma chave formatada RFC4716 a partir de uma chave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH para o seu VM com um cliente SSH
Com a chave pública implantada no seu VM Azure e a chave privada no seu sistema local, SSH para o seu VM utilizando o endereço IP ou o nome DNS do seu VM. Substitua *o azureuser* e *myvm.westus.cloudapp.azure.com* no seguinte comando pelo nome de utilizador do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se forneceu uma palavra-passe quando criou o seu par de chaves, introduza a palavra-passe quando solicitada durante o processo de entrada. (O servidor é adicionado à pasta `~/.ssh/known_hosts` e não lhe será pedido para ligar novamente até a chave pública na VM do Azure ser alterada ou o nome do servidor ser removido do `~/.ssh/known_hosts`.)

Se o VM estiver a utilizar a política de acesso just-in-time, precisa de solicitar acesso antes de se ligar ao VM. Para obter mais informações sobre a política just-in-time, consulte Gerir o acesso virtual à [máquina utilizando a política de tempo justo.](../../security-center/security-center-just-in-time.md)

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Use o ssh-agent para armazenar a sua palavra-chave privada

Para evitar escrever a sua palavra-passe de ficheiro de chave `ssh-agent` privada com cada sessão de sessão ssh, pode usar para cache a sua palavra-passe de ficheiro de chave privada. Se estiver a utilizar um Mac, o macOS Keychain armazena de forma segura a palavra-chave privada quando invoca `ssh-agent`.

Verifique e `ssh-agent` `ssh-add` utilize e informe o sistema SSH sobre os ficheiros chave para que não seja necessário utilizar a palavra-passe interactivamente.

```bash
eval "$(ssh-agent -s)"
```

Em seguida, adicione a chave privada a `ssh-agent` utilizando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

A palavra-chave privada está `ssh-agent`agora armazenada em .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Utilize ssh-copy-id para copiar a chave de um VM existente
Se já criou um VM, pode instalar a nova chave pública SSH para o seu VM Linux com um comando semelhante ao seguinte:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um ficheiro de configuração SSH

Pode criar e configurar um ficheiro config SSH para`~/.ssh/config`acelerar os logins e otimizar o comportamento do seu cliente SSH. 

O exemplo seguinte mostra uma configuração simples que pode usar para iniciar sessão rapidamente como utilizador para um VM específico usando a chave privada Padrão SSH. 

### <a name="create-the-file"></a>Criar o ficheiro

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Editar o ficheiro para adicionar a nova configuração SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Configuração de exemplo

Adicione as definições de configuração adequadas para o seu VM anfitrião.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Pode adicionar configurações para anfitriões adicionais que permitam que cada um utilize o seu próprio par de chaves dedicado. Consulte o [ficheiro de config SSH](https://www.ssh.com/ssh/config/) para obter opções de configuração mais avançadas.

Agora que tem um par de chaves SSH e um ficheiro de config SSH configurado, é possível iniciar sessão no seu VM Linux de forma rápida e segura. Quando executa o seguinte comando, o SSH `Host myvm` localiza e carrega quaisquer definições do bloco no ficheiro config SSH.

```bash
ssh myvm
```

A primeira vez que insere num servidor utilizando uma tecla SSH, o comando pede-lhe a frase-passe desse ficheiro chave.

## <a name="next-steps"></a>Passos seguintes

O passo seguinte consiste na criação de VMs com Linux do Azure utilizando a nova chave pública SSH. Os VMs azure que são criados com uma chave pública SSH, uma vez que o sign-in é mais seguro do que os VMs criados com o método de entrada predefinido, palavras-passe.

* [Criar uma máquina virtual Linux com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Crie uma máquina virtual Linux com o Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux utilizando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
