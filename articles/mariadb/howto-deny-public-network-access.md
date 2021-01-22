---
title: Negar acesso à rede pública - portal Azure - Base de Dados Azure para MariaDB
description: Saiba como configurar o Negar o Acesso à Rede Pública utilizando o portal Azure para a sua Base de Dados Azure para MariaDB
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 7925107f4334df7a844b3f3e029f3769eef51a9c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665077"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Negar acesso à rede pública na Base de Dados Azure para MariaDB usando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MariaDB para negar todas as configurações públicas e permitir apenas ligações através de pontos finais privados para aumentar ainda mais a segurança da rede.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

* Uma [Base de Dados Azure para MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Definir Negar acesso à rede pública

Siga estes passos para definir o servidor MariaDB Negar acesso à rede pública:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MariaDB.

1. Na página do servidor MariaDB, em **Definições,** clique na **segurança de ligação** para abrir a página de configuração de segurança de ligação.

1. No Deny Public Network Access, selecione **Sim** para permitir o acesso público ao seu servidor MariaDB.

    ![Base de Dados Azure para MariaDB Negar acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Azure Database for MariaDB Negar o sucesso do acesso à rede](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-metric.md)