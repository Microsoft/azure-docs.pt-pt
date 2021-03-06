---
title: Negar acesso à rede pública - portal Azure - Base de Dados Azure para o MySQL
description: Saiba como configurar o Negar o Acesso à Rede Pública utilizando o portal Azure para a sua Base de Dados Azure para o MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: b6fd5b5f70eb813792be003836790752db1d071f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732824"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Negar acesso à rede pública na base de dados Azure para o MySQL utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MySQL para negar todas as configurações públicas e permitir que apenas ligações através de pontos finais privados melhorem ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

* Uma [base de dados Azure para o MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) com finalidade geral ou nível de preços otimizados de memória

## <a name="set-deny-public-network-access"></a>Definir Negar acesso à rede pública

Siga estes passos para definir o servidor MySQL Negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL.

1. Na página do servidor MySQL, em **Definições,** clique na **segurança de ligação** para abrir a página de configuração de segurança de ligação.

1. In **Deny Public Network Access**, selecione **Sim** para permitir o acesso público ao seu servidor MySQL.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Base de Dados Azure para o Acesso à rede MySQL Deny":::

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Azure Database for MySQL Deny acesso à rede sucesso":::

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)
