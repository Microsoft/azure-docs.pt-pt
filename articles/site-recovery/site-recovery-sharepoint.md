---
title: Recuperação de desastres para uma app SharePoint de vários níveis usando a recuperação do site Azure
description: Este artigo descreve como configurar a recuperação de desastres para uma aplicação sharePoint de vários níveis usando as capacidades de recuperação do local de Azure.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: 08e971e52f994ec5fa5663708fa9f173daf33d80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013756"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Confiem a recuperação de desastres para uma aplicação sharePoint multi-nível para recuperação de desastres utilizando a recuperação do site Azure

Este artigo descreve em detalhe como proteger uma aplicação SharePoint usando  [a recuperação do site Azure](site-recovery-overview.md).


## <a name="overview"></a>Descrição Geral

O Microsoft SharePoint é uma aplicação poderosa que pode ajudar um grupo ou departamento a organizar, colaborar e partilhar informações. O SharePoint pode fornecer portais intranet, gestão de documentos e ficheiros, colaboração, redes sociais, extranets, websites, pesquisa de empresas e inteligência empresarial. Possui ainda integração de sistemas, integração de processos e capacidades de automatização do fluxo de trabalho. Normalmente, as organizações consideram-na uma aplicação Tier-1 sensível ao tempo de inatividade e perda de dados.

Hoje em dia, o Microsoft SharePoint não fornece nenhuma capacidade de recuperação de desastres fora da caixa. Independentemente do tipo e escala de um desastre, a recuperação envolve o uso de um centro de dados de espera para o qual você pode recuperar a fazenda. Os centros de dados de espera são necessários para cenários em que sistemas e backups locais redundantes não possam recuperar da paragem no centro de dados primário.

Uma boa solução de recuperação de desastres deve permitir modelar planos de recuperação em torno das complexas arquiteturas de aplicações, como o SharePoint. Também deve ter a capacidade de adicionar passos personalizados para lidar com mapeamentos de aplicações entre vários níveis e, portanto, fornecer um failover de um clique único com um RTO mais baixo em caso de desastre.

Este artigo descreve em detalhe como proteger uma aplicação SharePoint usando [a recuperação do site Azure](site-recovery-overview.md). Este artigo abrangerá as melhores práticas para replicar uma aplicação de três níveis sharePoint para o Azure, como você pode fazer um exercício de recuperação de desastres, e como você pode falhar a aplicação para Azure.

Pode ver o vídeo abaixo sobre a recuperação de uma aplicação de vários níveis para o Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de entender o seguinte:

