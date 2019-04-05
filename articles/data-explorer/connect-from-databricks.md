---
title: Ligar ao Explorador de dados do Azure a partir do Azure Databricks, com o Python
description: Este tópico mostra-lhe como utilizar uma biblioteca de Python no Azure Databricks para aceder a dados do Explorador de dados do Azure utilizando um dos dois métodos de autenticação.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 55257d441916971b505432247f28033d6222c3be
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047760"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Ligar ao Explorador de dados do Azure a partir do Azure Databricks, com o Python

[O Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) é uma plataforma de análise baseada no Apache Spark otimizado para a plataforma Microsoft Azure. Este artigo mostra-lhe como utilizar uma biblioteca de Python no Azure Databricks para aceder a dados a partir do Explorador de dados do Azure. Existem várias formas de autenticar com o Explorador de dados do Azure, incluindo um início de sessão do dispositivo e uma aplicação do Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um cluster do Explorador de dados do Azure e a base de dados](/azure/data-explorer/create-cluster-database-portal).
- [Criar uma área de trabalho do Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Sob **serviço Azure Databricks**, na **escalão de preço** na lista pendente, selecione **Premium**. Esta seleção permite-lhe utilizar o Azure Databricks segredos para armazenar as suas credenciais e referenciá-los em blocos de notas e tarefas.

- [Criar um cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) no Azure Databricks com as seguintes especificações (configurações mínimas necessárias para executar os blocos de notas do exemplo):

   ![Especificações para criar um cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Instalar a biblioteca de Python no seu cluster do Azure Databricks

Para instalar o [biblioteca de Python](/azure/kusto/api/python/kusto-python-client-library) no seu cluster do Azure Databricks:

1. Aceda à sua área de trabalho do Azure Databricks e [criar uma biblioteca](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Carregar um pacote do Python PyPI ou Python ovo](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Carregar, instalar e anexar biblioteca ao seu cluster do Databricks.
   - Introduza o nome de PyPi: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Ligar ao Explorador de dados do Azure com um início de sessão do dispositivo

[Importar um bloco de notas](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) utilizando o [consulta-ADX-dispositivo-início de sessão](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) bloco de notas. Em seguida, pode ligar ao Azure Data Explorer com as suas credenciais.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Ligar ao ADX com uma aplicação do Azure AD

1. Criar aplicação do Azure AD por [aprovisionamento de uma aplicação do Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Conceder acesso à sua aplicação do Azure AD na sua base de dados do Explorador de dados do Azure da seguinte forma:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | nome da sua base de dados |
    | ```AAD App ID``` | o ID da aplicação do Azure AD |
    | ```AAD Tenant ID``` | o ID de inquilino do Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Encontrar o seu ID de inquilino do Azure AD

Para autenticar uma aplicação, o Explorador de dados do Azure utiliza o ID de inquilino do Azure AD. Para localizar o ID de inquilino, utilize o seguinte URL. Substitua o seu domínio para *oseudomínio*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, o URL é: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Selecione este URL para ver os resultados. A primeira linha é o seguinte: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

O inquilino ID é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store e proteger os seus ID da aplicação do Azure AD e a chave 

Store e proteger o ID da aplicação do Azure AD e a chave com o Azure Databricks [segredos](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) da seguinte forma:
1. [Configurar a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Instalar a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Configurar a autenticação](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configurar o [segredos](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) utilizando os comandos de exemplo seguinte:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importar um bloco de notas
[Importar um bloco de notas](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) utilizando o [consulta-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) bloco de notas para ligar ao Explorador de dados do Azure. Atualize os valores de marcador de posição com seu nome do cluster, o nome de base de dados e o ID do inquilino do Azure AD.
