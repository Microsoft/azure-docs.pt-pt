---
title: Considerações de armazenamento para funções azure
description: Conheça os requisitos de armazenamento das Funções Azure e sobre encriptar dados armazenados.
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276585"
---
# <a name="storage-considerations-for-azure-functions"></a>Considerações de armazenamento para funções azure

As Funções Azure requerem uma conta de Armazenamento Azure quando cria uma instância de aplicação de funções. Os seguintes serviços de armazenamento podem ser utilizados pela sua aplicação de funções:


|Serviço de armazenamento  | Utilização de funções  |
|---------|---------|
| [Armazenamento Azure Blob](../storage/blobs/storage-blobs-introduction.md)     | Mantenha as ligações de estado e chaves de função.  <br/>Também utilizado por centros de [tarefas em Funções Duráveis.](durable/durable-functions-task-hubs.md) |
| [Ficheiros do Azure](../storage/files/storage-files-introduction.md)  | Partilha de ficheiros utilizada para armazenar e executar o código da aplicação de funções num Plano de [Consumo](functions-scale.md#consumption-plan). |
| [Armazenamento de fila azure](../storage/queues/storage-queues-introduction.md)     | Utilizado por centros de [tarefas em Funções Duráveis.](durable/durable-functions-task-hubs.md)   |
| [Armazenamento de mesa azure](../storage/tables/table-storage-overview.md)  |  Utilizado por centros de [tarefas em Funções Duráveis.](durable/durable-functions-task-hubs.md)       |

> [!IMPORTANT]
> Ao utilizar o plano de Consumo de alojamento, os ficheiros de configuração de enlace e código de função são armazenados no armazenamento de Ficheiro do Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar uma aplicação de função, deve criar ou ligar-se a uma conta de Armazenamento Azure de fins gerais que suporte o armazenamento de Blob, Queue e Mesa. Isto porque as Funções dependem do Armazenamento Azure para operações como a gestão de gatilhos e execuções de funções de exploração madeireira. Algumas contas de armazenamento não suportam filas e mesas. Estas contas incluem contas de armazenamento apenas blob, armazenamento Azure Premium e contas de armazenamento de uso geral com replicação ZRS. Estas contas não suportadas são filtradas da lâmina da Conta de Armazenamento ao criar uma aplicação de função.

Para saber mais sobre os tipos de conta de armazenamento, veja [Introdução dos Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services). 

Embora possa utilizar uma conta de armazenamento existente com a sua aplicação de funções, deve certificar-se de que satisfaz estes requisitos. As contas de armazenamento criadas como parte da aplicação de função criar fluxo são garantidas para satisfazer estes requisitos da conta de armazenamento.  

## <a name="storage-account-guidance"></a>Orientação da conta de armazenamento

Todas as aplicações de função requerem uma conta de armazenamento para funcionar. Se essa conta for eliminada, a sua aplicação de função não será executada. Para resolver problemas relacionados com o armazenamento, consulte [como resolver problemas relacionados com o armazenamento](functions-recover-storage-account.md). As seguintes considerações adicionais aplicam-se à conta de Armazenamento utilizada pelas aplicações de função.

### <a name="storage-account-connection-setting"></a>Definição de conexão de conta de armazenamento

A ligação da conta de armazenamento é mantida na definição da [aplicação AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

A cadeia de ligação à conta de armazenamento deve ser atualizada quando regenera as chaves de armazenamento. [Leia mais sobre a gestão da chave](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)de armazenamento aqui .

### <a name="shared-storage-accounts"></a>Contas de armazenamento partilhadas

É possível que várias aplicações de função partilhem a mesma conta de armazenamento sem qualquer problema. Por exemplo, no Estúdio Visual pode desenvolver várias aplicações usando o Emulador de Armazenamento Azure. Neste caso, o emulador age como uma única conta de armazenamento. A mesma conta de armazenamento utilizada pela sua aplicação de funções também pode ser utilizada para armazenar os dados da sua aplicação. No entanto, esta abordagem nem sempre é uma boa ideia num ambiente de produção.

### <a name="optimize-storage-performance"></a>Otimizar o desempenho de armazenamento

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Encriptação de dados de armazenamento

O Azure Storage encripta todos os dados numa conta de armazenamento em repouso. Para mais informações, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md).

Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados blob e ficheiros. Estas chaves devem estar presentes no Cofre de Chaves Azure para que as funções possam aceder à conta de armazenamento. Para saber mais, consulte as [chaves geridas pelo cliente Configure com o Cofre de Chaves Azure utilizando o portal Azure](../storage/common/storage-encryption-keys-portal.md).  

## <a name="mount-file-shares-linux"></a>Monte ações de ficheiro (Linux)

Pode montar as ações existentes do Azure Files para as suas aplicações de função Linux. Ao montar uma parte na sua aplicação de função Linux, pode aproveitar os modelos de aprendizagem automática existentes ou outros dados nas suas funções. Pode utilizar [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) o comando para montar uma parte existente na sua aplicação de função Linux. 

Neste comando, `share-name` é o nome da partilha dos `custom-id` Ficheiros Azure existentes, e pode ser qualquer cadeia que defina exclusivamente a parte quando montada na aplicação de funções. Além `mount-path` disso, é o caminho a partir do qual a partilha é acedida na sua aplicação de funções. `mount-path`deve estar no `/dir-name`formato , e `/home`não pode começar com .

Para um exemplo completo, consulte os scripts em [Create a Python function app e monte uma partilha de Ficheiros Azure](scripts/functions-cli-mount-files-storage-linux.md). 

Atualmente, apenas um `storage-type` de é `AzureFiles` apoiado. Só pode montar cinco ações numa determinada aplicação de função. A montagem de uma parte de ficheiro pode aumentar o tempo de início frio em pelo menos 200-300 ms, ou ainda mais quando a conta de armazenamento estiver numa região diferente.

A parte montada está disponível para `mount-path` o seu código de função no especificado. Por exemplo, `mount-path` `/path/to/mount`quando estiver, pode aceder ao directório-alvo por APIs do sistema de ficheiros, como no exemplo python seguinte:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as opções de hospedagem de Funções Azure.

> [!div class="nextstepaction"]
> [Dimensionamento e alojamento de Funções do Azure](functions-scale.md)


