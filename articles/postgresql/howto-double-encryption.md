---
title: Infraestrutura dupla encriptação - Portal Azure - Base de Dados Azure para PostgreSQL
description: Saiba como configurar e gerir a encriptação dupla da Infraestrutura para a sua Base de Dados de Azure para PostgreSQL.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 362c051cf1dd7e97430bd6afaf4821a9c960b71d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90901539"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Encriptação dupla de infraestrutura para Azure Database for PostgreSQL

Saiba como configurar e gerir a encriptação dupla da Infraestrutura para a sua Base de Dados Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter uma subscrição Azure e ser um administrador nessa subscrição.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Criar uma base de dados Azure para servidor PostgreSQL com infraestrutura dupla encriptação - Portal

Siga estes passos para criar uma Base de Dados Azure para o servidor MySQL com encriptação dupla infraestrutura a partir do portal Azure:

1. **Selecione Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Base de Dados**  >  **Azure Databases para PostgreSQL**. Também pode inserir PostgreSQL na caixa de pesquisa para encontrar o serviço. Ativou a opção de implementação **do servidor único.**

   :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="A Base de Dados Azure para PostgreSQL no menu":::

3. Forneça as informações básicas do servidor. Selecione **definições adicionais** e permitiu que a caixa **de verificação de encriptação dupla infraestrutura** definisse o parâmetro.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png" alt-text="A Base de Dados Azure para PostgreSQL no menu":::

4. Selecione **Review + criar** para obter o servidor.

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png" alt-text="A Base de Dados Azure para PostgreSQL no menu":::

5. Uma vez criado o servidor, pode validar a dupla encriptação da infraestrutura verificando o estado na lâmina do servidor **de encriptação de Dados.**

    :::image type="content" source="./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png" alt-text="A Base de Dados Azure para PostgreSQL no menu":::

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Criar uma base de dados Azure para servidor PostgreSQL com infraestrutura dupla encriptação - CLI

Siga estes passos para criar uma Base de Dados Azure para o servidor MySQL com encriptação dupla infraestrutura a partir de CLI:

Este exemplo cria um grupo de recursos nomeado `myresourcegroup` no `westus` local.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
O exemplo a seguir cria um servidor PostgreSQL 11 em West US nomeado `mydemoserver` no seu grupo de recursos com login de `myresourcegroup` administração de `myadmin` servidores . Este é um servidor **Ger 4 de ** **Fins Gerais** com **2 vCores**. Isto também irá permitir a dupla encriptação de infraestrutura para o servidor criado. Substitua `<server_admin_password>` pelo seu próprio valor.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre encriptação de dados, consulte [a Base de Dados Azure para a infraestrutura de dados PostgreSQL.](concepts-Infrastructure-double-encryption.md)

