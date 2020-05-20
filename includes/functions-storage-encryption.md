---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648801"
---
O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Para mais informações, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../articles/storage/common/storage-service-encryption.md).

Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados blob e ficheiros. Estas chaves devem estar presentes no Cofre de Chaves Azure para que as funções possam aceder à conta de armazenamento. Para saber mais, consulte [a Encriptação em repouso utilizando chaves geridas pelo cliente](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md).  