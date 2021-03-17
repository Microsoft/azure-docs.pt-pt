---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622206"
---
O emulador suporta uma única conta fixa e uma chave de autenticação bem conhecida para a autenticação da Chave Partilhada. Esta conta e chave são as únicas credenciais de Chave Partilhada permitidas para utilização com o emulador. A saber:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação suportada pelo emulador destina-se apenas a testar a funcionalidade do código de autenticação do seu cliente. Não serve para qualquer propósito de segurança. Não é possível utilizar a sua conta de armazenamento de produção e a chave com o emulador. Não deve utilizar a conta de desenvolvimento com dados de produção.
>
> O emulador suporta a ligação apenas através de HTTP. No entanto, HTTPS é o protocolo recomendado para aceder a recursos numa conta de armazenamento Azure de produção.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Ligue-se à conta do emulador utilizando o atalho

A forma mais fácil de ligar ao emulador da sua aplicação é configurar uma cadeia de ligação no ficheiro de configuração da sua aplicação que faz referência ao atalho `UseDevelopmentStorage=true` . O atalho é equivalente ao fio de ligação completo do emulador, que especifica o nome da conta, a chave da conta e os pontos finais do emulador para cada um dos serviços de Armazenamento Azure:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

O seguinte corte de código .NET mostra como pode utilizar o atalho a partir de um método que toma uma cadeia de ligação. Por exemplo, o [construtor BlobContainerClient (String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) toma uma corda de ligação.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Certifique-se de que o emulador está em funcionamento antes de ligar para o código no corte.
