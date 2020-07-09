---
title: Reiniciar servidor - Portal Azure - Base de Dados Azure para o MySQL
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: aa79d1ba2cba86bf3f381ff78fd630ce455128d9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107874"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Reinicie a base de dados do Azure para o servidor MySQL utilizando o portal Azure
Este tópico descreve como pode reiniciar uma Base de Dados Azure para o servidor MySQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MySQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Executar reiniciar o servidor

Os seguintes passos reiniciam o servidor MySQL:

1. No portal Azure, selecione a base de dados Azure para o servidor MySQL.

2. Na barra de ferramentas da página **'Visão Geral'** do servidor, clique em **Reiniciar**.

   ![Base de Dados Azure para MySQL - Visão geral - Botão de reinício](./media/howto-restart-server-portal/2-server.png)

3. Clique **em Sim** para confirmar o reinício do servidor.

   ![Azure Database for MySQL - Recomece confirmar](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o estado do servidor muda para "Reiniciar".

   ![Base de Dados Azure para MySQL - Recomeçamento do estado](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme que o reinício do servidor é bem sucedido.

   ![Azure Database for MySQL - Reiniciar o sucesso](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Próximos passos

[Quickstart: Criar base de dados Azure para servidor MySQL utilizando o portal Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
