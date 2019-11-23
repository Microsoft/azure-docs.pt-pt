---
title: 'Tutorial: Migrate on-premises data to Azure Storage with AzCopy| Microsoft Docs'
description: Neste tutorial, utiliza o AzCopy para migrar ou copiar dados de ou para o blob, a tabela e o conteúdo do ficheiro. Migre facilmente dados do armazenamento local para o Armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f7155053072b3533503765dc6f4fbf185d21f0d4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327524"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: Migrate on-premises data to cloud storage with AzCopy

O AzCopy é uma ferramenta de linha de comandos para copiar dados de ou para o armazenamento de Blobs do Azure, Ficheiros do Azure e armazenamento de Tabelas do Azure através de comandos simples. Os comandos foram concebidos para um desempenho ideal. Com o AzCopy, pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento. O AzCopy pode ser utilizado para copiar dados (no local) do local para uma conta de armazenamento.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento. 
> * Utilizar o AzCopy para carregar todos os seus dados.
> * Modificar os dados para fins de teste.
> * Criar uma tarefa agendada ou tarefa Cron para identificar os novos ficheiros a carregar.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

To complete this tutorial, download the latest version of AzCopy. See [Get started with AzCopy](storage-use-azcopy-v10.md).

Se estiver no Windows, irá precisar de [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), uma vez que este tutorial utiliza-o para agendar uma tarefa. Ao invés, os utilizadores do Linux utilizam o comando crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Criar um contentor

O primeiro passo é criar um contentor, porque os blobs têm de ser sempre carregados para um contentor. Os contentores são utilizados como um método de organizar grupos de blobs, como faria com ficheiros no seu computador, em pastas.

Siga estes passos para criar um contentor:

1. Selecione o botão **Contas de armazenamento** na página principal e selecione a conta de armazenamento que criou.
2. Selecione **Blobs** em **Serviços** e, em seguida, selecione **Contentor**.

   ![Criar um contentor](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Os nomes de contentores têm de começar com uma letra ou um número. Só podem conter letras, números e o caráter de hífen (-). Para ter acesso a outras regras sobre como atribuir nomes a blobs e contentores, consulte [Atribuir nomes e referenciar contentores, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Download AzCopy

Download the AzCopy V10 executable file.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Place the AzCopy file anywhere on your computer. Add the location of the file to your system path variable so that you can refer to this executable file from any folder on your computer.

## <a name="authenticate-with-azure-ad"></a>Autenticar com o Azure AD

First, assign the [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) role to your identity. See [Grant access to Azure blob and queue data with RBAC in the Azure portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Then, open a command prompt, type the following command, and press the ENTER key.

```azcopy
azcopy login
```

This command returns an authentication code and the URL of a website. Open the website, provide the code, and then choose the **Next** button.

![Criar um contentor](media/storage-use-azcopy-v10/azcopy-login.png)

A sign-in window will appear. In that window, sign into your Azure account by using your Azure account credentials. After you've successfully signed in, you can close the browser window and begin using AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Carregar conteúdos de uma pasta para o armazenamento de Blobs

Pode utilizar o AzCopy para carregar todos os ficheiros numa pasta para o armazenamento de Blobs no [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux). Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`).

* Replace the `<storage-account-name>` placeholder with the name of your storage account.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To upload the contents of the specified directory to Blob storage recursively, specify the `--recursive` option. When you run AzCopy with this option, all subfolders and their files are uploaded as well.

## <a name="upload-modified-files-to-blob-storage"></a>Carregar ficheiros modificados para o armazenamento de Blobs

You can use AzCopy to upload files based on their last-modified time. 

Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Then, use the AzCopy `sync` command.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Replace the `<local-folder-path>` placeholder with the path to a folder that contains files (For example: `C:\myFolder` or `/mnt/myFolder`.

* Replace the `<storage-account-name>` placeholder with the name of your storage account.

* Replace the `<container-name>` placeholder with the name of the container that you created.

To learn more about the `sync` command, see [Synchronize files](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Criar uma tarefa agendada

Pode criar uma tarefa agendada ou uma tarefa Cron que executa um script de comandos do AzCopy. O script identifica e carrega os novos dados no local para o armazenamento na cloud num intervalo de tempo específico.

Copie o comando do AzCopy para um editor de texto. Atualize os valores dos parâmetros do comando do AzCopy para os valores adequados. Guarde o ficheiro como `script.sh` (Linux) ou `script.bat` (Windows) para o AzCopy. 

These examples assume that your folder is named `myFolder`, your storage account name is `mystorageaccount` and your container name is `mycontainer`.

> [!NOTE]
> The Linux example appends a SAS token. You'll need to provide one in your command. The current version of AzCopy V10 doesn't support Azure AD authorization in cron jobs.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

    azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

    azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true

---

Neste tutorial, é utilizado o comando [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) para criar uma tarefa agendada no Windows. O comando [Crontab](http://crontab.org/) é utilizado para criar uma tarefa Cron no Linux.

 **Schtasks** permite a um administrador criar, eliminar, consultar, alterar, executar e terminar tarefas agendadas num computador local ou remoto. **Cron** permite aos utilizadores de Linux e Unix executar comandos ou scripts numa data e hora especificadas através de [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Para criar uma tarefa Cron no Linux, introduza o seguinte comando num terminal:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Especificar a expressão Cron `*/5 * * * *` no comando indica que o script da shell `script.sh` deve ser executado a cada cinco minutos. Pode agendar o script para ser executado numa hora específica diária, mensal ou anualmente. Para obter mais informações sobre como definir a data e hora de execução da tarefa, veja [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Para criar uma tarefa agendada no Windows, introduza o seguinte comando numa linha de comandos ou no PowerShell:

This example assumes that your script is located in the root drive of your computer, but your script can be anywhere that you want.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

O comando utiliza:
- O parâmetro `/SC` para especificar uma agenda de minutos.
- O parâmetro `/MO` para especificar um intervalo de cinco minutos.
- O parâmetro `/TN` para especificar o nome da tarefa.
- O parâmetro `/TR` para especificar o caminho para o ficheiro `script.bat`.

Para obter mais informações sobre como criar uma tarefa agendada no Windows, veja [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---

Para validar que a tarefa agendada/tarefa Cron é executada corretamente, crie novos ficheiros no diretório `myFolder`. Aguarde cinco minutos para confirmar que os novos ficheiros foram carregados para a conta de armazenamento. Aceda ao diretório de registos para ver os registos de saída da tarefa agendada ou tarefa Cron.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre formas de mover os dados no local para o Armazenamento do Azure e vice-versa, siga esta ligação:

* [Mover dados de e para o Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

For more information about AzCopy, see any of these articles:

* [Get started with AzCopy](storage-use-azcopy-v10.md)

* [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md)

* [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)

* [Transfer data with AzCopy and Amazon S3 buckets](storage-use-azcopy-s3.md)
 
* [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)
