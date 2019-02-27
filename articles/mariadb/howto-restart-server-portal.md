---
title: Reinicie a base de dados do Azure para o servidor de MariaDB com o portal do Azure
description: Este artigo descreve como pode reiniciar uma base de dados do Azure para o servidor de MariaDB com o Portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 185e605db366fb392758ad9870a3c15badc0f321
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874873"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Reinicie a base de dados do Azure para o servidor de MariaDB com o portal do Azure
Este tópico descreve como pode reiniciar uma base de dados do Azure para MariaDB server. Terá de reiniciar o servidor por motivos de manutenção, o que faz com que uma interrupção curta como o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço está ocupado. Por exemplo, o serviço poderá estar a processar uma operação de pedido anteriormente, como o dimensionamento vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação MariaDB. Para diminuir o tempo de reinício, recomendamos que estará a minimizar a quantidade de atividade ocorrida no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de procedimentos, terá de:
- Um [base de dados do Azure para MariaDB servidor e base de dados](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Reiniciar o servidor

Os seguintes passos reiniciar o servidor de MariaDB:

1. No portal do Azure, selecione a base de dados do Azure para MariaDB server.

2. Na barra de ferramentas do servidor **descrição geral** página, clique em **reiniciar**.

   ![Base de dados do Azure para MariaDB - descrição geral - Restart button](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar o reinício do servidor.

   ![Confirmar a base de dados do Azure para MariaDB - reinício](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o estado do servidor é alterado para "Reiniciar".

   ![Base de dados do Azure para MariaDB - estado de reinício](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme o reinício do servidor é efetuada com êxito.

   ![Base de dados do Azure para MariaDB - êxito de reinício](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos Seguintes

[Quickstart: Criar base de dados do Azure para o servidor de MariaDB através do portal do Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)