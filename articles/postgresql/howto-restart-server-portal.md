---
title: Reiniciar o servidor-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode reiniciar um banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770089"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Reiniciar o banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure
Este tópico descreve como você pode reiniciar um servidor de banco de dados do Azure para PostgreSQL. Talvez seja necessário reiniciar o servidor por motivos de manutenção, o que causa uma pequena interrupção à medida que o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente, como dimensionar vCores.
 
O tempo necessário para concluir uma reinicialização depende do processo de recuperação do PostgreSQL. Para diminuir o tempo de reinicialização, recomendamos que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa de:
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Executar a reinicialização do servidor

As etapas a seguir reiniciam o servidor PostgreSQL:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure para o servidor PostgreSQL.

2. Na barra de ferramentas da página **visão geral** do servidor, clique em **reiniciar**.

   ![Banco de dados do Azure para PostgreSQL-visão geral – botão reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar a reinicialização do servidor.

   ![Banco de dados do Azure para PostgreSQL-reiniciar confirmação](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o status do servidor é alterado para "reinicializando".

   ![Banco de dados do Azure para PostgreSQL-status de reinicialização](./media/howto-restart-server-portal/4-restarting-status.png)

5. A confirmação da reinicialização do servidor foi bem-sucedida.

   ![Banco de dados do Azure para PostgreSQL-reinicialização bem-sucedida](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [como definir parâmetros no banco de dados do Azure para PostgreSQL](howto-configure-server-parameters-using-portal.md)