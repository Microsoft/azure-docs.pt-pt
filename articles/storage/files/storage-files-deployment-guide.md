---
title: Como implementar o serviço ficheiros do Azure | Documentos da Microsoft
description: Saiba como implementar o serviço ficheiros do Azure do início ao fim.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 416040d91da2f6346027f738de507777f804fd1f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237736"
---
# <a name="how-to-deploy-azure-files"></a>Como implementar os Ficheiros do Azure
[Os ficheiros do Azure](storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo SMB padrão do setor. Este artigo irá mostrar como implementar praticamente ficheiros do Azure na sua organização.

Recomendamos vivamente a leitura [planear uma implementação de ficheiros do Azure](storage-files-planning.md) antes de seguir os passos neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já tenha concluído os passos seguintes:

- Criar uma conta de armazenamento do Azure com as opções de resiliência e encriptação pretendidas, na região desejado. Ver [criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para instruções passo a passo sobre como criar uma conta de armazenamento.
- Criou uma partilha de ficheiros do Azure com a sua quota pretendida na sua conta de armazenamento. Ver [criar uma partilha de ficheiros](storage-how-to-create-file-share.md) para instruções passo a passo sobre como criar uma partilha de ficheiros.

## <a name="transfer-data-into-azure-files"></a>Transferir dados para ficheiros do Azure
Pode pretender migrar as partilhas de ficheiros existentes, como esses armazenados no local, para a nova partilha de ficheiros do Azure. Esta seção mostrará a como mover dados para uma partilha de ficheiros do Azure através de vários métodos populares detalhados do [guia de planeamento](storage-files-planning.md#data-transfer-method)

### <a name="azure-file-sync"></a>Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Ele faz isso ao transformar os seus servidores do Windows numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

O Azure File Sync pode ser utilizado para migrar dados para uma partilha de ficheiros do Azure, mesmo que o mecanismo de sincronização não é pretendido para utilização a longo prazo. Obter mais informações sobre como utilizar o Azure File Sync para transferir dados para a partilha de ficheiros do Azure podem ser encontradas na [planear uma implementação do Azure File Sync](storage-sync-files-planning.md) e [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Importar/exportar do Azure
O serviço importar/exportar do Azure permite-lhe transferir de forma segura grandes quantidades de dados para uma partilha de ficheiros do Azure envie unidades de disco rígido num Datacenter do Azure. Ver [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter uma descrição mais detalhada do serviço.

> [!Note]  
> O serviço importar/exportar do Azure não suporta a exportação de ficheiros a partir de uma partilha de ficheiros do Azure neste momento.

Os passos seguintes irão importar dados de uma localização no local para a partilha de ficheiros do Azure.

1. Obtenha o número de discos rígidos para correio necessários para o Azure. Discos rígidos pode ser de qualquer tamanho de disco, mas tem de ser de qualquer um 2,5" ou 3,5" SSD ou HDD SATA II ou SATA III padrão de suporte. 

2. Connect and mount e cada disco no servidor/PC ao fazer a transferência de dados. Para otimizar o desempenho, recomendamos executar a tarefa de exportação locais localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável para montar a cada disco num PC.

3. Certifique-se de que cada unidade está online, inicializado e é atribuída uma letra de unidade. Para colocar uma unidade online, inicializar e atribuir uma letra de unidade, abra o snap-in MMC de gestão de discos (Diskmgmt. msc).

    - Para colocar um disco online (se ainda não estiver online), com o botão direito no disco no painel inferior da MMC de gestão do disco e selecione "Online".
    - Para inicializar um disco, clique com botão direito no disco no painel (depois do disco está online) mais baixo e selecione "Initialize". Certifique-se de que selecione "GPT" quando lhe for pedido.

        ![Uma captura de ecrã do menu Inicializar disco na MMC de gestão de disco](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Para atribuir uma letra de unidade ao disco, clique com o botão direito no espaço de "não atribuído" do disco online e ser inicializado e clique em "Novo Volume simples". Isso permitirá que atribua a letra de unidade. Tenha em atenção que não é necessário para formatar o volume, como isso será feito mais tarde.

        ![Uma captura de ecrã do Assistente Novo Volume simples na MMC de gestão de disco](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Crie o ficheiro CSV de conjunto de dados. O ficheiro CSV de conjunto de dados é um mapeamento entre o caminho para os dados no local e a partilha de ficheiros do Azure que quer os dados deve ser copiada para. Por exemplo, o seguinte ficheiro CSV de conjunto de dados mapeia uma partilha de ficheiros no local ("F:\shares\scratch") para uma partilha de ficheiros do Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Múltiplas partilhas com uma conta de armazenamento podem ser especificadas. Ver [preparar o arquivo do conjunto de dados CSV](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file) para obter mais informações.

5. Crie o ficheiro CSV de driveset. O ficheiro CSV de driveset apresenta os discos disponíveis para o agente de exportação no local. Por exemplo, o seguinte driveset CSV ficheiro listas `X:`, `Y:`, e `Z:` unidades a ser utilizado no local exportar tarefa:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Ver [preparar o ficheiro CSV de driveset](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file) para obter mais informações.

6. Utilize o [WAImportExport ferramenta](https://www.microsoft.com/download/details.aspx?id=55280) para copiar os dados para um ou mais unidades de disco rígido.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Não modifique os dados em unidades de disco rígido ou o ficheiro de diário depois de concluir a preparação do disco.

7. [Criar uma tarefa de importação](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
O Robocopy é uma ferramenta de cópia bem conhecido que acompanha o Windows e Windows Server. Robocopy pode ser utilizado para transferir dados para ficheiros do Azure ao montar a partilha de ficheiros localmente e, em seguida, utilizar a localização de montado como o destino no comando Robocopy. Utilizar o Robocopy é bastante simples:

1. [Montar a partilha de ficheiros](storage-how-to-use-files-windows.md). Para otimizar o desempenho, recomendamos que montar a partilha de ficheiros do Azure localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável para montar a partilha de ficheiros do Azure num PC. Neste exemplo, `net use` é usado na linha de comandos para montar a partilha de ficheiros:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Utilize `robocopy` na linha de comandos para mover dados para a partilha de ficheiros do Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy tem um número significativo de opções para modificar o comportamento de cópia conforme pretendido. Para obter mais informações, consulte a [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) página manual.

### <a name="azcopy"></a>AzCopy
O AzCopy é um utilitário de linha de comandos concebido para copiar dados de e para ficheiros do Azure, bem como armazenamento de Blobs do Azure, utilizando os comandos simples com um desempenho ideal. Com o AzCopy é fácil:

1. Transfira o [versão mais recente do AzCopy no Windows](https://aka.ms/downloadazcopy) ou [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Utilize `azcopy` na linha de comandos para mover dados para a partilha de ficheiros do Azure. A sintaxe no Windows é o seguinte: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    No Linux, a sintaxe de comando é um pouco diferente:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    O AzCopy tem um número significativo de opções para modificar o comportamento de cópia conforme pretendido. Para obter mais informações, veja [AzCopy no Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [AzCopy no Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Montar automaticamente nos PCs/servidores necessários
Para substituir uma partilha de ficheiros no local, é útil para previamente montar as partilhas nas máquinas que será usado. Isso pode ser feito automaticamente numa lista de máquinas.

> [!Note]  
> Montar uma partilha de ficheiros do Azure requer a utilização da chave de conta de armazenamento como a palavra-passe, por isso só Recomendamos montar em ambientes confiáveis. 

### <a name="windows"></a>Windows
PowerShell pode ser utilizado, execute o comando de montagem em vários PCs. No exemplo a seguir, `$computers` manualmente é preenchido, mas pode gerar a lista de computadores para montar automaticamente. Por exemplo, pode preencher essa variável com resultados do Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Um script de bash simples combinado com o SSH pode ter o mesmo resultado no exemplo a seguir. O `$computer` variável da mesma forma é deixada para ser preenchido pelo utilizador:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Passos Seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Resolver problemas de ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Resolver problemas de ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
