---
title: Restaurar - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve como executar operações de restauro na Base de Dados Azure para PostgreSQL através do portal Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90938867"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Restauro pontual de um Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo fornece um procedimento passo a passo para realizar recuperações pontuais em servidor flexível utilizando cópias de segurança. Pode realizar-se até um ponto de restauro mais antigo ou um ponto de restauro personalizado dentro do seu período de retenção.

## <a name="pre-requisites"></a>Pré-requisitos

Para completar este guia, precisa:

-   Deve ter uma base de dados Azure para PostgreSQL - Servidor Flexível. O mesmo procedimento também é aplicável para servidor flexível configurado com redundância de zona.

## <a name="restoring-to-the-earliest-restore-point"></a>Restaurando o primeiro ponto de restauro

Siga estes passos para restaurar o seu servidor flexível utilizando uma cópia de segurança mais antiga existente.

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível de onde pretende restaurar a cópia de segurança.

2.  Clique **na visão geral** do painel esquerdo e clique em **Restaurar**
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Restaurar a visão geral":::

3.  A página de restauro será mostrada com uma opção de escolher entre o ponto de restauro mais antigo e o ponto de restauro personalizado.

4.  Selecione **o ponto de restauro mais antigo** e forneça um novo nome de servidor no campo Restaurar para novo **servidor.** A primeira hora de tempo a que pode restaurar é exibida. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="O tempo de restauro mais antigo":::

5.  Clique em **OK**.

6.  Será apresentada uma notificação de que a operação de restauro foi iniciada.

## <a name="restoring-to-a-custom-restore-point"></a>Restaurar a um ponto de restauração personalizado

Siga estes passos para restaurar o seu servidor flexível utilizando uma cópia de segurança mais antiga existente.

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível de onde pretende restaurar a cópia de segurança.

2.  A partir da página geral, clique em **Restaurar**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Restaurar a visão geral":::
    
3.  A página de restauro será mostrada com uma opção de escolher entre o ponto de restauro mais antigo e o ponto de restauro personalizado.

4.  Escolha **o ponto de restauro personalizado.**

5.  Selecione data e hora e forneça um novo nome de servidor no campo **Restaurar para novo servidor.** 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Tempo de restauração personalizado":::
 
6.  Clique em **OK**.

7.  Será apresentada uma notificação de que a operação de restauro foi iniciada.

## <a name="next-steps"></a>Passos seguintes

-   Conheça a [continuidade do negócio](./concepts-business-continuity.md)
-   Saiba mais sobre [zona redundante alta disponibilidade](./concepts-high-availability.md)
-   Saiba mais [sobre backup e recuperação](./concepts-backup-restore.md)
