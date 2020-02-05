---
title: Ligue-se ao Azure Data Explorer a partir de Tijolos de Dados Azure usando Python
description: Este tópico mostra como usar uma biblioteca Python em Azure Databricks para aceder a dados do Azure Data Explorer utilizando um dos dois métodos de autenticação.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985684"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Ligue-se ao Azure Data Explorer a partir de Databricks Azure usando Python

[O Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) é uma plataforma de análise baseada em Apache Spark que está otimizada para a plataforma Microsoft Azure. Este artigo mostra-lhe como usar uma biblioteca Python em Azure Databricks para aceder a dados do Azure Data Explorer. Existem várias formas de autenticar com o Azure Data Explorer, incluindo um login de dispositivo e uma aplicação Azure Ative Directory (Azure AD).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um cluster azure Data Explorer e base de dados.](/azure/data-explorer/create-cluster-database-portal)
- Criar um espaço de [trabalho Azure Databricks.](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace) No **serviço Azure Databricks,** na lista de descida seletiva do **Nível de Preços,** selecione **Premium**. Esta seleção permite-lhe utilizar os segredos da Azure Databricks para armazenar as suas credenciais e referencia-las em cadernos e empregos.

- [Criar um cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) em Tijolos de Dados Azure com as seguintes especificações (definições mínimas necessárias para executar os cadernos de amostras):

   ![Especificações para a criação de um cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Instale a biblioteca Python no seu cluster Azure Databricks

Para instalar a [biblioteca Python](/azure/kusto/api/python/kusto-python-client-library) no seu cluster De Tijolos De Dados Azure:

1. Vá ao seu espaço de trabalho Azure Databricks e [crie uma biblioteca.](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Faça upload de um pacote Python PyPI ou Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Faça upload, instale e prenda a biblioteca ao seu cluster databricks.
   - Introduza o nome PyPi: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Ligue-se ao Azure Data Explorer utilizando um login de dispositivo

[Importar um portátil](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) utilizando o caderno de [login do dispositivo Consulta-ADX.](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) Em seguida, pode ligar-se ao Azure Data Explorer utilizando as suas credenciais.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Ligue-se ao ADX utilizando uma aplicação Azure AD

1. Crie a aplicação Azure AD [através do fornecimento de uma aplicação Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Conceda acesso à sua aplicação Azure AD na sua base de dados do Azure Data Explorer da seguinte forma:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | seu nome de base de dados |
    | ```AAD App ID``` | o seu ID de aplicação Azure AD |
    | ```AAD Tenant ID``` | sua identidade de inquilino Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Encontre a sua identidade de inquilino Azure AD

Para autenticar uma aplicação, o Azure Data Explorer utiliza o seu ID de inquilino Azure AD. Para encontrar a identificação do seu inquilino, use o seguinte URL. Substitua o seu domínio pelo *Seu Domínio.*

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, o URL é: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Selecione este URL para ver os resultados. A primeira linha é a seguinte: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Sua identificação do inquilino é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Armazenar e proteger o id e a chave da aplicação Azure AD 

Guarde e proteja o id e a chave da aplicação Azure AD utilizando [os segredos](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) dos Azure Databricks da seguinte forma:
1. [Configurar o CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Instale o CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Configurar a autenticação](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configure os [segredos](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) utilizando os seguintes comandos de amostra:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importar um caderno
[Importe um caderno](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) utilizando o caderno [Dery-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) para ligar ao Azure Data Explorer. Atualize os valores do espaço reservado com o nome do seu cluster, nome de base de dados e ID de inquilino azure AD.
