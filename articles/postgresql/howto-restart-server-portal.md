---
title: Reiniciar servidor - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode reiniciar uma Base de Dados Azure para PostgreSQL - Servidor Único utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74770089"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Reiniciar base de dados Azure para PostgreSQL - Servidor Único utilizando o portal Azure
Este tópico descreve como pode reiniciar uma Base de Dados Azure para servidor PostgreSQL. Pode ser necessário reiniciar o servidor por razões de manutenção, o que provoca uma pequena paragem à medida que o servidor executa a operação.

O reinício do servidor será bloqueado se o serviço estiver ocupado. Por exemplo, o serviço pode estar a processar uma operação previamente solicitada, como a escala vCores.
 
O tempo necessário para completar um reinício depende do processo de recuperação do PostgreSQL. Para diminuir o tempo de reinício, recomendamos que minimize a quantidade de atividade que ocorre no servidor antes do reinício.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia de como orientar, precisa de:
- Uma [base de dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Executar o reinício do servidor

Os seguintes passos reiniciam o servidor PostgreSQL:

1. No [portal Azure,](https://portal.azure.com/)selecione a sua Base de Dados Azure para o servidor PostgreSQL.

2. Na barra de ferramentas da página **'Overview'** do servidor, clique em **Reiniciar**.

   ![Base de Dados Azure para PostgreSQL - Visão geral - Botão de reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar o reinício do servidor.

   ![Base de Dados Azure para PostgreSQL - Restart confirm](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o estado do servidor muda para "Reiniciar".

   ![Base de Dados Azure para PostgreSQL - Estado de reinício](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirmar que o reinício do servidor é bem sucedido.

   ![Base de Dados Azure para PostgreSQL - Reiniciar o sucesso](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Passos seguintes

Saiba [como definir parâmetros na Base de Dados Azure para PostgreSQL](howto-configure-server-parameters-using-portal.md)