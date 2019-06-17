---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 6911e06dc023027ab32b99387b9f7d3f5e708f86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158588"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Configurar a sua aplicação para aceder ao armazenamento do Azure
Existem duas formas de autenticar a sua aplicação para aceder aos serviços de armazenamento:

* Chave partilhada: Utilizar a chave partilhada para apenas para fins de teste
* Assinatura de acesso partilhado (SAS): Utilizar a SAS para aplicações de produção

### <a name="shared-key"></a>Chave Partilhada
Autenticação de chave partilhada significa que seu aplicativo irá utilizar o nome da conta e a chave de conta para aceder aos serviços de armazenamento. Para efeitos de rapidamente que mostram como utilizar esta biblioteca, usaremos a autenticação de chave partilhada neste introdução ao.

> [!WARNING] 
> **Utilize apenas a autenticação de chave partilhada para fins de teste!** Nome da conta e chave da conta, que concede acesso de leitura/escrita total para a conta de armazenamento associada, serão distribuídos para cada pessoa que transfere a sua aplicação. Isto é **não** uma boa prática, corre o risco de ter a sua chave fica comprometido por clientes não fidedignos.
> 
> 

Quando utilizar a autenticação de chave partilhada, irá criar um [cadeia de ligação](../articles/storage/common/storage-configure-connection-string.md). A cadeia de ligação é composta por:  

* O **DefaultEndpointsProtocol** -é possível escolher HTTP ou HTTPS. No entanto, através de HTTPS é altamente recomendado.
* O **nome da conta** -o nome da conta de armazenamento
* O **chave da conta** - na [Portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento e clique no **chaves** ícone para encontrar estas informações.
* (Opcional) **EndpointSuffix** -isto é utilizado para serviços de armazenamento em regiões com sufixos de ponto de extremidade diferente, como o Azure China ou Azure governação.

Eis um exemplo de cadeia de ligação com a autenticação de chave partilhada:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Assinaturas de Acesso Partilhado (SAS)
Para uma aplicação móvel, o método recomendado para autenticar um pedido por um cliente contra o serviço de armazenamento do Azure é usar uma assinatura de acesso partilhado (SAS). SAS permite-lhe conceder um acesso de cliente a um recurso para um determinado período de tempo, com um conjunto especificado de permissões.
Como o proprietário da conta de armazenamento, terá de gerar uma SAS para os seus clientes móveis consumir. Para gerar a SAS, provavelmente desejará escrever um serviço separado, que gera a SAS para ser distribuída aos seus clientes. Para fins de teste, pode utilizar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) ou o [Portal do Azure](https://portal.azure.com) para gerar uma SAS. Ao criar a SAS, pode especificar o intervalo de tempo durante o qual a SAS é válida e as permissões que a SAS concede ao cliente.

O exemplo seguinte mostra como utilizar o Explorador de armazenamento do Microsoft Azure para gerar uma SAS.

1. Se ainda não o fez, [instalar o Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com)
2. Estabelecer a ligação à subscrição.
3. Clique na sua conta de armazenamento e clique no separador "Ações" na parte inferior esquerda. Clique em "Obter assinatura de acesso partilhado" para gerar uma "cadeia de ligação" para a SAS.
4. Eis um exemplo de uma cadeia de ligação de SAS, que concede ler e escrever o serviço, ao nível de objeto para o serviço blob da conta de armazenamento e contentor.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Como pode ver, ao utilizar uma SAS, não está expondo a chave da conta na sua aplicação. Pode saber mais sobre SAS e melhores práticas para utilizar a SAS por dar uma olhada em [assinaturas de acesso partilhado: Compreender o modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

