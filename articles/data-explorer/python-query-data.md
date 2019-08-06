---
title: Consultar dados usando a biblioteca do Azure Data Explorer Python
description: Neste artigo, você aprenderá a consultar dados do Azure Data Explorer usando o Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 2c32e67bb2b47a24036a341ea4e1b83037abbaee
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827524"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Consultar dados usando a biblioteca do Azure Data Explorer Python

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer fornece uma [biblioteca de cliente de dados ao Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Esta biblioteca permite-lhe consultar dados a partir do código. Neste artigo, você se conecta a uma tabela no *cluster de ajuda* que configuramos para ajudar a aprender. Em seguida, pode consultar uma tabela nesse cluster e devolver os resultados.

Este artigo também está disponível como um [bloco de anotações do Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail organizacional que seja membro do Azure Active Directory (AAD)

* [Python](https://www.python.org/downloads/) instalado no seu computador de desenvolvimento

## <a name="install-the-data-library"></a>Instalar a biblioteca de dados

Instale *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Adicionar declarações e constantes de importação

Importe classes de biblioteca, bem como o *pandas*, uma biblioteca de análise de dados.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Para autenticar uma aplicação, o Azure Data Explorer utiliza o ID de inquilino do AAD. Para localizar o ID de inquilino, utilize o seguinte URL, substituindo o domínio pelo *SeuDomínio*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, o URL é: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Clique neste URL para ver os resultados; a primeira linha é igual à seguinte.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Neste caso, o ID de inquilino é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Defina o valor para AAD_TENANT_ID antes de executar este código.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

Agora construa a cadeia de ligação. Este exemplo utiliza a autenticação do dispositivo para aceder ao cluster. Você também pode usar o [certificado de aplicativo do AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), a chave de aplicativo do [AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)e o [usuário e a senha do AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Ligar ao Azure Data Explorer e executar uma consulta

Executar uma consulta contra o cluster e armazenar o resultado num pacote de dados. Quando esse código é executado, ele retorna uma mensagem semelhante à seguinte: *Para entrar, use um navegador da Web para abrir a página https://microsoft.com/devicelogin e insira o código F3W4VWZDM para autenticar*. Siga os passos para iniciar sessão e volte para executar o bloco de código seguinte.

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Explorar dados em DataFrame

Depois de introduzir um início de sessão, a consulta devolve resultados e estes são armazenados num pacote de dados. Pode trabalhar com os resultados, tal como faz com qualquer outro pacote de dados.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Deve ver os dez principais resultados da tabela StormEvents.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ingerir dados usando a biblioteca do Azure Data Explorer Python](python-ingest-data.md)
