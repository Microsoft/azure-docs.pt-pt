---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070540"
---
A Azurite suporta uma única conta fixa e uma chave de autenticação bem conhecida para a autenticação da Chave Partilhada. Esta conta e chave são as únicas credenciais de Chave Partilhada permitidas para uso com a Azurite. A saber:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação suportada pela Azurite destina-se apenas a testar a funcionalidade do código de autenticação do seu cliente. Não serve para qualquer propósito de segurança. Não pode utilizar a sua conta de armazenamento de produção e a chave com a Azurite. Não deve utilizar a conta de desenvolvimento com dados de produção.
> 
> A Azurite suporta a ligação apenas via HTTP. No entanto, HTTPS é o protocolo recomendado para aceder a recursos numa conta de armazenamento Azure de produção.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ligue-se à conta do emulador usando um atalho
A forma mais fácil de ligar ao Azurite da sua aplicação é configurar uma cadeia de ligação no ficheiro de configuração da sua aplicação que faz referência ao atalho `UseDevelopmentStorage=true` . Aqui está um exemplo de uma ligação a Azurite num ficheiro *app.config:* 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Ligue-se à conta do emulador usando o nome e chave de conta bem conhecidos
Para criar uma cadeia de ligação que faça referência ao nome e tecla da conta do emulador, deve especificar os pontos finais de cada um dos serviços que pretende utilizar a partir do emulador na cadeia de ligação. Isto é necessário para que a cadeia de ligação refira os pontos finais do emulador, que são diferentes dos de uma conta de armazenamento de produção. Por exemplo, o valor da sua cadeia de ligação será assim:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Este valor é idêntico ao atalho acima `UseDevelopmentStorage=true` indicado.
