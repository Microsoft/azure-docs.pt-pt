---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75469490"
---
### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
O serviço ligado ao **Armazenamento Azure** permite-lhe ligar uma conta de armazenamento Azure a uma fábrica de dados Azure utilizando a chave de **conta,** que fornece à fábrica de dados acesso global ao Armazenamento Azure. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Armazenamento Azure.

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **AzureStorage** |Sim |
| conexãoString |Especifique as informações necessárias para ligar ao armazenamento Azure para a propriedade connectionString. |Sim |

Para obter informações sobre como recuperar as chaves de acesso à conta de armazenamento, consulte [Gerir as chaves](../articles/storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .

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

### <a name="azure-storage-sas-linked-service"></a>Serviço Ligado ao Armazenamento Azure
As assinaturas de acesso partilhado (SAS) disponibilizam acesso delegado a recursos na sua conta de armazenamento. Permite-lhe conceder permissões limitadas a objetos na sua conta de armazenamento por um determinado período de tempo e com um conjunto de permissões especificado, sem ter de partilhar as chaves de acesso à sua conta. O SAS é um URI que engloba nos seus parâmetros de consulta toda a informação necessária para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com o SAS, o cliente só precisa de passar no SAS para o construtor ou método apropriado. Para obter mais informações sobre o SAS, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> A Azure Data Factory agora só suporta o **Serviço SAS,** mas não a Conta SAS. Note que o URL SAS gerador do portal Azure ou do Storage Explorer é um SAS de conta, que não é suportado.

> [!TIP]
> Pode executar abaixo os comandos PowerShell para gerar um SAS de serviço para a sua conta de armazenamento (substitua os detentores de lugares e conceda a permissão necessária):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

O serviço ligado ao Azure Storage SAS permite-lhe ligar uma Conta de Armazenamento Azure a uma fábrica de dados Azure utilizando uma Assinatura de Acesso Partilhado (SAS). Fornece à fábrica de dados um acesso restrito/limite de tempo a todos/recursos específicos (blob/container) no armazenamento. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Azure Storage SAS. 

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **AzureStorageSas** |Sim |
| sasUri |Especifique a assinatura de acesso partilhado URI aos recursos de armazenamento do Azure, tais como blob, contentor ou mesa.  |Sim |

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

Ao criar um **SAS URI,** considerando o seguinte:  

* Detete **permissões** de leitura/escrita adequadas em objetos com base na forma como o serviço ligado (ler, escrever, ler/escrever) é utilizado na sua fábrica de dados.
* Definir o **tempo de validade** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento azure não expira no período ativo do gasoduto.
* Uri deve ser criado no recipiente/blob direito ou nível de mesa com base na necessidade. Um SAS Uri a uma bolha Azure permite ao serviço data factory aceder a essa bolha em particular. Um SAS Uri para um recipiente de blob Azure permite que o serviço data Factory aite através de bolhas nesse recipiente. Se necessitar de fornecer acesso mais/menos objetos mais tarde, ou atualizar o SAS URI, lembre-se de atualizar o serviço ligado com o novo URI.   

