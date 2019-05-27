---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115509"
---
Se possui um URL de assinatura (SAS) de acesso partilhado que concede acesso a recursos numa conta de armazenamento, pode utilizar a SAS numa cadeia de ligação. Uma vez que a SAS contém as informações necessárias para autenticar o pedido, uma cadeia de ligação com uma SAS permite o protocolo, o ponto final de serviço e as credenciais necessárias para aceder ao recurso.

Para criar uma cadeia de ligação que inclui uma assinatura de acesso partilhado, especifique a cadeia de caracteres no seguinte formato:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Cada ponto de extremidade de serviço é opcional, embora a cadeia de ligação tem de conter, pelo menos, um.

> [!NOTE]
> Através de HTTPS com uma SAS é recomendado como melhor prática.
>
> Se estiver especificando uma SAS numa cadeia de ligação num ficheiro de configuração, terá de codificar os carateres especiais no URL.
>
>

### <a name="service-sas-example"></a>Exemplo de SAS de serviço
Eis um exemplo de uma cadeia de ligação que inclui um serviço SAS para armazenamento de BLOBs:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

E aqui está um exemplo da mesma cadeia de ligação com codificação de carateres especiais:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Exemplo SAS de conta
Eis um exemplo de uma cadeia de ligação que inclui uma SAS de conta para o armazenamento de BLOBs e ficheiros. Tenha em atenção que os pontos finais para ambos os serviços são especificados:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

E aqui está um exemplo da mesma cadeia de ligação com a codificação do URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

