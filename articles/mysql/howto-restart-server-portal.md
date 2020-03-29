---
title: Servidor de reinício - Portal Azure - Base de Dados Azure para MySQL
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para servidor MySQL utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d7e158124347b302492364df46ccf5b5e78e75c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063288"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Reiniciar base de dados Azure para servidor MySQL utilizando portal Azure
Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como a escala vCores.

O tempo necessário para completar um reinício depende do processo de recuperação do MySQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [base de dados azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Executar o reinício do servidor

Os seguintes passos reiniciam o servidor MySQL:

1. No portal Azure, selecione a sua Base de Dados Azure para o servidor MySQL.

2. Na barra de ferramentas da página **'Overview'** do servidor, clique em **Reiniciar**.

   ![Base de Dados Azure para MySQL - Visão geral - Botão de reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar o reinício do servidor.

   ![Base de Dados Azure para MySQL - Restart confirme](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o estado do servidor muda para "Reiniciar".

   ![Base de Dados Azure para MySQL - Estado de reinício](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirmar que o reinício do servidor é bem sucedido.

   ![Base de Dados Azure para MySQL - Reiniciar o sucesso](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar base de dados Azure para servidor MySQL utilizando portal Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
