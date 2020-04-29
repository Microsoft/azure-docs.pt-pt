---
title: Servidor de reinício - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o servidor MariaDB utilizando o Portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed95304807a1a03880cc824c1a58f010203d418d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534716"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Reiniciar base de dados Azure para servidor MariaDB utilizando portal Azure
Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MariaDB. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como a escala vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MariaDB. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [Base de Dados Azure para servidor MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Executar o reinício do servidor

Os seguintes passos reiniciam o servidor MariaDB:

1. No portal Azure, selecione a sua Base de Dados Azure para o servidor MariaDB.

2. Na barra de ferramentas da página **'Overview'** do servidor, clique em **Reiniciar**.

   ![Base de Dados Azure para MariaDB - Visão geral - Botão de reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar o reinício do servidor.

   ![Base de Dados Azure para MariaDB - Restart confirme](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o estado do servidor muda para "Reiniciar".

   ![Base de Dados Azure para MariaDB - Estado de reinício](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirmar que o reinício do servidor é bem sucedido.

   ![Base de Dados Azure para MariaDB - Reiniciar o sucesso](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar base de dados Azure para servidor MariaDB utilizando portal Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)