---
title: Restaurar uma base de dados numa aplicação multiarrendatária SaaS
description: Saiba como restaurar a base de dados SQL de um único inquilino após apagar acidentalmente dados
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73818532"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Restaurar um único inquilino com uma aplicação SaaS de base de dados por inquilino

O modelo de base de dados por inquilino facilita a restauração de um único inquilino a um ponto anterior sem afetar outros inquilinos.

Neste tutorial, você aprende dois padrões de recuperação de dados:

> [!div class="checklist"]
> * Restaurar uma base de dados numa base de dados paralela (lado a lado).
> * Restaurar uma base de dados no lugar, substituindo a base de dados existente.

|||
|:--|:--|
| Restaurar numa base de dados paralela | Este padrão pode ser utilizado para tarefas como revisão, auditoria e conformidade para permitir que um inquilino inspecione os seus dados a partir de um ponto anterior. A base de dados atual do inquilino permanece on-line e inalterada. |
| Restaurar no lugar | Este padrão é normalmente usado para recuperar um inquilino para um ponto anterior, depois de um inquilino acidentalmente apagar ou corromper dados. A base de dados original é desligada da linha e substituída pela base de dados restaurada. |
|||

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip SaaS está implantada. Para implantar em menos de cinco minutos, consulte [O Deploy e explore a aplicação Wingtip SaaS](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para mais detalhes, consulte [Start start with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introdução aos padrões de restauro do inquilino saaS

Existem dois padrões simples para restaurar os dados de um inquilino individual. Como as bases de dados dos inquilinos estão isoladas umas das outras, restaurar um inquilino não tem impacto nos dados de qualquer outro inquilino. A função de restauro ponto-a-tempo da Base de Dados Azure SQL (PITR) é utilizada em ambos os padrões. O PITR cria sempre uma nova base de dados.

* **Restaurar em paralelo**: No primeiro padrão, é criada uma nova base de dados paralela ao lado da base de dados atual do inquilino. O inquilino tem então acesso apenas à base de dados restaurada. Os dados restaurados podem ser revistos e potencialmente utilizados para substituir os valores de dados atuais. Cabe ao designer de aplicações determinar como é que o inquilino acede à base de dados restaurada e que opções de recuperação são fornecidas. Simplesmente permitir que o inquilino reveja os seus dados num ponto mais cedo pode ser tudo o que é necessário em alguns cenários.

* **Restaurar no lugar**: O segundo padrão é útil se os dados foram perdidos ou corrompidos e o inquilino quer voltar a um ponto mais cedo. O inquilino é retirado da linha enquanto a base de dados é restaurada. A base de dados original é eliminada e a base de dados restaurada é renomeada. A cadeia de cópia de segurança da base de dados original permanece acessível após a eliminação, para que possa restaurar a base de dados para um ponto mais precoce, se necessário.

Se a base de dados utilizar [geo-replicação ativa](sql-database-active-geo-replication.md) e restaurar em paralelo, recomendamos que copie os dados necessários da cópia restaurada para a base de dados original. Se substituir a base de dados original pela base de dados restaurada, terá de reconfigurar e resincronizar a geo-replicação.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de bases de dados SaaS de bilhetes de ala e de bilhetes por inquilino

Os scripts de base de dados multiarrendatários SaaS Multitenant E o código de origem da aplicação estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Para que os passos descarreguem e desbloqueiem os scripts SaaS dos Bilhetes Wingtip, consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Antes de começar

Quando uma base de dados é criada, pode demorar 10 a 15 minutos até que a primeira cópia de segurança esteja disponível para restaurar. Se acabou de instalar a aplicação, poderá ter de esperar alguns minutos antes de experimentar este cenário.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simular um inquilino apagando acidentalmente dados

Para demonstrar estes cenários de recuperação, primeiro "acidentalmente" apague um evento numa das bases de dados dos inquilinos. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Abra a app Eventos para rever os eventos atuais

1. Abra o Centrohttp://events.wtp.&ltde&gt;Eventos (.utilizador .trafficmanager.net) e selecione **Contoso Concert Hall**.

   ![Centro de Eventos](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Percorra a lista de eventos e tome nota do último evento da lista.

   ![Último evento aparece](media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Acidentalmente" apaga o último evento

1. No PowerShell ISE, abra... \\Módulos\\de Aprendizagem Continuidade\\de\\Negócios e Recuperação de Desastres RestoreTenant*Demo-RestoreTenant.ps1,* e definir o seguinte valor:

   * **$DemoScenario** = **1**, Apagar o último *evento (sem venda de bilhetes)*.
2. Pressione F5 para executar o script e apague o último evento. Aparece a seguinte mensagem de confirmação:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. A página de eventos contoso abre. Desça e verifique se o evento se foi. Se o evento ainda estiver na lista, selecione **Refresh** e verifique se já se foi.
   ![Último evento removido](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurar uma base de dados de inquilinos em paralelo com a base de dados de produção

Este exercício restaura a base de dados da Sala de Concertos de Contoso a um ponto do tempo antes do evento ser apagado. Este cenário pressupõe que pretende rever os dados eliminados numa base de dados paralela.

 O script *Restore-TenantInParallel.ps1* cria uma base de dados paralela de inquilinos chamada *ContosoConcertHall\_antiga,* com uma entrada paralela de catálogo. Este padrão de restauro é mais adequado para recuperar de uma pequena perda de dados. Também pode utilizar este padrão se precisar de rever dados para efeitos de conformidade ou auditoria. É a abordagem recomendada quando se usa [geo-replicação ativa.](sql-database-active-geo-replication.md)

1. Complete o Simular um inquilino que [apagando acidentalmente a](#simulate-a-tenant-accidentally-deleting-data) secção de dados.
2. No PowerShell ISE, abra... \\Módulos\\de Aprendizagem Continuidade\\de\\Negócios e Recuperação de Desastres Restaurar Tenant_Demo-RestoreTenant.ps1_.
3. Conjunto **$DemoScenario** = **2,** *Restaurar o inquilino em paralelo.*
4. Para executar o guião, prima F5.

O guião restaura a base de dados do inquilino a um ponto do tempo antes de apagar o evento. A base de dados é restaurada para uma nova base de dados chamada _\_ContosoConcertHall antiga._ Os metadados do catálogo que existem nesta base de dados restaurada são eliminados, e depois a base de dados é adicionada ao catálogo utilizando uma chave construída a partir do nome *antigo ContosoConcertHall.\_*

O script de demonstração abre a página de eventos para esta nova base de dados de inquilinos no seu navegador. Nota do ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` URL que esta página mostra dados da base de dados restaurada onde *_old* é adicionado ao nome.

Percorra os eventos listados no navegador para confirmar que o evento eliminado na secção anterior foi restaurado.

Expor o inquilino restaurado como inquilino adicional, com a sua própria app Eventos, é improvável que seja a forma como fornece o acesso de um inquilino a dados restaurados. Serve para ilustrar o padrão de restauro. Normalmente, dá acesso apenas a leitura saem dos dados antigos e mantém a base de dados restaurada por um período definido. Na amostra, pode eliminar a entrada restaurada do inquilino depois de terminar, executando o cenário de _inquilino restaurado removendo._

1. Conjunto **$DemoScenario** = **4**, Remova o *inquilino restaurado.*
2. Para executar o guião, prima F5.
3. A antiga entrada do *ContosoConcertHall\_* está agora apagada do catálogo. Feche a página de eventos deste inquilino no seu navegador.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurar um inquilino no local, substituindo a base de dados de inquilinos existente

Este exercício restaura o inquilino da Sala de Concertos de Contoso a um ponto antes do evento ser apagado. O script *Restore-TenantInPlace* restaura uma base de dados de inquilinos para uma nova base de dados e elimina o original. Este padrão de restauro é mais adequado para recuperar de uma grave corrupção de dados, e o inquilino pode ter que acomodar perdas significativas de dados.

1. No PowerShell ISE, abra o ficheiro **Demo-RestoreTenant.ps1.**
2. Conjunto **$DemoScenario** = **5,** *Restaurar o inquilino no lugar.*
3. Para executar o guião, prima F5.

O guião restaura a base de dados do inquilino a um ponto antes do evento ser apagado. Primeiro, tira o inquilino da Sala de Concertos de Contoso para evitar novas atualizações. Em seguida, uma base de dados paralela é criada restaurando a partir do ponto de restauro. A base de dados restaurada é nomeada com um carimbo de tempo para garantir que o nome da base de dados não entra em conflito com o nome de base de dados dos inquilinos existente. Em seguida, a antiga base de dados de inquilinos é eliminada, e a base de dados restaurada é renomeada para o nome original da base de dados. Por fim, a Sala de Concertos contoso é trazida online para permitir o acesso da app à base de dados restaurada.

Conseguiu restaurar a base de dados a um ponto antes de o evento ser apagado. Quando a página **eventoabrir,** confirme que o último evento foi restaurado.

Depois de restaurar a base de dados, leva mais 10 a 15 minutos antes que a primeira cópia de segurança esteja disponível para restaurar de novo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Restaurar uma base de dados numa base de dados paralela (lado a lado).
> * Restaurar uma base de dados no lugar.

Experimente o tutorial de schema de base de dados de [inquilinos.](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure](sql-database-business-continuity.md)
* [Saiba mais sobre cópias de segurança da Base de Dados SQL](sql-database-automated-backups.md)
