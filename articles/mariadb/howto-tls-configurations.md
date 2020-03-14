---
title: Configuração TLS - Portal Azure - Base de Dados Azure para MariaDB
description: Saiba como definir a configuração TLS utilizando o portal Azure para a sua Base de Dados Azure para MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 063fc82131a66012c7329f590c0fdf2ce4d4da79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375008"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Configurar as definições de TLS na Base de Dados Azure para MariaDB utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MariaDB para impor ligações para uma versão TLS mínima para passar e negar todas as ligações com versão TLS mais baixa, melhorando assim a segurança da rede.

Os clientes têm agora a capacidade de impor a versão TLS para a ligação à sua Base de Dados Azure para O DAD, definindo a versão TLS mínima para o seu servidor de base de dados. Por exemplo, definir a versão mínima de definição de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes utilizando TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2 e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de procedimentos, terá de:

* Uma [Base de Dados Azure para MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Definir configurações TLS para Base de Dados Azure para MariaDB

Siga estes passos para definir a versão TLS mínima do servidor MariaDB:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MariaDB.

1. Na página do servidor MariaDB, em **Definições,** clique na **segurança da Ligação** para abrir a página de configuração de segurança da ligação.

1. Na **versão Mínima TLS**, selecione **1.2** para negar ligações com versão TLS menos do que TLS 1.2 para o seu servidor MariaDB.

    ![Base de Dados Azure para configuração MariaDB TLS](./media/howto-tls-configurations/tls-configurations.png)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para sucesso de configuração MariaDB TLS](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-metric.md)
