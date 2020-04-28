---
title: Recuperação de desastres para uma aplicação sharePoint de vários níveis usando a recuperação do site Azure
description: Este artigo descreve como configurar a recuperação de desastres para uma aplicação de sharePoint de vários níveis usando capacidades de recuperação de sites Azure.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: d74e28ce470c23bbc8ee2081532a198c260ccea5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74706369"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Configurar a recuperação de desastres para uma aplicação sharePoint de vários níveis para recuperação de desastres usando a Recuperação do Site Azure

Este artigo descreve detalhadamente como proteger uma aplicação SharePoint utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)


## <a name="overview"></a>Descrição geral

O Microsoft SharePoint é uma aplicação poderosa que pode ajudar um grupo ou departamento a organizar, colaborar e partilhar informações. O SharePoint pode fornecer portais intranet, gestão de documentos e ficheiros, colaboração, redes sociais, extranets, websites, pesquisa empresarial e inteligência empresarial. Possui também capacidades de integração do sistema, integração de processos e automatização de fluxos de trabalho. Tipicamente, as organizações consideram-na uma aplicação Tier-1 sensível ao tempo de inatividade e perda de dados.

Hoje em dia, o Microsoft SharePoint não fornece quaisquer capacidades de recuperação de desastres fora da caixa. Independentemente do tipo e escala de um desastre, a recuperação envolve o uso de um centro de dados de espera para onde pode recuperar a quinta. Os centros de dados de espera são necessários para cenários em que sistemas e backups locais redundantes não possam recuperar da paralisação no centro de dados primário.

Uma boa solução de recuperação de desastres deve permitir a modelação de planos de recuperação em torno das complexas arquiteturas de aplicações, como o SharePoint. Deve também ter a capacidade de adicionar passos personalizados para lidar com mapeamentos de aplicações entre vários níveis e, consequentemente, fornecer um failover de um clique com um RTO mais baixo em caso de desastre.

Este artigo descreve detalhadamente como proteger uma aplicação SharePoint utilizando a Recuperação do [Site Azure.](site-recovery-overview.md) Este artigo abrangerá as melhores práticas para replicar uma aplicação sharePoint de três níveis para o Azure, como pode fazer um exercício de recuperação de desastres e como pode falhar a aplicação ao Azure.

Pode ver o vídeo abaixo sobre a recuperação de uma aplicação de vários níveis para o Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de compreender o seguinte:

