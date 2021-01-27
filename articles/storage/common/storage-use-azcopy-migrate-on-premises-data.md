---
title: 'Tutorial: Migrar dados no local para o Azure Storage com AzCopy| Microsoft Docs'
description: Neste tutorial, utiliza o AzCopy para migrar ou copiar dados de ou para o blob, a tabela e o conteúdo do ficheiro. Migre facilmente dados do armazenamento local para o Armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 682394329205d74859c1af0c0a68a37539da7872
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881032"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Tutorial: Migrar dados no local para armazenamento em nuvem com AzCopy

O AzCopy é uma ferramenta de linha de comandos para copiar dados de ou para o armazenamento de Blobs do Azure, Ficheiros do Azure e armazenamento de Tabelas do Azure através de comandos simples. Os comandos foram concebidos para um desempenho ideal. Com o AzCopy, pode copiar dados entre um sistema de ficheiros e uma conta de armazenamento ou entre contas de armazenamento. O AzCopy pode ser utilizado para copiar dados (no local) do local para uma conta de armazenamento.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento. 
> * Utilizar o AzCopy para carregar todos os seus dados.
> * Modificar os dados para fins de teste.
> * Criar uma tarefa agendada ou tarefa Cron para identificar os novos ficheiros a carregar.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, descarregue a versão mais recente do AzCopy. Ver [Começar com a AzCopy.](storage-use-azcopy-v10.md)

Se estiver no Windows, irá precisar de [Schtasks](/windows/win32/taskschd/schtasks), uma vez que este tutorial utiliza-o para agendar uma tarefa. Ao invés, os utilizadores do Linux utilizam o comando crontab.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Criar um contentor

O primeiro passo é criar um contentor, porque os blobs têm de ser sempre carregados para um contentor. Os contentores são utilizados como um método de organizar grupos de blobs, como faria com ficheiros no seu computador, em pastas.

Siga estes passos para criar um contentor:

