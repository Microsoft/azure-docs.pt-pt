---
title: Configuração TLS - Portal Azure - Base de Dados Azure para MySQL
description: Saiba como definir a configuração do TLS utilizando o portal Azure para a sua Base de Dados Azure para o MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: d94e589a19b29e68883c0217b62b883f9d026789
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902742"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Configurar definições de TLS na Base de Dados Azure para o MySQL utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MySQL para impor a versão mínima TLS permitida para que as ligações passem e negue todas as ligações com a versão TLS mais baixa do que a versão mínima de TLS configurada, aumentando assim a segurança da rede.

Pode impor a versão TLS para ligar à sua Base de Dados Azure para o MySQL. Os clientes têm agora a opção de definir a versão TLS mínima para o seu servidor de base de dados. Por exemplo, definir esta versão Mínima TLS para 1.0 significa que deve permitir aos clientes ligar-se utilizando TLS 1.0,1.1 e 1.2. Em alternativa, definir isto para 1.2 significa que só permite que os clientes que se conectem utilizando o TLS 1.2+ e todas as ligações de entrada com TLS 1.0 e TLS 1.1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

* Uma [base de dados Azure para o MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Definir configurações TLS para Azure Database para MySQL

Siga estes passos para definir a versão mínima TLS do servidor MySQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL.

1. Na página do servidor MySQL, em **Definições,** clique na **segurança de ligação** para abrir a página de configuração de segurança de ligação.

1. Na **versão Mínima TLS**, selecione **1.2** para negar ligações com a versão TLS inferior a TLS 1.2 para o seu servidor MySQL.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Base de Dados Azure para configuração mySQL TLS":::

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Base de Dados Azure para configuração mySQL TLS":::

## <a name="next-steps"></a>Passos seguintes

- Saiba [como criar alertas em métricas](howto-alert-on-metric.md)