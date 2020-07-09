---
title: Negar acesso à rede pública - portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como configurar o Acesso à Rede Pública de Negação utilizando o portal Azure para a sua Base de Dados Azure para o servidor Single PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: b51a79027ce834190a4fae5d893e47f2be16abf7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102196"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Negar acesso à rede pública na base de dados Azure para servidor único postgresQL usando portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor PostgreSQL Single para negar todas as configurações públicas e permitir que apenas ligações através de pontos finais privados melhorem ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

* Uma [base de dados Azure para servidor único postgresql](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Definir Negar acesso à rede pública

Siga estes passos para definir postgreSQL servidor único negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor PostgreSQL Single.

1. Na página de servidor single PostgreSQL, em **Definições,** clique na **segurança de ligação** para abrir a página de configuração de segurança de ligação.

1. In **Deny Public Network Access**, selecione **Sim** para permitir o acesso público ao seu servidor PostgreSQL Single.

    ![Base de dados Azure para postgresQL servidor único Negar acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Azure Database para PostgreSQL Single servidor Negar o sucesso do acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Próximos passos

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)