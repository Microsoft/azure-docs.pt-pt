---
title: Negar acesso à rede pública - portal Azure - Base de Dados Azure para MariaDB
description: Saiba como configurar o Acesso à Rede Pública Deny utilizando o portal Azure para a sua Base de Dados Azure para MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79375242"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Negar acesso à rede pública na Base de Dados Azure para MariaDB utilizando portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MariaDB negar todas as configurações públicas e permitir apenas ligações através de pontos finais privados para aumentar ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

* Uma [Base de Dados Azure para MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Set Negar acesso à rede pública

Siga estes passos para definir o servidor MariaDB Negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MariaDB.

1. Na página do servidor MariaDB, em **Definições,** clique na **segurança da Ligação** para abrir a página de configuração de segurança da ligação.

1. No Deny Public Network Access, selecione **Sim** para permitir negar o acesso público ao seu servidor MariaDB.

    ![Base de Dados Azure para acesso à rede MariaDB Deny](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para mariaDB Negar sucesso de acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-metric.md)