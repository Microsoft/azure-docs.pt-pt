---
title: Reiniciar servidor - Portal Azure - Base de Dados Azure para MySQL - Servidor Flexível
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para o MySQL Flexible Server utilizando o portal Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 88a1524875f168b49f50f1684c650d5bc178bf38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542665"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Reinicie a base de dados do Azure para o MySQL Flexible Server utilizando o portal Azure
Este tópico descreve como pode reiniciar uma Base de Dados Azure para o MySQL Flexible Server. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.

O tempo necessário para concluir um reinício depende do processo de recuperação do MySQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o servidor flexível MySQL](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Executar reiniciar o servidor

Os seguintes passos reiniciam o servidor MySQL:

1. No portal Azure, selecione a sua Base de Dados Azure para o MySQL Flexible Server.

2. Na barra de ferramentas da página **'Visão Geral'** do servidor, clique em **Reiniciar**.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Base de Dados Azure para MySQL - Visão geral - Botão de reinício":::

3. Clique **em Sim** para confirmar o reinício do servidor.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL - Recomece confirmar":::

4. Observe que o estado do servidor muda para "Reiniciar".

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Base de Dados Azure para MySQL - Recomeçamento do estado":::

5. Confirme que o reinício do servidor é bem sucedido.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL - Reiniciar o sucesso":::

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Criar base de dados Azure para o MySQL Flexible Server utilizando o portal Azure](quickstart-create-server-portal.md)
