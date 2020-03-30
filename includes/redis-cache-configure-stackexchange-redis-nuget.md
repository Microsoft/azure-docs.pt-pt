---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68286254"
---
As aplicações .NET podem utilizar o cliente de cache **StackExchange.Redis**, que poderá ser configurado no Visual Studio com um pacote NuGet que simplifica a configuração das aplicações cliente de cache. 

> [!NOTE]
> Para mais informações, consulte a página [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) GitHub e o [StackExchange.Azure Cache para obter documentação](https://github.com/StackExchange/StackExchange.Redis#documentation)do cliente Redis .
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

O pacote NuGet descarrega e adiciona as referências de montagem necessárias para a sua aplicação de cliente aceder ao Azure Cache para Redis com o StackExchange.Azure Cache para o cliente Redis.

> [!NOTE]
> Se configurou anteriormente o seu projeto para utilizar StackExchange.Redis, pode procurar atualizações para o pacote a partir do **Gestor de Pacotes NuGet**. Para verificar e instalar versões atualizadas do pacote StackExchange.Redis NuGet, clique em **Atualizações** na janela **NuGet Package Manager.** Se estiver disponível uma atualização do pacote NuGet do StackExchange.Redis, pode atualizar o seu projeto para utilizar a versão atualizada.
>
>

Também é possível instalar o pacote de StackExchange.Redis NuGet clicando em **Gestor de Pacotes NuGet**, **Gestor de Pacotes** a partir do menu **Ferramentas** e executar o seguinte comando a partir da janela **Consola do Gestor de Pacotes**.

```
Install-Package StackExchange.Redis
```
