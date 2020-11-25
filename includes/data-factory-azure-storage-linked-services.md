---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001752"
---
### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
O **serviço ligado ao Azure Storage** permite-lhe ligar uma conta de armazenamento Azure a uma fábrica de dados Azure utilizando a chave de **conta**, que fornece à fábrica de dados acesso global ao Azure Storage. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Armazenamento Azure.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |O tipo de propriedade deve ser definido para: **AzureStorage** |Sim |
| conexãoStragem |Especifique as informações necessárias para ligar ao armazenamento Azure para a propriedade connectionString. |Sim |

Para obter informações sobre como recuperar as chaves de acesso à conta de armazenamento, consulte [as teclas de acesso à conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).

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

### <a name="azure-storage-sas-linked-service"></a>Serviço Azure Storage Sas Linked
As assinaturas de acesso partilhado (SAS) disponibilizam acesso delegado a recursos na sua conta de armazenamento. Permite-lhe conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um determinado período de tempo e com um conjunto especificado de permissões, sem ter de partilhar as chaves de acesso à sua conta. O SAS é um URI que engloba nos seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com o SAS, o cliente apenas precisa passar no SAS para o construtor ou método apropriado. Para obter mais informações sobre o SAS, consulte [Grant acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> A Azure Data Factory agora só suporta **o Serviço SAS,** mas não a Conta SAS. Note que o URL SAS generable do portal Azure ou Storage Explorer é uma Conta SAS, que não é suportada.

> [!TIP]
> Pode executar abaixo os comandos PowerShell para gerar um Serviço SAS para a sua conta de armazenamento (substitua os titulares de lugares e conceda a permissão necessária): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

O serviço Azure Storage SAS permite-lhe ligar uma Conta de Armazenamento Azure a uma fábrica de dados Azure utilizando uma Assinatura de Acesso Partilhado (SAS). Fornece à fábrica de dados um acesso restrito/temporal a todos/recursos específicos (blob/contentor) no armazenamento. A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Azure Storage SAS. 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |O tipo de propriedade deve ser definido para: **AzureStorageSas** |Sim |
| SasUri |Especifique a assinatura de acesso partilhado URI aos recursos de armazenamento Azure, tais como bolha, recipiente ou mesa.  |Sim |

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

* Descreva permissões de leitura/escrita apropriadas em **objetos baseados** na forma como o serviço ligado (ler, escrever, ler/escrever) é utilizado na sua fábrica de dados.
* Desavendar **o tempo de validade** adequadamente. Certifique-se de que o acesso aos objetos de armazenamento Azure não expira dentro do período ativo do gasoduto.
* Uri deve ser criado ao nível certo do recipiente/bolha ou da tabela com base na necessidade. Um SAS Uri a uma bolha Azure permite ao serviço de Data Factory aceder a essa bolha em particular. Um SAS Uri a um recipiente de bolhas Azure permite que o serviço de Fábrica de Dados itere através de bolhas nesse recipiente. Se precisar de fornecer mais/menos objetos mais tarde ou atualizar o SAS URI, lembre-se de atualizar o serviço ligado com o novo URI.   

