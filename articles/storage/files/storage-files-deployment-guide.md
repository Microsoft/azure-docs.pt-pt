---
title: Como implantar arquivos do Azure | Microsoft Docs
description: Saiba como implantar arquivos do Azure do início ao fim.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 802b4deb91f1df784ac0aed2952d3f915b54ce73
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699706"
---
# <a name="how-to-deploy-azure-files"></a>Como implementar os Ficheiros do Azure
[Os ficheiros do Azure](storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo SMB padrão do setor. Este artigo mostrará como implantar de forma prática os arquivos do Azure em sua organização.

É altamente recomendável ler o [planejamento de uma implantação de arquivos do Azure](storage-files-planning.md) antes de seguir as etapas neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já tenha concluído as seguintes etapas:

- Criou uma conta de armazenamento do Azure com as opções de resiliência e criptografia desejadas na região desejada. Consulte [criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter instruções passo a passo sobre como criar uma conta de armazenamento.
- Criou um compartilhamento de arquivos do Azure com a cota desejada em sua conta de armazenamento. Consulte [criar um compartilhamento de arquivos](storage-how-to-create-file-share.md) para obter instruções passo a passo sobre como criar um compartilhamento de arquivos.

## <a name="transfer-data-into-azure-files"></a>Transferir dados para arquivos do Azure
Talvez você queira migrar compartilhamentos de arquivos existentes, como aqueles armazenados localmente, para seu novo compartilhamento de arquivos do Azure. Esta seção mostrará como mover dados para um compartilhamento de arquivos do Azure por meio de vários métodos populares detalhados no [Guia de planejamento](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync"></a>Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Ele faz isso transformando os servidores do Windows em um cache rápido do compartilhamento de arquivos do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Sincronização de Arquivos do Azure pode ser usado para migrar dados para um compartilhamento de arquivos do Azure, mesmo que o mecanismo de sincronização não seja desejado para uso de longo prazo. Mais informações sobre como usar Sincronização de Arquivos do Azure para transferir dados para o compartilhamento de arquivos do Azure podem ser encontradas no [planejamento de uma implantação de sincronização de arquivos do Azure](storage-sync-files-planning.md) e [como implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Importação/exportação do Azure
O serviço de importação/exportação do Azure permite que você transfira com segurança grandes quantidades de dados em um compartilhamento de arquivos do Azure enviando unidades de disco rígido para um datacenter do Azure. Consulte [usar o serviço de importação/exportação Microsoft Azure para transferir dados para o armazenamento do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter uma visão geral mais detalhada do serviço.

> [!Note]  
> O serviço de importação/exportação do Azure não oferece suporte à exportação de arquivos de um compartilhamento de arquivos do Azure neste momento.

As etapas a seguir importarão dados de um local para o compartilhamento de arquivos do Azure.

1. Adquira o número necessário de discos rígidos para email no Azure. Os discos rígidos podem ser de qualquer tamanho de disco, mas devem ser uma SSD de 2,5 "ou 3,5" ou HDD com suporte para o padrão SATA II ou SATA III. 

2. Conecte e monte cada disco no servidor/computador fazendo a transferência de dados. Para obter um desempenho ideal, é recomendável executar o trabalho de exportação local localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de arquivos que atende os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável montar cada disco em um PC.

3. Verifique se cada unidade está online, inicializada e se é atribuída uma letra de unidade. Para colocar uma unidade online, inicializar e atribuir uma letra da unidade, abra o snap-in do MMC de gerenciamento de disco (diskmgmt. msc).

    - Para colocar um disco online (se ele ainda não estiver online), clique com o botão direito do mouse no disco no painel inferior do MMC de gerenciamento de disco e selecione "online".
    - Para inicializar um disco, clique com o botão direito do mouse no disco no painel inferior (depois que o disco estiver online) e selecione "inicializar". Certifique-se de selecionar "GPT" quando solicitado.

        ![Uma captura de tela do menu inicializar disco no MMC de gerenciamento de disco](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Para atribuir uma letra de unidade ao disco, clique com o botão direito do mouse no espaço "não alocado" do disco online e inicializado e clique em "novo volume simples". Isso permitirá que você atribua a letra da unidade. Observe que você não precisa formatar o volume, pois isso será feito posteriormente.

        ![Uma captura de tela do assistente de novo volume simples no MMC de gerenciamento de disco](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Crie o arquivo CSV de conjunto de arquivos. O arquivo CSV de DataSet é um mapeamento entre o caminho para os dados locais e o compartilhamento de arquivos do Azure desejado para o qual os dados devem ser copiados. Por exemplo, o seguinte arquivo CSV de conjunto de arquivos mapeia um compartilhamento de arquivos local ("F:\shares\scratch") para um compartilhamento de arquivos do Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Vários compartilhamentos com uma conta de armazenamento podem ser especificados. Consulte [preparar o arquivo CSV do conjunto](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) de dados para obter mais informações.

5. Crie o arquivo CSV do driveset. O arquivo CSV do driveset lista os discos disponíveis para o agente de exportação local. Por exemplo, as seguintes listas `X:`de arquivos CSV do driveset, `Z:` `Y:`e unidades a serem usadas no trabalho de exportação local:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Consulte [preparar o arquivo CSV](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) do driveset para obter mais informações.

6. Use a [ferramenta WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) para copiar seus dados para um ou mais discos rígidos.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Não modifique os dados nas unidades de disco rígido ou no arquivo de diário depois de concluir a preparação do disco.

7. [Crie um trabalho de importação](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy é uma ferramenta de cópia bem conhecida que é fornecida com o Windows e o Windows Server. O Robocopy pode ser usado para transferir dados para arquivos do Azure, montando o compartilhamento de arquivos localmente e, em seguida, usando o local montado como o destino no comando Robocopy. Usar o Robocopy é bem simples:

1. [Monte o compartilhamento de arquivos do Azure](storage-how-to-use-files-windows.md). Para obter um desempenho ideal, é recomendável montar o compartilhamento de arquivos do Azure localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de arquivos que atende os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável montar o compartilhamento de arquivos do Azure em um PC. Neste exemplo, `net use` é usado na linha de comando para montar o compartilhamento de arquivos:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Use `robocopy` na linha de comando para mover dados para o compartilhamento de arquivos do Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    O Robocopy tem um número significativo de opções para modificar o comportamento de cópia conforme desejado. Para obter mais informações, veja a página manual do [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) .

### <a name="azcopy"></a>AzCopy
AzCopy é um utilitário de linha de comando projetado para copiar dados de e para arquivos do Azure, bem como o armazenamento de BLOBs do Azure, usando comandos simples com desempenho ideal. Usar o AzCopy é fácil:

1. Baixe a [versão mais recente do AzCopy no Windows](https://aka.ms/downloadazcopy) ou [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Use `azcopy` na linha de comando para mover dados para o compartilhamento de arquivos do Azure. A sintaxe no Windows é a seguinte: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    No Linux, a sintaxe do comando é um pouco diferente:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    O AzCopy tem um número significativo de opções para modificar o comportamento de cópia conforme desejado. Para obter mais informações, veja [AzCopy no Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [AzCopy no Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montar automaticamente em PCs/servidores necessários
Para substituir um compartilhamento de arquivos local, é útil pré-configurar os compartilhamentos nos computadores em que eles serão usados. Isso pode ser feito automaticamente em uma lista de computadores.

> [!Note]  
> A montagem de um compartilhamento de arquivos do Azure requer o uso da chave da conta de armazenamento como a senha, portanto, recomendamos apenas a montagem em ambientes confiáveis. 

### <a name="windows"></a>Windows
O PowerShell pode ser usado para executar o comando mount em vários PCs. No exemplo a seguir, `$computers` é populado manualmente, mas você pode gerar a lista de computadores a serem montados automaticamente. Por exemplo, você pode popular essa variável com resultados de Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Um script bash simples combinado com o SSH pode produzir o mesmo resultado no exemplo a seguir. A `$computer` variável é, da mesma forma, deixada para ser populada pelo usuário:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Passos Seguintes
- [Planejar uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Solucionar problemas de arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Solucionar problemas de arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
