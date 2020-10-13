---
title: Como implementar ficheiros Azure Microsoft Docs
description: Saiba como implementar ficheiros Azure do início ao fim. Transfira dados para ficheiros Azure. Monte automaticamente em computadores ou servidores necessários.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 08bcb41dd8d9f4643b03d855960d8632b778ff84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88034503"
---
# <a name="how-to-deploy-azure-files"></a>Como implementar os Ficheiros do Azure
[O Azure Files](storage-files-introduction.md) oferece ações de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo SMB padrão da indústria. Este artigo irá mostrar-lhe como implementar praticamente ficheiros Azure dentro da sua organização.

Recomendamos vivamente a leitura [de Planeamento para uma implementação de Ficheiros Azure](storage-files-planning.md) antes de seguir os passos deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já completou os seguintes passos:

- Criei uma Conta de Armazenamento Azure com as opções de resiliência e encriptação desejadas, na região que deseja. Consulte [criar uma conta](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) de armazenamento para obter instruções passo a passo sobre como criar uma Conta de Armazenamento.
- Criei uma partilha de ficheiros Azure com a quota desejada na sua Conta de Armazenamento. Consulte Criar uma partilha de [ficheiros](storage-how-to-create-file-share.md) para obter instruções passo a passo sobre como criar uma partilha de ficheiros.

