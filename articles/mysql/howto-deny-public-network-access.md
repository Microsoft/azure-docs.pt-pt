---
title: Negar acesso à rede pública - portal Azure - Base de Dados Azure para MySQL
description: Saiba como configurar o Acesso à Rede Pública Deny utilizando o portal Azure para a sua Base de Dados Azure para mySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79374956"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Negar acesso à rede pública na Base de Dados Azure para o MySQL utilizando portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MySQL para negar todas as configurações públicas e permitir apenas ligações através de pontos finais privados para aumentar ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

* Uma [base de dados azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Set Negar acesso à rede pública

Siga estes passos para definir o servidor MySQL Negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL.

1. Na página do servidor MySQL, em **Definições,** clique na **segurança da Ligação** para abrir a página de configuração de segurança da ligação.

1. No **Deny Public Network Access,** selecione **Sim** para permitir negar o acesso público ao seu servidor MySQL.

    ![Base de Dados Azure para acesso à rede MySQL Deny](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para mySQL Negar sucesso de acesso à rede](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)