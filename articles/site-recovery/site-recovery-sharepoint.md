---
title: Configurar a recuperação de desastre para um aplicativo do SharePoint de várias camadas usando Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a recuperação de desastres para um aplicativo do SharePoint de várias camadas usando Azure Site Recovery recursos.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: e9b688d54049c21da3276a20e27dcc9ad3d4ceca
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231486"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Configurar a recuperação de desastre para um aplicativo do SharePoint de várias camadas para recuperação de desastre usando Azure Site Recovery

Este artigo descreve detalhadamente como proteger um aplicativo do SharePoint usando o [Azure site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Descrição geral

O Microsoft SharePoint é um aplicativo poderoso que pode ajudar um grupo ou departamento a organizar, colaborar e compartilhar informações. O SharePoint pode fornecer portais de intranet, gerenciamento de documentos e arquivos, colaboração, redes sociais, extranets, sites, pesquisa empresarial e business intelligence. Ele também tem recursos de integração do sistema, integração de processos e automação de fluxo de trabalho. Normalmente, as organizações consideram isso como um aplicativo de camada 1 sensível ao tempo de inatividade e à perda de dados.

Hoje, o Microsoft SharePoint não fornece nenhum recurso de recuperação de desastres pronto para uso. Independentemente do tipo e da escala de um desastre, a recuperação envolve o uso de um data center em espera no qual você pode recuperar o farm. Os data centers em espera são necessários para cenários em que os sistemas e backups com redundância local não podem se recuperar da interrupção no data center primário.

Uma boa solução de recuperação de desastres deve permitir a modelagem de planos de recuperação em relação a arquiteturas de aplicativos complexas, como o SharePoint. Ele também deve ter a capacidade de adicionar etapas personalizadas para lidar com mapeamentos de aplicativos entre várias camadas e, portanto, fornecer um failover de clique único com um RTO inferior em caso de desastre.

Este artigo descreve detalhadamente como proteger um aplicativo do SharePoint usando o [Azure site Recovery](site-recovery-overview.md). Este artigo abordará as práticas recomendadas para replicar um aplicativo do SharePoint de três camadas para o Azure, como você pode fazer uma análise de recuperação de desastre e como é possível realizar o failover do aplicativo para o Azure.

Você pode assistir ao vídeo abaixo sobre como recuperar um aplicativo de várias camadas para o Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de entender o seguinte:

1. [Replicando uma máquina virtual para o Azure](site-recovery-vmware-to-azure.md)
2. Como [criar uma rede de recuperação](site-recovery-network-design.md)
3. [Fazendo um failover de teste para o Azure](site-recovery-test-failover-to-azure.md)
4. [Fazendo um failover para o Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como [replicar SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Arquitetura do SharePoint

O SharePoint pode ser implantado em um ou mais servidores usando topologias em camadas e funções de servidor para implementar um design de farm que atenda a metas e objetivos específicos. Um farm de servidores do SharePoint grande e de alta demanda, que dá suporte a um grande número de usuários simultâneos, e que vários itens de conteúdo usam o agrupamento de serviços como parte de sua estratégia de escalabilidade. Essa abordagem envolve a execução de serviços em servidores dedicados, o agrupamento desses serviços e a expansão dos servidores como um grupo. A topologia a seguir ilustra o serviço e o agrupamento de servidores para um farm de servidores do SharePoint de três camadas. Consulte a documentação do SharePoint e arquiteturas de linha de produto para obter diretrizes detalhadas sobre diferentes topologias do SharePoint. Você pode encontrar mais detalhes sobre a implantação do SharePoint 2013 neste [documento](https://technet.microsoft.com/library/cc303422.aspx).



![Padrão de implantação 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Site Recovery é independente de aplicativo e deve funcionar com qualquer versão do SharePoint em execução em um computador com suporte. Para criar este artigo, as máquinas virtuais VMware com o Windows Server 2012 R2 Enterprise foram usadas. O SharePoint 2013 Enterprise Edition e o SQL Server 2014 Enterprise Edition foram usados.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim
**Azure** | ND | Sim


### <a name="things-to-keep-in-mind"></a>Coisas para ter em mente

Se você estiver usando um cluster compartilhado baseado em disco como qualquer camada em seu aplicativo, não será possível usar Site Recovery replicação para replicar essas máquinas virtuais. Você pode usar a replicação nativa fornecida pelo aplicativo e, em seguida, usar um [plano de recuperação](site-recovery-create-recovery-plans.md) para fazer failover de todas as camadas.

## <a name="replicating-virtual-machines"></a>Replicando máquinas virtuais

Siga [estas diretrizes](site-recovery-vmware-to-azure.md) para iniciar a replicação da máquina virtual para o Azure.

* Depois que a replicação for concluída, verifique se você vai para cada máquina virtual de cada camada e selecione o mesmo conjunto de disponibilidade em ' item replicado > Configurações > Propriedades > Computação e rede '. Por exemplo, se sua camada da Web tiver 3 VMs, verifique se todas as três VMs estão configuradas como parte do mesmo conjunto de disponibilidade no Azure.

    ![Set-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Para obter orientação sobre como proteger Active Directory e DNS, consulte [proteger o Active Directory e](site-recovery-active-directory.md) o documento DNS.

* Para obter orientação sobre como proteger a camada de banco de dados em execução no SQL Server, consulte [proteger SQL Server](site-recovery-active-directory.md) documento.

## <a name="networking-configuration"></a>Configuração de rede

### <a name="network-properties"></a>Propriedades da rede

* Para as VMs do aplicativo e da camada da Web, defina as configurações de rede no portal do Azure para que as VMs sejam anexadas à rede de DR correta após o failover.

    ![Selecionar rede](./media/site-recovery-sharepoint/select-network.png)


* Se você estiver usando um IP estático, especifique o IP que deseja que a máquina virtual execute no campo IP de **destino**

    ![Definir IP estático](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Roteamento de tráfego e DNS

Para sites voltados para a Internet, [crie um perfil do Gerenciador de tráfego do tipo ' prioridade '](../traffic-manager/traffic-manager-create-profile.md) na assinatura do Azure. Em seguida, configure seu perfil de DNS e do Gerenciador de tráfego da seguinte maneira.


| **Posição** | **Origem** | **Target**|
| --- | --- | --- |
| DNS público | DNS público para sites do SharePoint <br/><br/> Por exemplo: sharepoint.contoso.com | Gestor de Tráfego <br/><br/> contososharepoint.trafficmanager.net |
| DNS local | sharepointonprem.contoso.com | IP público no farm local |


No perfil do Gerenciador de tráfego, [crie os pontos de extremidade primário e de recuperação](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Use o ponto de extremidade externo para o ponto de extremidade local e o IP público para o ponto de extremidade do Azure. Verifique se a prioridade está definida como superior ao ponto de extremidade local.

Hospede uma página de teste em uma porta específica (por exemplo, 800) na camada da Web do SharePoint para que o Gerenciador de tráfego detecte automaticamente a disponibilidade após o failover. Isso é uma solução alternativa no caso de você não poder habilitar a autenticação anônima em nenhum dos seus sites do SharePoint.

[Configure o perfil do Gerenciador de tráfego](../traffic-manager/traffic-manager-configure-priority-routing-method.md) com as configurações abaixo.

* Método de roteamento-' prioridade '
* TTL (vida útil) do DNS-' 30 segundos '
* Configurações do monitor de ponto de extremidade – se você puder habilitar a autenticação anônima, poderá fornecer um ponto de extremidade de site específico. Ou, você pode usar uma página de teste em uma porta específica (por exemplo, 800).

## <a name="creating-a-recovery-plan"></a>Criando um plano de recuperação

Um plano de recuperação permite sequenciar o failover de várias camadas em um aplicativo de várias camadas, portanto, mantendo a consistência do aplicativo. Siga as etapas abaixo ao criar um plano de recuperação para um aplicativo Web de várias camadas. [Saiba mais sobre como criar um plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionando máquinas virtuais a grupos de failover

1. Crie um plano de recuperação adicionando as VMs do aplicativo e da camada da Web.
2. Clique em ' Personalizar ' para agrupar as VMs. Por padrão, todas as VMs fazem parte do ' grupo 1 '.

    ![Personalizar RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Crie outro grupo (grupo 2) e mova as VMs da camada da Web para o novo grupo. As VMs da camada de aplicativo devem fazer parte do ' grupo 1 ' e as VMs da camada da Web devem ser parte do ' grupo 2 '. Isso é para garantir que as VMs da camada de aplicativo sejam inicializadas primeiro, seguidas pelas VMs da camada da Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionando scripts ao plano de recuperação

Você pode implantar os scripts de Azure Site Recovery usados com mais frequência em sua conta de automação clicando no botão ' implantar no Azure ' abaixo. Quando você estiver usando qualquer script publicado, certifique-se de seguir as orientações no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação a ' grupo 1 ' para failover do grupo de disponibilidade do SQL. Use o script ' ASR-SQL-FailoverAG ' publicado nos scripts de exemplo. Certifique-se de seguir as diretrizes no script e faça as alterações necessárias no script adequadamente.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adicione um script de ação post para anexar um balanceador de carga nas máquinas virtuais com failover da camada da Web (grupo 2). Use o script ' ASR-AddSingleLoadBalancer ' publicado nos scripts de exemplo. Certifique-se de seguir as diretrizes no script e faça as alterações necessárias no script adequadamente.

    ![Add-LB-script – Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adicione uma etapa manual para atualizar os registros DNS para apontar para o novo farm no Azure.

    * Para sites voltados para a Internet, nenhuma atualização de DNS é necessária após o failover. Siga as etapas descritas na seção "diretrizes de rede" para configurar o Gerenciador de tráfego. Se o perfil do Gerenciador de tráfego tiver sido configurado conforme descrito na seção anterior, adicione um script para abrir a porta fictícia (800 no exemplo) na VM do Azure.

    * Para sites internos, adicione uma etapa manual para atualizar o registro DNS para apontar para o IP do balanceador de carga da VM da nova camada da Web.

4. Adicione uma etapa manual para restaurar o aplicativo de pesquisa de um backup ou iniciar um novo serviço de pesquisa.

5. Para restaurar o aplicativo de serviço de pesquisa de um backup, siga as etapas abaixo.

    * Esse método pressupõe que um backup do aplicativo de Serviço de Pesquisa foi executado antes do evento catastrófico e que o backup está disponível no local de recuperação de desastre.
    * Isso pode ser feito facilmente agendando o backup (por exemplo, uma vez por dia) e usando um procedimento de cópia para fazer o backup no local de recuperação de desastres. Os procedimentos de cópia podem incluir programas com script como AzCopy (cópia do Azure) ou configurar o DFSR (replicação de serviços de arquivos distribuídos).
    * Agora que o farm do SharePoint está em execução, navegue pela administração central, ' backup e restauração ' e selecione restaurar. A restauração interroga o local de backup especificado (talvez seja necessário atualizar o valor). Selecione o backup do aplicativo Serviço de Pesquisa que você deseja restaurar.
    * A pesquisa é restaurada. Tenha em mente que a restauração espera encontrar a mesma topologia (mesmo número de servidores) e as mesmas letras de disco rígido atribuídas a esses servidores. Para obter mais informações, consulte o documento [' restaurar aplicativo de serviço de pesquisa no SharePoint 2013 '](https://technet.microsoft.com/library/ee748654.aspx) .


6. Para iniciar com um novo aplicativo de serviço de pesquisa, siga as etapas abaixo.

    * Esse método pressupõe que um backup do banco de dados "administração de pesquisa" está disponível no local de recuperação de desastre.
    * Como os outros bancos de dados de aplicativo Serviço de Pesquisa não são replicados, eles precisam ser recriados. Para fazer isso, navegue até a administração central e exclua o aplicativo Serviço de Pesquisa. Em todos os servidores que hospedam o índice de pesquisa, exclua os arquivos de índice.
    * Recrie o aplicativo Serviço de Pesquisa e isso recriará os bancos de dados. É recomendável ter um script preparado que recria esse aplicativo de serviço, pois não é possível executar todas as ações por meio da GUI. Por exemplo, definir o local da unidade de índice e configurar a topologia de pesquisa só é possível usando cmdlets do SharePoint PowerShell. Use o cmdlet do Windows PowerShell Restore-SPEnterpriseSearchServiceApplication e especifique o banco de dados de administração de pesquisa replicado e enviado por log, Search_Service__DB. Esse cmdlet fornece a configuração de pesquisa, o esquema, as propriedades gerenciadas, as regras e as fontes e cria um conjunto padrão de outros componentes.
    * Depois que o aplicativo Serviço de Pesquisa for recriado, você deverá iniciar um rastreamento completo para cada fonte de conteúdo a fim de restaurar o Serviço de Pesquisa. Você perde algumas informações de análise do farm local, como recomendações de pesquisa.

7. Depois que todas as etapas forem concluídas, salve o plano de recuperação e o plano de recuperação final será semelhante ao seguinte.

    ![RP salvo](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Fazendo um failover de teste
Siga [estas diretrizes](site-recovery-test-failover-to-azure.md) para fazer um failover de teste.

1.  Vá para portal do Azure e selecione o cofre do serviço de recuperação.
2.  Clique no plano de recuperação criado para o aplicativo do SharePoint.
3.  Clique em "failover de teste".
4.  Selecione ponto de recuperação e rede virtual do Azure para iniciar o processo de failover de teste.
5.  Depois que o ambiente secundário estiver ativo, você poderá executar suas validações.
6.  Depois que as validações forem concluídas, você poderá clicar em ' limpar failover de teste ' no plano de recuperação e o ambiente de failover de teste será limpo.

Para obter orientação sobre como fazer failover de teste para AD e DNS, consulte [considerações de failover de teste para o documento do AD e DNS](site-recovery-active-directory.md#test-failover-considerations) .

Para obter orientação sobre como fazer failover de teste para grupos de disponibilidade AlwaysOn do SQL, consulte [executando o aplicativo de recuperação de desastre com Azure site Recovery e fazendo failover de teste](site-recovery-sql.md#disaster-recovery-of-an-application) .

## <a name="doing-a-failover"></a>Fazendo um failover
Siga [estas diretrizes](site-recovery-failover.md) para fazer um failover.

1.  Vá para portal do Azure e selecione o cofre dos serviços de recuperação.
2.  Clique no plano de recuperação criado para o aplicativo do SharePoint.
3.  Clique em ' failover '.
4.  Selecione ponto de recuperação para iniciar o processo de failover.

## <a name="next-steps"></a>Passos seguintes
Você pode aprender mais sobre a [replicação de outros aplicativos](site-recovery-workload.md) usando o site Recovery.
