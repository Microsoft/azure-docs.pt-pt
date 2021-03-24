---
title: Use ficheiros Azure com | Linux Microsoft Docs
description: Saiba como montar uma partilha de ficheiros Azure sobre sMB em Linux. Consulte a lista de pré-requisitos. Reveja considerações de segurança da SMB sobre os clientes Linux.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4ace5620bf98b06956c294a12b6b08881422e718
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952342"
---
# <a name="use-azure-files-with-linux"></a>Utilizar os Ficheiros do Azure com o Linux
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As ações de ficheiros Azure podem ser montadas nas distribuições linux utilizando o [cliente kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas formas de montar uma partilha de ficheiros Azure: a pedido com o `mount` comando e no arranque, criando uma entrada em `/etc/fstab` .

A forma recomendada de montar uma partilha de ficheiros Azure no Linux está a utilizar sMB 3.0. Por padrão, os Ficheiros Azure requerem encriptação em trânsito, que só é suportado por SMB 3.0. O Azure Files também suporta sMB 2.1, que não suporta encriptação em trânsito, mas não pode montar partilhas de ficheiros Azure com SMB 2.1 de outra região de Azure ou no local por razões de segurança. A menos que a sua aplicação exija especificamente SMB 2.1, não há razão para usá-lo desde o mais popular, recentemente lançado, apoio de distribuição Linux SMB 3.0:  

| Distribuição linux | SMB 2.1 <br>(Montes em VMs na mesma região de Azure) | SMB 3.0 <br>(Montes a partir de instalações e de região transversal) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | Mais de 10 |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP2+ |

Se estiver a utilizar uma distribuição Linux não listada na tabela acima, pode verificar se a sua distribuição Linux suporta SMB 3.0 com encriptação, verificando a versão do kernel Do Linux. SMB 3.0 com encriptação foi adicionado à versão 4.11 do kernel linux. O `uname` comando devolverá a versão do kernel Linux em uso:

```bash
uname -r
```

## <a name="prerequisites"></a>Pré-requisitos
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Certifique-se de que a embalagem cifs-utils está instalada.**  
    O pacote cifs-utils pode ser instalado usando o gestor de pacotes na distribuição Linux à sua escolha. 

    Nas distribuições baseadas em **Ubuntu** e **Debian,** utilize o `apt` gestor de pacotes:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    No **Fedora,** **Red Hat Enterprise Linux 8+** e **CentOS 8 +**, utilize o gestor do `dnf` pacote:

    ```bash
    sudo dnf install cifs-utils
    ```

    Nas versões mais antigas da **Red Hat Enterprise Linux** e **CentOS,** utilize o `yum` gestor do pacote:

    ```bash
    sudo yum install cifs-utils 
    ```

    No **openSUSE,** utilize o `zypper` gestor do pacote:

    ```bash
    sudo zypper install cifs-utils
    ```

    Noutras distribuições, utilize o gestor de pacotes apropriado ou [compile a partir da fonte](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **A versão mais recente da Interface da Linha de Comando Azure (CLI).** Para obter mais informações sobre como instalar o Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli) e selecione o seu sistema operativo. Se preferir utilizar o módulo Azure PowerShell no PowerShell 6+, pode, no entanto, as instruções abaixo são apresentadas para o Azure CLI.

* **Certifique-se de que a porta 445 está aberta**: SMB comunica através da porta TCP 445 - verifique se a sua firewall não está a bloquear as portas TCP 445 da máquina do cliente.  Substitua `<your-resource-group>` `<your-storage-account>` e, em seguida, execute o seguinte script:
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Se a ligação tiver sido bem sucedida, deverá ver algo semelhante à seguinte saída:

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Se não conseguir abrir a porta 445 da sua rede corporativa ou estiver impedido de o fazer por um ISP, poderá utilizar uma ligação VPN ou ExpressRoute para trabalhar na porta 445. Para obter mais informações, consulte [considerações de Networking para acesso direto à partilha de ficheiros Azure](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Montagem Azure partilha de ficheiros
Para utilizar uma partilha de ficheiros Azure com a sua distribuição Linux, tem de criar um diretório para servir de ponto de montagem para a partilha de ficheiros Azure. Um ponto de montagem pode ser criado em qualquer lugar do seu sistema Linux, mas é uma convenção comum para criar isto sob /montagem. Depois do ponto de montagem, utilize o `mount` comando para aceder à partilha de ficheiros Azure.

Pode montar a mesma partilha de ficheiros Azure em vários pontos de montagem, se desejar.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Monte o arquivo Azure partilhar a pedido com `mount`
1. **Crie uma pasta para o ponto de montagem**: Substitua, e com as `<your-resource-group>` `<your-storage-account>` `<your-file-share>` informações adequadas para o seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Utilize o comando de montagem para montar a partilha de ficheiros Azure**. No exemplo abaixo, o ficheiro e as permissões de pastas Linux locais padrão 0755, o que significa ler, escrever e executar para o proprietário (com base no proprietário do ficheiro/diretório Linux), ler e executar para os utilizadores no grupo proprietário, e ler e executar para outros no sistema. Pode utilizar as `uid` opções e `gid` montagem para definir o ID do utilizador e o ID do grupo para a montagem. Também pode usar `dir_mode` e `file_mode` definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte a [notação numérica da UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia. 

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > O comando de montagem acima monta com SMB 3.0. Se a sua distribuição Linux não suportar SMB 3.0 com encriptação ou se apenas suportar SMB 2.1, só poderá montar a partir de um VM Azure na mesma região que a conta de armazenamento. Para montar a sua partilha de ficheiros Azure numa distribuição Linux que não suporta SMB 3.0 com encriptação, terá de [desativar a encriptação em trânsito para a conta de armazenamento.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Quando terminar de usar a partilha de ficheiros Azure, poderá utilizar `sudo umount $mntPath` para desmontar a partilha.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Crie um ponto de montagem persistente para a partilha de ficheiros Azure com `/etc/fstab`
1. **Criar uma pasta para o ponto de montagem**: Uma pasta para um ponto de montagem pode ser criada em qualquer lugar do sistema de ficheiros, mas é comum criar esta sob /montagem. Por exemplo, o seguinte comando cria um novo diretório, `<your-resource-group>` substitui, `<your-storage-account>` e com as `<your-file-share>` informações apropriadas para o seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mount/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Crie um ficheiro de credencial para armazenar o nome de utilizador (o nome da conta de armazenamento) e a palavra-passe (a chave da conta de armazenamento) para a partilha de ficheiros.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Altere as permissões no ficheiro credencial para que apenas a raiz possa ler ou modificar o ficheiro de palavra-passe.** Uma vez que a chave da conta de armazenamento é essencialmente uma senha de super-administrador para a conta de armazenamento, definir as permissões no ficheiro de modo a que apenas a raiz possa aceder é importante para que os utilizadores de privilégios mais baixos não possam recuperar a chave da conta de armazenamento.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Utilize o seguinte comando para anexar a `/etc/fstab` seguinte linha para:** No exemplo abaixo, as permissões locais de ficheiro e pasta Linux predefinidos 0755, que significa ler, escrever e executar para o proprietário (com base no proprietário do ficheiro/diretório Linux), ler e executar para os utilizadores do grupo proprietário, e ler e executar para outros no sistema. Pode utilizar as `uid` opções e `gid` montagem para definir o ID do utilizador e o ID do grupo para a montagem. Também pode usar `dir_mode` e `file_mode` definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte a [notação numérica da UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia.

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > O comando de montagem acima monta com SMB 3.0. Se a sua distribuição Linux não suportar SMB 3.0 com encriptação ou se apenas suportar SMB 2.1, só poderá montar a partir de um VM Azure na mesma região que a conta de armazenamento. Para montar a sua partilha de ficheiros Azure numa distribuição Linux que não suporta SMB 3.0 com encriptação, terá de [desativar a encriptação em trânsito para a conta de armazenamento.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Utilizar autofs para montar automaticamente a(s) partilha de ficheiros Azure

1. **Certifique-se de que a embalagem autofs está instalada.**  

    O pacote autofs pode ser instalado usando o gestor de pacotes na distribuição Linux à sua escolha. 

    Nas distribuições baseadas em **Ubuntu** e **Debian,** utilize o `apt` gestor de pacotes:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    No **Fedora,** **Red Hat Enterprise Linux 8+** e **CentOS 8 +**, utilize o gestor do `dnf` pacote:
    ```bash
    sudo dnf install autofs
    ```
    Nas versões mais antigas da **Red Hat Enterprise Linux** e **CentOS,** utilize o `yum` gestor do pacote:
    ```bash
    sudo yum install autofs 
    ```
    No **openSUSE,** utilize o `zypper` gestor do pacote:
    ```bash
    sudo zypper install autofs
    ```
2. **Criar um ponto de montagem para a(s) partilha:**
   ```bash
    sudo mkdir /fileshares
    ```
3. **Creta um novo ficheiro de configuração de autofs personalizado**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Adicione as seguintes entradas a /etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Adicione a seguinte entrada a /etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Reiniciar autofs**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Aceda à pasta designada para a partilha**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Segurança Linux
Para montar uma partilha de ficheiros Azure no Linux, a porta 445 deve estar acessível. Muitas organizações optam por bloquear esta porta devido a riscos de segurança inerentes ao SMB 1. SMB 1, também conhecido como CIFS (Common Internet File System), é um protocolo de sistema de ficheiros antigo incluído com muitas distribuições de Linux. O SMB 1 é um protocolo desatualizado, ineficiente e, acima de tudo, inseguro. A boa notícia é que o Azure Files não suporta SMB 1, e a começar pela versão 4.18 do Kernel Linux, o Linux permite desativar o SMB 1. Recomendamos sempre [vivamente](https://aka.ms/stopusingsmb1) a desativação do SMB 1 nos seus clientes Linux antes de utilizar as ações de ficheiro SMB em produção.

Começando pelo kernel Linux 4.18, o módulo de kernel SMB, chamado `cifs` por razões antigas, expõe um novo parâmetro do módulo (muitas vezes referido como *parm* por várias documentações externas), denominado `disable_legacy_dialects` . Apesar de introduzido no kernel 4.18 do Linux, alguns vendedores recorreram a esta mudança para núcleos mais antigos que suportam. Por conveniência, a tabela seguinte detalha a disponibilidade deste parâmetro do módulo nas distribuições comuns do Linux.

| Distribuição | Pode desativar o SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Sim |
| Ubuntu 19.04+ | Sim |
| Debian 8-9 | No |
| Debian 10+ | Sim |
| Fedora 29+ | Sim |
| CentOS 7 | No | 
| CentOS 8+ | Sim |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8+ | Sim |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1+ | Sim |
| openSUSE Tumbleweed | Sim |
| SUSE Linux Enterprise 11.x-12.x | No |
| Empresa SUSE Linux 15 | No |
| Empresa SUSE Linux 15.1 | No |

Pode verificar se a sua distribuição Linux suporta o parâmetro do `disable_legacy_dialects` módulo através do seguinte comando.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Este comando deve enviar a seguinte mensagem:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Antes de desativar o SMB 1, deve verificar se o módulo SMB não está atualmente carregado no seu sistema (isto acontece automaticamente se tiver montado uma quota SMB). Pode fazê-lo com o seguinte comando, que não deverá dar saída se o SMB não estiver carregado:

```bash
lsmod | grep cifs
```

Para descarregar o módulo, em primeiro lugar desmonte todas as ações SMB (utilizando o `umount` comando como descrito acima). Pode identificar todas as ações SMB montadas no seu sistema com o seguinte comando:

```bash
mount | grep cifs
```

Uma vez desmontadas todas as ações de ficheiro SMB, é seguro descarregar o módulo. Pode fazer isto com o comando `modprobe`:

```bash
sudo modprobe -r cifs
```

Pode carregar manualmente o módulo com SMB 1 descarregado com o `modprobe` comando:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Finalmente, pode verificar se o módulo SMB foi carregado com o parâmetro, olhando para os parâmetros carregados em `/sys/module/cifs/parameters` :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Para desativar persistentemente o SMB 1 nas distribuições baseadas em Ubuntu e Debian, tem de criar um novo ficheiro (se ainda não tiver opções personalizadas para outros módulos) chamado `/etc/modprobe.d/local.conf` com a definição. Pode fazê-lo com o seguinte comando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Pode verificar se isto funcionou carregando o módulo SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Passos seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure:

* [Planear uma implementação de Ficheiros do Azure](storage-files-planning.md)
* [FAQ](./storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-linux-file-connection-problems.md)