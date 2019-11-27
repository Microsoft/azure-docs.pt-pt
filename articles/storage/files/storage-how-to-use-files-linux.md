---
title: Usar os arquivos do Azure com o Linux | Microsoft Docs
description: Saiba como montar um compartilhamento de arquivos do Azure por meio de SMB no Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3d8d7c6d3c4e752480310c122bcb7db237b3022b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209401"
---
# <a name="use-azure-files-with-linux"></a>Utilizar os Ficheiros do Azure com o Linux
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o [cliente kernel SMB](https://wiki.samba.org/index.php/LinuxCIFS). Este artigo mostra duas maneiras de montar um compartilhamento de arquivos do Azure: sob demanda com o comando `mount` e na inicialização criando uma entrada no `/etc/fstab`.

A maneira recomendada para montar um compartilhamento de arquivos do Azure no Linux é usando SMB 3,0. Por padrão, os arquivos do Azure exigem criptografia em trânsito, que tem suporte apenas no SMB 3,0. Os arquivos do Azure também dão suporte ao SMB 2,1, que não dá suporte à criptografia em trânsito, mas você não pode montar compartilhamentos de arquivos do Azure com o SMB 2,1 de outra região do Azure ou local por motivos de segurança. A menos que seu aplicativo exija especificamente o SMB 2,1, há pouco motivo para usá-lo desde que as distribuições do Linux mais populares, lançadas recentemente, dão suporte ao SMB 3,0:  

| | SMB 2.1 <br>(Montagens em VMs na mesma região do Azure) | SMB 3.0 <br>(Montagens do local e entre regiões) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + | 10+ |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | mais de 12 | 12 SP3 + |

Se você estiver usando uma distribuição do Linux não listada na tabela acima, poderá verificar se sua distribuição do Linux dá suporte ao SMB 3,0 com criptografia verificando a versão do kernel do Linux. O SMB 3,0 com criptografia foi adicionado ao kernel do Linux versão 4,11. O comando `uname` retornará a versão do kernel do Linux em uso:

```bash
uname -r
```

## <a name="prerequisites"></a>Pré-requisitos
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Verifique se o pacote CIFS-utils está instalado.**  
    O pacote CIFS-utils pode ser instalado usando o Gerenciador de pacotes na distribuição do Linux de sua escolha. 

    Em distribuições **baseadas** em **Ubuntu** e Debian, use o Gerenciador de pacotes `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Em **Fedora**, **Red Hat Enterprise Linux 8 +** e **CentOS 8 +** , use o Gerenciador de pacotes `dnf`:

    ```bash
    sudo dnf install cifs-utils
    ```

    Em versões mais antigas do **Red Hat Enterprise Linux** e do **CentOS**, use o gerenciador de pacotes do `yum`:

    ```bash
    sudo yum install cifs-utils 
    ```

    No **openSUSE**, use o Gerenciador de pacotes `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    Em outras distribuições, use o Gerenciador de pacotes apropriado ou [Compile da origem](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **A versão mais recente da CLI (interface de linha de comando) do Azure.** Para obter mais informações sobre como instalar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e selecionar o sistema operacional. Se preferir usar o módulo Azure PowerShell no PowerShell 6 +, você pode, no entanto, as instruções a seguir são apresentadas para o CLI do Azure.

* **Verifique se a porta 445 está aberta**: o SMB se comunica pela porta TCP 445-Verifique se o firewall não está bloqueando as portas TCP 445 do computador cliente.  Substitua **< seu > de grupo de recursos** e **< sua conta de armazenamento >**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $fileHost | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Se a conexão tiver sido bem-sucedida, você verá algo semelhante à seguinte saída:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Se não for possível abrir a porta 445 em sua rede corporativa ou se estiver impedido de fazer isso por um ISP, você poderá usar uma conexão VPN ou o ExpressRoute para contornar a porta 445. Para obter mais informações, consulte [considerações de rede para acesso direto ao compartilhamento de arquivos do Azure](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Montando o compartilhamento de arquivos do Azure
Para usar um compartilhamento de arquivos do Azure com sua distribuição do Linux, você deve criar um diretório para servir como o ponto de montagem para o compartilhamento de arquivos do Azure. Um ponto de montagem pode ser criado em qualquer lugar no seu sistema Linux, mas é uma convenção comum criar isso em/mnt. Após o ponto de montagem, use o comando `mount` para acessar o compartilhamento de arquivos do Azure.

Você pode montar o mesmo compartilhamento de arquivos do Azure para vários pontos de montagem, se desejar.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar o compartilhamento de arquivos do Azure sob demanda com `mount`
1. **Crie uma pasta para o ponto de montagem**: substitua `<your-resource-group>`, `<your-storage-account>`e `<your-file-share>` pelas informações apropriadas para o seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Use o comando mount para montar o compartilhamento de arquivos do Azure**. No exemplo abaixo, as permissões locais do arquivo e da pasta do Linux padrão são 0755, o que significa ler, gravar e executar para o proprietário (com base no proprietário do Linux de arquivo/diretório), ler e executar para usuários no grupo proprietário e ler e executar para outras pessoas no sistema. Você pode usar as opções de montagem `uid` e `gid` para definir a ID de usuário e a ID de grupo para a montagem. Você também pode usar `dir_mode` e `file_mode` para definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte [notação numérica do UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia. 

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
    > O comando de montagem acima é montado com SMB 3,0. Se sua distribuição do Linux não oferecer suporte a SMB 3,0 com criptografia ou se ela der suporte apenas ao SMB 2,1, você só poderá montar de uma VM do Azure na mesma região que a conta de armazenamento. Para montar o compartilhamento de arquivos do Azure em uma distribuição do Linux que não dá suporte a SMB 3,0 com criptografia, você precisará [desabilitar a criptografia em trânsito para a conta de armazenamento](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Quando terminar de usar o compartilhamento de arquivos do Azure, você poderá usar `sudo umount $mntPath` para desmontar o compartilhamento.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para o compartilhamento de arquivos do Azure com `/etc/fstab`
1. **Criar uma pasta para o ponto de montagem**: uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é uma convenção comum criá-la em/mnt. Por exemplo, o comando a seguir cria um novo diretório, substitui `<your-resource-group>`, `<your-storage-account>`e `<your-file-share>` com as informações apropriadas para seu ambiente:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Crie um arquivo de credencial para armazenar o nome de usuário (o nome da conta de armazenamento) e a senha (a chave da conta de armazenamento) para o compartilhamento de arquivos.** 

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

1. **Altere as permissões no arquivo de credencial para que somente a raiz possa ler ou modificar o arquivo de senha.** Como a chave da conta de armazenamento é essencialmente uma senha de superadministrador para a conta de armazenamento, definir as permissões no arquivo de modo que somente a raiz possa acessar é importante para que os usuários com privilégios mais baixos não possam recuperar a chave da conta de armazenamento.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Use o comando a seguir para acrescentar a seguinte linha a `/etc/fstab`** : no exemplo abaixo, as permissões de pasta e arquivo Linux local padrão 0755, que significa leitura, gravação e execução para o proprietário (com base no proprietário do Linux de arquivo/diretório), leitura e execução para usuários no grupo proprietário e leitura e execução para outras pessoas no sistema. Você pode usar as opções de montagem `uid` e `gid` para definir a ID de usuário e a ID de grupo para a montagem. Você também pode usar `dir_mode` e `file_mode` para definir permissões personalizadas conforme desejado. Para obter mais informações sobre como definir permissões, consulte [notação numérica do UNIX](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) na Wikipédia.

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
    > O comando de montagem acima é montado com SMB 3,0. Se sua distribuição do Linux não oferecer suporte a SMB 3,0 com criptografia ou se ela der suporte apenas ao SMB 2,1, você só poderá montar de uma VM do Azure na mesma região que a conta de armazenamento. Para montar o compartilhamento de arquivos do Azure em uma distribuição do Linux que não dá suporte a SMB 3,0 com criptografia, você precisará [desabilitar a criptografia em trânsito para a conta de armazenamento](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Protegendo o Linux
Para montar um compartilhamento de arquivos do Azure no Linux, a porta 445 deve estar acessível. Muitas organizações optam por bloquear esta porta devido a riscos de segurança inerentes ao SMB 1. O SMB 1, também conhecido como CIFS (Common Internet File System), é um protocolo de sistema de arquivos herdado incluído com muitas distribuições do Linux. O SMB 1 é um protocolo desatualizado, ineficiente e, acima de tudo, inseguro. A boa notícia é que os arquivos do Azure não oferecem suporte a SMB 1 e, a partir do Linux kernel versão 4,18, o Linux possibilita a desabilitação do SMB 1. Sempre é [altamente recomendável](https://aka.ms/stopusingsmb1) desabilitar o SMB 1 em seus clientes Linux antes de usar compartilhamentos de arquivos SMB em produção.

A partir do kernel do Linux 4,18, o módulo kernel SMB, chamado `cifs` por motivos herdados, expõe um novo parâmetro de módulo (muitas vezes conhecido como *Parm* por várias documentações externas), chamado `disable_legacy_dialects`. Embora introduzido no kernel do Linux 4,18, alguns fornecedores têm reportado essa alteração para os kernels mais antigos aos quais dão suporte. Para sua conveniência, a tabela a seguir detalha a disponibilidade desse parâmetro de módulo em distribuições comuns do Linux.

| Distribuição | Pode desabilitar o SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04 – 16.04 | Não |
| Ubuntu 18.04 | Sim |
| Ubuntu 19.04 + | Sim |
| Debian 8-9 | Não |
| Debian 10 + | Sim |
| Fedora 29 + | Sim |
| CentOS 7 | Não | 
| CentOS 8 + | Sim |
| Red Hat Enterprise Linux 6. x-7. x | Não |
| Red Hat Enterprise Linux 8 + | Sim |
| openSUSE Leap 15,0 | Não |
| openSUSE Leap 15.1 + | Sim |
| openSUSE Tumbleweed | Sim |
| SUSE Linux Enterprise 11. x-12. x | Não |
| SUSE Linux Enterprise 15 | Não |
| SUSE Linux Enterprise 15,1 | Não |

Você pode verificar se sua distribuição do Linux dá suporte ao parâmetro do módulo `disable_legacy_dialects` por meio do comando a seguir.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Esse comando deve gerar a seguinte mensagem:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Antes de desabilitar o SMB 1, você deve verificar se o módulo SMB não está carregado atualmente no seu sistema (isso ocorre automaticamente se você tiver montado um compartilhamento SMB). Você pode fazer isso com o comando a seguir, que não deverá gerar nada se o SMB não for carregado:

```bash
lsmod | grep cifs
```

Para descarregar o módulo, primeiro Desmonte todos os compartilhamentos SMB (usando o comando `umount`, conforme descrito acima). Você pode identificar todos os compartilhamentos SMB montados em seu sistema com o seguinte comando:

```bash
mount | grep cifs
```

Depois de desmontar todos os compartilhamentos de arquivos SMB, é seguro descarregar o módulo. Pode fazer isto com o comando `modprobe`:

```bash
sudo modprobe -r cifs
```

Você pode carregar manualmente o módulo com o SMB 1 descarregado usando o comando `modprobe`:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Por fim, você pode verificar se o módulo SMB foi carregado com o parâmetro examinando os parâmetros carregados em `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Para desabilitar o SMB 1 de forma persistente em distribuições baseadas em Ubuntu e Debian, você deve criar um novo arquivo (se você ainda não tiver opções personalizadas para outros módulos) chamado `/etc/modprobe.d/local.conf` com a configuração. Você pode fazer isso com o seguinte comando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Você pode verificar se isso funcionou carregando o módulo SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>Comentários
Usuários do Linux, queremos ouvir você!

O grupo de arquivos do Azure para usuários do Linux fornece um fórum para você compartilhar comentários ao avaliar e adotar o armazenamento de arquivos no Linux. Enviar email para [os usuários do Azure](mailto:azurefileslinuxusers@microsoft.com) para o grupo de usuários.

## <a name="next-steps"></a>Passos seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure:

* [Planning for an Azure Files deployment](storage-files-planning.md) (Planear uma implementação de Ficheiros do Azure)
* [FAQ](../storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
