---
title: Usar os arquivos do Azure com o Linux | Microsoft Docs
description: Saiba como montar um compartilhamento de arquivos do Azure por meio de SMB no Linux.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06df5d403ba10489ea9a36a79a94f4b94782e4ef
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501320"
---
# <a name="use-azure-files-with-linux"></a>Utilizar os Ficheiros do Azure com o Linux

[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o [cliente kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas maneiras de montar um compartilhamento de arquivos do Azure: sob demanda com `mount` o comando e na inicialização criando uma entrada no. `/etc/fstab`

> [!NOTE]  
> Para montar um compartilhamento de arquivos do Azure fora da região do Azure em que ele está hospedado, como no local ou em uma região diferente do Azure, o sistema operacional deve dar suporte à funcionalidade de criptografia do SMB 3,0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Pré-requisitos para montar um compartilhamento de arquivos do Azure com Linux e o pacote CIFS-utils
<a id="smb-client-reqs"></a>

* **Uma conta de armazenamento do Azure e um compartilhamento de arquivos existentes**: Para concluir este artigo, você precisa ter uma conta de armazenamento e um compartilhamento de arquivos. Se você ainda não criou um, consulte um dos nossos guias de início rápido no assunto: [Criar compartilhamentos de arquivos-CLI](storage-how-to-use-files-cli.md).

* **O nome e a chave da conta de armazenamento** Você precisará do nome da conta de armazenamento e da chave para concluir este artigo. Se você criou um usando o início rápido da CLI, você já deve tê-los, caso contrário, consulte o guia de início rápido da CLI que foi vinculado anteriormente para saber como recuperar sua chave de conta de armazenamento.

* **Escolha uma distribuição do Linux para atender às suas necessidades de montagem.**  
      Os arquivos do Azure podem ser montados via SMB 2,1 e SMB 3,0. Para conexões provenientes de clientes locais ou em outras regiões do Azure, você deve usar SMB 3,0; Os arquivos do Azure rejeitarão SMB 2,1 (ou SMB 3,0 sem criptografia). Se estiver acessando o compartilhamento de arquivos do Azure de uma VM na mesma região do Azure, você poderá acessar o compartilhamento de arquivos usando o SMB 2,1, se e somente se a *transferência segura necessária* estiver desabilitada para a conta de armazenamento que hospeda o compartilhamento de arquivos do Azure. É sempre recomendável exigir transferência segura e usar apenas o SMB 3,0 com criptografia.

    O suporte à criptografia SMB 3,0 foi introduzido na versão 4,11 do kernel do Linux e foi reportado para versões mais antigas do kernel para distribuições populares do Linux. No momento da publicação deste documento, as seguintes distribuições da galeria do Azure dão suporte à opção de montagem especificada nos cabeçalhos da tabela. 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>Versões mínimas recomendadas com recursos de montagem correspondentes (versão SMB 2,1 vs versão SMB 3,0)

|   | SMB 2.1 <br>(Montagens em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens do local e entre regiões) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04 + | 16.04 + |
| RHEL | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + |   |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12 | 12 SP3 + |

Se sua distribuição do Linux não estiver listada aqui, você poderá verificar a versão do kernel do Linux com o seguinte comando:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**O pacote CIFS-utils está instalado.**  
    O pacote CIFS-utils pode ser instalado usando o Gerenciador de pacotes na distribuição do Linux de sua escolha. 

    Em distribuições **baseadas** no **Ubuntu** e no Debian, use `apt-get` o Gerenciador de pacotes:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    No **RHEL** e no **CentOS**, use `yum` o Gerenciador de pacotes:

    ```bash
    sudo yum install cifs-utils
    ```

    No **openSUSE**, use o `zypper` Gerenciador de pacotes:

    ```bash
    sudo zypper install cifs-utils
    ```

    Em outras distribuições, use o Gerenciador de pacotes apropriado ou [Compile da origem](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Decida as permissões de diretório/arquivo do compartilhamento montado**: Nos exemplos abaixo, a permissão `0777` é usada para conceder permissões de leitura, gravação e execução a todos os usuários. Você pode substituí-lo por outras [permissões de chmod](https://en.wikipedia.org/wiki/Chmod) conforme desejado, embora isso signifique potencialmente restringir o acesso. Se você usar outras permissões, considere também usar UID e GID para manter o acesso a usuários e grupos locais de sua escolha.

> [!NOTE]
> Se você não atribuir explicitamente permissão de diretório e arquivo com dir_mode e file_mode, eles serão padronizados como 0755.

* **Verifique se a porta 445 está aberta**: O SMB comunica através da porta TCP 445 - verifique se a firewall não está a bloquear as portas TCP 445 no computador cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar o compartilhamento de arquivos do Azure sob demanda com`mount`

1. **[Instale o pacote CIFS-utils para sua distribuição do Linux](#install-cifs-utils)** .

1. **Crie uma pasta para o ponto de montagem**: Uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é uma convenção comum criar isso em uma nova pasta. Por exemplo, o comando a seguir cria um novo diretório, substitui **< storage_account_name >** e **< file_share_name >** com as informações apropriadas para o seu ambiente:

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Use o comando mount para montar o compartilhamento de arquivos do Azure**: Lembre-se de substituir **< storage_account_name >** , **< share_name >** , **< smb_version >** , **< storage_account_key >** e **< mount_point >** com as informações apropriadas para seu ambiente. Se sua distribuição do Linux der suporte ao SMB 3,0 com criptografia (consulte [entender os requisitos do cliente SMB](#smb-client-reqs) para obter mais informações), use **3,0** para **< smb_version >** . Para distribuições do Linux que não dão suporte a SMB 3,0 com criptografia, use **2,1** para **< smb_version >** . Um compartilhamento de arquivos do Azure só pode ser montado fora de uma região do Azure (incluindo o local ou em uma região diferente do Azure) com o SMB 3,0. Se desejar, você pode alterar as permissões de diretório e arquivo do seu compartilhamento montado, mas isso significaria restringir o acesso.

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quando terminar de usar o compartilhamento de arquivos do Azure, você poderá `sudo umount <mount_point>` usar o para desmontar o compartilhamento.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para o compartilhamento de arquivos do Azure com`/etc/fstab`

1. **[Instale o pacote CIFS-utils para sua distribuição do Linux](#install-cifs-utils)** .

1. **Crie uma pasta para o ponto de montagem**: Uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é uma convenção comum criar isso em uma nova pasta. Sempre que você criar isso, observe o caminho absoluto da pasta. Por exemplo, o comando a seguir cria um novo diretório, substitui **< storage_account_name >** e **< file_share_name >** com as informações apropriadas para o seu ambiente.

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **Crie um arquivo de credencial para armazenar o nome de usuário (o nome da conta de armazenamento) e a senha (a chave da conta de armazenamento) para o compartilhamento de arquivos.** Substitua **< storage_account_name >** e **< storage_account_key >** com as informações apropriadas para o seu ambiente.

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **Altere as permissões no arquivo de credencial para que somente a raiz possa ler ou modificar o arquivo de senha.** Como a chave da conta de armazenamento é essencialmente uma senha de superadministrador para a conta de armazenamento, definir as permissões no arquivo de modo que somente a raiz possa acessar é importante para que os usuários com privilégios mais baixos não possam recuperar a chave da conta de armazenamento.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **Use o seguinte comando para acrescentar a seguinte linha a `/etc/fstab`** : Lembre-se de substituir **< storage_account_name >** , **< share_name >** , **< smb_version >** e **< mount_point >** com as informações apropriadas para o seu ambiente. Se sua distribuição do Linux der suporte ao SMB 3,0 com criptografia (consulte [entender os requisitos do cliente SMB](#smb-client-reqs) para obter mais informações), use **3,0** para **< smb_version >** . Para distribuições do Linux que não dão suporte a SMB 3,0 com criptografia, use **2,1** para **< smb_version >** . Um compartilhamento de arquivos do Azure só pode ser montado fora de uma região do Azure (incluindo o local ou em uma região diferente do Azure) com o SMB 3,0.

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> Você pode usar `sudo mount -a` o para montar o compartilhamento de arquivos do `/etc/fstab` Azure após a edição em vez de reinicializar.

## <a name="feedback"></a>Comentários

Usuários do Linux, queremos ouvir você!

O grupo de arquivos do Azure para usuários do Linux fornece um fórum para você compartilhar comentários ao avaliar e adotar o armazenamento de arquivos no Linux. Enviar email para [os usuários do Azure](mailto:azurefileslinuxusers@microsoft.com) para o grupo de usuários.

## <a name="next-steps"></a>Passos Seguintes

Veja estas ligações para obter mais informações sobre os Ficheiros do Azure:

* [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
