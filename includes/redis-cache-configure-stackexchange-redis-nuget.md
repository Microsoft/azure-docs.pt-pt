---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8ebf5ddfa118e0aeadeab0c00a981871a4b5708e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132895"
---
As aplicações .NET podem utilizar o cliente de cache **StackExchange.Redis**, que poderá ser configurado no Visual Studio com um pacote NuGet que simplifica a configuração das aplicações cliente de cache. 

> [!NOTE]
> Para obter mais informações, consulte a [stackexchange. redis](http://github.com/StackExchange/StackExchange.Redis) página do GitHub e o [StackExchange.Azure Cache para a documentação do cliente Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Para configurar uma aplicação cliente no Visual Studio com o pacote NuGet StackExchange.Redis, clique com o botão direito do rato no projeto em **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**. 

![Gerir pacotes NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Introduza **StackExchange.Redis** ou **StackExchange.Redis.StrongName** na caixa de texto de pesquisa, selecione a versão pretendida apresentada nos resultados e clique em **Instalar**.

> [!NOTE]
> Se preferir utilizar uma versão da biblioteca de clientes **StackExchange.Redis** com nome seguro, escolha **StackExchange.Redis.StrongName**; caso contrário, escolha **StackExchange.Redis**.
>
>

![Pacote NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias para a sua aplicação de cliente acessar o Cache do Azure para Redis com a Cache de StackExchange.Azure para cliente do Redis.

> [!NOTE]
> Se configurou anteriormente o seu projeto para utilizar StackExchange.Redis, pode procurar atualizações para o pacote a partir do **Gestor de Pacotes NuGet**. Para procurar e instalar versões atualizadas do pacote NuGet do stackexchange. redis, clique em **atualizações** no **Gestor de pacotes NuGet** janela. Se estiver disponível uma atualização do pacote NuGet do StackExchange.Redis, pode atualizar o seu projeto para utilizar a versão atualizada.
>
>

Também é possível instalar o pacote de StackExchange.Redis NuGet clicando em **Gestor de Pacotes NuGet**, **Gestor de Pacotes** a partir do menu **Ferramentas** e executar o seguinte comando a partir da janela **Consola do Gestor de Pacotes**.

```
Install-Package StackExchange.Redis
```
