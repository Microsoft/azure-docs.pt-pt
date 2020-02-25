---
title: Recuperação do contentor da conta de armazenamento
description: Recuperação do contentor da conta de armazenamento
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562280"
---
# <a name="storage-account-container-recovery"></a>Recuperação do contentor da Conta de Armazenamento

O Azure Storage fornece resiliência de dados através de réplicas automatizadas. No entanto, isto não impede que o código de aplicação ou os utilizadores corrompam dados, acidentalmente ou maliciosamente. Manter a fidelidade de dados face à aplicação ou erro do utilizador requer técnicas mais avançadas, tais como copiar os dados para um local de armazenamento secundário com um registo de auditoria.

## <a name="checking-azure-storage-account-type"></a>Verificação do tipo de conta de armazenamento azure

1. Navegue para o [portal do Azure](https://portal.azure.com/).

2. Localize a sua conta de armazenamento.

3. Na secção **Visão Geral,** verifique se a **replicação**.

   ![Imagem](media/storage-account-container-recovery/1.png)

4. Se o Tipo de Replicação for **GRS/RA-GRS,** a recuperação do contentor da conta é possível sem garantia. Para todos os outros tipos de replicação, não é possível.

5. Recolha as seguintes informações e preencha um pedido de suporte com o Suporte microsoft.

   * Nome da conta de armazenamento:
   * Nome do contentor:
   * Hora da eliminação:

   O quadro seguinte fornece uma visão geral do âmbito de recuperação do contentor da conta de armazenamento, dependendo da estratégia de replicação.

   |Tipo de Conteúdo|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |Contentor de armazenamento|Não|Não|Sim|Sim| 

   * Podemos tentar restaurar o contentor da conta de armazenamento, mas sem qualquer garantia. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Coisas a não fazer para recuperar para ser bem sucedido

* Por favor, não recrie o recipiente (com o mesmo nome).  
* Se já recriou o recipiente, tem de apagar o recipiente antes de apresentar um pedido de apoio para a recuperação.

## <a name="steps-to-prevent-this-in-the-future"></a>Passos para evitar isto no futuro

1. Para evitar isto no futuro, a funcionalidade mais recomendada para usar é [soft Delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Também recomendamos a função [Snapshot.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
 
## <a name="next-steps"></a>Passos seguintes

Aqui estão dois códigos de amostra na funcionalidade:

  * [Crie e gerencie um instantâneo blob em .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Usando fotos blob com PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

