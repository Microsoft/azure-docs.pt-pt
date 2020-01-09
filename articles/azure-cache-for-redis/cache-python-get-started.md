---
title: 'Início rápido: criar um aplicativo Python-cache do Azure para Redis'
description: Neste guia de início rápido, você aprende a criar um aplicativo Python que usa o cache do Azure para Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
ms.date: 11/05/2019
ms.openlocfilehash: 7b05f12ad3fd3a0f56605d708bbbf06df7e341ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433473"
---
# <a name="quickstart-create-a-python-app-that-uses-azure-cache-for-redis"></a>Início rápido: criar um aplicativo Python que usa o cache do Azure para Redis

Neste artigo, você incorporará o cache do Azure para Redis em um aplicativo Python para ter acesso a um cache seguro e dedicado que pode ser acessado de qualquer aplicativo no Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Python 2 ou 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Criar um cache do Azure para a instância do Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalar redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) é uma interface Python para o cache do Azure para Redis. Use a ferramenta de pacotes do Python, *Pip*, para instalar o pacote *Redis-py* de um prompt de comando. 

O exemplo a seguir usou *pip3* para Python 3 para instalar o *Redis-py* no Windows 10 a partir de um prompt de comando de administrador.

![Instalar a interface Python Redis-py no cache do Azure para Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Ler e escrever na cache

Execute o Python na linha de comando e teste seu cache usando o código a seguir. Substitua `<Your Host Name>` e `<Your Access Key>` pelos valores de seu cache do Azure para instância Redis. O nome do host está no formato *\<nome DNS >. Redis. cache. Windows. net*.

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
> Para o cache do Azure para Redis versão 3,0 ou superior, a verificação de certificado SSL é imposta. ssl_ca_certs deve ser definido explicitamente ao se conectar ao cache do Azure para Redis. Para o RedHat Linux, ssl_ca_certs estão no módulo de certificado */etc/PKI/TLS/certs/CA-Bundle.CRT* .

## <a name="create-a-python-sample-app"></a>Criar um aplicativo de exemplo do Python

Crie um novo arquivo de texto, adicione o script a seguir e salve o arquivo como *PythonApplication1.py*. Substitua `<Your Host Name>` e `<Your Access Key>` pelos valores de seu cache do Azure para instância Redis. O nome do host está no formato *\<nome DNS >. Redis. cache. Windows. net*.

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

Execute *PythonApplication1.py* com Python. Você deverá ver resultados como o exemplo a seguir:

![Executar script Python para testar o acesso ao cache](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você tiver concluído o grupo de recursos do Azure e os recursos criados neste guia de início rápido, poderá excluí-los para evitar cobranças.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos nele são excluídos permanentemente. Se você criou o cache do Azure para a instância Redis em um grupo de recursos existente que deseja manter, você pode excluir apenas o cache selecionando **excluir** na página **visão geral** do cache. 

Para excluir o grupo de recursos e seu cache Redis para a instância do Azure:

1. Na [portal do Azure](https://portal.azure.com), procure e selecione grupos de **recursos**.
1. Na caixa de texto **Filtrar por nome** , digite o nome do grupo de recursos que contém a instância de cache e, em seguida, selecione-o nos resultados da pesquisa. 
1. Na página do grupo de recursos, selecione **excluir grupo de recursos**.
1. Digite o nome do grupo de recursos e, em seguida, selecione **excluir**.
   
   ![Exclua seu grupo de recursos para o cache do Azure para Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie um aplicativo Web ASP.NET simples que usa um cache do Azure para Redis.](./cache-web-app-howto.md)

