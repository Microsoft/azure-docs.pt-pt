---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582597"
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

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ligue-se à conta do emulador usando um atalho
A forma mais fácil de ligar ao emulador da sua aplicação é configurar uma cadeia de ligação no ficheiro de configuração da sua aplicação que faz referência ao atalho `UseDevelopmentStorage=true` . Aqui está um exemplo de uma cadeia de ligação ao emulador num ficheiro *app.config:* 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

O equivalente a especificar totalmente o nome da conta, a chave da conta e os pontos finais de cada um dos serviços de emulador que pretende utilizar na cadeia de ligação. Isto é necessário para que a cadeia de ligação refira os pontos finais do emulador, que são diferentes dos de uma conta de armazenamento de produção. Por exemplo, o valor da sua cadeia de ligação será assim:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
