---
title: 'Quickstart: Criar uma app Python - Azure Cache for Redis'
description: Neste arranque rápido, aprende-se a criar uma App Python que utiliza o Azure Cache para redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
ms.date: 11/05/2019
ms.openlocfilehash: 6ce3dd1cbb694988af3555765342a1c4ca3850b5
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010856"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Quickstart: Criar uma app Python que usa Azure Cache para Redis

Neste artigo, incorpora o Azure Cache for Redis numa aplicação Python para ter acesso a uma cache segura e dedicada que é acessível a partir de qualquer aplicação dentro do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Python 2 ou 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar um Cache Azure para o caso Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalar redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) é uma interface Python para Azure Cache para Redis. Utilize a ferramenta de embalagem Python, *pip,* para instalar a embalagem *redis-py* a partir de um pedido de comando. 

O exemplo seguinte utilizou *pip3* para Python 3 para instalar *redis-py* no Windows 10 a partir de um pedido de comando administrador.

![Instale a interface Python redis-py para Azure Cache para Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Ler e escrever na cache

Execute python a partir da linha de comando e teste a sua cache utilizando o seguinte código. `<Your Host Name>` Substitua `<Your Access Key>` e com os valores do seu Cache Azure para a instância Redis. O nome do anfitrião é do * \<nome DNS>.redis.cache.windows.net*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Para o Azure Cache para a versão 3.0 ou superior do Redis, a verificação de certificados TLS/SSL é executada. ssl_ca_certs deve ser explicitamente definido quando ligar a Azure Cache para Redis. Para o RedHat Linux, ssl_ca_certs estão no módulo de certificado */etc/pki/tls/certs/ca-bundle.crt.*

## <a name="create-a-python-sample-app"></a>Criar uma aplicação de amostra Python

Crie um novo ficheiro de texto, adicione o seguinte script e guarde o ficheiro como *PythonApplication1.py*. `<Your Host Name>` Substitua `<Your Access Key>` e com os valores do seu Cache Azure para a instância Redis. O nome do anfitrião é do * \<nome DNS>.redis.cache.windows.net*.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Corre PythonApplication1.py com *o* Python. Deve ver resultados como o seguinte exemplo:

![Executar roteiro Python para testar acesso à cache](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se terminar com o grupo de recursos Azure e os recursos que criou neste arranque rápido, pode eliminá-los para evitar encargos.

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos nele são permanentemente eliminados. Se criou o seu Cache Azure para Redis num grupo de recursos existente que pretende manter, pode eliminar apenas a cache selecionando **Delete** a partir da página de cloud **Overview.** 

Para eliminar o grupo de recursos e o seu Redis Cache por exemplo azure:

1. A partir do [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.**
1. Na caixa de texto **Filter by name,** introduza o nome do grupo de recursos que contém a sua instância de cache e, em seguida, selecione-o a partir dos resultados da pesquisa. 
1. Na página do grupo de recursos, selecione **Eliminar o grupo de recursos**.
1. Digite o nome do grupo de recursos e, em seguida, **selecione Delete**.
   
   ![Elimine o seu grupo de recursos para Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie uma aplicação web simples ASP.NET que utilize um Azure Cache para Redis.](./cache-web-app-howto.md)

