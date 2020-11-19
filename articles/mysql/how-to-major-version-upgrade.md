---
title: Atualização de versão principal - Portal Azure - Base de Dados Azure para MySQL - Servidor Único
description: Este artigo descreve como pode atualizar a versão principal para Azure Database para MySQL - Single Server usando o portal Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4dd4729589e429cb1b028b183fdfd144617d1d1b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920649"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Atualização principal da versão em Azure Database para MySQL Single Server usando o portal Azure

> [!IMPORTANT]
> A atualização da versão principal para a base de dados Azure para o MySQL Single Server está em pré-visualização pública.

Este artigo descreve como pode atualizar a sua versão principal do MySQL no local na Base de Dados Azure para o servidor único MySQL.

Esta funcionalidade permitirá que os clientes realizem atualizações in-place dos seus servidores MySQL 5.6 para o MySQL 5.7 com um clique de botão sem qualquer movimento de dados ou a necessidade de alterações nas cordas de ligação da aplicação.

> [!Note]
> * A atualização da versão principal só está disponível para atualização de versão principal do MySQL 5.6 para MySQL 5.7<br>
> * A atualização da versão principal ainda não está suportada no servidor de réplicas.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o MySQL Single Server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Execute a atualização da versão principal do MySQL 5.6 para MySQL 5.7

Siga estes passos para realizar a atualização da versão principal para o seu Azure Database do servidor MySQL 5.6

> [!IMPORTANT]
> Recomendamos realizar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Veja [como realizar a restauração pontual](howto-restore-server-portal.md#point-in-time-restore).

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL 5.6.

2. Na página **'Vista Geral',** clique no botão **'Upgrade'** na barra de ferramentas.

3. Na secção **De Atualização,** selecione **OK** para atualizar a base de dados Azure para o servidor MySQL 5.6 para o servidor 5.7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - visão geral - upgrade":::

4. Uma notificação confirmará que a atualização é bem sucedida.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a política de versão Azure Database para a política de versão MySQL](concepts-version-policy.md).