---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184201"
---
1. Enquanto estiver ligado à máquina virtual com o ambiente de trabalho remoto, procure pelo **Gestor de Configuração**:

    ![Abrir o SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. No Gestor de Configuração do SQL Server, no painel da consola, expanda **Configuração de Rede do SQL Server**.

1. No painel de consola, clique em **Protocolos para MSSQLSERVER** (o nome de instância padrão.) No painel de detalhes, clique com o botão direito **E** clique **em Ativar** se ainda não estiver ativado.

    ![Ativar TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. No painel da consola, clique em **Serviços do SQL Server**. No painel de detalhes, clique à direita **NO SQL Server *(nome*** de instância) (a instância padrão é **SQL Server (MSSQLSERVER)**), e, em seguida, clique em **Reiniciar**, para parar e reiniciar a instância do SQL Server.

    ![Reiniciar o Motor de Base de Dados](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Feche o Gestor de Configuração do SQL Server.

Para obter mais informações sobre como ativar protocolos para o Motor de Base de Dados do SQL Server, veja [Ativar ou Desativar um Protocolo de Rede do Servidor](https://msdn.microsoft.com/library/ms191294.aspx).
