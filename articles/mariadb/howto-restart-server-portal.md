---
title: Reinicie o servidor-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como você pode reiniciar um banco de dados do Azure para o MariaDB Server usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb4e56a3f40573a65b679ee026c22dfc5d6e5fa2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769426"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Reiniciar o banco de dados do Azure para MariaDB Server usando portal do Azure
Este tópico descreve como você pode reiniciar um banco de dados do Azure para o servidor MariaDB. Talvez seja necessário reiniciar o servidor por motivos de manutenção, o que causa uma pequena interrupção à medida que o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente, como dimensionar vCores.

O tempo necessário para concluir uma reinicialização depende do processo de recuperação do MariaDB. Para diminuir o tempo de reinicialização, recomendamos que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Executar a reinicialização do servidor

As etapas a seguir reiniciam o servidor MariaDB:

1. Na portal do Azure, selecione o banco de dados do Azure para o servidor MariaDB.

2. Na barra de ferramentas da página **visão geral** do servidor, clique em **reiniciar**.

   ![Banco de dados do Azure para MariaDB-visão geral-botão reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar a reinicialização do servidor.

   ![Banco de dados do Azure para MariaDB-reiniciar confirmação](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o status do servidor é alterado para "reinicializando".

   ![Banco de dados do Azure para MariaDB-status de reinicialização](./media/howto-restart-server-portal/4-restarting-status.png)

5. A confirmação da reinicialização do servidor foi bem-sucedida.

   ![Banco de dados do Azure para MariaDB-reinicialização com êxito](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos seguintes

[Início rápido: criar banco de dados do Azure para o servidor MariaDB usando portal do Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)