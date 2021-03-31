---
title: 'Aplicativos SaaS: Backups geo-redundantes para recuperação de desastres'
description: Aprenda a usar backups geo-redundantes da Base de Dados Azure SQL para recuperar uma aplicação SaaS multitenant em caso de paragem
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 3fe6095595f5270b18536e6ef46afe4a0a5b3268
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607716"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Utilize o geo-restauro para recuperar uma aplicação SaaS multitensante a partir de backups de bases de dados
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este tutorial explora um cenário completo de recuperação de desastres para uma aplicação SaaS multitenante implementada com a base de dados por modelo de inquilino. Você usa [geo-restauro](recovery-using-backups.md) para recuperar as bases de dados do catálogo e dos inquilinos de backups geo-redundantes automaticamente mantidos em uma região de recuperação alternativa. Depois de a paralisação ser resolvida, [utiliza-se a geo-replicação](active-geo-replication-overview.md) para repatriar as bases de dados alteradas para a sua região original.

![O diagrama mostra uma região original e de recuperação, ambas com uma app, catálogo, imagens originais ou espelhadas de servidores e piscinas, backups automáticos para armazenamento, com a região de recuperação a aceitar a geo-replicação de backup e ter servidor e piscina para novos inquilinos.](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geo-restauração é a solução de recuperação de desastres mais rentável para a Base de Dados Azure SQL. No entanto, restaurar a partir de backups geo-redundantes pode resultar em perda de dados de até uma hora. Pode levar um tempo considerável, dependendo do tamanho de cada base de dados. 

> [!NOTE]
> Recuperar aplicações com o RPO e RTO mais baixos possíveis utilizando a geo-replicação em vez de geo-restauro.

Este tutorial explora fluxos de trabalho de restauração e repatriamento. Saiba como:
> [!div class="checklist"]
> 
> * Sync base de dados e informação de configuração de piscina elástica no catálogo do inquilino.
> * Crie um ambiente de imagem espelhado numa região de recuperação que inclua aplicações, servidores e piscinas.   
> * Recupere as bases de dados do catálogo e dos inquilinos utilizando o geo-restauro.
> * Use a geo-replicação para repatriar o catálogo do inquilino e altere as bases de dados dos inquilinos após a interrupção ser resolvida.
> * Atualize o catálogo à medida que cada base de dados é restaurada (ou repatriada) para rastrear a localização atual da cópia ativa da base de dados de cada inquilino.
> * Certifique-se de que a base de dados de aplicação e de inquilinos está sempre co-localizada na mesma região de Azure para reduzir a latência. 
 

Antes de iniciar este tutorial, complete os seguintes pré-requisitos:
* Implemente a base de dados SaaS de Bilhetes Wingtip por app de inquilinos. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a base de dados De Bilhetes SaaS wingtip por aplicação de inquilino.](saas-dbpertenant-get-started-deploy.md) 
* Instale o Azure PowerShell. Para mais detalhes, consulte [Começar com a Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introdução ao padrão de recuperação de geo-restauro

A recuperação de desastres (DR) é uma consideração importante para muitas aplicações, seja por razões de conformidade ou continuidade do negócio. Se houver uma interrupção prolongada do serviço, um plano DR bem preparado pode minimizar a perturbação do negócio. Um plano DR baseado no geo-restauro deve atingir vários objetivos:
 * Reserve toda a capacidade necessária na região de recuperação escolhida o mais rápido possível para garantir que está disponível para restaurar as bases de dados dos inquilinos.
 * Estabeleça um ambiente de recuperação de imagem espelhada que reflita a configuração original da piscina e da base de dados. 
 * Permitir o cancelamento do processo de restauro em meados do voo se a região original voltar a funcionar.
 * Permitir o provisionamento do inquilino rapidamente para que o novo inquilino a bordo possa recomeçar o mais rápido possível.
 * Esteja otimizado para restaurar os inquilinos em ordem prioritária.
 * Esteja otimizado para colocar os inquilinos on-line o mais rápido possível, fazendo passos em paralelo onde prático.
 * Seja resiliente ao fracasso, recomeçante e idempotente.
 * Repatriar bases de dados para a sua região original com o mínimo impacto para os inquilinos quando a paralisação for resolvida.  

> [!NOTE]
> O pedido é recuperado na região emparelhada da região em que a aplicação é implementada. Para mais informações, consulte [as regiões emparelhadas Azure.](../../best-practices-availability-paired-regions.md)   

Este tutorial utiliza funcionalidades da Base de Dados Azure SQL e da plataforma Azure para responder a estes desafios:

* [Modelos de Gestor de Recursos Azure,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)para reservar toda a capacidade necessária o mais rapidamente possível. Os modelos Azure Resource Manager são usados para providenciar uma imagem espelhada dos servidores originais e piscinas elásticas na região de recuperação. Um servidor e piscina separados também são criados para o provisionamento de novos inquilinos.
* [Biblioteca de Clientes de Base de Dados Elástica](elastic-database-client-library.md) (EDCL), para criar e manter um catálogo de bases de dados de inquilinos. O catálogo alargado inclui informações de configuração de piscina e base de dados periodicamente renovadas.
* [Funcionalidades](elastic-database-recovery-manager.md) de recuperação de gestão de fragmentos do EDCL, para manter as entradas de localização da base de dados no catálogo durante a recuperação e repatriamento.  
* [Geo-restauração,](../../key-vault/general/disaster-recovery-guidance.md)para recuperar as bases de dados do catálogo e dos inquilinos de cópias de segurança geo-redundantes automaticamente mantidas. 
* [As operações de restauro assíncronos, enviadas](../../azure-resource-manager/management/async-operations.md)por ordem de prioridade do arrendatário, são em fila para cada piscina pelo sistema e processadas em lotes para que a piscina não esteja sobrecarregada. Estas operações podem ser canceladas antes ou durante a execução, se necessário.   
* [Geo-replicação,](active-geo-replication-overview.md)para repatriar bases de dados para a região original após a paragem. Não há perda de dados e impacto mínimo no inquilino quando se utiliza a geo-replicação.
* [Pseudónimos DNS do servidor SQL,](./dns-alias-overview.md)para permitir que o processo de sincronização do catálogo se conecte ao catálogo ativo independentemente da sua localização.  

## <a name="get-the-disaster-recovery-scripts"></a>Obtenha os scripts de recuperação de desastres

Os scripts DR utilizados neste tutorial estão disponíveis na [base de dados Wingtip Tickets SaaS por inquilino gitHub repositório](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts de gestão de Bilhetes Wingtip.

> [!IMPORTANT]
> Como todos os scripts de gestão de Bilhetes Wingtip, os scripts DR são de qualidade de amostra e não devem ser usados na produção.

## <a name="review-the-healthy-state-of-the-application"></a>Reveja o estado saudável da aplicação
Antes de iniciar o processo de recuperação, reveja o estado normal e saudável da aplicação.

1. No seu navegador web, abra o centro de eventos Wingtip Tickets http://events.wingtip-dpt.&lt &gt; (.user .trafficmanager.net, substitua &lt; o utilizador pelo valor do utilizador da sua &gt; implementação).
    
   Percorra a parte inferior da página e note o nome e a localização do servidor do catálogo no rodapé. A localização é a região em que implementou a app.    

   > [!TIP]
   > Passe o rato sobre o local para ampliar o visor.

   ![Eventos hub estado saudável na região original](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Selecione o inquilino do Salão de Concertos Contoso e abra a sua página de eventos.

   No rodapé, reparem no nome do servidor do inquilino. A localização é a mesma da localização do servidor do catálogo.

   ![Região original da Sala de Concertos contoso](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. No [portal Azure](https://portal.azure.com), reveja e abra o grupo de recursos no qual implementou a app.

   Observe os recursos e a região em que os componentes do serviço de aplicações e a Base de Dados SQL são implantados.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Sincronizar a configuração do inquilino no catálogo

Nesta tarefa, iniciará um processo para sincronizar a configuração dos servidores, piscinas elásticas e bases de dados no catálogo do inquilino. Esta informação é usada mais tarde para configurar um ambiente de imagem espelhada na região de recuperação.

> [!IMPORTANT]
> Para simplificar, o processo de sincronização e outros processos de recuperação e repatriamento de longa duração são implementados nestas amostras como empregos ou sessões locais da PowerShell que funcionam sob o login do seu cliente. As fichas de autenticação emitidas quando você entrar expiram após várias horas, e os trabalhos vão então falhar. Num cenário de produção, os processos de longo prazo devem ser implementados como serviços fiáveis da Azure de algum tipo, funcionando sob um principal de serviço. Consulte [a Utilização Azure PowerShell para criar um principal de serviço com um certificado](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). 

1. No PowerShell ISE, abra o ficheiro ...\Learning Modules\UserConfig.psm1. Substitua `<resourcegroup>` e `<user>` nas linhas 10 e 11 pelo valor utilizado quando implementou a aplicação. Guarde o ficheiro.

2. No PowerShell ISE, abra o script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 script.

    Neste tutorial, executou cada um dos cenários neste script PowerShell, por isso mantenha este ficheiro aberto.

3. Desa esta medida:

    $DemoScenario = 1: Inicie um trabalho de fundo que sincroniza o servidor do inquilino e a configuração da piscina no catálogo.

4. Para executar o script de sincronização, selecione F5. 

    Esta informação é usada mais tarde para garantir que a recuperação cria uma imagem espelhada dos servidores, piscinas e bases de dados na região de recuperação.  

    ![Processo de sincronização](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Deixe a janela PowerShell em segundo plano e continue com o resto deste tutorial.

> [!NOTE]
> O processo de sincronização liga-se ao catálogo através de um pseudónimo DNS. O pseudónimo é modificado durante a restauração e repatriamento para apontar para o catálogo ativo. O processo de sincronização mantém o catálogo atualizado com quaisquer alterações na base de dados ou na configuração da piscina efetuadas na região de recuperação. Durante o repatriamento, estas alterações são aplicadas aos recursos equivalentes na região original.

## <a name="geo-restore-recovery-process-overview"></a>Visão geral do processo de recuperação de geo-restauro

O processo de recuperação de geo-restauro implementa a aplicação e restaura bases de dados de backups para a região de recuperação.

O processo de recuperação faz o seguinte:

1. Desativa o ponto final do Azure Traffic Manager para a aplicação web na região original. A desativação do ponto final impede que os utilizadores se conectem à app num estado inválido caso a região original esteja online durante a recuperação.

2. Fornece um servidor de catálogo de recuperação na região de recuperação, restaura geo-restaura a base de dados do catálogo e atualiza o pseudónimo activecatalog para apontar para o servidor de catálogo restaurado. A alteração do pseudónimo do catálogo garante que o processo de sincronização do catálogo se sincroniza sempre com o catálogo ativo.

3. Marca todos os inquilinos existentes no catálogo de recuperação como offline para impedir o acesso às bases de dados dos inquilinos antes de serem restaurados.

4. Prevê uma instância da app na região de recuperação e configura-a para utilizar o catálogo restaurado naquela região. Para manter a latência ao mínimo, a aplicação da amostra é projetada para ligar sempre a uma base de dados de inquilinos na mesma região.

5. Disposições um servidor e piscina elástica em que novos inquilinos são a provisionados. A criação destes recursos garante que o a provisionamento de novos inquilinos não interfere com a recuperação dos inquilinos existentes.

6. Atualiza o pseudónimo do novo inquilino para apontar para o servidor para novas bases de dados de inquilinos na região de recuperação. A alteração deste pseudónimo garante que as bases de dados para novos inquilinos sejam a provisionadas na região de recuperação.
        
7. Provisões servidores e piscinas elásticas na região de recuperação para restaurar bases de dados de inquilinos. Estes servidores e piscinas são uma imagem espelhada da configuração na região original. O provisionamento de piscinas à frente reserva a capacidade necessária para restaurar todas as bases de dados.

    Uma paralisação numa região pode colocar uma pressão significativa sobre os recursos disponíveis na região emparelhada. Se você depender de geo-restauro para DR, então é recomendado reservar recursos rapidamente. Considere a geo-replicação se é fundamental que uma aplicação seja recuperada numa região específica. 

8. Permite o ponto final do Gestor de Tráfego para a aplicação web na região de recuperação. A ativação deste ponto final permite a aplicação de novos inquilinos. Nesta fase, os inquilinos existentes ainda estão offline.

9. Submete lotes de pedidos para restaurar bases de dados por ordem prioritária. 

    * Os lotes são organizados para que as bases de dados sejam restauradas em paralelo em todas as piscinas.  

    * Os pedidos de restauro são apresentados assíncroneamente para que sejam submetidos rapidamente e em fila para execução em cada piscina.

    * Como os pedidos de restauro são processados em paralelo em todas as piscinas, é melhor distribuir inquilinos importantes em muitas piscinas. 

10. Monitoriza o serviço para determinar quando as bases de dados são restauradas. Depois de uma base de dados de inquilinos ser restaurada, está marcada online no catálogo, e uma soma de rowversão para a base de dados do inquilino é gravada. 

    * As bases de dados dos inquilinos podem ser acedidas pela aplicação assim que estiverem marcadas online no catálogo.

    * Uma soma de valores de remar na base de dados dos inquilinos está armazenada no catálogo. Esta soma funciona como uma impressão digital que permite ao processo de repatriamento determinar se a base de dados foi atualizada na região de recuperação.

## <a name="run-the-recovery-script"></a>Executar o roteiro de recuperação

> [!IMPORTANT]
> Este tutorial restaura bases de dados de cópias de segurança geo-redundantes. Embora estes backups estejam normalmente disponíveis dentro de 10 minutos, pode demorar até uma hora. O guião para até estarem disponíveis.

Imagine que há uma paragem na região em que a aplicação é implementada, e executar o roteiro de recuperação:

1. No PowerShell ISE, no script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definirá o seguinte valor:

    $DemoScenario = 2: Recuperar a app numa região de recuperação, restaurando a partir de cópias de segurança geo-redundantes.

2. Para executar o script, selecione F5.  

    * O script abre numa nova janela PowerShell e, em seguida, inicia um conjunto de trabalhos PowerShell que funcionam em paralelo. Estes trabalhos restauram servidores, piscinas e bases de dados para a região de recuperação.

    * A região de recuperação é a região emparelhada associada à região de Azure em que implementou a aplicação. Para mais informações, consulte [as regiões emparelhadas Azure.](../../best-practices-availability-paired-regions.md) 

3. Monitorize o estado do processo de recuperação na janela PowerShell.

    ![Screenshot que mostra a janela PowerShell onde pode monitorizar o estado do processo de recuperação.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Para explorar o código para os trabalhos de recuperação, reveja os scripts PowerShell na pasta ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Reveja o estado de aplicação durante a recuperação
Enquanto o ponto final da aplicação é desativado no Gestor de Tráfego, a aplicação não está disponível. O catálogo foi restaurado, e todos os inquilinos estão marcados offline. O ponto final da aplicação na região de recuperação é então ativado, e a aplicação está novamente online. Embora a aplicação esteja disponível, os inquilinos aparecem offline no centro de eventos até que as suas bases de dados sejam restauradas. É importante desenhar a sua aplicação para lidar com bases de dados de inquilinos offline.

* Depois de a base de dados do catálogo ter sido recuperada, mas antes que os inquilinos estejam novamente on-line, atualize o centro de eventos wingtip tickets no seu navegador web.

  * No rodapé, note que o nome do servidor do catálogo tem agora um sufixo de recuperação e está localizado na região de recuperação.

  * Note que os inquilinos que ainda não foram restaurados estão marcados como offline e não são selecionáveis.   
 
    ![Processo de recuperação](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Se abrir a página de eventos de um inquilino diretamente enquanto o inquilino está offline, a página apresenta uma notificação offline do inquilino. Por exemplo, se o Contoso Concert Hall estiver offline, tente abrir http://events.wingtip-dpt.&lt o utilizador &gt; .trafficmanager.net/contosoconcerthall.

    ![Screenshot que mostra uma página de eventos offline.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Provisiona um novo inquilino na região de recuperação
Mesmo antes de as bases de dados dos inquilinos serem restauradas, pode providenciar novos inquilinos na região de recuperação. As novas bases de dados de inquilinos previstas na região de recuperação são repatriadas posteriormente com as bases de dados recuperadas.   

1. No PowerShell ISE, no script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definirá a seguinte propriedade:

    $DemoScenario = 3: Provision a um novo inquilino na região de recuperação.

2. Para executar o script, selecione F5.

3. A página de eventos do Hawthorn Hall abre no navegador quando o provisionamento termina. 

    Note que a base de dados hawthorn Hall está localizada na região de recuperação.

    ![Hawthorn Hall a provisionado na região de recuperação](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. No navegador, atualize a página do hub de eventos wingtip tickets para ver Hawthorn Hall incluído. 

    Se assevisionou o Hawthorn Hall sem esperar que os outros inquilinos restaurassem, outros inquilinos ainda podem estar offline.

## <a name="review-the-recovered-state-of-the-application"></a>Rever o estado recuperado do pedido

Quando o processo de recuperação termina, a aplicação e todos os inquilinos estão totalmente funcionais na região de recuperação. 

1. Depois do visor na janela da consola PowerShell indicar que todos os inquilinos estão recuperados, refresque o centro de eventos. 

    Os inquilinos aparecem todos online, incluindo o novo inquilino, Hawthorn Hall.

    ![Recuperados e novos inquilinos no centro de eventos](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Clique na Sala de Concertos Contoso e abra a página de eventos. 

    No rodapé, note que a base de dados está localizada no servidor de recuperação localizado na região de recuperação.

    ![Contoso na região de recuperação](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. No [portal Azure,](https://portal.azure.com)abra a lista de grupos de recursos.  

    Note o grupo de recursos que implementou, mais o grupo de recursos de recuperação, com o sufixo de recuperação. O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de novos recursos criados durante a paralisação. 

4. Abra o grupo de recursos de recuperação e note os seguintes itens:

   * As versões de recuperação do catálogo e dos inquilinos1 servidores, com o sufixo de recuperação. As bases de dados restauradas do catálogo e dos inquilinos destes servidores têm os nomes usados na região original.

   * Os inquilinos2-dpt- &lt; utilizador &gt; -recovery SQL servidor. Este servidor é utilizado para o fornecimento de novos inquilinos durante a paralisação.

   * O serviço de aplicações nomeou o utilizador de região de recuperação de &lt; &gt; - &lt; &gt; eventos, que é a instância de recuperação da aplicação de eventos.

     ![Recursos contoso na região de recuperação](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Abra o servidor SQL de recuperação de &lt; inquilinos2-dpt- &gt; utilizador. Note que contém a base de dados hawthornhall e a piscina elástica Pool1. A base de dados hawthornhall é configurada como uma base de dados elástica na piscina elástica Pool1.

## <a name="change-the-tenant-data"></a>Alterar os dados do inquilino 
Nesta tarefa, atualize uma das bases de dados de inquilinos restauradas. O processo de repatriamento copia bases de dados restauradas que foram alteradas para a região original. 

1. No seu navegador, encontre a lista de eventos para a Sala de Concertos contoso, percorra os eventos e note o último evento, Serious Strauss.

2. No PowerShell ISE, no script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definirá o seguinte valor:

    $DemoScenario = 4: Apagar um evento de um inquilino na região de recuperação.

3. Para executar o script, selecione F5.

4. Refresque a página de eventos do Contoso Concert Hall http://events.wingtip-dpt.&lt &gt; (.user .trafficmanager.net/contosoconcerthall) e note que o evento Seriously Strauss está desaparecido.

Neste momento do tutorial, recuperou a candidatura, que está agora a decorrer na região de recuperação. Você adifou um novo inquilino na região de recuperação e modificou os dados de um dos inquilinos restaurados.  

> [!NOTE]
> Outros tutoriais na amostra não foram concebidos para funcionar com a app no estado de recuperação. Se quiser explorar outros tutoriais, não se esqueça de repatriar a aplicação primeiro.

## <a name="repatriation-process-overview"></a>Visão geral do processo de repatriamento

O processo de repatriamento reverte a aplicação e as suas bases de dados para a sua região original após a paragem.

![Repatriamento geo-restaurador](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

O processo:

1. Interrompe qualquer atividade de restauro em curso e cancela quaisquer pedidos de restauro de bases de dados pendentes ou a bordo.

2. Reativa nas bases de dados de inquilinos originais da região que não foram alteradas desde a paralisação. Estas bases de dados incluem as que ainda não foram recuperadas e as recuperadas, mas não alteradas posteriormente. As bases de dados reativadas são exatamente como as últimas acedidas pelos seus inquilinos.

3. Fornece uma imagem espelhada do servidor do novo inquilino e piscina elástica na região original. Após esta ação estar concluída, o novo pseudónimo do inquilino é atualizado para apontar para este servidor. A atualização do pseudónimo faz com que o novo inquilino a bordo ocorra na região original em vez da região de recuperação.

3. Utiliza a geo-replicação para mover o catálogo para a região original da região de recuperação.

4. Atualiza a configuração da piscina na região original, pelo que é consistente com as alterações que foram feitas na região de recuperação durante a paralisação.

5. Cria os servidores e piscinas necessários para hospedar quaisquer novas bases de dados criadas durante a paralisação.

6. Utiliza a geo-replicação para repatriar bases de dados de inquilinos restaurados que foram atualizadas após a restauração e todas as novas bases de dados de inquilinos a provisionadas durante a paralisação. 

7. Limpa os recursos criados na região de recuperação durante o processo de restauro.

Para limitar o número de bases de dados de inquilinos que precisam de ser repatriadas, os passos 1 a 3 são feitos rapidamente.  

O passo 4 só é feito se o catálogo da região de recuperação tiver sido modificado durante a paralisação. O catálogo é atualizado se forem criados novos inquilinos ou se alguma base de dados ou configuração de piscina for alterada na região de recuperação.

É importante que o passo 7 cause uma perturbação mínima para os inquilinos e nenhum dado seja perdido. Para atingir este objetivo, o processo utiliza a geo-replicação.

Antes de cada base de dados ser geo-replicada, a base de dados correspondente na região original é eliminada. A base de dados na região de recuperação é então geo-replicada, criando uma réplica secundária na região original. Após a replicação estar concluída, o inquilino é marcado offline no catálogo, que quebra quaisquer ligações à base de dados na região de recuperação. A base de dados é então chumbada, fazendo com que quaisquer transações pendentes processem no secundário para que nenhum dado seja perdido. 

No plano de trabalho, as funções de base de dados são invertidas. O secundário na região original torna-se a base de dados primária de leitura-escrita, e a base de dados na região de recuperação torna-se apenas secundária. A entrada do inquilino no catálogo é atualizada para fazer referência à base de dados na região original, e o inquilino está marcado online. Neste momento, o repatriamento da base de dados está completo. 

As aplicações devem ser escritas com lógica de relemis para garantir que se reconectam automaticamente quando as ligações são quebradas. Quando usam o catálogo para intermediar a reconexão, ligam-se à base de dados repatriada na região original. Embora a breve desconexão não seja muitas vezes notada, pode optar por repatriar bases de dados fora do horário comercial.

Após o repatriamento de uma base de dados, a base de dados secundária na região de recuperação pode ser eliminada. A base de dados na região original depende novamente do geo-restauro para proteção de DR.

Na etapa 8, os recursos na região de recuperação, incluindo os servidores de recuperação e piscinas, são eliminados.

## <a name="run-the-repatriation-script"></a>Executar o roteiro de repatriamento
Imaginemos que a paralisação está resolvida e executam o guião de repatriamento.

Se seguiu o tutorial, o guião reativa imediatamente o Fabrikam Jazz Club e o Dogwood Dojo na região original porque não têm decisão. Em seguida, repatria o novo inquilino, Hawthorn Hall, e Contoso Concert Hall porque foi modificado. O guião também repatria o catálogo, que foi atualizado quando Hawthorn Hall foi a provisionado.
  
1. No PowerShell ISE, no script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 script, verifique se o processo de Sincronização de Catálogo ainda está em execução na sua instância PowerShell. Se necessário, reinicie-o definindo:

    $DemoScenario = 1: Comece a sincronizar informações de configuração do servidor de inquilinos, piscina e base de dados no catálogo.

    Para executar o script, selecione F5.

2.  Em seguida, para iniciar o processo de repatriamento, definir:

    $DemoScenario = 5: Repatriar a app para a sua região original.

    Para executar o script de recuperação numa nova janela PowerShell, selecione F5. O repatriamento demora vários minutos e pode ser monitorizado na janela PowerShell.

3. Enquanto o script estiver em execução, refresque a página do centro de eventos http://events.wingtip-dpt.&lt &gt; (.user .trafficmanager.net).

    Note que todos os inquilinos estão online e acessíveis em todo este processo.

4. Selecione o Fabrikam Jazz Club para abri-lo. Se não modificou este inquilino, note a partir do rodapé que o servidor já está revertido para o servidor original.

5. Abra ou refresque a página de eventos do Contoso Concert Hall. Note-se a partir do rodapé que, inicialmente, a base de dados ainda está no servidor de recuperação. 

6. Refresque a página de eventos da Sala de Concertos Contoso quando o processo de repatriamento terminar, e note que a base de dados está agora na sua região original.

7. Refresque o centro de eventos novamente e abra o Hawthorn Hall. Note-se que a sua base de dados também está localizada na região original. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Limpar recursos da região de recuperação após repatriamento
Após o repatriamento estar completo, é seguro apagar os recursos na região de recuperação. 

> [!IMPORTANT]
> Apague estes recursos prontamente para parar toda a faturação para eles.

O processo de restauro cria todos os recursos de recuperação num grupo de recursos de recuperação. O processo de limpeza elimina este grupo de recursos e remove todas as referências aos recursos do catálogo. 

1. No PowerShell ISE, no seguinte script ...\Módulos de aprendizagem\Continuidade de Negócios e Desastres Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, definido:
    
    $DemoScenario = 6: Eliminar recursos obsoletos da região de recuperação.

2. Para executar o script, selecione F5.

Depois de limpar os scripts, a aplicação está de volta ao ponto de partida. Neste ponto, pode executar o script novamente ou experimentar outros tutoriais.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Projetar a aplicação para garantir que a app e a base de dados estão co-localizadas 
A aplicação destina-se sempre a ligar-se a partir de uma instância na mesma região que a base de dados do arrendatário. Este design reduz a latência entre a aplicação e a base de dados. Esta otimização pressupõe que a interação app-to-database é mais chattier do que a interação utilizador-app.  

As bases de dados dos inquilinos podem estar espalhadas por regiões de recuperação e originais durante algum tempo durante o repatriamento. Para cada base de dados, a aplicação procura a região em que a base de dados está localizada fazendo uma pesquisas de DNS no nome do servidor do inquilino. O nome do servidor é um pseudónimo. O nome do servidor pseudónimo contém o nome da região. Se a aplicação não estiver na mesma região que a base de dados, redireciona para o caso na mesma região que o servidor. Redirecionamento para o caso na mesma região que a base de dados minimiza a latência entre a app e a base de dados.  

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Utilize o catálogo de inquilinos para guardar periodicamente informações de configuração atualizadas, o que permite criar um ambiente de recuperação de imagem espelhada noutra região.
> * Recuperar bases de dados na região de recuperação utilizando o geo-restauro.
> * Atualize o catálogo de inquilinos para refletir as localizações restauradas da base de dados dos inquilinos. 
> * Utilize um pseudónimo DNS para permitir que uma aplicação se conecte ao catálogo do inquilino sem reconfiguração.
> * Utilize a geo-replicação para repatriar bases de dados recuperadas para a sua região original após a solução de uma paragem.

Experimente a [recuperação de desastres para uma aplicação SaaS multitenante usando tutorial de geo-replicação de base de dados](./saas-dbpertenant-dr-geo-replication.md) para aprender a usar a geo-replicação para reduzir drasticamente o tempo necessário para recuperar uma aplicação multitenante em larga escala.

## <a name="additional-resources"></a>Recursos adicionais

[Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)