1. [Replicando uma máquina virtual para Azure](./vmware-azure-tutorial.md)
2. Como [desenhar uma rede de recuperação](./concepts-on-premises-to-azure-networking.md)
3. [Fazendo um teste falhado para Azure](site-recovery-test-failover-to-azure.md)
4. [Fazendo um falhanço em Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como replicar o [SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Arquitetura SharePoint

O SharePoint pode ser implementado em um ou mais servidores usando topologias hierárquicos e funções de servidor para implementar um design agrícola que cumpra objetivos e objetivos específicos. Uma típica grande e alta demanda fazenda de servidores SharePoint que suporta um elevado número de utilizadores simultâneos e um grande número de itens de conteúdo usam o agrupamento de serviços como parte da sua estratégia de escalabilidade. Esta abordagem envolve executar serviços em servidores dedicados, agrupar estes serviços em conjunto e, em seguida, escalar os servidores como um grupo. A topologia seguinte ilustra o agrupamento de serviços e servidores para uma fazenda de servidores SharePoint de três níveis. Consulte a documentação do SharePoint e as arquiteturas da linha de produtos para obter orientações detalhadas sobre diferentes topologias do SharePoint. Pode encontrar mais detalhes sobre a implementação do SharePoint 2013 [neste documento.](/SharePoint/sharepoint-server)



![Padrão de implantação 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

A Recuperação do Site é agnóstica de aplicações e deve trabalhar com qualquer versão do SharePoint a funcionar numa máquina suportada. Para a criação deste artigo, foram utilizadas máquinas virtuais VMware com Windows Server 2012 R2 Enterprise. SharePoint 2013 Edição Empresarial e edição empresarial SQL 2014 foram usados.

### <a name="source-and-target"></a>Fonte e alvo

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Servidor físico** | Yes | Yes
**Azure** | ND | Sim


### <a name="things-to-keep-in-mind"></a>Aspetos a ter em conta

Se estiver a utilizar um cluster baseado em disco partilhado como qualquer nível da sua aplicação, então não poderá utilizar a replicação da Recuperação do Site para replicar essas máquinas virtuais. Pode utilizar a replicação nativa fornecida pela aplicação e, em seguida, usar um [plano de recuperação](site-recovery-create-recovery-plans.md) para falhar em todos os níveis.

## <a name="replicating-virtual-machines"></a>Replicação de máquinas virtuais

Siga [esta orientação](./vmware-azure-tutorial.md) para começar a replicar a máquina virtual para Azure.

* Uma vez concluída a replicação, certifique-se de que vai a cada máquina virtual de cada nível e selecione a mesma disponibilidade definida em 'Replicado item > Definições > Propriedades > Computação e Rede'. Por exemplo, se o seu nível web tiver 3 VMs, certifique-se de que todos os 3 VMs estão configurados para fazer parte da mesma disponibilidade definida em Azure.

    ![Conjunto de disponibilidade](./media/site-recovery-sharepoint/select-av-set.png)

* Para obter orientações sobre a proteção do Diretório Ativo e do DNS, consulte o documento [Protect Ative Directory e DNS.](site-recovery-active-directory.md)

* Para obter orientações sobre a proteção do nível de base de dados em execução no servidor SQL, consulte o documento [Protect SQL Server.](site-recovery-sql.md)

## <a name="networking-configuration"></a>Configuração da rede

### <a name="network-properties"></a>Propriedades da rede

* Para os VMs de nível Web e aplicação, configurar as definições de rede no portal Azure para que os VMs se adquiram à rede DR certa após o failover.

    ![Selecione Rede](./media/site-recovery-sharepoint/select-network.png)


* Se estiver a utilizar um IP estático, especifique o IP que pretende que a máquina virtual tome no campo **IP alvo**

    ![Definir IP estático](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS e Encaminhamento de Tráfego

Para sites virados para a Internet, [crie um perfil de Gestor de Tráfego do tipo 'Prioridade'](../traffic-manager/quickstart-create-traffic-manager-profile.md) na subscrição do Azure. E, em seguida, configurar o seu perfil DNS e Traffic Manager da seguinte forma.


| **Onde** | **Origem** | **Destino**|
| --- | --- | --- |
| DNS público | DNS público para sites SharePoint <br/><br/> Ex: sharepoint.contoso.com | Gestor de Tráfego <br/><br/> contososharepoint.trafficmanager.net |
| DNS no local | sharepointonprem.contoso.com | IP público na exploração agrícola |


No perfil de Gestor de Tráfego, [crie os pontos finais primários e de recuperação.](../traffic-manager/traffic-manager-configure-priority-routing-method.md) Utilize o ponto final externo para o ponto final no local e IP público para o ponto final do Azure. Certifique-se de que a prioridade é mais elevada para o ponto final no local.

Hospedar uma página de teste numa porta específica (por exemplo, 800) no nível web do SharePoint, de modo a que o Gestor de Tráfego detete automaticamente a falha no registo de disponibilidade. Esta é uma solução alternativa para o caso de não conseguir ativar a autenticação anónima em nenhum dos seus sites SharePoint.

[Configure o perfil do Gestor de Tráfego](../traffic-manager/traffic-manager-configure-priority-routing-method.md) com as definições abaixo.

* Método de encaminhamento - 'Prioridade'
* Tempo DNS para viver (TTL) - '30 segundos'
* Definições do monitor de pontos finais - Se conseguir ativar a autenticação anónima, pode dar um ponto final específico do site. Ou, pode utilizar uma página de teste numa porta específica (por exemplo, 800).

## <a name="creating-a-recovery-plan"></a>Criação de um plano de recuperação

Um plano de recuperação permite sequenciar a falha de vários níveis numa aplicação multi-nível, mantendo assim a consistência da aplicação. Siga os passos abaixo enquanto cria um plano de recuperação para uma aplicação web multi-nível. [Saiba mais sobre a criação de um plano de recuperação.](site-recovery-runbook-automation.md#customize-the-recovery-plan)

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais a grupos de failover

1. Crie um plano de recuperação adicionando os VMs de nível web e app.
2. Clique em 'Personalizar' para agrupar os VMs. Por predefinição, todos os VMs fazem parte do 'Grupo 1'.

    ![Personalizar RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Crie outro Grupo (Grupo 2) e mova os VMs de nível Web para o novo grupo. Os VMs de nível da aplicação devem fazer parte do 'Grupo 1' e os VMs de nível Web devem fazer parte do 'Grupo 2'. Isto é para garantir que o arranque de VMs de nível de aplicação seja seguido primeiro por VMs de nível Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação

Pode implementar os scripts de Recuperação do Local de Azure mais utilizados na sua conta de Automação clicando no botão 'Implementar para Azure' abaixo. Quando estiver a utilizar qualquer script publicado, certifique-se de que segue a orientação no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script pré-ação ao 'Grupo 1' para falhar no grupo SQL Availability. Utilize o script 'ASR-SQL-FailoverAG' publicado nos scripts da amostra. Certifique-se de que segue a orientação no script e faz as alterações necessárias no script de forma adequada.

    ![Adicionar-AG-Script-Passo-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Adicionar-AG-Script-Passo-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adicione um script de ação pós-post para anexar um equilibrador de carga nas máquinas virtuais falhadas de nível Web (Grupo 2). Utilize o script 'ASR-AddSingleLoadBalancer' publicado nos scripts da amostra. Certifique-se de que segue a orientação no script e faz as alterações necessárias no script de forma adequada.

    ![Adicionar-LB-Script-Passo-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Adicionar LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adicione um passo manual para atualizar os registos dns para apontar para a nova quinta em Azure.

    * Para sites virados para a Internet, não são necessárias atualizações de DNS post failover. Siga os passos descritos na secção "Orientação de rede" para configurar o Gestor de Tráfego. Se o perfil de Gestor de Tráfego tiver sido configurado como descrito na secção anterior, adicione um script para abrir a porta falsa (800 no exemplo) no VM Azure.

    * Para sites de face interna, adicione um passo manual para atualizar o registo DNS para apontar para o ip do novo equilibrador de carga do novo nível Web VM.

4. Adicione um passo manual para restaurar a aplicação de pesquisa a partir de uma cópia de segurança ou iniciar um novo serviço de pesquisa.

5. Para restaurar a aplicação do serviço de busca a partir de uma cópia de segurança, siga abaixo dos passos.

    * Este método pressupõe que foi realizada uma cópia de segurança da Aplicação do Serviço de Busca antes do evento catastrófico e que a cópia de segurança está disponível no site dr.
    * Isto pode ser facilmente conseguido agendando a cópia de segurança (por exemplo, uma vez por dia) e usando um procedimento de cópia para colocar a cópia no site DR. Os procedimentos de cópia podem incluir programas scripted como AzCopy (Azure Copy) ou configurar o DFSR (Replicação de Serviços de Ficheiros Distribuídos).
    * Agora que a quinta SharePoint está em funcionamento, navegue pela Administração Central, 'Backup and Restore' e selecione Restore. A restauração interroga a localização de backup especificada (pode ser necessário atualizar o valor). Selecione a cópia de segurança da Aplicação de Serviço de Busca que pretende restaurar.
    * A procura foi restaurada. Tenha em mente que o restauro espera encontrar a mesma topologia (o mesmo número de servidores) e as mesmas letras de disco rígido atribuídas a esses servidores. Para obter mais informações, consulte [a aplicação do serviço 'Restaurar a procura no documento SharePoint 2013'.](/SharePoint/administration/restore-a-search-service-application)


6. Para começar com uma nova aplicação de serviço de pesquisa, siga abaixo os passos.

    * Este método pressupõe que uma cópia de segurança da base de dados "Administração de pesquisa" está disponível no site dr.
    * Uma vez que as outras bases de dados de Aplicação de Serviço de Pesquisa não são replicadas, precisam de ser recriadas. Para tal, navegue para a Administração Central e elimine a Aplicação do Serviço de Pesquisa. Em quaisquer servidores que acolhem o Índice de Pesquisa, elimine os ficheiros de índice.
    * Re-crie a Aplicação do Serviço de Pesquisa e esta recria as bases de dados. Recomenda-se ter um script preparado que recria esta aplicação de serviço, uma vez que não é possível realizar todas as ações através do GUI. Por exemplo, definir a localização da unidade de índice e configurar a topologia de pesquisa só é possível utilizando cmdlets SharePoint PowerShell. Utilize o cmdlet do Windows PowerShell Restore-SPEnterpriseSearchServiceApplication e especifique a base de dados da Administração de Pesquisa, enviada e replicada, Search_Service__DB. Este cmdlet dá a configuração de pesquisa, esquema, propriedades geridas, regras e fontes e cria um conjunto padrão dos outros componentes.
    * Uma vez que a Aplicação do Serviço de Busca tenha sido recriada, tem de iniciar um rastreio completo para cada fonte de conteúdo restaurar o Serviço de Pesquisa. Perde-se algumas informações analíticas da quinta no local, como recomendações de pesquisa.

7. Uma vez concluídos todos os passos, guarde o plano de recuperação e o plano final de recuperação será o seguinte.

    ![RP salvo](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Fazer um teste falhado
Siga [esta orientação](site-recovery-test-failover-to-azure.md) para fazer um teste de failover.

1.  Vá ao portal Azure e selecione o cofre do Serviço de Recuperação.
2.  Clique no plano de recuperação criado para a aplicação SharePoint.
3.  Clique em 'Test Failover'.
4.  Selecione o ponto de recuperação e a rede virtual Azure para iniciar o processo de failover do teste.
5.  Uma vez que o ambiente secundário esteja em cima, pode realizar as suas validações.
6.  Uma vez concluídas as validações, pode clicar em "Falha de teste de limpeza" no plano de recuperação e o ambiente de falha de teste é limpo.

Para obter orientações sobre a realização de testes de failover para AD e DNS, consulte [considerações de failover test para documento AD e DNS.](site-recovery-active-directory.md#test-failover-considerations)

Para obter orientações sobre a execução do teste para grupos de disponibilidade SQL Always ON, consulte a [Aplicação DR com Azure Site Recovery e faça](site-recovery-sql.md#disaster-recovery-of-an-application) o documento de failover do teste.

## <a name="doing-a-failover"></a>Fazendo um failover
Siga [esta orientação](site-recovery-failover.md) para fazer um failover.

1.  Vá ao portal Azure e selecione o cofre dos Serviços de Recuperação.
2.  Clique no plano de recuperação criado para a aplicação SharePoint.
3.  Clique em 'Failover'.
4.  Selecione o ponto de recuperação para iniciar o processo de failover.

## <a name="next-steps"></a>Passos seguintes
Pode aprender mais sobre [a replicação de outras aplicações](site-recovery-workload.md) utilizando a Recuperação do Site.
