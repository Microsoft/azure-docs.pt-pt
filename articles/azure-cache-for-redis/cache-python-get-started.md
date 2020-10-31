---
title: 'Quickstart: Use Azure Cache para Redis em Python'
description: Neste quickstart, você aprende a criar uma App Python que usa Azure Cache para Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.date: 11/05/2019
ms.openlocfilehash: 79add54478e9cd52ffd3041d5f85bf588929b44f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099441"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Quickstart: Use Azure Cache para Redis em Python

Neste artigo, incorpora o Azure Cache for Redis numa aplicação Python para ter acesso a uma cache segura e dedicada que esteja acessível a partir de qualquer aplicação dentro do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Python 2 ou 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar uma Cache Azure para a instância Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalar redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) é uma interface Python para Azure Cache para Redis. Utilize a ferramenta de embalagem Python, *pip,* para instalar o pacote *redis-py* a partir de uma indicação de comando. 

O exemplo seguinte utilizou *o pip3* para python 3 para instalar *redis-py* no Windows 10 a partir de uma solicitação de comando do Administrador.

![Instale a interface python redis-py em Azure Cache para Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Ler e escrever na cache

Executar Python a partir da linha de comando e testar o seu cache utilizando o seguinte código. Substitua `<Your Host Name>` e `<Your Access Key>` pelos valores da sua Cache Azure para a instância Redis. O seu nome de anfitrião é do formulário *\<DNS name> .redis.cache.windows.net* .

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
> Para a cache Azure para a versão 3.0 ou superior, é aplicada a verificação do certificado TLS/SSL. ssl_ca_certs deve ser explicitamente definido ao ligar a Cache Azure para redis. Para o RedHat Linux, ssl_ca_certs estão no módulo de certificado */etc/pki/tls/certs/ca-bundle.crt.*

## <a name="create-a-python-sample-app"></a>Criar uma aplicação de amostra python

Crie um novo ficheiro de texto, adicione o seguinte script e guarde o ficheiro como *PythonApplication1.py* . Substitua `<Your Host Name>` e `<Your Access Key>` pelos valores da sua Cache Azure para a instância Redis. O seu nome de anfitrião é do formulário *\<DNS name> .redis.cache.windows.net* .

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

Corre *PythonApplication1.py* com o Python. Deve ver resultados como o seguinte exemplo:

![Executar script Python para testar o acesso à cache](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se terminar com o grupo de recursos Azure e os recursos que criou neste quickstart, pode eliminá-los para evitar encargos.

> [!IMPORTANT]
> A supressão de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele eliminados. Se criou o seu Azure Cache para a instância Redis num grupo de recursos existente que pretende manter, pode eliminar apenas a cache selecionando **Eliminar** a partir da página **de visão geral** da cache. 

Para eliminar o grupo de recursos e a sua Cache Redis para a instância Azure:

1. A partir do [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.**
1. Na caixa de texto **Filter por nome,** insira o nome do grupo de recursos que contém a sua instância de cache e, em seguida, selecione-o a partir dos resultados da pesquisa. 
1. Na sua página de grupo de recursos, selecione **Eliminar o grupo de recursos** .
1. Digite o nome do grupo de recursos e, em seguida, **selecione Delete** .
   
   ![Elimine o seu grupo de recursos para Azure Cache para Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie uma simples aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)

