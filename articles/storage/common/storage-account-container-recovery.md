---
title: Recuperação de contêiner da conta de armazenamento
description: Recuperação de contêiner da conta de armazenamento
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693582"
---
# <a name="storage-account-container-recovery"></a>Recuperação de contêiner da conta de armazenamento

O armazenamento do Azure fornece resiliência de dados por meio de réplicas automatizadas. No entanto, isso não impede que o código do aplicativo ou os usuários corrompam dados, seja acidentalmente ou maliciosamente. Manter a fidelidade de dados em face de erro de aplicativo ou de usuário requer técnicas mais avançadas, como copiar os dados para um local de armazenamento secundário com um log de auditoria.

## <a name="checking-azure-storage-account-type"></a>Verificando o tipo de conta de armazenamento do Azure

1. Navegue para o [portal do Azure](https://portal.azure.com/).

2. Localize a sua conta de armazenamento.

3. Na seção **visão geral** , verifique a **replicação**.

   ![Imagem](media/storage-account-container-recovery/1.png)

4. Se o tipo de replicação for **grs/ra-grs**, a recuperação de contêiner de conta será possível sem garantia. Para todos os outros tipos de replicação, não é possível.

5. Reúna as informações a seguir e arquivo uma solicitação de suporte com Suporte da Microsoft.

   * Nome da conta de armazenamento:
   * Nome do contêiner:
   * Hora da exclusão:

   A tabela a seguir fornece uma visão geral do escopo da recuperação de contêiner da conta de armazenamento, dependendo da estratégia de replicação.

   |Tipo de conteúdo|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |Contêiner de armazenamento|Não|Não|Sim|Sim| 

   * Podemos tentar restaurar o contêiner da conta de armazenamento, mas sem nenhuma garantia. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Coisas que não devem ser impossíveis para a recuperação serem bem-sucedidas

* Não recrie o contêiner (com o mesmo nome).  
* Se você já tiver recriado o contêiner, precisará excluir o contêiner antes de fazer o arquivamento de uma solicitação de suporte para recuperação.

## <a name="steps-to-prevent-this-in-the-future"></a>Etapas para evitar isso no futuro

1. Para evitar isso no futuro, o recurso mais recomendado para usar é a [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Também recomendamos o recurso de [instantâneo](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) .
 
## <a name="next-steps"></a>Passos seguintes

Aqui estão dois códigos de exemplo sobre o recurso:

  * [Criar e gerenciar um instantâneo de blob no .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Usando instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

