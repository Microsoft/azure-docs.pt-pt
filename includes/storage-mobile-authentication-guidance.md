---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67673473"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Configure a sua aplicação para aceder ao Armazenamento Azure
Existem duas formas de autenticar a sua aplicação de acesso aos serviços de armazenamento:

* Chave partilhada: Utilize a chave partilhada apenas para fins de teste
* Assinatura de Acesso Partilhado (SAS): Utilizar sas para aplicações de produção

### <a name="shared-key"></a>Chave Partilhada
A autenticação da Chave Partilhada significa que a sua aplicação utilizará o nome da sua conta e chave de conta para aceder aos serviços de armazenamento. Para mostrar rapidamente como usar esta biblioteca, estaremos a usar a autenticação Da Chave Partilhada neste início.

> [!WARNING] 
> **Utilize apenas a autenticação de chave partilhada para fins de teste!** O nome da sua conta e chave de conta, que dão acesso completo à conta de Armazenamento associada, serão distribuídos a todas as pessoas que descarregam a sua app. Esta **não** é uma boa prática, pois arrisca-se a ter a sua chave comprometida por clientes não confiáveis.
> 
> 

Ao utilizar a autenticação da chave partilhada, criará uma cadeia de [ligação](../articles/storage/common/storage-configure-connection-string.md). A cadeia de ligação é composta por:  

* O **DefaultEndpointsProtocol** - pode escolher HTTP ou HTTPS. No entanto, a utilização de HTTPS é altamente recomendada.
* O **Nome da Conta** - o nome da sua conta de armazenamento
* A **Chave de Conta** - No Portal [Azure,](https://portal.azure.com)navegue para a sua conta de armazenamento e clique no ícone **Keys** para encontrar esta informação.
* (Opcional) **EndpointSuffix** - Este é utilizado para serviços de armazenamento em regiões com diferentes sufixos finais, como a Azure China ou a Azure Governance.

Aqui está um exemplo de cadeia de ligação usando a autenticação de chave partilhada:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Assinaturas de Acesso Partilhado (SAS)
Para uma aplicação móvel, o método recomendado para autenticar um pedido de um cliente contra o serviço de armazenamento Azure é utilizando uma Assinatura de Acesso Partilhado (SAS). A SAS permite-lhe conceder a um cliente acesso a um recurso durante um determinado período de tempo, com um conjunto de permissões especificado.
Como proprietário da conta de armazenamento, terá de gerar um SAS para os seus clientes móveis consumirem. Para gerar o SAS, provavelmente quererá escrever um serviço separado que gere o SAS a ser distribuído aos seus clientes. Para efeitos de teste, pode utilizar o [Microsoft Azure Storage Explorer](https://storageexplorer.com) ou o Portal [Azure](https://portal.azure.com) para gerar um SAS. Ao criar o SAS, pode especificar o intervalo de tempo sobre o qual o SAS é válido, e as permissões que o SAS concede ao cliente.

O exemplo que se segue mostra como usar o Microsoft Azure Storage Explorer para gerar um SAS.

1. Se ainda não o fez, [instale o Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Estabelecer a ligação à subscrição.
3. Clique na sua conta de Armazenamento e clique no separador "Ações" na parte inferior à esquerda. Clique em "Obter Assinatura de Acesso Partilhado" para gerar uma "cadeia de ligação" para o seu SAS.
4. Aqui está um exemplo de uma cadeia de ligação SAS que concede permissões de leitura e escrita no nível de serviço, contentor e objeto para o serviço de blob da conta de Armazenamento.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Como pode ver, ao utilizar um SAS, não está a expor a chave da sua conta na sua aplicação. Pode saber mais sobre o SAS e as melhores práticas para utilizar o SAS, verificando as Assinaturas de [Acesso Partilhado: Compreender o modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

