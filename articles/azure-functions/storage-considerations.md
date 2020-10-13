---
title: Considerações de armazenamento para funções Azure
description: Saiba mais sobre os requisitos de armazenamento das Funções Azure e sobre a encriptação de dados armazenados.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: aefd9a35235a09d94973f383603349f6862bbdd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318186"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerações de armazenamento para funções Azure

As Funções Azure requerem uma conta de Armazenamento Azure quando cria uma instância de aplicação de função. Os seguintes serviços de armazenamento podem ser utilizados pela sua aplicação de função:


|Serviço de armazenamento  | Utilização de funções  |
|---------|---------|
| [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md)     | Mantenha as chaves de estado e de função de encadernação.  <br/>Também utilizado por [centros de tarefas em Funções Duradouras](durable/durable-functions-task-hubs.md). |
| [Ficheiros do Azure](../storage/files/storage-files-introduction.md)  | Partilha de ficheiros usada para armazenar e executar o código de aplicação da sua função num [Plano de Consumo](functions-scale.md#consumption-plan) e Plano [Premium.](functions-scale.md#premium-plan) |
| [Armazenamento de Filas do Azure](../storage/queues/storage-queues-introduction.md)     | Utilizado por [centros de tarefas em Funções Duradouras](durable/durable-functions-task-hubs.md).   |
| [Armazenamento de tabelas do Azure](../storage/tables/table-storage-overview.md)  |  Utilizado por [centros de tarefas em Funções Duradouras](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Ao utilizar o plano de hospedagem Consumption/Premium, o código de função e os ficheiros de configuração de ligação são armazenados no armazenamento do Ficheiro Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar uma aplicação de função, deve criar ou ligar para uma conta de Armazenamento Azure de uso geral que suporte o armazenamento de Blob, Queue e Table. Isto porque as funções dependem do Azure Storage para operações como a gestão de gatilhos e execuções de funções de registo. Algumas contas de armazenamento não suportam filas e mesas. Estas contas incluem contas de armazenamento apenas blob, armazenamento Azure Premium e contas de armazenamento de uso geral com replicação ZRS. Estas contas não suportadas são filtradas a partir da lâmina da Conta de Armazenamento ao criar uma aplicação de função.

Para saber mais sobre os tipos de conta de armazenamento, veja [Introdução dos Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#core-storage-services). 

Embora possa utilizar uma conta de armazenamento existente com a sua aplicação de função, deve certificar-se de que cumpre estes requisitos. As contas de armazenamento criadas como parte do fluxo de criação de apps de função são garantidas para satisfazer estes requisitos de conta de armazenamento.  

## <a name="storage-account-guidance"></a>Orientação da conta de armazenamento

Cada aplicação de função requer que uma conta de armazenamento funcione. Se essa conta for eliminada, a sua aplicação de função não será executada. Para resolver problemas relacionados com o armazenamento, consulte [como resolver problemas relacionados com o armazenamento](functions-recover-storage-account.md). As seguintes considerações adicionais aplicam-se à conta de Armazenamento utilizada pelas aplicações de função.

### <a name="storage-account-connection-setting"></a>Definição de ligação de conta de armazenamento

A ligação à conta de armazenamento é mantida na definição da [aplicação AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

O fio de ligação da conta de armazenamento deve ser atualizado quando regenerar as chaves de armazenamento. [Leia mais sobre a gestão de chaves de armazenamento aqui.](../storage/common/storage-account-create.md)

### <a name="shared-storage-accounts"></a>Contas de armazenamento partilhadas

É possível que várias aplicações de funções partilhem a mesma conta de armazenamento sem qualquer problema. Por exemplo, no Visual Studio pode desenvolver várias aplicações usando o Emulador de Armazenamento Azure. Neste caso, o emulador age como uma única conta de armazenamento. A mesma conta de armazenamento utilizada pela sua aplicação de função também pode ser usada para armazenar os dados da sua aplicação. No entanto, esta abordagem nem sempre é uma boa ideia num ambiente de produção.

### <a name="optimize-storage-performance"></a>Otimizar o desempenho de armazenamento

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Encriptação de dados de armazenamento

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="mount-file-shares-linux"></a>Monte ações de ficheiros (Linux)

Pode montar as partilhas de Ficheiros Azure existentes nas suas aplicações de função Linux. Ao montar uma parte na sua aplicação de função Linux, pode aproveitar os modelos de aprendizagem automática existentes ou outros dados nas suas funções. Pode utilizar o [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) comando para montar uma parte existente na sua aplicação de função Linux. 

Neste comando, `share-name` é o nome da partilha Azure Files existente, e pode ser qualquer cadeia que `custom-id` defina exclusivamente a partilha quando montada na aplicação de função. Além disso, `mount-path` é o caminho a partir do qual a partilha é acedida na sua aplicação de função. `mount-path` deve estar no formato `/dir-name` , e não pode começar com `/home` .

Para um exemplo completo, consulte os scripts na [aplicação de função Create a Python e monte uma partilha de Ficheiros Azure](scripts/functions-cli-mount-files-storage-linux.md). 

Atualmente, apenas um `storage-type` dos `AzureFiles` é apoiado. Só é possível montar cinco partilhas numa determinada aplicação de função. A montagem de uma partilha de ficheiros pode aumentar o tempo de início a frio em pelo menos 200-300ms, ou ainda mais quando a conta de armazenamento está em uma região diferente.

A parte montada está disponível para o seu código de função no `mount-path` especificado. Por exemplo, quando `mount-path` `/path/to/mount` estiver, pode aceder ao directório-alvo por APIs do sistema de ficheiros, como no exemplo python seguinte:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as opções de hospedagem do Azure Functions.

> [!div class="nextstepaction"]
> [Dimensionamento e alojamento de Funções do Azure](functions-scale.md)
