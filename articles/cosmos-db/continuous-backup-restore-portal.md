---
title: Configure a cópia de segurança contínua e o ponto no tempo restaurado usando o portal Azure em Azure Cosmos DB.
description: Saiba como identificar o ponto de restauro e configurar a cópia de segurança contínua utilizando o portal Azure. Mostra como restaurar uma conta ao vivo e apagada.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 707ef9f60891c1da7c13638e233ee74e78fc20dd
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283942"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Configure e gerencie backup contínuo e ponto no tempo restauração (Preview) - usando o portal Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A funcionalidade de restauro pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A funcionalidade de restauro pontual da Azure Cosmos DB (Preview) ajuda-o a recuperar de uma alteração acidental dentro de um contentor, a restaurar uma conta, base de dados ou um contentor apagado ou a restaurar em qualquer região (onde existiam cópias de segurança). O modo de backup contínuo permite-lhe restaurar qualquer ponto de tempo nos últimos 30 dias.

Este artigo descreve como identificar o ponto de restauro e configurar a cópia de segurança contínua utilizando o portal Azure.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Disponibilização de uma conta com backup contínuo

Ao criar uma nova conta DB Azure Cosmos, para a opção **política de Backup,** escolha o modo **contínuo** para ativar o ponto de tempo para restaurar a funcionalidade da nova conta. Depois desta funcionalidade estar ativada para a conta, todas as bases de dados e contentores estão disponíveis para cópia de segurança contínua. Com a restauração pontual, os dados são sempre restaurados para uma nova conta, atualmente não é possível restaurar uma conta existente.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Fornecer uma conta DB Azure Cosmos com configuração de backup contínua." border="true":::

## <a name="backup-storage-redundancy"></a>Redundância de armazenamento de backup

Por padrão, a Azure Cosmos DB armazena dados de backup contínuos em bolhas de armazenamento redundantes locais. Para as regiões que têm redundância de zona configurada, o backup é armazenado em bolhas de armazenamento redundantes de zona. Neste modo não é possível atualizar a redundância de armazenamento de cópias de segurança.

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Restaurar uma conta ao vivo de modificação acidental

Pode utilizar o portal Azure para restaurar uma conta ao vivo ou bases de dados selecionadas e contentores debaixo dela. Utilize os seguintes passos para restaurar os seus dados:

