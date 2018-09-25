---
title: Migrar dados no local para o Armazenamento do Azure com o AzCopy | Microsoft Docs
description: Utilize o AzCopy para migrar ou copiar dados de ou para o blob, a tabela e o conteúdo do ficheiro. Migre facilmente dados do armazenamento local para o Armazenamento do Azure.
services: storage
author: roygara
ms.service: storage
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: bb3df82e66024b49e399ab2f7795a27932f7559e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953978"
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Migrar dados no local para o armazenamento na cloud com o AzCopy

O AzCopy é uma ferramenta de linha de comandos para copiar dados de ou para o armazenamento de Blobs do Azure, Ficheiros do Azure e armazenamento de Tabelas do Azure através de comandos simples. Os comandos foram concebidos para um desempenho ideal. Pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento.  

Pode transferir duas versões do AzCopy:

* O [AzCopy no Linux](storage-use-azcopy-linux.md) inclui o .NET Core Framework. Destina-se às plataformas Linux e tem opções de linha de comandos do estilo POSIX. 
* O [AzCopy no Windows](storage-use-azcopy.md) inclui o .NET Framework. Tem opções de linha de comandos do estilo Windows. 
 
Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento. 
> * Utilizar o AzCopy para carregar todos os seus dados.
> * Modificar os dados para fins de teste.
> * Criar uma tarefa agendada ou tarefa Cron para identificar os novos ficheiros a carregar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, transfira a versão mais recente do AzCopy no [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) ou [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

>[!NOTE]
>Se quiser transferir blobs a partir de uma região secundária para o armazenamento local e vice-versa, defina **Replicação** para **Armazenamento georredundante com acesso de leitura**. A seleção desta opção cria uma conta de [armazenamento georredundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage). 
>
>

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode utilizar contentores para organizar grupos de blobs, como organiza os ficheiros em pastas no seu computador. 

Siga estes passos para criar um contentor:

1. Selecione o botão **Contas de armazenamento** na página principal e selecione a conta de armazenamento que criou.
2. Selecione **Blobs** em **Serviços** e, em seguida, selecione **Contentor**. 

   ![Criar um contentor](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Os nomes de contentores têm de começar com uma letra ou um número. Só podem conter letras, números e o caráter de hífen (-). Para ter acesso a outras regras sobre como atribuir nomes a blobs e contentores, consulte [Atribuir nomes e referenciar contentores, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Carregar todos os ficheiros numa pasta para o armazenamento de Blobs

Pode utilizar o AzCopy para carregar todos os ficheiros numa pasta para o armazenamento de Blobs no [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S
---

Substitua `<key>` e `key` pela sua chave de conta. No portal do Azure, pode obter a chave de conta ao selecionar **Chaves de acesso** em **Definições** na sua conta de armazenamento. Selecione uma chave e cole-a no comando do AzCopy. Se o contentor de destino especificado não existir, o AzCopy cria-o e carrega o ficheiro para o mesmo. Atualize o caminho de origem para o diretório de dados e substitua **myaccount** no URL de destino pelo nome da conta de armazenamento.

Para carregar o conteúdo do diretório especificado para o armazenamento de Blobs recursivamente, especifique a opção `--recursive` (Linux) ou `/S` (Windows). Quando executar o AzCopy com uma destas opções, todas as subpastas e respetivos ficheiros são também carregados.

## <a name="upload-modified-files-to-blob-storage"></a>Carregar ficheiros modificados para o armazenamento de Blobs
Pode utilizar o AzCopy para [carregar ficheiros](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) com base na hora da última modificação. Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Para carregar apenas ficheiros novos ou atualizados, adicione o parâmetro `--exclude-older` (Linux) ou `/XO` (Windows) ao comando do AzCopy.

Se quiser copiar apenas recursos de origem que não existem no destino, especifique os parâmetros `--exclude-older` e `--exclude-newer` (Linux) ou `/XO` e `/XN` (Windows) no comando do AzCopy. O AzCopy carrega apenas os dados atualizados, com base no respetivo carimbo de data/hora.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Criar uma tarefa agendada ou tarefa Cron 
Pode criar uma tarefa agendada ou uma tarefa Cron que executa um script de comandos do AzCopy. O script identifica e carrega os novos dados no local para o armazenamento na cloud num intervalo de tempo específico. 

Copie o comando do AzCopy para um editor de texto. Atualize os valores dos parâmetros do comando do AzCopy para os valores adequados. Guarde o ficheiro como `script.sh` (Linux) ou `script.bat` (Windows) para o AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:<key> /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

O AzCopy é executado com a opção verbosa `--verbose` (Linux) ou `/V` (Windows). O resultado é redirecionado para um ficheiro de registo. 

Neste tutorial, é utilizado o comando [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) para criar uma tarefa agendada no Windows. O comando [Crontab](http://crontab.org/) é utilizado para criar uma tarefa Cron no Linux. 
 **Schtasks** permite a um administrador criar, eliminar, consultar, alterar, executar e terminar tarefas agendadas num computador local ou remoto. **Cron** permite aos utilizadores de Linux e Unix executar comandos ou scripts numa data e hora especificadas através de [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Para criar uma tarefa Cron no Linux, introduza o seguinte comando num terminal: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Especificar a expressão Cron `*/5 * * * * ` no comando indica que o script da shell `script.sh` deve ser executado a cada cinco minutos. Pode agendar o script para ser executado numa hora específica diária, mensal ou anualmente. Para obter mais informações sobre como definir a data e hora de execução da tarefa, veja [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Para criar uma tarefa agendada no Windows, introduza o seguinte comando numa linha de comandos ou no PowerShell:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

O comando utiliza:
- O parâmetro `/SC` para especificar uma agenda de minutos.
- O parâmetro `/MO` para especificar um intervalo de cinco minutos.
- O parâmetro `/TN` para especificar o nome da tarefa.
- O parâmetro `/TR` para especificar o caminho para o ficheiro `script.bat`. 

Para obter mais informações sobre como criar uma tarefa agendada no Windows, veja [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Para validar que a tarefa agendada/tarefa Cron é executada corretamente, crie novos ficheiros no diretório `myfolder`. Aguarde cinco minutos para confirmar que os novos ficheiros foram carregados para a conta de armazenamento. Aceda ao diretório de registos para ver os registos de saída da tarefa agendada ou tarefa Cron. 

Para obter mais informações sobre formas de mover os dados no local para o Armazenamento do Azure e vice-versa, veja [Mover dados de e para o Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, veja os seguintes recursos:

* [Introdução ao Armazenamento do Azure](../storage-introduction.md)
* [Transferir dados com o AzCopy no Windows](storage-use-azcopy.md) 
* [Transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md) 
* [Criar uma conta de armazenamento](../storage-create-storage-account.md)
* [Gerir blobs com o Explorador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

