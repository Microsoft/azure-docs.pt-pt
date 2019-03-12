---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: f7c189c59b5098ef22491a914a618afda2b5f51e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554656"
---
### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
O **serviço ligado do armazenamento do Azure** permite-lhe associar uma conta de armazenamento do Azure para uma fábrica de dados do Azure utilizando o **chave de conta**, que fornece a fábrica de dados com o acesso global para o armazenamento do Azure. A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do armazenamento do Azure.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida como: **AzureStorage** |Sim |
| connectionString |Especifica as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. |Sim |

Consulte a secção seguinte para obter passos para ver/copiar a chave da conta de armazenamento do Azure: [Chaves de acesso](../articles/storage/common/storage-account-manage.md#access-keys).

**Exemplo:**  

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

### <a name="azure-storage-sas-linked-service"></a>Serviço ligado do armazenamento do Azure Sas
Uma assinatura de acesso partilhado (SAS) disponibiliza acesso delegado a recursos na sua conta de armazenamento. Permite-lhe conceder que um cliente permissões limitadas a objetos na conta de armazenamento durante um determinado período de tempo e com um conjunto especificado de permissões, sem ter de partilhar as chaves de acesso da conta. A SAS é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente precisa apenas passar a SAS para o método ou construtor apropriado. Para obter informações detalhadas sobre SAS, consulte [assinaturas de acesso partilhado: Compreender o modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> O Azure Data Factory agora só suporta **SAS de serviço** , mas não a conta SAS. Ver [tipos de assinaturas de acesso partilhado](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre estes dois tipos e como construir. Anote o URL de SAS generable a partir do portal do Azure ou o Explorador de armazenamento é uma SAS de conta, que não é suportado.

> [!TIP]
> Pode executar comandos do PowerShell para gerar uma SAS de serviço para a sua conta de armazenamento (substitua os marcadores e conceder a permissão necessária) abaixo: `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

O serviço de SAS de armazenamento do Azure ligada permite-lhe ligar uma conta de armazenamento do Azure a uma fábrica de dados do Azure através de uma assinatura de acesso partilhado (SAS). Ele fornece a fábrica de dados com acesso restrito/com limite de tempo específico/todos os recursos (blob/contentor) no armazenamento. A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço de SAS de armazenamento do Azure ligado. 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida como: **AzureStorageSas** |Sim |
| sasUri |Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure, como BLOBs, contentores ou tabela.  |Sim |

**Exemplo:**

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

Ao criar uma **URI de SAS**, Considerando o seguinte:  

* Conjunto apropriada de leitura/escrita **permissões** em objetos com base em como o serviço ligado (leitura, escrita, de leitura/escrita) é utilizado na sua fábrica de dados.
* Definir **tempo de expiração** adequadamente. Certifique-se de que o acesso a objetos de armazenamento do Azure não expirar com o período ativo do pipeline.
* Deve ser criado no nível de tabela com base na necessidade ou blob/contentor certo. Um Uri de SAS para um blob do Azure permite que o serviço Data Factory aceder a esse determinado blob. Um Uri de SAS para um contentor de Blobs do Azure permite que o serviço Data Factory para fazer a iteração por meio de blobs existentes nesse contentor. Se precisar de fornecer acesso mais/menos objetos mais tarde ou Atualize o URI de SAS, lembre-se de atualizar o serviço ligado com o URI de novo.   

