---
title: Configuração TLS - Portal Azure - Base de Dados Azure para MariaDB
description: Saiba como definir a configuração do TLS utilizando o portal Azure para a sua Base de Dados Azure para MariaDB
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: b4e167714ffccd20b4628fd21fb6b284d5400112
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664771"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Configurar definições de TLS na Base de Dados Azure para MariaDB utilizando o portal Azure

Este artigo descreve como pode configurar uma Base de Dados Azure para o servidor MariaDB para impor a versão mínima de TLS para as ligações a passar e negar todas as ligações com a versão TLS mais baixa do que a versão mínima de TLS configurada, aumentando assim a segurança da rede.

Pode impor a versão TLS para ligar à sua Base de Dados Azure para MariaDB, definindo a versão mínima TLS para o seu servidor de base de dados. Por exemplo, definir a versão mínima de definição de TLS para TLS 1.0 significa que o seu servidor permitirá ligações de clientes que utilizem TLS 1.0, 1.1 e 1.2+. Em alternativa, defini-lo para 1.2 significa que só permite ligações de clientes que utilizem TLS 1.2+ e todas as ligações com TLS 1.0 e TLS 1.1 serão rejeitadas.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

* Uma [Base de Dados Azure para MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Definir configurações TLS para Azure Database para MariaDB

Siga estes passos para definir a versão mínima TLS do servidor MariaDB:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MariaDB.

1. Na página do servidor MariaDB, em **Definições,** clique na **segurança de ligação** para abrir a página de configuração de segurança de ligação.

1. Na **versão TLS mínima,** selecione **1.2** para negar ligações com a versão TLS inferior a TLS 1.2 para o seu servidor MariaDB.

    ![Base de Dados Azure para configuração de TLS MariaDB](./media/howto-tls-configurations/tls-configurations.png)

1. Clique em **Guardar** para guardar as alterações.

1. Uma notificação confirmará que a definição de segurança da ligação foi ativada com sucesso.

    ![Base de Dados Azure para o sucesso da configuração da TLS MariaDB](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como criar alertas em métricas](howto-alert-metric.md)