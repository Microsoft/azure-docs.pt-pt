---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: f7af6962c3343cd9d3e35e96d88650aa6a42c6b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558350"
---
1. Enquanto estiver ligado à máquina virtual com o ambiente de trabalho remoto, procure pelo **Gestor de Configuração**:

    ![Abrir o SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. No Gestor de Configuração do SQL Server, no painel da consola, expanda **Configuração de Rede do SQL Server**.

1. No painel de consola, clique em **Protocolos para MSSQLSERVER** (o nome de instância padrão.) No painel de detalhes, clique com o botão direito **E** clique **em Ativar** se ainda não estiver ativado.

    ![Ativar TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. No painel da consola, clique em **Serviços do SQL Server**. No painel de detalhes, clique à direita **NO SQL Server *(nome*** de instância) (a instância padrão é **SQL Server (MSSQLSERVER)**), e, em seguida, clique em **Reiniciar**, para parar e reiniciar a instância do SQL Server.

    ![Reiniciar o Motor de Base de Dados](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Feche o Gestor de Configuração do SQL Server.

Para obter mais informações sobre como ativar protocolos para o Motor de Base de Dados do SQL Server, veja [Ativar ou Desativar um Protocolo de Rede do Servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).