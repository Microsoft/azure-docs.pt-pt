---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184463"
---
O emulador de armazenamento suporta uma única conta fixa e uma chave de autenticação bem conhecida para autenticação de chave partilhada. Esta conta e a chave são as únicas credenciais de chave partilhada saem para utilização com o emulador de armazenamento. São:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação suportada pelo emulador de armazenamento destina-se apenas a testar a funcionalidade do código de autenticação do seu cliente. Não serve a nenhum propósito de segurança. Não é possível utilizar a sua conta de armazenamento de produção e a chave com o emulador de armazenamento. Não deve utilizar a conta de desenvolvimento com dados de produção.
> 
> O emulador de armazenamento suporta a ligação apenas através de HTTP. No entanto, https é o protocolo recomendado para o acesso de recursos numa conta de armazenamento Azure de produção.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ligue-se à conta do emulador utilizando um atalho
A forma mais fácil de ligar ao emulador de armazenamento da sua aplicação é configurar `UseDevelopmentStorage=true`uma cadeia de ligação no ficheiro de configuração da sua aplicação que faz referência ao atalho . Aqui está um exemplo de uma cadeia de ligação ao emulador de armazenamento num ficheiro *app.config:* 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Ligue-se à conta emulador utilizando o nome e a chave bem conhecidos da conta
Para criar uma cadeia de ligação que refira o nome e a chave da conta do emulador, deve especificar os pontos finais de cada um dos serviços que pretende utilizar a partir do emulador na cadeia de ligação. Isto é necessário para que a cadeia de ligação refira os pontos finais do emulador, que são diferentes dos de uma conta de armazenamento de produção. Por exemplo, o valor da sua cadeia de ligação será assim:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Este valor é idêntico ao atalho `UseDevelopmentStorage=true`acima indicado, .

#### <a name="specify-an-http-proxy"></a>Especifique um proxy HTTP
Também pode especificar um proxy HTTP a utilizar quando estiver a testar o seu serviço contra o emulador de armazenamento. Isto pode ser útil para observar pedidos e respostas http enquanto você está depurando operações contra os serviços de armazenamento. Para especificar um proxy, adicione a opção `DevelopmentStorageProxyUri` à cadeia de ligação e detete o seu valor para o proxy URI. Por exemplo, aqui está uma cadeia de ligação que aponta para o emulador de armazenamento e confunde um proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

