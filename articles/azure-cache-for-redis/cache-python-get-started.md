---
title: 'Início rápido: criar um aplicativo Python que usa o cache do Azure para Redis'
description: Neste guia de início rápido, você aprende a criar um aplicativo Python que usa o cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/11/2018
ms.author: yegu
ms.custom:
- mvc
- seo-python-october2019
ms.openlocfilehash: 5367896c931bf7c5f52b0874d49ede2fc78614ab
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435518"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Início rápido: criar um aplicativo Python que usa o cache do Azure para Redis

Neste artigo, você incorporará o cache do Azure para Redis em um aplicativo Python para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Python 2 ou 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Criar um cache do Azure para Redis no Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalar redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) é uma interface Python para o cache do Azure para Redis. Utilize a ferramenta de pacotes Python, *pip*, para instalar o pacote redis-py. 

O exemplo a seguir usa *pip3* para Python3 para instalar o pacote Redis-py no Windows 10 usando um prompt de comando do desenvolvedor do Visual Studio 2019 executando com privilégios de administrador elevado.

```python
    pip3 install redis
```

![Instalar a interface Python Redis-py no cache do Azure para Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Ler e escrever na cache

Execute o Python e teste-o com a cache a partir da linha de comandos. Substitua `<Your Host Name>` e `<Your Access Key>` pelos valores para o cache do Azure para Redis. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Para a versão Redis é 3,0 ou superior, a verificação de certificado SSL é imposta. ssl_ca_certs deve ser definido explicitamente ao se conectar ao Redis. No caso do RH Linux, ssl_ca_certs pode ser encontrado no módulo de certificado "/etc/PKI/TLS/certs/CA-Bundle.CRT".

## <a name="create-a-python-script"></a>Criar um script Python

Crie um novo ficheiro de texto de script com o nome *PythonApplication1.py*.

Adicione o seguinte script a *PythonApplication1.py* e guarde o ficheiro. Este script irá testar o acesso da cache. Substitua `<Your Host Name>` e `<Your Access Key>` pelos valores para o cache do Azure para Redis. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
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

Execute o script com o Python.

![Executar script Python para testar o acesso ao cache no cache do Azure para Redis](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para outro tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, pode eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

Inicie sessão no [Portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , insira o nome do seu grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. Em seu grupo de recursos na lista de resultados, selecione **...** em seguida, **excluir grupo de recursos**.

![Excluir o grupo de recursos de início rápido para o cache do Azure para Redis](./media/cache-web-app-howto/delete-your-resource-group-for-azure-cache-for-redis.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Insira o nome do grupo de recursos a ser confirmado e selecione **excluir**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET simples que usa um cache do Azure para Redis.](./cache-web-app-howto.md)

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
