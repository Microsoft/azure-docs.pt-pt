---
title: Restaurar - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL - Portal Azure
description: Este artigo descreve como executar operações de restauro na Base de Dados Azure para PostgreSQL - Hiperescala (Citus) através do portal Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: aebfeed055fad7c1108620ab494236640285aa1e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495078"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>Restauro pontual de um grupo de servidores de Hiperescala (Citus)

Este artigo fornece procedimentos passo a passo para executar [recuperações pontuais](concepts-hyperscale-backup.md#point-in-time-restore-pitr) para um grupo de servidor de Hiperescala (Citus) usando cópias de segurança. Pode restaurar a cópia de segurança mais antiga ou um ponto de restauro personalizado dentro do seu período de retenção.

## <a name="restoring-to-the-earliest-restore-point"></a>Restaurando o primeiro ponto de restauro

Siga estes passos para restaurar o seu grupo de servidor Hyperscale (Citus) até à sua cópia de segurança mais antiga.

1.  No [portal Azure,](https://portal.azure.com/)escolha o grupo de servidores que pretende restaurar.

2.  Clique **na visão geral** do painel esquerdo e clique em **Restaurar.**

    > [!IMPORTANT]
    > Se o botão **Restaurar** ainda não estiver presente para o seu grupo de servidor, abra um pedido de suporte Azure.

3.  A página de restauro pedir-lhe-á para escolher entre o ponto de **restauro** **mais antigo** e personalizado, e apresentará a data mais cedo.

4.  Selecione **o ponto de restauro mais antigo**.

5.  Fornecer um novo nome de grupo de servidor no campo **Restaurar para novo servidor.** Os outros campos (subscrição, grupo de recursos e localização) são apresentados, mas não editáveis.

6.  Clique em **OK**.

7.  Será apresentada uma notificação de que a operação de restauro foi iniciada.

Por fim, siga as [tarefas pós-restauro](#post-restore-tasks).

## <a name="restoring-to-a-custom-restore-point"></a>Restaurar a um ponto de restauração personalizado

Siga estes passos para restaurar o seu grupo de servidor Hyperscale (Citus) até uma data e hora à sua escolha.

1.  No [portal Azure,](https://portal.azure.com/)escolha o grupo de servidores que pretende restaurar.

2.  Clique **na visão geral** do painel esquerdo e clique em **Restaurar**

    > [!IMPORTANT]
    > Se o botão **Restaurar** ainda não estiver presente para o seu grupo de servidor, abra um pedido de suporte Azure.

3.  A página de restauro pedir-lhe-á para escolher entre o ponto de **restauro** **mais antigo** e personalizado, e apresentará a data mais cedo.

4.  Escolha **o ponto de restauro personalizado.**

5.  Selecione data e hora para **o ponto de restauro (UTC)** e forneça um novo nome de grupo de servidor no campo Restaurar para novo **servidor.** Os outros campos (subscrição, grupo de recursos e localização) são apresentados, mas não editáveis.
 
6.  Clique em **OK**.

7.  Será apresentada uma notificação de que a operação de restauro foi iniciada.

Por fim, siga as [tarefas pós-restauro](#post-restore-tasks).

## <a name="post-restore-tasks"></a>Post-restore tasks

Após uma restauração, deve fazer o seguinte para que os seus utilizadores e aplicações voltem a funcionar:

* Se o novo servidor pretende substituir o servidor original, redirecione clientes e aplicações de clientes para o novo servidor
* Certifique-se de que existe uma firewall adequada ao nível do servidor para que os utilizadores se conectem. Estas regras não são copiadas do grupo de servidores originais.
* Ajuste os parâmetros do servidor PostgreSQL conforme necessário. Os parâmetros não são copiados do grupo de servidor original.
* Certifique-se de que estão em vigor os logins adequados e as permissões de nível de base de dados.
* Alertas de configuração, conforme apropriado.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [backup e restauro](concepts-hyperscale-backup.md) em Hyperscale (Citus).
*  [Desenhe alertas sugeridos](./howto-hyperscale-alert-on-metric.md#suggested-alerts) em grupos de servidores Hyperscale (Citus).
