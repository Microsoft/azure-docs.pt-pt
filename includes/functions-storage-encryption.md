---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648801"
---
O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Para obter mais informações, consulte [a encriptação do Azure Storage para obter dados em repouso](../articles/storage/common/storage-service-encryption.md).

Por predefinição, os dados são encriptados com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados de blob e ficheiros. Estas teclas devem estar presentes no Cofre da Chave Azure para que as funções possam aceder à conta de armazenamento. Para saber mais, consulte [a Encriptação em repouso utilizando as teclas geridas pelo cliente](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md).  