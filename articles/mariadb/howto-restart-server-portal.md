---
title: Reiniciar servidor - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para servidor MariaDB utilizando o Portal Azure.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: a912c1cde092b082e4c0229b1f454e80f32e319e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664890"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Reinicie a base de dados do Azure para o servidor MariaDB utilizando o portal Azure
Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor MariaDB. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MariaDB. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para servidor MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Executar reiniciar o servidor

Os seguintes passos reiniciam o servidor MariaDB:

1. No portal Azure, selecione a sua Base de Dados Azure para o servidor MariaDB.

2. Na barra de ferramentas da página **'Visão Geral'** do servidor, clique em **Reiniciar**.

   ![Base de Dados Azure para MariaDB - Visão geral - Botão de reinício](./media/howto-restart-server-portal/2-server.png)

3. Clique **em Sim** para confirmar o reinício do servidor.

   ![Azure Database for MariaDB - Recomece confirmar](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o estado do servidor muda para "Reiniciar".

   ![Base de Dados Azure para MariaDB - Reiniciar estado](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme que o reinício do servidor é bem sucedido.

   ![Azure Database for MariaDB - Reiniciar o sucesso](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar base de dados Azure para servidor MariaDB utilizando o portal Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)