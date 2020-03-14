---
title: Configuração TLS - Portal Azure - Base de Dados Azure para MySQL
description: Saiba como definir a configuração TLS utilizando o portal Azure para a sua Base de Dados Azure para mySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375294"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Configurar as definições de TLS na Base de Dados Azure para MySQL utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MySQL para impor ligações para uma versão TLS mínima para passar e negar todas as ligações com versão TLS mais baixa, melhorando assim a segurança da rede.

Os clientes têm agora a capacidade de impor a versão TLS para a ligação à sua Base de Dados Azure para o MySQL. Os clientes têm agora a opção de definir a versão TLS mínima para o seu servidor de base de dados. Por exemplo, definir esta versão TLS mínima para 1.0 significa que permitirá que os clientes que se conectam utilizando TLS 1.0,1.1 e 1.2. Em alternativa, defini-lo para 1.2 significa que só permite que os clientes que se conectam utilizando TLS 1.2 e todas as ligações de entrada com TLS 1.0 e TLS 1.1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de procedimentos, terá de:

* Uma [base de dados azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Definir configurações TLS para Base de Dados Azure para MySQL

Siga estes passos para definir a versão TLS mínima do servidor MySQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL.

1. Na página do servidor MySQL, em **Definições,** clique na **segurança da Ligação** para abrir a página de configuração de segurança da ligação.

1. Na **versão Mínima TLS**, selecione **1.2** para negar ligações com versão TLS menos do que TLS 1.2 para o seu servidor MySQL.

    ![Base de Dados Azure para configuração MySQL TLS](./media/howto-tls-configurations/setting-tls-value.png)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para sucesso de configuração MySQL TLS](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas sobre métricas.](howto-alert-on-metric.md)
