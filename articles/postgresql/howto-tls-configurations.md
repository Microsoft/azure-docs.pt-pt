---
title: Configuração TLS - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor único
description: Saiba como definir a configuração do TLS utilizando o portal Azure para o seu Azure Database para o servidor Single PostgreSQL
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 4cf491a27fbe53a5f5bf0e8351e5bb684b3492f1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101992"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Configurar definições de TLS na Base de Dados Azure para PostgreSQL Single - servidor utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para PostgreSQL para impor a versão mínima TLS permitida para ligações e negar todas as ligações com a versão TLS mais baixa do que a versão mínima de TLS configurada, aumentando assim a segurança da rede.

Pode impor a versão TLS para ligar à sua Base de Dados Azure para PostgreSQL. Os clientes têm agora a opção de definir a versão TLS mínima para o seu servidor de base de dados. Por exemplo, definir a versão mínima de definição de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes que utilizem TLS 1.0, 1.1 e 1.2+. Em vez disso, a definição da versão mínima tls para 1.2+ significa que só permite ligações de clientes que utilizem TLS 1.2 e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

* Uma [base de dados Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Definir configurações TLS para Azure Database para PostgreSQL - Servidor único

Siga estes passos para definir a versão TLS mínima postgreSQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para PostgreSQL.

1.  Na Base de Dados Azure para PostgreSQL - Página de servidor único, em **Definições**, clique na **segurança de ligação** para abrir a página de configuração de segurança de ligação.

1. Na **versão Mínima TLS**, selecione **1.2** para negar ligações com a versão TLS inferior a TLS 1.2 para o seu servidor PostgreSQL Single.

    ![Base de dados Azure para configuração de TLS single pósgresql - servidor](./media/howto-tls-configurations/setting-tls-value.png)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para Pós-SQL - Sucesso de configuração de STS de servidor único](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Próximos passos

Saiba [como criar alertas em métricas](howto-alert-on-metric.md)