## <a name="transfer-data-into-azure-files"></a>Transferir dados para ficheiros Azure
Pode desejar migrar as ações de ficheiros existentes, como as armazenadas no local, para a sua nova partilha de ficheiros Azure. Esta secção irá mostrar-lhe como mover dados para uma partilha de ficheiros Azure através de vários métodos populares detalhados do guia de [planeamento](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Fá-lo transformando os seus Servidores Windows numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

O Azure File Sync pode ser usado para migrar dados para uma partilha de ficheiros Azure, mesmo que o mecanismo de sincronização não seja desejado para uso a longo prazo. Mais informações sobre como utilizar o Azure File Sync para transferir dados para a partilha de ficheiros Azure podem ser encontradas no [Planeamento de uma implementação de Azure File Sync](storage-sync-files-planning.md) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Importar/Exportar do Microsoft Azure
O serviço Azure Import/Export permite transferir de forma segura grandes quantidades de dados para uma partilha de ficheiros Azure, enviando discos rígidos para um datacenter Azure. Consulte [o serviço Microsoft Azure Import/Export para transferir dados para o armazenamento da Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para uma visão geral mais detalhada do serviço.

> [!Note]  
> O serviço Azure Import/Export não apoia a exportação de ficheiros de uma parte de ficheiros Azure neste momento.

Os seguintes passos importarão dados de um local no local para a sua parte de ficheiro Azure.

1. Procure o número necessário de discos rígidos para enviar para Azure. Os discos rígidos podem ser de qualquer tamanho de disco, mas devem ser um SSD de 2,5" ou 3,5" ou HDD suportando a norma SATA II ou SATA III. 

2. Conecte e monte cada disco no servidor/PC fazendo a transferência de dados. Para um melhor desempenho, recomendamos executar o trabalho de exportação no local local no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável montar cada disco num PC.

3. Certifique-se de que cada unidade está on-line, inicializada e é atribuída uma letra de unidade. Para fazer uma unidade on-line, inicializar e atribuir uma letra de unidade, abra o snap-in MMC de gestão de disco (diskmgmt.msc).

    - Para colocar um disco on-line (se ainda não estiver online), clique com o botão direito no painel inferior do MMC de Gestão de Discos e selecione "Online".
    - Para rubricar um disco, clique com o botão direito no disco no painel inferior (depois de o disco estar online) e selecione "Initialize". Certifique-se de selecionar "GPT" quando solicitado.

        ![Uma imagem do menu Do Disco Inicialize no MMC de Gestão de Discos](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Para atribuir uma letra de unidade ao disco, clique com o botão direito no espaço "não atribuído" do disco online e inicializado, e clique em "Novo Volume Simples". Isto permitir-lhe-á atribuir a carta de condução. Note que não precisa de formatar o volume, uma vez que este será feito mais tarde.

        ![Uma imagem do novo assistente de volume simples no MMC de gestão de discos](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Crie o ficheiro CSV do conjunto de dados. O ficheiro CSV do conjunto de dados é um mapeamento entre o caminho para os dados no local e a partilha de ficheiros Azure desejado para a qual os dados devem ser copiados. Por exemplo, o seguinte conjunto de dados CSV mapeia uma partilha de ficheiros no local ("F:\shares\scratch") para uma partilha de ficheiros Azure ("MyAzureFileShare"):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Podem ser especificadas várias ações com uma Conta de Armazenamento. Consulte [preparar o ficheiro CSV do conjunto de dados](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter mais informações.

5. Crie o ficheiro CSV do driveset. O ficheiro CSV driveset lista os discos disponíveis para o agente de exportação no local. Por exemplo, as seguintes listas de ficheiros de driveset CSV, `X:` `Y:` e `Z:` unidades a utilizar no local de exportação:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Consulte [preparar o ficheiro CSV do conjunto de acionamento](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter mais informações.

6. Utilize a [Ferramenta WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) para copiar os seus dados para um ou mais discos rígidos.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Não modifique os dados nas unidades de disco rígido ou no ficheiro do diário após completar a preparação do disco.

7. [Criar um trabalho de importação.](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job)
    
### <a name="robocopy"></a>Robocopy
Robocopy é uma ferramenta de cópia bem conhecida que envia com Windows e Windows Server. A Robocopia pode ser usada para transferir dados para ficheiros Azure, montando a partilha de ficheiros localmente e, em seguida, utilizando a localização montada como destino no comando Robocopy. Usar robocopia é muito simples:

1. [Monte a sua partilha de ficheiros Azure.](storage-how-to-use-files-windows.md) Para um melhor desempenho, recomendamos a montagem da partilha de ficheiros Azure localmente no servidor que contém os dados. Em alguns casos, como quando o servidor de ficheiros que serve os dados é um dispositivo NAS, isso pode não ser possível. Nesse caso, é perfeitamente aceitável montar a partilha de ficheiros Azure num PC. Neste exemplo, `net use` é usado na linha de comando para montar a partilha de ficheiros:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Utilize `robocopy` na linha de comando para mover dados para a partilha de ficheiros Azure:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopia tem um número significativo de opções para modificar o comportamento da cópia conforme desejado. Para mais informações, consulte a página manual da [Robocopia.](https://technet.microsoft.com/library/cc733145.aspx)

### <a name="azcopy"></a>AzCopy
AzCopy é um utilitário de linha de comando projetado para copiar dados de e para Azure Files, bem como armazenamento Azure Blob, usando comandos simples com o melhor desempenho. Usar a AzCopy é fácil:

1. Descarregue a [versão mais recente do AzCopy no Windows](https://aka.ms/downloadazcopy) ou [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy).
2. Utilize `azcopy` na linha de comando para mover dados para a partilha de ficheiros Azure. A sintaxe no Windows é a seguinte: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Em Linux, a sintaxe de comando é um pouco diferente:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    A AzCopy tem um número significativo de opções para modificar o comportamento da cópia conforme desejado. Para mais informações, consulte [a AzCopy no Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [AzCopy no Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Monte automaticamente em PCs/Servidores necessários
Para substituir uma partilha de ficheiros no local, é útil pré-montar as ações nas máquinas em que serão utilizadas. Isto pode ser feito automaticamente numa lista de máquinas.

> [!Note]  
> A montagem de uma partilha de ficheiros Azure requer a utilização da chave da conta de armazenamento como palavra-passe, pelo que recomendamos apenas a montagem em ambientes fidedignos. 

### <a name="windows"></a>Windows
PowerShell pode ser utilizado executar o comando de montagem em vários computadores. No exemplo seguinte, `$computers` é preenchido manualmente, mas pode gerar a lista de computadores para montar automaticamente. Por exemplo, pode povoar esta variável com resultados do Ative Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Um simples guião de bash combinado com SSH pode produzir o mesmo resultado no seguinte exemplo. A `$computer` variável é igualmente deixada para ser povoada pelo utilizador:

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