1. [Replicando uma máquina virtual para Azure](site-recovery-vmware-to-azure.md)
2. Como [conceber uma rede de recuperação](site-recovery-network-design.md)
3. [Fazer um teste falha em Azure](site-recovery-test-failover-to-azure.md)
4. [Fazendo uma falha para Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como replicar o [Servidor SQL](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Arquitetura SharePoint

O SharePoint pode ser implantado em um ou mais servidores usando topologias e funções de servidor escalonados para implementar um design de fazenda que cumpra objetivos e objetivos específicos. Uma típica grande e alta demanda de servidores SharePoint que suporta um elevado número de utilizadores simultâneos e um grande número de itens de conteúdo usam o agrupamento de serviços como parte da sua estratégia de escalabilidade. Esta abordagem envolve executar serviços em servidores dedicados, agrupar estes serviços e, em seguida, escalar os servidores como um grupo. A topologia seguinte ilustra o serviço e o agrupamento de servidores para uma quinta de servidores SharePoint de três níveis. Consulte a documentação do SharePoint e as arquiteturas da linha de produtos para obter orientações detalhadas sobre diferentes topoologias do SharePoint. Pode encontrar mais detalhes sobre a implementação do SharePoint 2013 [neste documento.](https://technet.microsoft.com/library/cc303422.aspx)



![Padrão de implantação 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

A Recuperação do Site é agnóstica de aplicações e deve funcionar com qualquer versão do SharePoint a funcionar numa máquina suportada. Para criar este artigo, foram utilizadas máquinas virtuais VMware com Windows Server 2012 R2 Enterprise. Foram utilizadas edição da SharePoint 2013 Enterprise e edição do servidor SQL 2014 Enterprise.

### <a name="source-and-target"></a>Fonte e alvo

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim
**Azure** | ND | Sim


### <a name="things-to-keep-in-mind"></a>Aspetos a ter em conta

Se estiver a utilizar um cluster baseado em disco partilhado como qualquer nível da sua aplicação, não poderá utilizar a replicação da Recuperação do Site para replicar essas máquinas virtuais. Pode utilizar a replicação nativa fornecida pela aplicação e, em seguida, usar um plano de [recuperação](site-recovery-create-recovery-plans.md) para falhar em todos os níveis.

## <a name="replicating-virtual-machines"></a>Replicação de máquinas virtuais

Siga [esta orientação](site-recovery-vmware-to-azure.md) para começar a replicar a máquina virtual para Azure.

* Uma vez concluída a replicação, certifique-se de que vai a cada máquina virtual de cada nível e selecione a mesma disponibilidade definida em 'Replicated item > Definições > Propriedades > Compute e Rede'. Por exemplo, se o seu nível web tiver 3 VMs, certifique-se de que todos os 3 VMs estão configurados para fazer parte da mesma disponibilidade definida no Azure.

    ![Conjunto de disponibilidade de conjunto](./media/site-recovery-sharepoint/select-av-set.png)

* Para obter orientações sobre a proteção do Diretório Ativo e do DNS, consulte o [documento Protect Ative Directory e DNS.](site-recovery-active-directory.md)

* Para obter orientações sobre a proteção do nível de base de dados em funcionamento no servidor SQL, consulte o documento [Protect SQL Server.](site-recovery-sql.md)

## <a name="networking-configuration"></a>Configuração de rede

### <a name="network-properties"></a>Propriedades da rede

* Para os VMs de nível Web e App, configure as definições de rede no portal Azure para que os VMs se adequem à rede DER direita após a falha.

    ![Rede selecionada](./media/site-recovery-sharepoint/select-network.png)


* Se estiver a utilizar um IP estático, especifique o IP que pretende que a máquina virtual tome no campo **IP target**

    ![Definir IP estático](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS e Encaminhamento de Tráfego

Para sites virados para a Internet, [crie um perfil de Gestor de Tráfego do tipo 'Prioridade'](../traffic-manager/traffic-manager-create-profile.md) na subscrição do Azure. E, em seguida, configurar o seu perfil DeNs e Traffic Manager da seguinte forma.


| **Onde** | **Origem** | **Destino**|
| --- | --- | --- |
| DNS público | DNS públicos para sites sharePoint <br/><br/> Ex: sharepoint.contoso.com | Gestor de Tráfego <br/><br/> contososharepoint.trafficmanager.net |
| No local DNS | sharepointonprem.contoso.com | IP público na quinta no local |


No perfil do Gestor de Tráfego, [crie os pontos finais primários e de recuperação.](../traffic-manager/traffic-manager-configure-priority-routing-method.md) Utilize o ponto final externo para o ponto final no local e IP público para o ponto final do Azure. Certifique-se de que a prioridade é definida mais para o ponto final no local.

Hospedar uma página de teste numa porta específica (por exemplo, 800) no nível web do SharePoint para que o Gestor de Tráfego detete automaticamente o failover do post de disponibilidade. Esta é uma suposição no caso de não conseguir ativar a autenticação anónima em nenhum dos seus sites do SharePoint.

[Configure o perfil do Gestor de Tráfego](../traffic-manager/traffic-manager-configure-priority-routing-method.md) com as definições abaixo.

* Método de encaminhamento - 'Prioridade'
* Hora do DNS para viver (TTL) - '30 segundos'
* Definições de monitor de endpoint - Se puder ativar a autenticação anónima, pode dar um ponto final específico do site. Ou, pode utilizar uma página de teste numa porta específica (por exemplo, 800).

## <a name="creating-a-recovery-plan"></a>Criação de um plano de recuperação

Um plano de recuperação permite sequenciar a falha de vários níveis numa aplicação de vários níveis, mantendo assim a consistência da aplicação. Siga os passos abaixo, criando um plano de recuperação para uma aplicação web de vários níveis. [Saiba mais sobre a criação de um plano de recuperação.](site-recovery-runbook-automation.md#customize-the-recovery-plan)

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais a grupos de failover

1. Crie um plano de recuperação adicionando os VMs de app e nível Web.
2. Clique em 'Personalizar' para agrupar os VMs. Por padrão, todos os VMs fazem parte do 'Grupo 1'.

    ![Personalizar RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Crie outro Grupo (Grupo 2) e mova os VMs de nível Web para o novo grupo. Os VMs do nível de aplicações devem fazer parte do 'Grupo 1' e os VMs de nível Web devem fazer parte do 'Grupo 2'. Isto é para garantir que os VMs de nível app arranquem primeiro seguidos por VMs de nível Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação

Pode implantar os scripts de recuperação de sítios azure mais utilizados na sua conta Automation clicando no botão 'Deploy to Azure' abaixo. Quando estiver a utilizar qualquer guião publicado, certifique-se de que segue a orientação no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação ao 'Grupo 1' para falhar no grupo de disponibilidade SQL. Utilize o script 'ASR-SQL-FailoverAG' publicado nos scripts da amostra. Certifique-se de que segue a orientação no script e faça as alterações necessárias no script adequadamente.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adicione um script de pós-acção para fixar um equilibrador de carga nas máquinas virtuais falhadas do nível Web (Grupo 2). Utilize o script 'ASR-AddSingleLoadBalancer' publicado nos scripts da amostra. Certifique-se de que segue a orientação no script e faça as alterações necessárias no script adequadamente.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adicione um passo manual para atualizar os registos dNS para apontar para a nova quinta em Azure.

    * Para sites virados para a Internet, não são necessárias atualizações de DNS. Siga os passos descritos na secção "Orientação de Rede" para configurar o Gestor de Tráfego. Se o perfil do Gestor de Tráfego tiver sido configurado como descrito na secção anterior, adicione um script para abrir a porta do boneco (800 no exemplo) no VM Azure.

    * Para sites internos virados, adicione um passo manual para atualizar o registo DNS para apontar para o novo IP do equilíbrio de carga do novo nível Web VM.

4. Adicione um passo manual para restaurar a aplicação de pesquisa a partir de uma cópia de segurança ou iniciar um novo serviço de pesquisa.

5. Para restaurar a aplicação de serviço de pesquisa a partir de uma cópia de segurança, siga os passos abaixo.

    * Este método pressupõe que foi realizada uma cópia de segurança da Aplicação de Serviço de Busca antes do evento catastrófico e que a cópia de segurança está disponível no site da DR.
    * Isto pode ser facilmente conseguido agendando a cópia de segurança (por exemplo, uma vez por dia) e utilizando um procedimento de cópia para colocar a cópia no site dr. Os procedimentos de cópia podem incluir programas scripted como AzCopy (Azure Copy) ou configuração do DFSR (Replicação de Serviços de Ficheiros Distribuídos).
    * Agora que a quinta SharePoint está em funcionamento, navegue na Administração Central, 'Backup and Restore' e selecione Restore. A restauração interroga a localização de reserva especificada (pode ser necessário atualizar o valor). Selecione a cópia de segurança do Pedido de Serviço de Pesquisa que deseja restaurar.
    * A procura está restaurada. Tenha em mente que o restauro espera encontrar a mesma topologia (mesmo número de servidores) e as mesmas letras de disco rígido atribuídas a esses servidores. Para mais informações, consulte 'Restaurar a aplicação de serviço de pesquisa no documento [SharePoint 2013'.](https://technet.microsoft.com/library/ee748654.aspx)


6. Para começar com uma nova aplicação de serviço de pesquisa, siga abaixo os passos.

    * Este método pressupõe que uma cópia de segurança da base de dados "Search Administration" está disponível no site da DR.
    * Uma vez que as outras bases de dados de aplicações de serviço de pesquisa não são replicadas, precisam de ser recriadas. Para isso, navegue para a Administração Central e elimine a Aplicação de Serviço de Busca. Em quaisquer servidores que apresentem o Índice de Pesquisa, elimine os ficheiros de índice.
    * Recrie a Aplicação de Serviço de Pesquisa e isto recria as bases de dados. Recomenda-se ter um script preparado que recria esta aplicação de serviço, uma vez que não é possível realizar todas as ações através do GUI. Por exemplo, definir a localização da unidade de índice e configurar a topologia de pesquisa só é possível utilizando cmdlets SharePoint PowerShell. Utilize a aplicação de serviços de base de pesquisa do Windows PowerShell cmdlet Restore-SPEnterpriseSearchService Application e especifique a base de dados da Administração de Pesquisa enviada e replicada, Search_Service__DB. Este cmdlet dá a configuração de pesquisa, esquema, propriedades geridas, regras e fontes e cria um conjunto padrão dos outros componentes.
    * Uma vez recriada a Aplicação de Serviço de Pesquisa, deve iniciar um rastreio completo para cada fonte de conteúdo para restaurar o Serviço de Pesquisa. Perde-se algumas informações de análise da quinta no local, como recomendações de pesquisa.

7. Uma vez concluídos todos os passos, guarde o plano de recuperação e o plano final de recuperação será o seguinte.

    ![RP guardado](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Fazendo um teste failover
Siga [esta orientação](site-recovery-test-failover-to-azure.md) para fazer uma falha no teste.

1.  Vá ao portal Azure e selecione o seu cofre de Serviço de Recuperação.
2.  Clique no plano de recuperação criado para a aplicação SharePoint.
3.  Clique em 'Failover test'.
4.  Selecione ponto de recuperação e rede virtual Azure para iniciar o processo de failover do teste.
5.  Uma vez que o ambiente secundário esteja em cima, pode realizar as suas validações.
6.  Uma vez concluídas as validações, pode clicar em 'Cleanup test failover' no plano de recuperação e o ambiente de failover do teste é limpo.

Para obter orientações sobre a realização de falhas no teste para AD e DNS, consulte as considerações de failover do Teste para o documento [AD e DNS.](site-recovery-active-directory.md#test-failover-considerations)

Para obter orientações sobre a falha do teste para os grupos de disponibilidade SQL Always ON, consulte a [Aplicação DE Execução DR com recuperação do site Azure e efetuar](site-recovery-sql.md#disaster-recovery-of-an-application) o documento de failover do teste.

## <a name="doing-a-failover"></a>Fazendo uma falha
Siga [esta orientação](site-recovery-failover.md) para fazer uma falha.

1.  Vá ao portal Azure e selecione o seu cofre de Serviços de Recuperação.
2.  Clique no plano de recuperação criado para a aplicação SharePoint.
3.  Clique em 'Failover'.
4.  Selecione ponto de recuperação para iniciar o processo de failover.

## <a name="next-steps"></a>Passos seguintes
Pode saber mais sobre a replicação de [outras aplicações](site-recovery-workload.md) utilizando a Recuperação do Site.
