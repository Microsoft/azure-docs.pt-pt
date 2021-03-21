---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996827"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Configure a sua aplicação para aceder ao Azure Storage
Existem duas formas de autenticar a sua aplicação para aceder aos serviços de Armazenamento:

* Chave partilhada: Utilize a chave partilhada apenas para fins de teste
* Assinatura de acesso compartilhado (SAS): Utilizar SAS para aplicações de produção

### <a name="shared-key"></a>Chave Partilhada
A autenticação da Chave Partilhada significa que a sua aplicação utilizará o nome da sua conta e a chave da conta para aceder aos serviços de Armazenamento. Para mostrar rapidamente como usar esta biblioteca, estaremos a usar a autenticação da Chave Partilhada neste início.

> [!WARNING] 
> **Utilize apenas a autenticação da Chave Partilhada para efeitos de teste!** O nome da sua conta e a chave da conta, que dão acesso total à conta de Armazenamento associado, serão distribuídos a todas as pessoas que descarregarem a sua aplicação. Esta **não** é uma boa prática, pois arrisca-se a ter a sua chave comprometida por clientes não fidedquiros.
> 
> 

Ao utilizar a autenticação da Chave Partilhada, criará uma [cadeia de ligação](../articles/storage/common/storage-configure-connection-string.md). A cadeia de ligação é composta por:  

* O **PrescindidoEndpointsProtocol** - pode escolher HTTP ou HTTPS. No entanto, a utilização do HTTPS é altamente recomendada.
* O **Nome da Conta** - o nome da sua conta de armazenamento
* A **Chave de Conta** - No Portal [Azure,](https://portal.azure.com)navegue para a sua conta de armazenamento e clique no ícone **Chaves** para encontrar esta informação.
* (Opcional) **EndpointSfixix** - Este é utilizado para serviços de armazenamento em regiões com diferentes sufixos de ponto final, como a Azure China ou a Azure Governance.

Aqui está um exemplo de cadeia de ligação usando a autenticação da Chave Partilhada:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Assinaturas de Acesso Partilhado (SAS)
Para uma aplicação móvel, o método recomendado para autenticar um pedido de um cliente contra o serviço de Armazenamento Azure é utilizando uma Assinatura de Acesso Partilhado (SAS). O SAS permite-lhe conceder a um cliente acesso a um recurso por um determinado período de tempo, com um conjunto especificado de permissões.
Como proprietário da conta de armazenamento, terá de gerar um SAS para que os seus clientes móveis consumam. Para gerar o SAS, provavelmente irá querer escrever um serviço separado que gere o SAS para ser distribuído aos seus clientes. Para efeitos de testes, pode utilizar o [Microsoft Azure Storage Explorer](https://storageexplorer.com) ou o Portal [Azure](https://portal.azure.com) para gerar um SAS. Ao criar o SAS, pode especificar o intervalo de tempo sobre o qual o SAS é válido e as permissões que o SAS concede ao cliente.

O exemplo a seguir mostra como utilizar o Microsoft Azure Storage Explorer para gerar um SAS.

1. Se ainda não o fez, [instale o Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Estabelecer a ligação à subscrição.
3. Clique na sua conta de Armazenamento e clique no separador "Ações" no canto inferior esquerdo. Clique em "Obter Assinatura de Acesso Partilhado" para gerar uma "cadeia de ligação" para o seu SAS.
4. Aqui está um exemplo de uma cadeia de conexão SAS que concede permissões de leitura e escrita no nível de serviço, recipiente e objeto para o serviço blob da conta de Armazenamento.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Como pode ver, ao utilizar um SAS, não está a expor a chave da sua conta na sua aplicação. Pode aprender mais sobre SAS e as melhores práticas para a utilização do SAS, verificando [assinaturas de acesso partilhado: compreender o modelo SAS.](../articles/storage/common/storage-sas-overview.md)