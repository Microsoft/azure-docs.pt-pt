---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: ee368b58195d61a1c6792a3a3655122af7104d58
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012238"
---
### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
O **serviço vinculado do armazenamento do Azure** permite vincular uma conta de armazenamento do Azure a uma data Factory do Azure usando a **chave de conta**, que fornece o data Factory com acesso global ao armazenamento do Azure. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do armazenamento do Azure.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type |A propriedade Type deve ser definida como: **AzureStorage** |Sim |
| connectionString |Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. |Sim |

Consulte a seção a seguir para ver as etapas para exibir/copiar a chave de conta para um armazenamento do Azure: [Chaves de acesso](../articles/storage/common/storage-account-manage.md#access-keys).

**Example:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Serviço vinculado de SAS do armazenamento do Azure
Uma SAS (assinatura de acesso compartilhado) fornece acesso delegado aos recursos em sua conta de armazenamento. Ele permite conceder a um cliente permissões limitadas a objetos em sua conta de armazenamento por um período de tempo especificado e com um conjunto especificado de permissões, sem precisar compartilhar as chaves de acesso da conta. A SAS é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar os recursos de armazenamento com a SAS, o cliente só precisa passar a SAS para o construtor ou método apropriado. Para obter mais informações sobre SAS, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory agora só dá suporte à **SAS do serviço** , mas não à SAS da conta. Observe que a URL SAS generable de portal do Azure ou Gerenciador de Armazenamento é uma SAS de conta, que não tem suporte.

> [!TIP]
> Você pode executar os comandos do PowerShell abaixo para gerar uma SAS de serviço para sua conta de armazenamento (substituir os locais e conceder a permissão necessária):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

O serviço vinculado de SAS do armazenamento do Azure permite vincular uma conta de armazenamento do Azure a uma data factory do Azure usando uma SAS (assinatura de acesso compartilhado). Ele fornece o data factory com acesso restrito/com limite de tempo a todos/recursos específicos (blob/contêiner) no armazenamento. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado de SAS do armazenamento do Azure. 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type |A propriedade Type deve ser definida como: **AzureStorageSas** |Sim |
| sasUri |Especifique o URI da assinatura de acesso compartilhado para os recursos de armazenamento do Azure, como BLOB, contêiner ou tabela.  |Sim |

**Example:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Ao criar um **URI de SAS**, considere o seguinte:  

* Defina **permissões** de leitura/gravação apropriadas em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) é usado em seu data Factory.
* Definir **tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento do Azure não expire no período ativo do pipeline.
* O URI deve ser criado no nível de contêiner/BLOB ou tabela correto com base na necessidade. Um URI de SAS para um blob do Azure permite que o serviço de Data Factory acesse esse blob específico. Um URI de SAS para um contêiner de BLOBs do Azure permite que o serviço de Data Factory itere por BLOBs nesse contêiner. Se você precisar fornecer acesso a mais/menos objetos mais tarde, ou atualizar o URI de SAS, lembre-se de atualizar o serviço vinculado com o novo URI.   

