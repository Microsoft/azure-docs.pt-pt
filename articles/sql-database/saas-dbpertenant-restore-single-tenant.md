---
title: Restaurar um banco de dados em um aplicativo SaaS multilocatário
description: Saiba como restaurar um banco de dados SQL do locatário
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 12/04/2018
ms.openlocfilehash: a54e8e5629f6f8ad688b6fe11bbf50fc038095bf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818532"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Restaurar um único locatário com um aplicativo SaaS de banco de dados por locatário

O modelo de banco de dados por locatário facilita a restauração de um único locatário para um ponto anterior, sem afetar outros locatários.

Neste tutorial, você aprenderá dois padrões de recuperação de dados:

> [!div class="checklist"]
> * Restaurar um banco de dados em um banco de dados paralelo (lado a lado).
> * Restaurar um banco de dados no local, substituindo o banco de dados existente.

|||
|:--|:--|
| Restaurar em um banco de dados paralelo | Esse padrão pode ser usado para tarefas como revisão, auditoria e conformidade para permitir que um locatário inspecione seus dados de um ponto anterior. O banco de dados atual do locatário permanece online e inalterado. |
| Restaurar no local | Esse padrão geralmente é usado para recuperar um locatário para um ponto anterior, depois que um locatário exclui ou corrompe dados acidentalmente. O banco de dados original é tirado da linha e substituído pelo banco de dados restaurado. |
|||

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo SaaS Wingtip](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter detalhes, consulte Introdução [ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introdução aos padrões de restauração de locatário de SaaS

Há dois padrões simples para restaurar dados de um locatário individual. Como os bancos de dados de locatário são isolados uns dos outros, a restauração de um locatário não afeta nenhum outro dado de locatário. O recurso de restauração pontual (PITR) do banco de dados SQL do Azure é usado em ambos os padrões. O PITR sempre cria um novo banco de dados.

* **Restaurar em paralelo**: no primeiro padrão, um novo banco de dados paralelo é criado junto com o banco de dados atual do locatário. Em seguida, o locatário recebe acesso somente leitura ao banco de dados restaurado. Os dados restaurados podem ser revisados e potencialmente usados para substituir os valores de dados atuais. Cabe ao designer de aplicativo determinar como o locatário acessa o banco de dados restaurado e quais opções de recuperação são fornecidas. Simplesmente permitir que o locatário examine seus dados em um ponto anterior pode ser tudo o que é necessário em alguns cenários.

* **Restauração in-loco**: o segundo padrão é útil se os dados foram perdidos ou corrompidos e o locatário deseja reverter para um ponto anterior. O locatário é tirado da linha enquanto o banco de dados é restaurado. O banco de dados original é excluído e o banco de dados restaurado é renomeado. A cadeia de backup do banco de dados original permanece acessível após a exclusão, para que você possa restaurar o banco de dados para um ponto anterior no tempo, se necessário.

Se o banco de dados usar a [replicação geográfica ativa e a](sql-database-active-geo-replication.md) restauração em paralelo, recomendamos que você copie todos os dados necessários da cópia restaurada para o banco de dados original. Se você substituir o banco de dados original pelo banco de dados restaurado, precisará reconfigurar e ressincronizar a replicação geográfica.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo de banco de dados por locatário SaaS Wingtip tickets

Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets, consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Antes de começar

Quando um banco de dados é criado, pode levar de 10 a 15 minutos antes que o primeiro backup completo esteja disponível para restauração. Se você acabou de instalar o aplicativo, talvez seja necessário aguardar alguns minutos antes de tentar esse cenário.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simular um locatário excluindo dados acidentalmente

Para demonstrar esses cenários de recuperação, primeiro exclua "acidentalmente" um evento em um dos bancos de dados de locatário. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Abrir o aplicativo de eventos para examinar os eventos atuais

1. Abra o Hub de eventos (http://events.wtp.&lt; User&gt;. trafficmanager.net) e selecione **contoso Concert Hall**.

   ![Hub de eventos](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Role a lista de eventos e anote o último evento na lista.

   ![O último evento é exibido](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>Excluir "acidentalmente" o último evento

1. No ISE do PowerShell, abra...\\módulos de aprendizado\\continuidade dos negócios e recuperação de desastres\\RestoreTenant\\*restoretenant. ps1*e defina o seguinte valor:

   * **$DemoScenario** = **1**, *exclua o último evento (sem vendas de ingressos)* .
2. Pressione F5 para executar o script e excluir o último evento. A seguinte mensagem de confirmação é exibida:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. A página de eventos da Contoso é aberta. Role para baixo e verifique se o evento não existe mais. Se o evento ainda estiver na lista, selecione **Atualizar** e verifique se ele está ausente.
   ![último evento removido](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurar um banco de dados de locatário em paralelo com o banco de dados de produção

Este exercício restaura o banco de dados do contoso Concert Hall para um ponto no tempo antes da exclusão do evento. Esse cenário pressupõe que você deseja examinar os dados excluídos em um banco de dado paralelo.

 O script *Restore-TenantInParallel. ps1* cria um banco de dados de locatário paralelo chamado *ContosoConcertHall\_antigo*, com uma entrada de catálogo paralela. Esse padrão de restauração é mais adequado para a recuperação de uma perda de dados secundária. Você também pode usar esse padrão se precisar revisar os dados para fins de conformidade ou auditoria. É a abordagem recomendada quando você usa a [replicação geográfica ativa](sql-database-active-geo-replication.md).

1. Conclua a seção [simular um locatário excluindo dados acidentalmente](#simulate-a-tenant-accidentally-deleting-data) .
2. No ISE do PowerShell, abra...\\módulos de aprendizado\\continuidade dos negócios e recuperação de desastres\\RestoreTenant\\_restoretenant. ps1_.
3. Defina **$DemoScenario** = **2**, *restaurar locatário em paralelo*.
4. Para executar o script, pressione F5.

O script restaura o banco de dados de locatário para um ponto no tempo antes de você excluir o evento. O banco de dados é restaurado para um novo banco de dados chamado _ContosoConcertHall\_antigo_. Os metadados do catálogo que existem neste banco de dados restaurado são excluídos e, em seguida, o banco de dados é adicionado ao catálogo usando uma chave construída do *ContosoConcertHall\_nome antigo* .

O script de demonstração abre a página de eventos para esse novo banco de dados de locatário em seu navegador. Observe a partir da URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` que essa página mostra dados do banco de dado restaurado onde *_old* é adicionado ao nome.

Role os eventos listados no navegador para confirmar se o evento excluído na seção anterior foi restaurado.

Expor o locatário restaurado como um locatário adicional, com seu próprio aplicativo de eventos, é improvável que você forneça um acesso de locatário a dados restaurados. Ele serve para ilustrar o padrão de restauração. Normalmente, você concede acesso somente leitura aos dados antigos e mantém o banco de dado restaurado por um período definido. No exemplo, você pode excluir a entrada de locatário restaurado depois de concluir a execução do cenário _remover locatário restaurado_ .

1. Defina **$DemoScenario** = **4**, *remover locatário restaurado*.
2. Para executar o script, pressione F5.
3. O *ContosoConcertHall\_entrada antiga* agora é excluído do catálogo. Feche a página de eventos deste locatário em seu navegador.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurar um locatário no local, substituindo o banco de dados de locatário existente

Este exercício restaura o locatário do contoso Concert Hall para um ponto anterior à exclusão do evento. O script *Restore-TenantInPlace* restaura um banco de dados de locatário para um novo banco de dados e exclui o original. Esse padrão de restauração é mais adequado para recuperação de dados corrompidos sérios, e o locatário pode ter que acomodar perda de dados significativa.

1. No ISE do PowerShell, abra o arquivo **restoretenant. ps1** .
2. Defina **$DemoScenario** = **5**, *restaurar locatário no local*.
3. Para executar o script, pressione F5.

O script restaura o banco de dados de locatário para um ponto antes de o evento ser excluído. Primeiro, ele coloca o locatário contoso Concert Hall fora da linha para evitar mais atualizações. Em seguida, um banco de dados paralelo é criado por meio da restauração do ponto de restauração. O banco de dados restaurado é nomeado com um carimbo de data/hora para garantir que o nome do banco de dados não entre em conflito com o nome do banco de dados de Em seguida, o banco de dados de locatário antigo é excluído e o banco de dados restaurado é renomeado para o nome do banco de dados original. Finalmente, contoso Concert Hall é colocado online para permitir que o aplicativo acesse o banco de dados restaurado.

Você restaurou com êxito o banco de dados para um ponto no tempo antes de o evento ser excluído. Quando a página **eventos** for aberta, confirme se o último evento foi restaurado.

Depois de restaurar o banco de dados, levará mais de 10 a 15 minutos antes que o primeiro backup completo esteja disponível para restauração novamente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Restaurar um banco de dados em um banco de dados paralelo (lado a lado).
> * Restaurar um banco de dados no local.

Experimente o tutorial [gerenciar esquema de banco de dados de locatário](saas-tenancy-schema-management.md) .

## <a name="additional-resources"></a>Recursos adicionais

* [TUTORIAIS adicionais que se baseiam no aplicativo SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md)
* [Saiba mais sobre backups do banco de dados SQL](sql-database-automated-backups.md)
