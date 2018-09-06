---
title: Utilizar ficheiros do Azure com Linux | Documentos da Microsoft
description: Saiba como montar uma partilha de ficheiros do Azure através de SMB no Linux.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: bbd250e9ee987403d670b2605fdb8deda8c19092
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782060"
---
# <a name="use-azure-files-with-linux"></a>Utilizar ficheiros do Azure com o Linux
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. Partilhas de ficheiros do Azure podem ser montadas em distribuições do Linux com o [cliente do SMB kernel](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas formas de montar uma partilha de ficheiros do Azure: a pedido com o `mount` comando e de arranque através da criação de uma entrada no `/etc/fstab`.

> [!NOTE]  
> Para montar uma partilha de ficheiros do Azure fora da região do Azure que está alojada, como no local ou numa região diferente do Azure, o sistema operacional tem de suportar a funcionalidade de encriptação do SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Pré-requisitos para montar uma partilha de ficheiros do Azure com Linux e o pacote de utils do cifs
<a id="smb-client-reqs"></a>
* **Escolha uma distribuição do Linux para se adequar às suas necessidades de montagem.**  
      Os ficheiros do Azure podem ser montados através de SMB 2.1 e o SMB 3.0. Para ligações futuras a partir de clientes no local ou noutras regiões do Azure, ficheiros do Azure irão rejeitar o SMB 2.1 (ou SMB 3.0 sem encriptação). Se *transferência segura necessária* está ativada para uma conta de armazenamento, ficheiros do Azure irá permitir apenas ligações através de SMB 3.0 com a encriptação.
    
    Suporte de encriptação SMB 3.0 foi introduzido na versão de kernel 4.11 Linux e tem sido backported para versões mais antigas do kernel para distribuições populares do Linux. No momento da publicação deste documento, as distribuições seguintes a partir da galeria do Azure suportam a opção de montagem especificada nos cabeçalhos de tabela. 

* **Mínimo recomendado para as versões com recursos de montagem correspondentes (versão do SMB versão 2.1 vs do SMB 3.0)**    
    
    |   | SMB 2.1 <br>(Monta em VMs na mesma região do Azure) | SMB 3.0 <br>(Monta no local e em várias regiões) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04 + | 16.04 + |
    | RHEL | 7 e posteriores | 7.5 + |
    | CentOS | 7 e posteriores |  7.5 + |
    | Debian | 8 e posteriores |   |
    | openSUSE | 13.2 + | 42.3 + |
    | SUSE Linux Enterprise Server | 12 | 12 SP3 + |
    
    Se sua distribuição do Linux não estiver listada aqui, pode verificar a versão de kernel do Linux com o seguinte comando:    

   ```bash
   uname -r
   ```    

* <a id="install-cifs-utils"></a>**O pacote do cifs utils está instalado.**  
    O pacote do cifs utils pode ser instalado utilizando o Gestor de pacotes no distribuição Linux da sua preferência. 

    No **Ubuntu** e **baseada no Debian** distribuições, utilize o `apt-get` Gestor de pacotes:

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    No **RHEL** e **CentOS**, utilize o `yum` Gestor de pacotes:

    ```bash
    sudo yum install cifs-utils
    ```

    No **openSUSE**, utilize o `zypper` Gestor de pacotes:

    ```bash
    sudo zypper install cifs-utils
    ```

    Em outras distribuições, utilize o Gestor de pacotes apropriada ou [compilar a partir da origem](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)
    
* **Decidir sobre as permissões de diretório/ficheiro da partilha montada**: nos exemplos abaixo, a permissão `0777` é usada para dar a leitura, escrita e permissões a todos os utilizadores de execução. Pode substituí-la com os outros [chmod permissões](https://en.wikipedia.org/wiki/Chmod) conforme pretendido. 

* **Nome da conta de armazenamento**: para montar uma partilha de ficheiros do Azure, é necessário o nome da conta de armazenamento.

* **Chave da conta de armazenamento**: para montar uma partilha de ficheiros do Azure, precisa da chave de armazenamento primária (ou secundário). Atualmente, não são suportadas chaves SAS para a montagem.

* **Certifique-se de que a porta 445 está aberta**: SMB comunica através da porta TCP 445 - verifique ver se a firewall não está a bloquear TCP 445 a partir do computador cliente de portas.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar a arquivo do Azure partilha a pedido com `mount`
1. **[Instale o pacote de utils do cifs para sua distribuição do Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é Convenção comuns para criar este sob o `/mnt` pasta. Por exemplo:

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

3. **Utilize o comando de montagem para montar a partilha de ficheiros do Azure**: não se esqueça de substituir `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>`, e `<mount-point>` com as informações apropriadas para o seu ambiente. Se a sua distribuição do Linux suporta o SMB 3.0 com encriptação (consulte [requisitos do cliente SMB compreender](#smb-client-reqs) para obter mais informações), utilize `3.0` para `<smb-version>`. Para as distribuições de Linux que suporta o SMB 3.0 com encriptação, utilize `2.1` para `<smb-version>`. Tenha em atenção que uma partilha de ficheiros do Azure só pode ser montada fora de uma região do Azure (incluindo no local ou numa região diferente do Azure) com SMB 3.0. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quando tiver terminado com a partilha de ficheiros do Azure, pode utilizar `sudo umount <mount-point>` para desmontar a partilha.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para a partilha de ficheiros do Azure com o `/etc/fstab`
1. **[Instale o pacote de utils do cifs para sua distribuição do Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é Convenção comuns para criar este sob o `/mnt` pasta. Sempre que criar isso, tenha em atenção o caminho absoluto da pasta. Por exemplo, o comando seguinte cria uma nova pasta no `/mnt` (o caminho é um caminho absoluto).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Crie um ficheiro de credenciais para armazenar o nome de utilizador (o nome de conta de armazenamento) e a palavra-passe (a chave de conta de armazenamento) para a partilha de ficheiros.** Não se esqueça de substituir `<storage-account-name>` e `<storage-account-key>` com as informações apropriadas para o seu ambiente. 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

4. **Alterar permissões no arquivo de credencial, de modo a raiz só pode ler ou modificar o ficheiro de palavra-passe.** Uma vez que a chave de conta de armazenamento é, essencialmente, uma senha de administrador superutilizadores para a conta de armazenamento, a definir as permissões no ficheiro, de modo que apenas raiz pode aceder é importante para que os utilizadores de privilégio mais baixos não foi possível obter a chave de conta de armazenamento.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

5. **Utilize o seguinte comando para acrescentar a seguinte linha para `/etc/fstab`** : não se esqueça de substituir `<storage-account-name>`, `<share-name>`, `<smb-version>`, e `<mount-point>` com as informações apropriadas para o seu ambiente. Se a sua distribuição do Linux suporta o SMB 3.0 com encriptação (consulte [requisitos do cliente SMB compreender](#smb-client-reqs) para obter mais informações), utilize `3.0` para `<smb-version>`. Para as distribuições de Linux que suporta o SMB 3.0 com encriptação, utilize `2.1` para `<smb-version>`. Tenha em atenção que uma partilha de ficheiros do Azure só pode ser montada fora de uma região do Azure (incluindo no local ou numa região diferente do Azure) com SMB 3.0. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Pode usar `sudo mount -a` para montar a partilha de ficheiros do Azure depois de editar `/etc/fstab` em vez de reiniciar.

## <a name="feedback"></a>Comentários
Os utilizadores do Linux, queremos a sua opinião!

Ficheiros do Azure para o grupo de utilizadores do Linux fornece um fórum para que possa partilhar comentários à medida que avalia e adotar o armazenamento de ficheiros no Linux. Correio eletrónico [os utilizadores de Linux do Azure ficheiros](mailto:azurefileslinuxusers@microsoft.com) para aderir a grupo do utilizador.

## <a name="next-steps"></a>Passos Seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.
* [Introdução aos ficheiros do Azure](storage-files-introduction.md)
* [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
