---
title: 'Tutorial: migrar dados locais para o armazenamento do Azure com o AzCopy | Microsoft Docs'
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
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: migrar dados locais para o armazenamento em nuvem com o AzCopy

O AzCopy é uma ferramenta de linha de comandos para copiar dados de ou para o armazenamento de Blobs do Azure, Ficheiros do Azure e armazenamento de Tabelas do Azure através de comandos simples. Os comandos foram concebidos para um desempenho ideal. Com o AzCopy, pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento. O AzCopy pode ser utilizado para copiar dados (no local) do local para uma conta de armazenamento.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento. 
> * Utilizar o AzCopy para carregar todos os seus dados.
> * Modificar os dados para fins de teste.
> * Criar uma tarefa agendada ou tarefa Cron para identificar os novos ficheiros a carregar.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, baixe a versão mais recente do AzCopy. Consulte Introdução [ao AzCopy](storage-use-azcopy-v10.md).

Se estiver no Windows, irá precisar de [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx), uma vez que este tutorial utiliza-o para agendar uma tarefa. Ao invés, os utilizadores do Linux utilizam o comando crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Criar um contentor

O primeiro passo é criar um contentor, porque os blobs têm de ser sempre carregados para um contentor. Os contentores são utilizados como um método de organizar grupos de blobs, como faria com ficheiros no seu computador, em pastas.

Siga estes passos para criar um contentor:

1. Selecione o botão **Contas de armazenamento** na página principal e selecione a conta de armazenamento que criou.
2. Selecione **Blobs** em **Serviços** e, em seguida, selecione **Contentor**.

   ![Criar um contentor](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Os nomes de contentores têm de começar com uma letra ou um número. Só podem conter letras, números e o caráter de hífen (-). Para ter acesso a outras regras sobre como atribuir nomes a blobs e contentores, consulte [Atribuir nomes e referenciar contentores, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Baixar o AzCopy

Baixe o arquivo executável AzCopy v10.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Coloque o arquivo AzCopy em qualquer lugar no computador. Adicione o local do arquivo à variável de caminho do sistema para que você possa se referir a esse arquivo executável de qualquer pasta no seu computador.

## <a name="authenticate-with-azure-ad"></a>Autenticar com o Azure AD

Primeiro, atribua a função de [colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) à sua identidade. Consulte [conceder acesso ao blob do Azure e aos dados da fila com RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal).

Em seguida, abra um prompt de comando, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login
```

Esse comando retorna um código de autenticação e a URL de um site. Abra o site, forneça o código e, em seguida, escolha o botão **Avançar** .

![Criar um contentor](media/storage-use-azcopy-v10/azcopy-login.png)

Uma janela de entrada será exibida. Nessa janela, entre em sua conta do Azure usando suas credenciais de conta do Azure. Depois de entrar com êxito, você pode fechar a janela do navegador e começar a usar o AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Carregar conteúdos de uma pasta para o armazenamento de Blobs

Pode utilizar o AzCopy para carregar todos os ficheiros numa pasta para o armazenamento de Blobs no [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux). Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Substitua o espaço reservado `<local-folder-path>` pelo caminho para uma pasta que contém arquivos (por exemplo: `C:\myFolder` ou `/mnt/myFolder`).

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o espaço reservado `<container-name>` pelo nome do contêiner que você criou.

Para carregar o conteúdo do diretório especificado para o armazenamento de BLOBs recursivamente, especifique a opção `--recursive`. Quando você executa o AzCopy com essa opção, todas as subpastas e seus arquivos também são carregados.

## <a name="upload-modified-files-to-blob-storage"></a>Carregar ficheiros modificados para o armazenamento de Blobs

Você pode usar o AzCopy para carregar arquivos com base na hora da última modificação. 

Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Em seguida, use o comando AzCopy `sync`.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Substitua o espaço reservado `<local-folder-path>` pelo caminho para uma pasta que contém arquivos (por exemplo: `C:\myFolder` ou `/mnt/myFolder`.

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua o espaço reservado `<container-name>` pelo nome do contêiner que você criou.

Para saber mais sobre o comando `sync`, consulte [sincronizar arquivos](storage-use-azcopy-blobs.md#synchronize-files).

## <a name="create-a-scheduled-task"></a>Criar uma tarefa agendada

Pode criar uma tarefa agendada ou uma tarefa Cron que executa um script de comandos do AzCopy. O script identifica e carrega os novos dados no local para o armazenamento na cloud num intervalo de tempo específico.

Copie o comando do AzCopy para um editor de texto. Atualize os valores dos parâmetros do comando do AzCopy para os valores adequados. Guarde o ficheiro como `script.sh` (Linux) ou `script.bat` (Windows) para o AzCopy. 

Esses exemplos pressupõem que sua pasta é denominada `myFolder`, o nome da conta de armazenamento é `mystorageaccount` e o nome do contêiner é `mycontainer`.

> [!NOTE]
> O exemplo do Linux acrescenta um token SAS. Você precisará fornecer um em seu comando. A versão atual do AzCopy V10 não dá suporte à autorização do Azure AD em trabalhos cron.

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

Este exemplo pressupõe que o script está localizado na unidade raiz do seu computador, mas o seu script pode estar em qualquer lugar desejado.

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

Para obter mais informações sobre o AzCopy, consulte qualquer um destes artigos:

* [Introdução ao AzCopy](storage-use-azcopy-v10.md)

* [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)

* [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)

* [Transferir dados com os buckets AzCopy e Amazon S3](storage-use-azcopy-s3.md)
 
* [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