1. Selecione o botão **Contas de armazenamento** na página principal e selecione a conta de armazenamento que criou.
2. Selecione **Blobs** em **Serviços** e, em seguida, selecione **Contentor**.

   ![Screenshot mostrando criação de recipiente](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Os nomes de contentores têm de começar com uma letra ou um número. Só podem conter letras, números e o caráter de hífen (-). Para ter acesso a outras regras sobre como atribuir nomes a blobs e contentores, consulte [Atribuir nomes e referenciar contentores, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>Transferir o AzCopy

Descarregue o ficheiro executável AzCopy V10.

- [Janelas](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (alcatrão)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Coloque o ficheiro AzCopy em qualquer lugar do seu computador. Adicione a localização do ficheiro à variável do caminho do sistema para que possa consultar este ficheiro executável a partir de qualquer pasta do seu computador.

## <a name="authenticate-with-azure-ad"></a>Autenticar com o Azure AD

Em primeiro lugar, atribua o papel [de Contribuinte de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) à sua identidade. Consulte [o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](./storage-auth-aad-rbac-portal.md).

Em seguida, abra um pedido de comando, digite o seguinte comando e prima a tecla ENTER.

```azcopy
azcopy login
```

Este comando devolve um código de autenticação e o URL de um website. Abra o site, forneça o código e, em seguida, escolha o botão **Seguinte.**

![Screenshot mostrando o pedido de login](media/storage-use-azcopy-v10/azcopy-login.png)

Aparecerá uma janela de inscrição. Nessa janela, inscreva-se na sua conta Azure utilizando as credenciais da sua conta Azure. Depois de ter assinado com sucesso, pode fechar a janela do navegador e começar a usar a AzCopy.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Carregar conteúdos de uma pasta para o armazenamento de Blobs

Pode utilizar o AzCopy para carregar todos os ficheiros numa pasta para o armazenamento de Blobs no [Windows](./storage-use-azcopy-v10.md) ou [Linux](./storage-use-azcopy-v10.md). Para carregar todos os blobs numa pasta, introduza o seguinte comando do AzCopy:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* Substitua o `<local-folder-path>` espaço reservado por um caminho para uma pasta que contenha ficheiros (por exemplo: `C:\myFolder` ou `/mnt/myFolder` ).

* Substitua o `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

* Substitua o `<container-name>` espaço reservado pelo nome do recipiente que criou.

Para enviar o conteúdo do diretório especificado para o armazenamento blob de forma recorrente, especifique a `--recursive` opção. Quando executar a AzCopy com esta opção, todas as sub-dobradeiras e os seus ficheiros também são carregados.

## <a name="upload-modified-files-to-blob-storage"></a>Carregar ficheiros modificados para o armazenamento de Blobs

Pode utilizar o AzCopy para carregar ficheiros com base na hora da última modificação. 

Para experimentar, modifique ou crie novos ficheiros no diretório de origem para fins de teste. Em seguida, use o comando AzCopy. `sync`

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* Substitua o `<local-folder-path>` espaço reservado por um caminho para uma pasta que contenha ficheiros (por exemplo: `C:\myFolder` ou `/mnt/myFolder` .

* Substitua o `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

* Substitua o `<container-name>` espaço reservado pelo nome do recipiente que criou.

Para saber mais sobre o `sync` comando, consulte [ficheiros Synchronize](./storage-use-azcopy-v10.md#transfer-data).

## <a name="create-a-scheduled-task"></a>Criar uma tarefa agendada

Pode criar uma tarefa agendada ou uma tarefa Cron que executa um script de comandos do AzCopy. O script identifica e carrega os novos dados no local para o armazenamento na cloud num intervalo de tempo específico.

Copie o comando do AzCopy para um editor de texto. Atualize os valores dos parâmetros do comando do AzCopy para os valores adequados. Guarde o ficheiro como `script.sh` (Linux) ou `script.bat` (Windows) para o AzCopy. 

Estes exemplos pressupõem que a sua pasta está `myFolder` nomeada, o nome da sua conta de armazenamento é `mystorageaccount` e o nome do seu recipiente é `mycontainer` .

> [!NOTE]
> O exemplo linux anexa um símbolo SAS. Terá de providenciar um ao seu comando. A versão atual do AzCopy V10 não suporta a autorização da AD Azure em trabalhos de cron.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

Neste tutorial, é utilizado o comando [Schtasks](/windows/win32/taskschd/schtasks) para criar uma tarefa agendada no Windows. O comando [Crontab](http://crontab.org/) é utilizado para criar uma tarefa Cron no Linux.

 **Schtasks** permite a um administrador criar, eliminar, consultar, alterar, executar e terminar tarefas agendadas num computador local ou remoto. **Cron** permite aos utilizadores de Linux e Unix executar comandos ou scripts numa data e hora especificadas através de [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="linux"></a>[Linux](#tab/linux)

Para criar uma tarefa Cron no Linux, introduza o seguinte comando num terminal:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Especificar a expressão Cron `*/5 * * * *` no comando indica que o script da shell `script.sh` deve ser executado a cada cinco minutos. Pode agendar o script para ser executado numa hora específica diária, mensal ou anualmente. Para obter mais informações sobre como definir a data e hora de execução da tarefa, veja [expressões Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windows"></a>[Windows](#tab/windows)

Para criar uma tarefa agendada no Windows, introduza o seguinte comando numa linha de comandos ou no PowerShell:

Este exemplo pressupõe que o seu script está localizado na unidade raiz do seu computador, mas o seu script pode estar em qualquer lugar que você quiser.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

O comando utiliza:
- O parâmetro `/SC` para especificar uma agenda de minutos.
- O parâmetro `/MO` para especificar um intervalo de cinco minutos.
- O parâmetro `/TN` para especificar o nome da tarefa.
- O parâmetro `/TR` para especificar o caminho para o ficheiro `script.bat`.

Para obter mais informações sobre como criar uma tarefa agendada no Windows, veja [Schtasks](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes).

---

Para validar que a tarefa agendada/tarefa Cron é executada corretamente, crie novos ficheiros no diretório `myFolder`. Aguarde cinco minutos para confirmar que os novos ficheiros foram carregados para a conta de armazenamento. Aceda ao diretório de registos para ver os registos de saída da tarefa agendada ou tarefa Cron.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre formas de mover os dados no local para o Armazenamento do Azure e vice-versa, siga esta ligação:

* [Mover dados de e para o Armazenamento do Azure](./storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

Para mais informações sobre a AzCopy, consulte qualquer um destes artigos:

* [Introdução ao AzCopy](storage-use-azcopy-v10.md)

* [Transferir dados com AzCopy e armazenamento de bolhas](./storage-use-azcopy-v10.md#transfer-data)

* [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)

* [Transfira dados com baldes AzCopy e Amazon S3](storage-use-azcopy-s3.md)
 
* [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)
