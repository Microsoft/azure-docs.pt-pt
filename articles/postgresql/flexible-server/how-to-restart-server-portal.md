---
title: Reiniciar - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve como executar operações de reinício na Base de Dados Azure para PostgreSQL através do portal Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936934"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Reinicie a base de dados de Azure para postgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo fornece procedimento passo a passo para realizar o reinício do servidor flexível. Esta operação é útil para aplicar quaisquer alterações de parâmetros estáticos que requerem o reinício do servidor da base de dados. O procedimento é o mesmo para servidores configurados com zona redundante alta disponibilidade. 

> [!IMPORTANT]
> Quando configurados com elevada disponibilidade, tanto os servidores primários como os servidores de espera são reiniciados ao mesmo tempo.

## <a name="pre-requisites"></a>Pré-requisitos

Para completar este guia, precisa:

-   Deve ter um servidor flexível.

## <a name="restart-your-flexible-server"></a>Reinicie o seu servidor flexível

Siga estes passos para reiniciar o seu servidor flexível.

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível que pretende reiniciar.

2.  Clique **na visão geral** do painel esquerdo e clique em **Reiniciar**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Reiniciar a seleção":::

3.  Aparecerá uma mensagem de confirmação pop-up.

4.  Clique **em Sim** se quiser continuar.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Reiniciar confirmar":::
 
6.  Será apresentada uma notificação de que a operação de reinício foi iniciada.

## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
