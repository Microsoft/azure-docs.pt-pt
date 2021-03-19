---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184477"
---
Se possuir um URL de assinatura de acesso partilhado (SAS) que lhe dá acesso a recursos numa conta de armazenamento, pode utilizar o SAS numa cadeia de ligação. Como o SAS contém as informações necessárias para autenticar o pedido, uma cadeia de ligação com um SAS fornece o protocolo, o ponto final de serviço e as credenciais necessárias para aceder ao recurso.

Para criar uma cadeia de ligação que inclua uma assinatura de acesso partilhado, especifique a cadeia no seguinte formato:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Cada ponto final de serviço é opcional, embora a cadeia de ligação contenha pelo menos um.

> [!NOTE]
> A utilização de HTTPS com um SAS é recomendada como uma boa prática.
>
> Se estiver a especificar um SAS numa cadeia de ligação num ficheiro de configuração, poderá ter de codificar caracteres especiais no URL.
>
>

### <a name="service-sas-example"></a>Serviço EXEMPLO SAS
Aqui está um exemplo de uma cadeia de conexão que inclui um serviço SAS para armazenamento Blob:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

E aqui está um exemplo da mesma cadeia de ligação com a codificação de caracteres especiais:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Exemplo DE CONTA SAS
Aqui está um exemplo de uma cadeia de ligação que inclui uma conta SAS para armazenamento de blob e arquivo. Note que os pontos finais para ambos os serviços são especificados:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

E aqui está um exemplo da mesma cadeia de ligação com a codificação de URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

