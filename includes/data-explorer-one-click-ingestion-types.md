---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523054"
---
## <a name="select-an-ingestion-type"></a>Selecione um tipo de ingestão

Para o tipo de **ingestão,** selecione uma das seguintes opções:
   * **do armazenamento** - no **campo de ligação ao** campo de armazenamento, adicione o URL da conta de armazenamento. Utilize [o URL Blob SAS](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) para contas de armazenamento privadas.
   
      ![Ingestão de um clique a partir do armazenamento](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **a partir do ficheiro** - selecione **Navegar** para localizar o ficheiro ou arraste o ficheiro para o campo.
  
      ![Ingestão de um clique a partir do arquivo](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **do recipiente** - no campo de **ligação ao** campo de armazenamento, adicione o [URL SAS](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) do recipiente e introduza opcionalmente o tamanho da amostra.

      ![Ingestão de um clique a partir do recipiente](media/data-explorer-one-click-ingestion-types/from-container.png)

  Uma amostra dos dados aparece. Se quiser, pode filtrar para mostrar apenas ficheiros que começam com caracteres específicos. Quando ajusta os filtros, a pré-visualização atualiza-se automaticamente.
  
  Por exemplo, pode filtrar todos os ficheiros que começam com os *dados* da palavra e terminar com uma extensão *.csv.gz.*

  ![Filtro de ingestão de um clique](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
