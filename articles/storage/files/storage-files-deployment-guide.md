---
title: Como implementar ficheiros Azure [ Microsoft Docs
description: Aprenda a implementar Ficheiros Azure do início ao fim.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38339defc9d06f3e809bc24f957ebbb30abb46d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77598787"
---
# <a name="how-to-deploy-azure-files"></a>Como implementar os Ficheiros do Azure
[O Azure Files](storage-files-introduction.md) oferece partilhas de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo Padrão SMB da indústria. Este artigo irá mostrar-lhe como praticamente implantar Ficheiros Azure dentro da sua organização.

Recomendamos vivamente a leitura de Planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md) antes de seguir os passos deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que já completou os seguintes passos:

- Criou uma Conta de Armazenamento Azure com as suas opções de resiliência e encriptação desejadas, na região que deseja. Consulte [criar uma Conta](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) de Armazenamento para direções passo a passo sobre como criar uma Conta de Armazenamento.
- Criou uma partilha de ficheiros Azure com a sua quota desejada na sua Conta de Armazenamento. Consulte Criar uma partilha de [ficheiros](storage-how-to-create-file-share.md) para direções passo a passo sobre como criar uma partilha de ficheiros.

## <a name="transfer-data-into-azure-files"></a>Transferir dados para Ficheiros Azure
Pode desejar migrar as ações de ficheiros existentes, como as armazenadas no local, para a sua nova quota de ficheiros Azure. Esta secção irá mostrar-lhe como mover dados para uma partilha de ficheiros Azure através de vários métodos populares detalhados do guia de [planeamento](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Fá-lo transformando os seus Servidores Windows numa cache rápida da partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

O Azure File Sync pode ser utilizado para migrar dados para uma partilha de ficheiros Azure, mesmo que o mecanismo de sincronização não seja desejado para uso a longo prazo. Mais informações sobre como utilizar o Azure File Sync para transferir dados para a partilha de ficheiros Azure podem ser encontradas no Planeamento de uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md) e como implementar o [Sync de Ficheiros Azure](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Importar/Exportar do Microsoft Azure
O serviço de importação/exportação Azure permite-lhe transferir de forma segura grandes quantidades de dados para uma partilha de ficheiros Azure, enviando discos rígidos para um datacenter Azure. Consulte [o serviço de importação/exportação do Microsoft Azure para transferir dados para](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) o armazenamento do Azure para uma visão geral mais detalhada do serviço.

> [!Note]  
> O serviço de importação/exportação Azure não apoia a exportação de ficheiros de uma parte de ficheiro sinuosa neste momento.

Os seguintes passos importarão dados de um local no local para a sua parte de ficheiros Azure.

1. Procure o número necessário de discos rígidos para enviar para o Azure. Os discos rígidos podem ter qualquer tamanho de disco, mas devem ter uma SSD ou HDD de 2,5" ou 3,5" suportando a norma SATA II ou SATA III. 

2. Ligue e monte cada disco no servidor/PC que efaça a transferência de dados. Para um desempenho ótimo, recomendamos executar o trabalho de exportação no local no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável montar cada disco num PC.

3. Certifique-se de que cada unidade está on-line, inicializada e é atribuída uma letra de unidade. Para fazer uma unidade on-line, inicializar e atribuir uma carta de unidade, abra o snap-in mMC de gestão do disco (diskmgmt.msc).

    - Para colocar um disco online (se ainda não estiver on-line), clique à direita no disco no painel inferior do MMC de Gestão de Discos e selecione "Online".
    - Para inicializar um disco, clique à direita no disco no painel inferior (depois de o disco estar on-line) e selecione "Inicializar". Certifique-se de selecionar "GPT" quando lhe for solicitado.

        ![Uma imagem do menu Disco inicializar no MMC de Gestão de Discos](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Para atribuir uma letra de unidade ao disco, clique à direita no espaço "não atribuído" do disco online e inicializado e clique em "Novo Volume Simples". Isto permitirá atribuir carta de unidade. Note que não precisa de formatar o volume, pois este será feito mais tarde.

        ![Uma imagem do novo assistente de volume simples no MMC de gestão do disco](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Crie o ficheiro CSV conjunto de dados. O ficheiro CSV conjunto de dados é um mapeamento entre o caminho para os dados no local e o ficheiro Azure desejado partilhar os dados para. Por exemplo, o ficheiro CSV do conjunto de dados seguinte mapeia uma parte de ficheiro no local ("F:\shares\scratch") para uma partilha de ficheiros Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Podem ser especificadas várias ações com uma Conta de Armazenamento. Consulte [Preparar o ficheiro CSV do conjunto de dados](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter mais informações.

5. Crie o ficheiro CSV do conjunto de acionamento. O ficheiro CSV do driveset lista os discos disponíveis para o agente de exportação no local. Por exemplo, as seguintes listas `X:` `Y:`de `Z:` ficheiros CSV de driveset, e unidades a utilizar no local de exportação:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Consulte [Preparar o ficheiro CSV do conjunto](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) de acionamento para obter mais informações.

6. Utilize a [Ferramenta WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) para copiar os seus dados para um ou mais discos rígidos.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Não modifique os dados das unidades de disco rígido ou do ficheiro do diário após completar a preparação do disco.

7. [Criar um trabalho de importação.](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job)
    
### <a name="robocopy"></a>Robocopy
A Robocopy é uma ferramenta de cópia bem conhecida que envia com Windows e Windows Server. A robocópia pode ser usada para transferir dados para Ficheiros Azure, montando a parte do ficheiro localmente e, em seguida, usando a localização montada como destino no comando Robocopy. Usar robocópia é muito simples:

1. [Monte a sua parte de ficheiro Azure.](storage-how-to-use-files-windows.md) Para um desempenho ótimo, recomendamos a montagem da partilha de ficheiros Azure localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável montar a parte do ficheiro Azure num PC. Neste exemplo, `net use` é utilizado na linha de comando para montar a partilha de ficheiros:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Utilize `robocopy` na linha de comando para mover dados para a partilha de ficheiros Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    A robocópia tem um número significativo de opções para modificar o comportamento da cópia conforme desejado. Para mais informações, consulte a página manual da [Robocopy.](https://technet.microsoft.com/library/cc733145.aspx)

### <a name="azcopy"></a>AzCopy
O AzCopy é um utilitário de linha de comando projetado para copiar dados de e para o Azure Files, bem como armazenamento Azure Blob, utilizando comandos simples com um desempenho ideal. Usar o AzCopy é fácil:

1. Descarregue a [versão mais recente do AzCopy no Windows](https://aka.ms/downloadazcopy) ou [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Utilize `azcopy` na linha de comando para mover dados para a partilha de ficheiros Azure. A sintaxe no Windows é a seguinte: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Em Linux, a sintaxe de comando é um pouco diferente:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    A AzCopy tem um número significativo de opções para modificar o comportamento da cópia conforme desejado. Para mais informações, consulte [a AzCopy no Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e a [AzCopy no Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Monte automaticamente em PCs/Servidores necessários
Para substituir uma parte de ficheiro no local, é útil pré-montar as ações nas máquinas em que será utilizada. Isto pode ser feito automaticamente numa lista de máquinas.

> [!Note]  
> A montagem de uma partilha de ficheiros Azure requer a utilização da chave da conta de armazenamento como senha, pelo que apenas recomendamos a montagem em ambientes fidedignos. 

### <a name="windows"></a>Windows
PowerShell pode ser usado executar o comando de montagem em vários Computadores. No exemplo seguinte, `$computers` é povoado manualmente, mas pode gerar a lista de computadores para montar automaticamente. Por exemplo, pode povoar esta variável com resultados do Diretório Ativo.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Um simples guião de bash combinado com SSH pode produzir o mesmo resultado no exemplo seguinte. A `$computer` variável é igualmente deixada para ser povoada pelo utilizador:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Passos seguintes
- [Plano para uma implementação de Sincronização de Ficheiros Azure](storage-sync-files-planning.md)
- [Resolução de problemas dos Ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Resolução de problemas dos Ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
