---
title: Reinicie a base de dados do Azure para o servidor MySQL com o portal do Azure
description: Este artigo descreve como pode reiniciar uma base de dados do Azure para o servidor MySQL com o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: a20030a1dc6cd8b89064731c283f9b462d30ec8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61422592"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Reinicie a base de dados do Azure para o servidor MySQL com o portal do Azure
Este tópico descreve como pode reiniciar uma base de dados do Azure para o servidor MySQL. Terá de reiniciar o servidor por motivos de manutenção, o que faz com que uma interrupção curta como o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço está ocupado. Por exemplo, o serviço poderá estar a processar uma operação de pedido anteriormente, como o dimensionamento vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MySQL. Para diminuir o tempo de reinício, recomendamos que estará a minimizar a quantidade de atividade ocorrida no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de procedimentos, terá de:
- Um [base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Reiniciar o servidor

Os seguintes passos de reiniciar o servidor MySQL:

1. No portal do Azure, selecione a base de dados do Azure para o servidor MySQL.

2. Na barra de ferramentas do servidor **descrição geral** página, clique em **reiniciar**.

   ![Base de dados do Azure para MySQL - descrição geral - Restart button](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar o reinício do servidor.

   ![Confirmar a base de dados do Azure para MySQL - reinício](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o estado do servidor é alterado para "Reiniciar".

   ![Base de dados do Azure para MySQL - estado de reinício](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme o reinício do servidor é efetuada com êxito.

   ![Base de dados do Azure para MySQL - êxito de reinício](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos Seguintes

[Quickstart: Criar base de dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
