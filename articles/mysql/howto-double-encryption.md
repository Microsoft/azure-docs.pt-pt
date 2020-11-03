---
title: Infraestrutura dupla encriptação - Portal Azure - Base de Dados Azure para MySQL
description: Saiba como configurar e gerir a encriptação dupla da Infraestrutura para a sua Base de Dados Azure para o MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 3f9c22a690859b459b6bb748c3b1001c4aa7660d
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241757"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Infraestrutura dupla encriptação para Azure Database para MySQL

Saiba como configurar e gerir a encriptação dupla da Infraestrutura para a sua Base de Dados Azure para o MySQL.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter uma subscrição Azure e ser um administrador nessa subscrição.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Criar uma base de dados Azure para servidor MySQL com infraestrutura dupla encriptação - Portal

Siga estes passos para criar uma Base de Dados Azure para o servidor MySQL com encriptação dupla infraestrutura a partir do portal Azure:

1. **Selecione Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Base de Dados**  >  **Azure Databases para o MySQL** . Também pode inserir **o MySQL** na caixa de pesquisa para encontrar o serviço.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Base de Dados do Azure para opção MySQL":::

3. Forneça as informações básicas do servidor. Selecione **definições adicionais** e permitiu que a caixa **de verificação de encriptação dupla infraestrutura** definisse o parâmetro.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Base de Dados do Azure para opção MySQL":::

4. Selecione **Review + criar** para obter o servidor.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Base de Dados do Azure para opção MySQL":::

5. Uma vez criado o servidor, pode validar a dupla encriptação da infraestrutura verificando o estado na lâmina do servidor **de encriptação de Dados.**

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Base de Dados do Azure para opção MySQL":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Criar uma base de dados Azure para servidor MySQL com infraestrutura dupla encriptação - CLI

Siga estes passos para criar uma Base de Dados Azure para o servidor MySQL com encriptação dupla infraestrutura a partir de CLI:

Este exemplo cria um grupo de recursos nomeado `myresourcegroup` no `westus` local.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
O exemplo seguinte cria um servidor MySQL 5.7 em E.U.A. Oeste com o nome `mydemoserver` no seu grupo de recursos `myresourcegroup`, com o início de sessão de administrador do servidor `myadmin`. Este é um servidor **Ger 4 de** **Fins Gerais** com **2 vCores** . Isto também irá permitir a dupla encriptação de infraestrutura para o servidor criado. Substitua `<server_admin_password>` pelo seu próprio valor.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Passos seguintes

 Para saber mais sobre encriptação de dados, consulte [a Base de Dados Azure para a infraestrutura de dados MySQL.](concepts-Infrastructure-double-encryption.md)