1. Inscreva-se no [portal Azure](https://portal.azure.com/)
1. Navegue até à sua conta DB Azure Cosmos e abra o **painel Point In Time Restore.**

   > [!NOTE]
   > O painel de restauro no portal Azure só é povoado se tiver `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` a permissão. Para saber mais sobre como definir esta permissão, consulte o [artigo de Backup e restaurar permissões.](continuous-backup-restore-permissions.md)

1. Preencha os seguintes detalhes para restaurar:

   * **Ponto de Restauração (UTC)** – Um calendário nos últimos 30 dias. A conta deve existir nessa hora. Pode especificar o ponto de restauro na UTC. Pode ser o mais próximo do segundo quando quiser restaurá-lo. Selecione o link **Click aqui** para obter ajuda na [identificação do ponto de restauro](#event-feed).

   * **Localização** – Região de destino onde a conta é restaurada. A conta deve existir nesta região na data de tempo dada (por exemplo, Eua Ocidental ou Leste dos EUA). Uma conta só pode ser restaurada às regiões em que a conta de origem existiu.

   * **Restaurar o Recurso** – Pode escolher **conta inteira** ou uma base **de dados/recipiente selecionado** para restaurar. As bases de dados e os contentores devem existir na hora certa. Com base no ponto de restauro e localização selecionados, os recursos de restauração são povoados, o que permite ao utilizador selecionar bases de dados ou contentores específicos que precisam de ser restaurados.

   * **Grupo de recursos** - Grupo de recursos sob o qual a conta-alvo será criada e restaurada. O grupo de recursos já deve existir.

   * **Restaurar a Conta Alvo** – O nome da conta-alvo. O nome da conta-alvo tem de seguir as mesmas orientações que quando está a criar uma nova conta. Esta conta será criada pelo processo de restauro na mesma região onde existe a sua conta de origem.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Restaurar uma conta ao vivo a partir de modificação acidental do portal Azure." border="true":::

1. Depois de selecionar os parâmetros acima, selecione o botão **Enviar** para iniciar uma restauração. O custo de restauro é uma taxa única, que se baseia na quantidade de dados e encargos para o armazenamento na região. Para saber mais, consulte a secção [de Preços.](continuous-backup-restore-introduction.md#continuous-backup-pricing)

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Use feed de eventos para identificar o tempo de restauração

Ao preencher o tempo de ponto de restauração no portal Azure, se precisar de ajuda para identificar o ponto de restauro, selecione o link **clique aqui,** leva-o à lâmina de alimentação do evento. O feed do evento fornece uma lista completa de fidelidade de criar, substituir, eliminar eventos em bases de dados e contentores da conta de origem. 

Por exemplo, se pretender restabelecer o ponto antes de um determinado recipiente ser eliminado ou atualizado, verifique este feed do evento. Os eventos são exibidos em ordem cronologicamente descendente do tempo quando ocorreram, com eventos recentes no topo. Pode navegar pelos resultados e selecionar o tempo antes ou depois do evento para reduzir ainda mais o seu tempo.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Utilize o feed do evento para identificar o tempo de ponto de restauração." border="true":::

> [!NOTE]
> O feed do evento não apresenta as alterações nos recursos do item. Pode sempre especificar manualmente qualquer relógio de tempo nos últimos 30 dias (o tempo que a conta existir nessa altura) para restauro.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Restaurar uma conta eliminada

Pode utilizar o portal Azure para restaurar completamente uma conta eliminada no prazo de 30 dias após a sua eliminação. Utilize os seguintes passos para restaurar uma conta eliminada:

1. Inscreva-se no [portal Azure](https://portal.azure.com/)
1. Procure recursos *DB da Azure Cosmos* na barra de pesquisa global. Ele lista todas as suas contas existentes.
1. Em seguida, selecione o botão **Restaurar.** O painel Restaurar apresenta uma lista de contas eliminadas que podem ser restauradas dentro do período de retenção, que é de 30 dias a partir do tempo de eliminação.
1. Escolha a conta que pretende restaurar.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Restaurar uma conta eliminada do portal Azure." border="true":::

   > [!NOTE]
   > Nota: O painel de restauro no portal Azure só é povoado se tiver a `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão. Para saber mais sobre como definir esta permissão, consulte o [artigo de Backup e restaurar permissões.](continuous-backup-restore-permissions.md)

1. Selecione uma conta para restaurar e inserir os seguintes detalhes para restaurar uma conta eliminada:

   * **Ponto de Restauração (UTC)** – Um calendário nos últimos 30 dias. A conta devia ter existido naquele momento. Especifique o ponto de restauro na UTC. Pode ser o mais próximo do segundo quando quiser restaurá-lo.

   * **Localização** – Região de destino onde a conta precisa de ser restaurada. A conta de origem deve existir nesta região no momento em que o tempo. Exemplo Dos EUA ou do Leste dos EUA.  

   * **Grupo de recursos** - Grupo de recursos sob o qual a conta-alvo será criada e restaurada. O grupo de recursos já deve existir.

   * **Restaurar a Conta Alvo** – O nome da conta-alvo tem de seguir as mesmas orientações que quando está a criar uma nova conta. Esta conta será criada pelo processo de restauro na mesma região onde existe a sua conta de origem.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Acompanhe o estado da operação de restauro

Depois de iniciar uma operação de restauro, selecione o ícone da campainha **de notificação** no canto superior direito do portal. Dá um link que mostra o estado da conta a ser restaurada. Enquanto a restauração estiver em curso, o estado da conta será *criar,* após a operação de restauro concluída, o estado da conta mudará para *Online*.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="O estado da conta restaurada muda de criação para online quando a operação está concluída." border="true":::

## <a name="next-steps"></a>Passos seguintes

* Configure e gere a cópia de segurança contínua utilizando [a Azure CLI,](continuous-backup-restore-command-line.md) [PowerShell](continuous-backup-restore-powershell.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
