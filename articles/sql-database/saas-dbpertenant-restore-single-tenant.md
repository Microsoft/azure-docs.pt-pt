---
title: Restaurar uma base de dados SQL do Azure numa aplicação SaaS multi-inquilino | Documentos da Microsoft
description: Saiba como restaurar a base de dados SQL de um único inquilino depois de eliminar acidentalmente dados
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 4059b0f979e7e6856905f1759129167d62d7b5f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326369"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Restaurar um único inquilino com uma aplicação SaaS de base de dados por inquilino

O modelo de base de dados por inquilino torna mais fácil restaurar um inquilino individual para um ponto anterior no tempo sem afetar outros inquilinos.

Neste tutorial, vai aprender dois padrões de recuperação de dados:

> [!div class="checklist"]
> * Restaure uma base de dados numa base de dados paralela (lado a lado).
> * Restaure uma base de dados no local, substituir a base de dados existente.

|||
|:--|:--|
| Restaurar para uma base de dados paralela | Este padrão pode ser utilizado para tarefas como revisão, auditoria e conformidade para permitir que um inquilino inspecionar os seus dados a partir de um ponto anterior. Base de dados do inquilino atual permanece inalterado e online. |
| Restaurar no local | Este padrão é normalmente utilizado para recuperar um inquilino para um ponto anterior, depois de um inquilino elimina acidentalmente ou corromper os dados. A base de dados original é colocado offline e substituído por base de dados restaurada. |
|||

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação Wingtip SaaS é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter detalhes, consulte [introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introdução aos padrões de restauro de inquilino SaaS

Existem dois padrões simples para restaurar dados de um inquilino individual. Como as bases de dados de inquilino são isoladas umas das outras, restaurar um inquilino não tem qualquer impacto nos dados de qualquer outro inquilino. A funcionalidade de (PITR) do ponto de restauro anterior no tempo base de dados do Azure SQL é utilizada em ambos os padrões. PITR sempre cria uma nova base de dados.

* **Restaurar em paralelo**: O primeiro padrão, é criada uma nova base de dados paralela juntamente com a base de dados atual do inquilino. O inquilino, em seguida, é atribuído acesso só de leitura para a base de dados restaurada. Os dados restaurados podem ser revistos e potencialmente usados para substituir os valores de dados atual. Cabe-lhe para o Estruturador da aplicação para determinar como o inquilino acessa o banco de dados restaurado e que opções de recuperação são fornecidas. Simplesmente permitindo que o inquilino rever os seus dados num ponto anterior pode ser tudo o que é necessário em alguns cenários.

* **Restaurar in-loco**: O segundo padrão é útil se dados tiver sido perdidos ou danificados e o inquilino que quer reverter para um ponto anterior. O inquilino é colocado offline enquanto a base de dados é restaurada. A base de dados original é eliminado e a base de dados restaurada foi mudado. A cadeia de cópia de segurança da base de dados original permanece acessível após a eliminação, para que possa restaurar a base de dados para um ponto anterior no tempo, se necessário.

Se a base de dados utiliza [georreplicação ativa](sql-database-active-geo-replication.md) e restaurar em paralelo, recomendamos que copie todos os dados necessários da cópia restaurada para a base de dados original. Se substituir a base de dados original pela base de dados restaurada, terá de reconfigurar e ressincronizar a georreplicação.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de base de dados por inquilino aplicação Wingtip Tickets SaaS

Os scripts de banco de dados do Wingtip Tickets SaaS multi-inquilino e o código de origem da aplicação estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Para obter os passos transferir e desbloquear os scripts de Wingtip Tickets SaaS, consulte a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Antes de começar

Quando é criada uma base de dados, pode demorar 10 a 15 minutos antes do primeiro backup completo está disponível para restaurar a partir de. Se acabou de instalar a aplicação, poderá ter de aguardar alguns minutos antes de tentar este cenário.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simular um inquilino acidentalmente a eliminação de dados

Para demonstrar esses cenários de recuperação, primeiro "" excluir acidentalmente um evento de uma das bases de dados do inquilino. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Abra a aplicação de eventos para rever os eventos atuais

1. Abra o Hub de eventos (http://events.wtp.&lt; usuário&gt;. trafficmanager.net) e selecione **Contoso Concert Hall**.

   ![Hub de eventos](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Desloque-se a lista de eventos e tome nota do último evento na lista.

   ![Aparece o último evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Acidentalmente" eliminar o último evento

1. No ISE do PowerShell, abra... \\Módulos de aprendizagem\\continuidade do negócio e recuperação após desastre\\RestoreTenant\\*demonstração RestoreTenant.ps1*e defina o valor seguinte:

   * **$DemoScenario** = **1**, *último evento de eliminação (com nenhuma vendas de bilhetes)* .
2. Prima F5 para executar o script e eliminar o último evento. É apresentada a seguinte mensagem de confirmação:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. É aberta a página de eventos de Contoso. Desloque para baixo e certifique-se de que o evento não existe mais. Se o evento é ainda na lista, selecione **atualizar** e certifique-se de que ele sumiu.
   ![Último evento removido](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurar uma base de dados de inquilino em paralelo com a base de dados de produção

Neste exercício restaura a base de dados de Contoso Concert Hall para um ponto no tempo antes do evento foi eliminado. Este cenário pressupõe que deseja examinar os dados eliminados numa base de dados paralela.

 O *TenantInParallel.ps1 restauro* script cria uma base de dados de inquilinos paralela com o nome *ContosoConcertHall\_antigo*, com uma entrada de catálogo paralela. Este padrão de restauro é mais adequada para recuperar a partir de uma perda de dados secundária. Também pode utilizar este padrão se precisar de rever os dados de conformidade ou de fins de auditoria. É a abordagem recomendada quando utiliza [georreplicação ativa](sql-database-active-geo-replication.md).

1. Concluir o [simular um inquilino eliminar acidentalmente dados](#simulate-a-tenant-accidentally-deleting-data) secção.
2. No ISE do PowerShell, abra... \\Módulos de aprendizagem\\continuidade do negócio e recuperação após desastre\\RestoreTenant\\_demonstração RestoreTenant.ps1_.
3. Definir **$DemoScenario** = **2**, *inquilino de restauro em paralelo*.
4. Para executar o script, pressione F5.

O script restaura a base de dados do inquilino para um ponto no tempo antes de ter eliminado o evento. Restaurar a base de dados para uma nova base de dados com o nome _ContosoConcertHall\_antigo_. Os metadados de catálogo que existe nesta base de dados restaurada é eliminado e, em seguida, a base de dados é adicionada ao catálogo com uma chave construída a partir da *ContosoConcertHall\_antigo* nome.

O script de demonstração abre a página de eventos para esta nova base de dados do inquilino no seu browser. Tenha em atenção a partir do URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` que esta página mostra os dados da base de dados restaurada em que *_old* é adicionado ao nome.

Desloque-se os eventos listados no browser para confirmar que o evento eliminado na secção anterior foi restaurado.

Expor o inquilino restaurado como um inquilino adicional, com sua própria aplicação de eventos, é pouco provável que seja a forma como fornecer um acesso de inquilino para dados restaurados. Ele serve para ilustrar o padrão de restauro. Normalmente, dar acesso só de leitura aos dados antigos e reter a base de dados restaurada durante um período de tempo definido. No exemplo, pode eliminar a entrada de inquilino restaurada depois de terminar ao executar o _remover restaurado inquilino_ cenário.

1. Definir **$DemoScenario** = **4**, *Remove restaurado inquilino*.
2. Para executar o script, pressione F5.
3. O *ContosoConcertHall\_antigo* entrada agora é eliminada do catálogo. Feche a página de eventos para este inquilino no seu browser.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurar um inquilino no local, substituir a base de dados do inquilino existente

Neste exercício restaura o inquilino de Contoso Concert Hall para um ponto antes do evento foi eliminado. O *restauro TenantInPlace* script restaura uma base de dados de inquilinos para uma nova base de dados e elimina o original. Este padrão de restauro é mais adequada para recuperar da Corrupção de dados grave e o inquilino pode ter que acomodar a perda de dados significativos.

1. No ISE do PowerShell, abra a **demonstração RestoreTenant.ps1** ficheiro.
2. Definir **$DemoScenario** = **5**, *inquilino de restauro num local*.
3. Para executar o script, pressione F5.

O script restaura a base de dados do inquilino para um ponto antes do evento foi eliminado. Demora pela primeira vez que o inquilino de Contoso Concert Hall offline para impedir operações futuras atualizações. Em seguida, é criada uma base de dados paralelo, restaurando a partir do ponto de restauro. A base de dados restaurada é denominado com um carimbo de data / hora para se certificar de que o nome de base de dados não entra em conflito com o nome de base de dados do inquilino existente. Em seguida, a base de dados de inquilino antiga é eliminada e a base de dados restaurada foi mudado para o nome de base de dados original. Por fim, a Contoso Concert Hall é colocado online, para permitir o acesso de aplicação para a base de dados restaurada.

Restaurado com êxito a base de dados para um ponto no tempo antes do evento foi eliminado. Quando o **eventos** é aberta a página, confirme que o último evento foi restaurado.

Depois de restaurar a base de dados, demora mais de 10 a 15 de minutos antes do primeiro backup completo está disponível para restaurar a partir de novamente.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Restaure uma base de dados numa base de dados paralela (lado a lado).
> * Restaure uma base de dados no local.

Experimente o [esquema de banco de dados de inquilino de gerir](saas-tenancy-schema-management.md) tutorial.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Descrição geral da continuidade comercial com a base de dados do Azure SQL](sql-database-business-continuity.md)
* [Saiba mais sobre cópias de segurança da base de dados SQL](sql-database-automated-backups.md)
