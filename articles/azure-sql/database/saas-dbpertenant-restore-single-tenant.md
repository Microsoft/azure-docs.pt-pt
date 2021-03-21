---
title: Restaurar uma base de dados numa aplicação SaaS multitenante
description: Saiba como restaurar a base de dados Azure SQL de um único inquilino depois de eliminar acidentalmente dados
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780483"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Restaurar um único inquilino com uma aplicação SaaS de base de dados por inquilino
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O modelo de base de dados por inquilino facilita a repor um único inquilino a um ponto anterior no tempo sem afetar outros inquilinos.

Neste tutorial, aprende-se dois padrões de recuperação de dados:

> [!div class="checklist"]
> * Restaurar uma base de dados numa base de dados paralela (lado a lado).
> * Restaurar uma base de dados no lugar, substituindo a base de dados existente.

| Padrão | Description |
|:--|:--|
| Restaurar numa base de dados paralela | Este padrão pode ser usado para tarefas como revisão, auditoria e conformidade para permitir que um inquilino inspecione os seus dados a partir de um ponto anterior. A base de dados atual do inquilino permanece on-line e inalterada. |
| Restaurar no lugar | Este padrão é normalmente usado para recuperar um inquilino para um ponto anterior, depois de um inquilino acidentalmente apagar ou corromper dados. A base de dados original é retirada da linha e substituída pela base de dados restaurada. |
|||

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip SaaS está implantada. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a aplicação Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para mais detalhes, consulte [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introdução ao inquilino SaaS restaurar padrões

Existem dois padrões simples para restaurar os dados de um inquilino individual. Como as bases de dados dos inquilinos estão isoladas umas das outras, restaurar um inquilino não tem impacto nos dados de qualquer outro inquilino. A função Azure SQL Database ponto-in-time-restore (PITR) é usada em ambos os padrões. A PITR cria sempre uma nova base de dados.

* **Restauro em paralelo**: No primeiro padrão, uma nova base de dados paralela é criada ao lado da base de dados atual do arrendatário. O inquilino tem então acesso apenas de leitura à base de dados restaurada. Os dados restaurados podem ser revistos e potencialmente utilizados para substituir os valores de dados atuais. Cabe ao designer de aplicações determinar como o inquilino acede à base de dados restaurada e quais as opções de recuperação que são fornecidas. Simplesmente permitir que o inquilino reveja os seus dados num ponto anterior pode ser tudo o que é necessário em alguns cenários.

* **Restaurar no lugar**: O segundo padrão é útil se os dados foram perdidos ou corrompidos e o inquilino quer voltar a um ponto anterior. O inquilino é retirado da linha enquanto a base de dados é restaurada. A base de dados original é eliminada e a base de dados restaurada é renomeada. A cadeia de backup da base de dados original permanece acessível após a eliminação, para que possa restaurar a base de dados para um ponto mais cedo no tempo, se necessário.

Se a base de dados utilizar [a geo-replicação ativa](active-geo-replication-overview.md) e restaurar em paralelo, recomendamos que copie todos os dados necessários da cópia restaurada para a base de dados original. Se substituir a base de dados original pela base de dados restaurada, tem de reconfigurar e ressincronizar a geo-replicação.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de aplicações de ingressos saas de ponta da ponta da asa

Os scripts de base de dados multitenant de wingtip SaaS e código fonte de aplicação estão disponíveis no [wingtipTicketsSaAS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Para obter medidas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip, consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Antes de começar

Quando uma base de dados é criada, pode levar 10 a 15 minutos até que a primeira cópia de segurança completa esteja disponível para restaurar. Se acabou de instalar a aplicação, poderá ter de esperar alguns minutos antes de experimentar este cenário.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simular um inquilino acidentalmente apagar dados

Para demonstrar estes cenários de recuperação, primeiro apagar "acidentalmente" um evento numa das bases de dados dos inquilinos. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Abra a app Eventos para rever os eventos atuais

1. Abra o Centro de Eventos http://events.wtp.&lt ((user &gt; .trafficmanager.net) e **selecione a Sala de Concertos Contoso.**

   ![Centro de Eventos](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Percorra a lista de eventos e tome nota do último evento da lista.

   ![O último evento aparece](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Acidentalmente" apagar o último evento

1. No PowerShell ISE, aberto... \\ Módulos de Aprendizagem \\ Continuidade de Negócios e Recuperação de \\ Desastres Restaurar \\ *Demo-RestoreTenant.ps1,* e definir o seguinte valor:

   * **$DemoScenario**  =  **1**, *Eliminar o último evento (sem venda de bilhetes)*.
2. Prima F5 para executar o script e apagar o último evento. Aparece a seguinte mensagem de confirmação:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. A página de eventos contoso abre. Desça e verifique se o evento se foi. Se o evento ainda estiver na lista, selecione **Refresh** e verifique se desapareceu.
   ![Último evento removido](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurar uma base de dados de inquilinos em paralelo com a base de dados de produção

Este exercício restaura a base de dados do Contoso Concert Hall a um ponto no tempo antes de o evento ser apagado. Este cenário pressupõe que pretende rever os dados eliminados numa base de dados paralela.

 O *Restore-TenantInParallel.ps1* script cria uma base de dados paralela de inquilinos chamada *ContosoConcertHall \_ antiga,* com uma entrada de catálogo paralelo. Este padrão de restauro é mais adequado para se recuperar de uma pequena perda de dados. Também pode utilizar este padrão se precisar de rever dados para fins de conformidade ou auditoria. É a abordagem recomendada quando se utiliza [a geo-replicação ativa.](active-geo-replication-overview.md)

1. Complete a [Simulação de um inquilino acidentalmente eliminando a secção de dados.](#simulate-a-tenant-accidentally-deleting-data)
2. No PowerShell ISE, aberto... \\ Módulos de Aprendizagem \\ Continuidade de Negócios e Recuperação de \\ Desastres Restaurar \\ _Demo-RestoreTenant.ps1_.
3. Definir **$DemoScenario**  =  **2**, *Restaurar o inquilino em paralelo.*
4. Para executar o guião, prima F5.

O script restaura a base de dados do inquilino a um ponto no tempo antes de apagar o evento. A base de dados é restaurada para uma nova base de dados chamada _ContosoConcertHall \_ old_. Os metadados de catálogo que existem nesta base de dados restaurada são eliminados e, em seguida, a base de dados é adicionada ao catálogo utilizando uma chave construída a partir do antigo nome *contosoConcertHall. \_*

O script de demonstração abre a página de eventos para esta nova base de dados de inquilinos no seu navegador. Note a partir do URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` que esta página mostra dados da base de dados restaurada onde *_old* é adicionado ao nome.

Percorra os eventos listados no navegador para confirmar que o evento eliminado na secção anterior foi restaurado.

Expor o inquilino restaurado como um inquilino adicional, com a sua própria app Events, é improvável que seja como você fornece a um inquilino acesso a dados restaurados. Serve para ilustrar o padrão de restauro. Normalmente, você dá acesso apenas de leitura aos dados antigos e conserva a base de dados restaurada por um período definido. Na amostra, você pode apagar a entrada restaurada do inquilino depois de terminar executando o cenário _de inquilino restaurado Remove._

1. Coloque **$DemoScenario**  =  **4,** *Remova o inquilino restaurado.*
2. Para executar o guião, prima F5.
3. A antiga entrada *do ContosoConcertHall \_* é agora eliminada do catálogo. Feche a página de eventos para este inquilino no seu browser.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurar um inquilino no lugar, substituindo a base de dados de inquilinos existente

Este exercício devolve o inquilino do Contoso Concert Hall a um ponto antes de o evento ser apagado. O script *Restore-TenantInPlace* restaura uma base de dados de inquilinos para uma nova base de dados e elimina o original. Este padrão de restauro é mais adequado para se recuperar de uma corrupção de dados graves, e o inquilino pode ter que acomodar perda significativa de dados.

1. No PowerShell ISE, abra o ficheiro **Demo-RestoreTenant.ps1.**
2. Definir **$DemoScenario**  =  **5**, *Restaurar o inquilino no lugar*.
3. Para executar o guião, prima F5.

O script restaura a base de dados do inquilino a um ponto antes do evento ser apagado. Primeiro, leva o inquilino do Contoso Concert Hall para fora da linha para evitar novas atualizações. Em seguida, uma base de dados paralela é criada restaurando a partir do ponto de restauro. A base de dados restaurada tem o nome de um carimbo temporal para garantir que o nome da base de dados não entra em conflito com o nome da base de dados dos inquilinos existente. Em seguida, a antiga base de dados de inquilinos é eliminada, e a base de dados restaurada é renomeada para o nome original da base de dados. Por fim, a Salas de Concertos Contoso é trazida online para permitir o acesso da app à base de dados restaurada.

Restaurou a base de dados com sucesso a um ponto no tempo antes de o evento ser eliminado. Quando a página **eventos** abrir, confirme que o último evento foi restaurado.

Depois de restaurar a base de dados, demora mais 10 a 15 minutos até que a primeira cópia de segurança completa esteja disponível para restaurar novamente.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Restaurar uma base de dados numa base de dados paralela (lado a lado).
> * Restaurar uma base de dados no lugar.

Experimente o tutorial de [plano de base de dados de inquilinos.](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Saiba mais sobre backups de bases de dados SQL](automated-backups-overview.md)