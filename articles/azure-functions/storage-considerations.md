---
title: Considerações de armazenamento para funções azure
description: Conheça os requisitos de armazenamento das Funções Azure e sobre encriptar dados armazenados.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 353fdd3bf7775e3bc7a9d017a9e8dd8238b09830
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964985"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerações de armazenamento para funções azure

As Funções Azure requerem uma conta de Armazenamento Azure quando cria uma instância de aplicação de funções. Os seguintes serviços de armazenamento podem ser utilizados pela sua aplicação de funções:


|Serviço de armazenamento  | Utilização de funções  |
|---------|---------|
| [Armazenamento de Blobs do Azure](/storage/blobs/storage-blobs-overview.md)     | Mantenha as ligações de estado e chaves de função.  <br/>Também utilizado por centros de [tarefas em Funções Duráveis.](durable/durable-functions-task-hubs.md) |
| [Ficheiros do Azure](../storage/files/storage-files-introduction.md)  | Partilha de ficheiros utilizada para armazenar e executar o código da aplicação de funções num Plano de [Consumo](functions-scale.md#consumption-plan). |
| [Armazenamento de filas do Azure](../storage/queues/storage-queues-introduction.md)     | Utilizado por centros de [tarefas em Funções Duráveis.](durable/durable-functions-task-hubs.md)   |
| [Armazenamento de tabelas do Azure](../storage/tables/table-storage-overview.md)  |  Utilizado por centros de [tarefas em Funções Duráveis.](durable/durable-functions-task-hubs.md)       |

> [!IMPORTANT]
> Ao utilizar o plano de Consumo de alojamento, os ficheiros de configuração de enlace e código de função são armazenados no armazenamento de Ficheiro do Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar uma aplicação de função, deve criar ou ligar-se a uma conta de Armazenamento Azure de fins gerais que suporte o armazenamento de Blob, Queue e Mesa. Isto porque as Funções dependem do Armazenamento Azure para operações como a gestão de gatilhos e execuções de funções de exploração madeireira. Algumas contas de armazenamento não suportam filas e mesas. Estas contas incluem contas de armazenamento apenas blob, armazenamento Azure Premium e contas de armazenamento de uso geral com replicação ZRS. Estas contas não suportadas são filtradas da lâmina da Conta de Armazenamento ao criar uma aplicação de função.

Para saber mais sobre os tipos de conta de armazenamento, veja [Introdução dos Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services). 

Embora possa utilizar uma conta de armazenamento existente com a sua aplicação de funções, deve certificar-se de que satisfaz estes requisitos. As contas de armazenamento criadas como parte da aplicação de função criar fluxo são garantidas para satisfazer estes requisitos da conta de armazenamento.  

## <a name="storage-account-guidance"></a>Orientação da conta de armazenamento

Cada aplicativo de funções requer uma conta de armazenamento para operar. Se essa conta for eliminada, a sua aplicação de função não será executada. Para resolver problemas relacionados com o armazenamento, consulte [como resolver problemas relacionados com o armazenamento](functions-recover-storage-account.md). As seguintes considerações adicionais aplicam-se à conta de Armazenamento utilizada pelas aplicações de função.

### <a name="storage-account-connection-setting"></a>Definição de conexão de conta de armazenamento

A conexão da conta de armazenamento é mantida na [configuração do aplicativo AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

A cadeia de ligação à conta de armazenamento deve ser atualizada quando regenera as chaves de armazenamento. [Leia mais sobre o gerenciamento de chaves de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

### <a name="shared-storage-accounts"></a>Contas de armazenamento partilhadas

É possível que várias aplicações de função partilhem a mesma conta de armazenamento sem qualquer problema. Por exemplo, no Estúdio Visual pode desenvolver várias aplicações usando o Emulador de Armazenamento Azure. Neste caso, o emulador age como uma única conta de armazenamento. A mesma conta de armazenamento utilizada pela sua aplicação de funções também pode ser utilizada para armazenar os dados da sua aplicação. No entanto, esta abordagem nem sempre é uma boa ideia num ambiente de produção.

### <a name="optimize-storage-performance"></a>Otimizar o desempenho do armazenamento

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Encriptação de dados de armazenamento

O armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Para obter mais informações, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados blob e ficheiros. Estas chaves devem estar presentes no Cofre de Chaves Azure para que as funções possam aceder à conta de armazenamento. Para saber mais, consulte as [chaves geridas pelo cliente Configure com o Cofre de Chaves Azure utilizando o portal Azure](../storage/common/storage-encryption-keys-portal.md).  

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as opções de hospedagem de Funções Azure.

> [!div class="nextstepaction"]
> [Dimensionamento e alojamento de Funções do Azure](functions-scale.md)


