---
title: Configuração TLS - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor único
description: Saiba como definir a configuração TLS utilizando o portal Azure para a sua Base de Dados Azure para o servidor Single PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375112"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>Configurar as definições de TLS na Base de Dados Azure para PostgreSQL - Servidor único utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para PostgreSQL - Servidor único para impor uma versão TLS mínima para todas as ligações de entrada, o que ajuda a melhorar a segurança da rede.

Os clientes têm agora a capacidade de impor a versão TLS para a ligação à sua Base de Dados Azure para PostgreSQL - Servidor Único. Os clientes têm agora a opção de definir a versão TLS mínima para o seu servidor de base de dados. Por exemplo, definir a versão mínima de definição de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes utilizando TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2 e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de procedimentos, terá de:

* Uma [base de dados Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Detete as configurações do TLS para base de dados Azure para PostgreSQL - Servidor único

Siga estes passos para definir a versão TLS mínima do servidor Single PostgreSQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para PostgreSQL - Servidor único.

1.  Na base de dados Azure para PostgreSQL - Página de servidor único, em **Definições,** clique na **segurança da Ligação** para abrir a página de configuração de segurança da ligação.

1. Na **versão Mínima TLS**, selecione **1.2** para negar ligações com versão TLS menos do que TLS 1.2 para o seu servidor Single PostgreSQL.

    ![Base de Dados Azure para configuração TLS single - servidor Pós-Grés](./media/howto-tls-configurations/setting-tls-value.png)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para PostgreSQL - Single server TLS sucesso de configuração](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)
