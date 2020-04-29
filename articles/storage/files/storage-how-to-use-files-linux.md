---
title: Use ficheiros Azure com Linux / Microsoft Docs
description: Aprenda a montar uma partilha de ficheiros Azure sobre sMB no Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fcc9876caf0c002650ab30b7eaed7dc44e2f135e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137744"
---
# <a name="use-azure-files-with-linux"></a>Utilizar os Ficheiros do Azure com o Linux
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As ações de ficheiros Azure podem ser montadas nas distribuições linux utilizando o [cliente kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas formas de montar uma partilha `mount` de ficheiros Azure: `/etc/fstab`a pedido com o comando e o arranque, criando uma entrada em .

A forma recomendada de montar uma partilha de ficheiros Azure no Linux é a utilização de SMB 3.0. Por padrão, o Azure Files requer encriptação em trânsito, que só é suportada por SMB 3.0. O Azure Files também suporta o SMB 2.1, que não suporta encriptação em trânsito, mas não pode montar ações de ficheiros Azure com SMB 2.1 de outra região do Azure ou no local por razões de segurança. A menos que a sua aplicação exija especificamente SMB 2.1, há poucarazão para usá-lo desde que a distribuição linux recentemente lançada suporta SMB 3.0:  

| | SMB 2.1 <br>(Montes em VMs dentro da mesma região de Azure) | SMB 3.0 <br>(Montes a partir de instalações e regiões transversais) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | Mais de 10 |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

Se estiver a utilizar uma distribuição Linux não listada na tabela acima, pode verificar se a sua distribuição Linux suporta SMB 3.0 com encriptação, verificando a versão kernel Linux. SMB 3.0 com encriptação foi adicionado à versão 4.11 do kernel linux. O `uname` comando devolverá a versão do kernel Linux em uso:

```bash
uname -r
```

## <a name="prerequisites"></a>Pré-requisitos
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Certifique-se de que o pacote cifs-utils está instalado.**  
    O pacote cifs-utils pode ser instalado utilizando o gestor de pacotes na distribuição linux à sua escolha. 

    Nas distribuições baseadas em **Ubuntu** e `apt` **Debian,** utilize o gestor de pacotes:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Em **Fedora,** **Red Hat Enterprise Linux 8+** e `dnf` **CentOS 8 +,** use o gestor de pacotes:

    ```bash
    sudo dnf install cifs-utils
    ```

    Nas versões mais antigas da Red Hat `yum` Enterprise **Linux** e **centOS,** utilize o gestor de pacotes:

    ```bash
    sudo yum install cifs-utils 
    ```

    No **openSUSE,** `zypper` utilize o gestor de pacotes:

    ```bash
    sudo zypper install cifs-utils
    ```

    Noutras distribuições, utilize o gestor de pacotes apropriado ou [compilar a partir da fonte](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **A versão mais recente da Interface da Linha de Comando Azure (CLI).** Para obter mais informações sobre como instalar o Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e selecionar o seu sistema operativo. Se preferir utilizar o módulo PowerShell Azure no PowerShell 6+, pode, no entanto, as instruções abaixo são apresentadas para o Azure CLI.

* **Certifique-se de que a porta 445 está aberta**: O SMB comunica sobre a porta TCP 445 - verifique se a sua firewall não está a bloquear as portas TCP 445 da máquina cliente.  Substitua **<>do seu grupo de recursos** e<>da sua conta de **armazenamento**
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

    Se a ligação foi bem sucedida, deve ver algo semelhante à seguinte saída:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Se não conseguir abrir a porta 445 da sua rede corporativa ou estiver impedido de o fazer por um ISP, poderá utilizar uma ligação VPN ou ExpressRoute para trabalhar em torno do porto 445. Para obter mais informações, consulte [as considerações de Networking para acesso direto ao ficheiro Azure](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Montagem da partilha de ficheiros Azure
Para utilizar uma partilha de ficheiros Azure com a sua distribuição Linux, tem de criar um diretório para servir de ponto de montagem para a partilha de ficheiros Azure. Um ponto de montagem pode ser criado em qualquer lugar do seu sistema Linux, mas é uma convenção comum para criar isso sob /mnt. Após o ponto de `mount` montagem, usa o comando para aceder à partilha de ficheiros Azure.

Pode montar a mesma partilha de ficheiros Azure em vários pontos de montagem, se desejar.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Monte o arquivo Azure partilha a pedido com`mount`
1. **Crie uma pasta para** `<your-resource-group>`o `<your-storage-account>`ponto de montagem : Substitua, e `<your-file-share>` com as informações adequadas para o seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Utilize o comando de montagem para montar a partilha de ficheiros Azure**. No exemplo abaixo, o ficheiro e as permissões de pasta linux locais padrão 0755, o que significa ler, escrever e executar para o proprietário (com base no proprietário do ficheiro/diretório Linux), ler e executar para utilizadores do grupo proprietário, e ler e executar para outros no sistema. Pode utilizar `uid` as `gid` opções e montar para definir o ID do utilizador e o ID do grupo para a montagem. Também pode `dir_mode` usar `file_mode` e definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte a [notação numérica da UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia. 

    ```bash
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
    > O comando de montagem acima monta com SMB 3.0. Se a sua distribuição Linux não suportar SMB 3.0 com encriptação ou se apenas suportar SMB 2.1, só poderá ser montado a partir de um VM Azure na mesma região que a conta de armazenamento. Para montar a sua quota de ficheiro SBe numa distribuição Linux que não suporta SMB 3.0 com encriptação, terá de [desativar](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)a encriptação em trânsito para a conta de armazenamento .

Quando terminar a utilização da partilha de `sudo umount $mntPath` ficheiros Azure, poderá utilizar para desmontar a parte.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para a partilha de ficheiros Azure com`/etc/fstab`
1. **Crie uma pasta para o ponto**de montagem : Uma pasta para um ponto de montagem pode ser criada em qualquer lugar do sistema de ficheiros, mas é uma convenção comum para criar isto sob /mnt. Por exemplo, o seguinte comando cria `<your-resource-group>`um `<your-storage-account>`novo `<your-file-share>` diretório, substitua, e com as informações apropriadas para o seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Crie um ficheiro credencial para armazenar o nome de utilizador (o nome da conta de armazenamento) e a palavra-passe (a chave da conta de armazenamento) para a parte do ficheiro.** 

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

1. **Alterar permissões no ficheiro credencial para que só a raiz possa ler ou modificar o ficheiro de palavra-passe.** Uma vez que a chave da conta de armazenamento é essencialmente uma palavra-passe super-administradora para a conta de armazenamento, definir as permissões no ficheiro de tal forma que só a raiz pode aceder é importante para que os utilizadores de menor privilégio não possam recuperar a chave da conta de armazenamento.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Utilize o seguinte comando para anexar a `/etc/fstab`seguinte linha para: **No exemplo abaixo, o ficheiro e as permissões de pasta linux locais padrão 0755, o que significa ler, escrever e executar para o proprietário (com base no proprietário do ficheiro/diretório Linux), ler e executar para utilizadores do grupo proprietário, e ler e executar para outros no sistema. Pode utilizar `uid` as `gid` opções e montar para definir o ID do utilizador e o ID do grupo para a montagem. Também pode `dir_mode` usar `file_mode` e definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte a [notação numérica da UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia.

    ```bash
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
    > O comando de montagem acima monta com SMB 3.0. Se a sua distribuição Linux não suportar SMB 3.0 com encriptação ou se apenas suportar SMB 2.1, só poderá ser montado a partir de um VM Azure na mesma região que a conta de armazenamento. Para montar a sua quota de ficheiro SBe numa distribuição Linux que não suporta SMB 3.0 com encriptação, terá de [desativar](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)a encriptação em trânsito para a conta de armazenamento .

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Utilização de autofs para montar automaticamente as ações de ficheiros Azure

1. **Certifique-se de que a embalagem de autofs está instalada.**  

    O pacote de autofs pode ser instalado utilizando o gestor de pacotes na distribuição Linux à sua escolha. 

    Nas distribuições baseadas em **Ubuntu** e `apt` **Debian,** utilize o gestor de pacotes:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    Em **Fedora,** **Red Hat Enterprise Linux 8+** e `dnf` **CentOS 8 +,** use o gestor de pacotes:
    ```bash
    sudo dnf install autofs
    ```
    Nas versões mais antigas da Red Hat `yum` Enterprise **Linux** e **centOS,** utilize o gestor de pacotes:
    ```bash
    sudo yum install autofs 
    ```
    No **openSUSE,** `zypper` utilize o gestor de pacotes:
    ```bash
    sudo zypper install autofs
    ```
2. **Criar um ponto**de montagem para a ou as partes:
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
5. **Adicione a seguinte entrada para /etc/auto.master**
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
## <a name="securing-linux"></a>Assegurar o Linux
Para montar uma partilha de ficheiros Azure no Linux, a porta 445 deve estar acessível. Muitas organizações optam por bloquear esta porta devido a riscos de segurança inerentes ao SMB 1. SMB 1, também conhecido como CIFS (Sistema Comum de Ficheiros de Internet), é um protocolo de sistema de ficheiros legado incluído com muitas distribuições linux. O SMB 1 é um protocolo desatualizado, ineficiente e, acima de tudo, inseguro. A boa notícia é que o Azure Files não suporta o SMB 1, e começando com a versão 4.18 do kernel Linux, o Linux permite desativar o SMB 1. [Recomendamos](https://aka.ms/stopusingsmb1) sempre que desafie o SMB 1 nos seus clientes Linux antes de utilizar as ações de ficheiro SMB na produção.

Começando pelo kernel Linux 4.18, o módulo `cifs` kernel SMB, chamado por razões antigas, expõe um novo parâmetro `disable_legacy_dialects`de módulo (muitas vezes referido como *parm* por várias documentações externas), chamado . Embora introduzido no kernel Linux 4.18, alguns vendedores têm reportado esta mudança para núcleos mais antigos que apoiam. Por conveniência, a tabela seguinte detalha a disponibilidade deste parâmetro de módulo em distribuições comuns de Linux.

| Distribuição | Pode desativar SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Não |
| Ubuntu 18.04 | Sim |
| Ubuntu 19.04+ | Sim |
| Debiano 8-9 | Não |
| Debiano 10+ | Sim |
| Fedora 29+ | Sim |
| CentOS 7 | Não | 
| CentOS 8+ | Sim |
| Red Hat Enterprise Linux 6.x-7.x | Não |
| Red Hat Enterprise Linux 8+ | Sim |
| abrir SUSE Salto 15.0 | Não |
| abre SUSE Leap 15.1+ | Sim |
| abrir Tumbleweed SUSE | Sim |
| SUSE Linux Enterprise 11.x-12.x | Não |
| SUSE Linux Enterprise 15 | Não |
| SUSE Linux Enterprise 15.1 | Não |

Pode verificar se a sua distribuição Linux suporta o parâmetro do `disable_legacy_dialects` módulo através do seguinte comando.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Este comando deve ser da seguinte mensagem:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Antes de desativar o SMB 1, tem de verificar se o módulo SMB não está atualmente carregado no seu sistema (isto acontece automaticamente se tiver montado uma parte SMB). Pode fazê-lo com o seguinte comando, que não deve ser feito se o SMB não estiver carregado:

```bash
lsmod | grep cifs
```

Para descarregar o módulo, primeiro desmonte `umount` todas as ações SMB (utilizando o comando como descrito acima). Pode identificar todas as ações smb montadas no seu sistema com o seguinte comando:

```bash
mount | grep cifs
```

Uma vez desmontado todas as partilhas de ficheiros SMB, é seguro descarregar o módulo. Pode fazer isto com o comando `modprobe`:

```bash
sudo modprobe -r cifs
```

Pode carregar manualmente o módulo com SMB `modprobe` 1 descarregado utilizando o comando:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Por fim, pode verificar se o módulo SMB foi carregado com `/sys/module/cifs/parameters`o parâmetro olhando para os parâmetros carregados em:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Para desativar persistentemente o SMB 1 nas distribuições baseadas em Ubuntu e Debian, tem de criar `/etc/modprobe.d/local.conf` um novo ficheiro (se ainda não tiver opções personalizadas para outros módulos) chamado com a definição. Pode fazê-lo com o seguinte comando:

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

* [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
