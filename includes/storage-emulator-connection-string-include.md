---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114665"
---
O emulador de armazenamento suporta uma única conta fixa e uma chave de autenticação bem conhecidos para autenticação de chave partilhada. Esta conta e chave são as credenciais de chave partilhada apenas permitidas para utilização com o emulador de armazenamento. São:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação suportada pelo emulador de armazenamento destina-se apenas para testar a funcionalidade do seu código de autenticação de cliente. Ele não serve qualquer finalidade de segurança. Não é possível utilizar a conta de armazenamento de produção e a chave com o emulador de armazenamento. Não deve utilizar a conta de desenvolvimento com dados de produção.
> 
> O emulador de armazenamento suporta a ligação através de HTTP apenas. No entanto, o HTTPS é o protocolo recomendado para aceder a recursos numa conta de armazenamento do Azure de produção.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ligar à conta do emulador usando um atalho
A maneira mais fácil para ligar para o emulador de armazenamento da sua aplicação consiste em configurar uma cadeia de ligação no ficheiro de configuração de seu aplicativo que referencia o atalho `UseDevelopmentStorage=true`. Eis um exemplo de uma cadeia de ligação para o emulador de armazenamento num *App. config* ficheiro: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Ligar à conta do emulador com o nome de conta bem conhecidos e a chave
Para criar uma cadeia de ligação que referencia o nome da conta do emulador e a chave, tem de especificar os pontos de extremidade para cada um dos serviços que deseja usar do emulador na cadeia de ligação. Isso é necessário para que a cadeia de ligação fará referência os pontos de extremidade do emulador, que são diferentes para uma conta de armazenamento de produção. Por exemplo, o valor da cadeia de ligação ficará assim:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Este valor é idêntico ao atalho mostrado acima, `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Especifique um proxy de HTTP
Também pode especificar um proxy de HTTP para utilizar quando está a testar o seu serviço contra o emulador de armazenamento. Isso pode ser útil para observar solicitações e respostas HTTP, enquanto estiver a depurar operações de acordo com os serviços de armazenamento. Para especificar um proxy, adicione o `DevelopmentStorageProxyUri` a opção de cadeia de ligação e defina seu valor para o URI de proxy. Por exemplo, aqui está uma cadeia de ligação que aponta para o emulador de armazenamento e configura um proxy de HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

