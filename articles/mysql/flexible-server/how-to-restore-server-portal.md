---
title: Restaurar uma Base de Dados Azure para o MySQL Flexible Server com o portal Azure.
description: Este artigo descreve como executar operações de restauro na Base de Dados Azure para o servidor flexível MySQL através do portal Azure
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 962a2cbdbcc238517616c9ade235eed9b8cae6f7
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502050"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Restauro pontual de uma Base de Dados Azure para MySQL - Servidor Flexível (Pré-visualização) utilizando o portal Azure


> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este artigo fornece um procedimento passo a passo para realizar recuperações pontuais em servidor flexível utilizando cópias de segurança.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

-   Deve ter uma base de dados Azure para o MySQL Flexible Server.

## <a name="restore-to-the-latest-restore-point"></a>Restaurar até ao último ponto de restauro

Siga estes passos para restaurar o seu servidor flexível utilizando uma cópia de segurança mais antiga existente.

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível de onde pretende restaurar a cópia de segurança.

2.  Clique **na visão geral** do painel esquerdo.

3.  A partir da página geral, clique em **Restaurar**.

4.  A página de restauro será mostrada com uma opção para escolher entre **o ponto de restauro mais recente** e o ponto de restauro personalizado.

5.  Selecione **o último ponto de restauro**.

6.  Fornecer um novo nome de servidor no campo **Restaurar para novo servidor.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-latest.png" alt-text="O tempo de restauro mais antigo":::

8.  Clique em **OK**.

9.  Será apresentada uma notificação de que a operação de restauro foi iniciada.

## <a name="restoring-to-a-custom-restore-point"></a>Restaurar a um ponto de restauração personalizado

Siga estes passos para restaurar o seu servidor flexível utilizando uma cópia de segurança mais antiga existente.

1.  No [portal Azure,](https://portal.azure.com/)escolha o seu servidor flexível de onde pretende restaurar a cópia de segurança.

2.  A partir da página geral, clique em **Restaurar**.

3.  A página de restauro será mostrada com uma opção de escolher entre o ponto de restauro mais antigo e o ponto de restauro personalizado.

4.  Escolha **o ponto de restauro personalizado.**

5.  Selecione data e hora.

6.  Fornecer um novo nome de servidor no campo **Restaurar para novo servidor.**

6.  Fornecer um novo nome de servidor no campo **Restaurar para novo servidor.**

    :::image type="content" source="./media/concept-backup-restore/restore-blade-custom.png" alt-text="ver visão geral":::

7.  Clique em **OK**.

8.  Será apresentada uma notificação de que a operação de restauro foi iniciada.


## <a name="perform-post-restore-tasks"></a>Executar tarefas pós-restauro
Após a restauração concluída, deverá executar as seguintes tarefas para que os seus utilizadores e aplicações voltem a funcionar:

- Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor.
- Certifique-se de que existem regras VNet adequadas para que os utilizadores se conectem. Estas regras não são copiadas do servidor original.
- Certifique-se de que estão em vigor os logins adequados e as permissões de nível de base de dados.
- Configure os alertas conforme apropriado para o servidor recém-restaurado.


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a continuidade do negócio](concepts-business-continuity.md)
