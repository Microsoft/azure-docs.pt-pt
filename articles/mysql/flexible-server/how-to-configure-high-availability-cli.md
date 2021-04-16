---
title: Gerir zona redundante alta disponibilidade - Azure CLI - Azure Database for MySQL Flexible Server
description: Este artigo descreve como configurar zona redundante alta disponibilidade em Azure Database for MySQL flexible Server com o Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509147"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Gerir zona redundante alta disponibilidade em Azure Database para MySQL Flexible Server com Azure CLI

> [!NOTE]
> A base de dados Azure para o MySQL Flexible Server está em pré-visualização pública. 

O artigo descreve como pode ativar ou desativar a configuração de alta disponibilidade redundante na altura da criação do servidor no seu servidor flexível. Também pode desativar a zona redundante de alta disponibilidade após a criação do servidor. Permitir a zona redundante alta disponibilidade após a criação do servidor não é suportado.

As disposições relativas à funcionalidade de alta disponibilidade separam fisicamente a réplica primária e de espera em diferentes zonas. Para mais informações, consulte [a documentação de conceitos de alta disponibilidade.](./concepts/../concepts-high-availability.md) Ativar ou desativar uma elevada disponibilidade não altera as outras definições, incluindo a configuração VNET, as definições de firewall e a retenção de backup. A desativação de alta disponibilidade não afeta a conectividade e operações da sua aplicação.

> [!IMPORTANT]
> Zona redundante alta disponibilidade está disponível em conjunto limitado de regiões. Por favor, reveja as regiões apoiadas [aqui.](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) 

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instale ou atualize o Azure CLI para a versão mais recente. Consulte [a instalação do Azure CLI](/cli/azure/install-azure-cli).
-  Faça login na conta Azure utilizando o comando [de login az.](/cli/azure/reference-index#az-login) Note a propriedade **id,** que se refere ao **ID de subscrição** para a sua conta Azure.

    ```azurecli-interactive
    az login
    ````

- Se tiver várias subscrições, escolha a subscrição apropriada na qual pretende criar o servidor utilizando o ```az account set``` comando.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>Ativar alta disponibilidade durante a criação do servidor
Só é possível criar servidor utilizando níveis de preços otimizados para fins gerais ou memória com elevada disponibilidade. Pode ativar uma elevada disponibilidade para um servidor apenas durante o tempo de criação.

**Utilização:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Exemplo:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Desativar a alta disponibilidade

Pode desativar a alta disponibilidade utilizando o comando [de atualização flexível do servidor az mysql.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) Note que desativar a alta disponibilidade só é suportado se o servidor foi criado com alta disponibilidade. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Exemplo:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
