---
title: Negar acesso à rede pública - portal Azure - Base de Dados Azure para PostgreSQL - Servidor único
description: Saiba como configurar o Acesso à Rede Pública Deny utilizando o portal Azure para a sua Base de Dados Azure para o servidor Single PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375125"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Negar acesso à rede pública na Base de Dados Azure para o servidor Single PostgreSQL utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor PostgreSQL Single para negar todas as configurações públicas e permitir apenas ligações através de pontos finais privados para aumentar ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia de como orientar, precisa de:

* Uma [base de dados Azure para o servidor single PostgreSQL](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Set Negar acesso à rede pública

Siga estes passos para definir postgresQL single server Negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor Single PostgreSQL.

1. Na página do servidor Single PostgreSQL, em **Definições,** clique na **segurança da Ligação** para abrir a página de configuração de segurança da ligação.

1. No **Deny Public Network Access**, selecione **Sim** para permitir negar o acesso público ao seu servidor PostgreSQL Single.

    ![Base de Dados Azure para PostgreSQL Single server Deny acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para PostgreSQL Single server Deny sucesso de acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)