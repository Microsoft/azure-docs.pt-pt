---
title: Reiniciar servidor - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para PostgreSQL - Servidor Único utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: d25c8908b46608ac64bcf135404a070740413fe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907371"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Reinicie a base de dados de Azure para PostgreSQL - Servidor Único utilizando o portal Azure
Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor PostgreSQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma falha curta à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como o escalar vCores.
 
O tempo necessário para concluir um reinício depende do processo de recuperação postgreSQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Executar reiniciar o servidor

Os seguintes passos reiniciam o servidor PostgreSQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure para o servidor PostgreSQL.

2. Na barra de ferramentas da página **'Visão Geral'** do servidor, clique em **Reiniciar**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Base de Dados Azure para PostgreSQL - Visão geral - Botão de reinício":::

3. Clique **em Sim** para confirmar o reinício do servidor.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Base de Dados Azure para PostgreSQL - Visão geral - Botão de reinício":::

4. Observe que o estado do servidor muda para "Reiniciar".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Base de Dados Azure para PostgreSQL - Visão geral - Botão de reinício":::

5. Confirme que o reinício do servidor é bem sucedido.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Base de Dados Azure para PostgreSQL - Visão geral - Botão de reinício":::

## <a name="next-steps"></a>Passos seguintes

Saiba [como definir parâmetros na Base de Dados Azure para PostgreSQL](howto-configure-server-parameters-using-portal.md)