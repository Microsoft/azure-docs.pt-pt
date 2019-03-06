---
title: Configurar a recuperação após desastre para uma aplicação do SharePoint de várias camadas com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a recuperação após desastre para uma aplicação do SharePoint de várias camadas utilizando funções do Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 5f477cf20b817d7a6c8be856636bf1e3755b5424
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443492"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Configurar a recuperação após desastre para uma aplicação do SharePoint de várias camadas para recuperação após desastre com o Azure Site Recovery

Este artigo descreve detalhadamente como proteger um aplicativo do SharePoint usando [do Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Descrição geral

Microsoft SharePoint é um aplicativo avançado que pode ajudar a um grupo ou departamento organizar, colaborar e compartilhar informações. SharePoint pode fornecer portais de intranet, gestão de ficheiros e documentos, colaboração, redes sociais, extranets, Web sites, pesquisa empresarial e business intelligence. Também tem a integração de sistemas, integração de processos e recursos de automação do fluxo de trabalho. Normalmente, as organizações considerá-lo como uma aplicação de camada 1 sensíveis ao tempo de inatividade e perda de dados.

Hoje em dia, o Microsoft SharePoint não fornece quaisquer capacidades de recuperação após desastre de out-of-the-box. Independentemente do tipo e o dimensionamento de um desastre, recuperação envolve a utilização de um centro de dados em modo de espera que pode recuperar o farm. Centros de dados em modo de espera são necessários para cenários em que local sistemas redundantes e cópias de segurança não consegue recuperar a falha no Centro de dados primário.

Uma solução de recuperação após desastre boa deve permitir que a Modelagem de planos de recuperação em torno das arquiteturas de aplicativo complexo como o SharePoint. Ele também deve ter a capacidade de adicionar etapas personalizadas para lidar com mapeamentos de aplicação entre vários escalões e, por conseguinte, fornecendo uma ativação pós-falha de clique único com um RTO inferior em caso de desastre.

Este artigo descreve detalhadamente como proteger um aplicativo do SharePoint usando [do Azure Site Recovery](site-recovery-overview.md). Este artigo abordará as práticas recomendadas para replicar um três de camada aplicativo do SharePoint para o Azure, como pode fazer um teste de recuperação após desastre e como pode de ativação pós-falha, a aplicação no Azure.

Pode ver o abaixo vídeo sobre como recuperar uma aplicação multicamada no Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende o seguinte:

1. [Replicar uma máquina virtual para o Azure](site-recovery-vmware-to-azure.md)
2. Como [estruturar uma rede de recuperação](site-recovery-network-design.md)
3. [Fazer uma ativação pós-falha de teste para o Azure](site-recovery-test-failover-to-azure.md)
4. [Efetuar uma ativação pós-falha para o Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como [replicação do SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Arquitetura do SharePoint

SharePoint pode ser implementado num ou mais servidores usando as topologias em camadas e funções de servidor para implementar um design de farm que cumpre os objetivos específicos e objetivos. Um típico grande e de alta demanda server farm do SharePoint que suporta um elevado número de utilizadores em simultâneo e um grande número de itens de conteúdo utilizar o agrupamento do serviço como parte de sua estratégia de escalabilidade. Essa abordagem envolve a executar os serviços em servidores dedicados, agrupar estes serviços e, em seguida, aumentar horizontalmente os servidores como um grupo. A seguinte topologia ilustra o serviço e o servidor de agrupamento para um farm de servidores do SharePoint de três camadas. Consulte a documentação do SharePoint e arquiteturas de linha de produto para obter instruções detalhadas sobre diferentes topologias do SharePoint. Pode encontrar mais detalhes sobre a implantação do SharePoint 2013 no [este documento](https://technet.microsoft.com/library/cc303422.aspx).



![Padrão de implementação 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para a criação deste artigo, máquinas virtuais VMware com o Windows Server 2012 R2 Enterprise foram utilizadas. SharePoint 2013 Enterprise edition e SQL server 2014 Enterprise edition foram utilizados. Como replicação do Site Recovery é independente de aplicativo, as recomendações fornecidas aqui devem manter para cenários seguintes também.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim
**Azure** | ND | Sim

### <a name="sharepoint-versions"></a>Versões do SharePoint
São suportadas as seguintes versões de servidor do SharePoint.

* SharePoint server 2013 Standard
* SharePoint server 2013 Enterprise
* SharePoint server 2016 Standard
* SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Aspetos a ter em mente

Se estiver a utilizar um cluster com base em disco partilhado como qualquer camada na sua aplicação, em seguida, não será possível usar replicação do Site Recovery para replicar essas máquinas virtuais. Pode utilizar os replicação nativo fornecido pela aplicação e, em seguida, utilizar um [plano de recuperação](site-recovery-create-recovery-plans.md) para ativação pós-falha de todas as camadas.

## <a name="replicating-virtual-machines"></a>Replicar máquinas virtuais

Siga [esta orientação](site-recovery-vmware-to-azure.md) para iniciar a replicação da máquina virtual para o Azure.

* Quando a replicação estiver concluída, certifique-se de que vá para cada máquina virtual de cada escalão e selecione o mesmo conjunto de disponibilidade no "item replicado > Definições > propriedades > computação e rede". Por exemplo, se sua camada web tem 3 VMs, certifique-se a todos os 3 VMs estão configuradas como parte do mesmo conjunto de disponibilidade no Azure.

    ![Conjunto de disponibilidade de conjunto](./media/site-recovery-sharepoint/select-av-set.png)

* Para obter orientações sobre como proteger o Active Directory e DNS, consulte [proteger o Active Directory e DNS](site-recovery-active-directory.md) documento.

* Para obter orientações sobre a proteção de camada de base de dados em execução no SQL server, consulte [proteger o SQL Server](site-recovery-active-directory.md) documento.

## <a name="networking-configuration"></a>Configuração de rede

### <a name="network-properties"></a>Propriedades da rede

* Para a aplicação e as VMs de camada Web, configure as definições de rede no portal do Azure para que as VMs são anexadas à rede certa DR após a ativação pós-falha.

    ![Selecione a rede](./media/site-recovery-sharepoint/select-network.png)


* Se estiver a utilizar um IP estático, em seguida, especifique o IP que pretende que a máquina virtual para tirar o **IP de destino** campo

    ![Definir o IP estático](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS e o encaminhamento de tráfego

Para sites, de acesso à internet [criar um perfil do Gestor de tráfego do tipo 'Priority'](../traffic-manager/traffic-manager-create-profile.md) na subscrição do Azure. E, em seguida, configure o seu perfil de DNS e o Gestor de tráfego da seguinte forma.


| **Where** | **Origem** | **Target**|
| --- | --- | --- |
| DNS público | DNS público para sites do SharePoint <br/><br/> Por exemplo: sharepoint.contoso.com | Gestor de Tráfego <br/><br/> contososharepoint.trafficmanager.net |
| DNS no local | sharepointonprem.contoso.com | IP público no farm de no local |


No perfil do Gestor de tráfego, [criar os pontos finais de principal e de recuperação](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Utilize o ponto final externo para o ponto de extremidade no local e um IP público para o ponto final do Azure. Certifique-se de que a prioridade é definida como superior ao ponto final de no local.

Aloje uma página de teste numa porta específica (por exemplo, 800) na camada de web do SharePoint por ordem para o Gestor de tráfego para detetar automaticamente a disponibilidade após a ativação pós-falha. Esta é uma solução alternativa, no caso de não é possível ativar a autenticação anónima em qualquer um dos seus sites do SharePoint.

[Configurar o perfil do Gestor de tráfego](../traffic-manager/traffic-manager-configure-priority-routing-method.md) com o abaixo as definições.

* Método de encaminhamento - 'Priority'
* Tempo DNS para live (TTL) - ' ' 30 segundos
* Definições do monitor de ponto final - se pode ativar a autenticação anónima, pode dar um ponto de extremidade do Web site específico. Em alternativa, pode usar uma página de teste numa porta específica (por exemplo, 800).

## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação permite sequenciar a ativação pós-falha de vários escalões num aplicativo de várias camado, por conseguinte, manter a consistência de aplicação. Siga os passos ao criar um plano de recuperação para uma aplicação web de várias camadas abaixo. [Saiba mais sobre como criar um plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>A adicionar máquinas virtuais para grupos de ativação pós-falha

1. Crie um plano de recuperação ao adicionar a aplicação e as VMs de camada Web.
2. Clique em "Personalizar" para agrupar as VMs. Por predefinição, todas as VMs são parte do 'Grupo 1'.

    ![Personalizar a RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Criar outro grupo (2 de grupo) e mover as VMs de camada Web para o novo grupo. As VMs de camada de aplicação devem fazer parte do "Grupo 1" e VMs de camada Web devem fazer parte do "Grupo 2". Isto é, para garantir que o arranque de VMs da camada de aplicação de cópia de segurança, primeiro, seguidos pelos VMs de camada de Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Adição de scripts para o plano de recuperação

Pode implementar os scripts mais utilizados do Azure Site Recovery para a sua conta de automatização, clicar no botão "Implementar para o Azure" abaixo. Quando estiver a utilizar qualquer script publicado, certifique-se de que siga as orientações no script.

[![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação para 'Grupo 1' ao grupo de disponibilidade de SQL de ativação pós-falha. Utilize o script de "ASR-SQL-FailoverAG" publicado nos scripts de exemplo. Certifique-se de que siga as orientações no script e faça as alterações necessárias no script adequadamente.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adicionar um script de ação de postagem para anexar um balanceador de carga na sobre as máquinas virtuais de camada Web (grupo de 2). Utilize o script de "ASR AddSingleLoadBalancer" publicado nos scripts de exemplo. Certifique-se de que siga as orientações no script e faça as alterações necessárias no script adequadamente.

    ![Adicionar-LB-Script-passo-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adicione um passo manual para atualizar os registos DNS para apontar para a nova farm no Azure.

    * Para sites com face de internet, não existem atualizações DNS são necessárias após a ativação pós-falha. Siga os passos descritos na secção "Diretrizes de rede" para configurar o Gestor de tráfego. Se tiver sido configurado o perfil do Gestor de tráfego, tal como descrito na secção anterior, adicione um script para abrir a porta fictícia (800 no exemplo) na VM do Azure.

    * Para sites com acesso internos, adicione um passo manual para atualizar o registo DNS para apontar para o IP do Balanceador de carga de novos Web escalão da VM.

4. Adicione um passo manual para restaurar o aplicativo de pesquisa a partir de uma cópia de segurança ou iniciar um novo serviço de pesquisa.

5. Para restaurar a aplicação de serviço de pesquisa de uma cópia de segurança, siga os passos abaixo.

    * Este método pressupõe que uma cópia de segurança da aplicação de serviço de pesquisa foi realizada antes do evento catastrófico e que a cópia de segurança está disponível no site de DR.
    * Isso pode ser conseguido facilmente com agendamento da cópia de segurança (por exemplo, uma vez diariamente) e usando um procedimento de cópia para colocar a cópia de segurança no site de DR. Procedimentos de cópia podem incluir programas com script, como o AzCopy (cópia do Azure) ou como configurar o DFSR (Serviços de replicação de ficheiros distribuído).
    * Agora que o farm do SharePoint está em execução, navegue até a Administração Central, "Cópia de segurança e restaurar" e selecione restaurar. O restauro consulta a localização de cópia de segurança especificada (poderá ter de atualizar o valor). Selecione a cópia de segurança de aplicação de serviço de pesquisa que pretende restaurar.
    * Pesquisa é restaurada. Tenha em atenção que o restauro espera encontrar a mesma topologia (mesmo número de servidores) e as mesmas difícil letras de unidade atribuídas a esses servidores. Para obter mais informações, consulte ["Aplicativo de serviço de pesquisa de restauro no SharePoint 2013"](https://technet.microsoft.com/library/ee748654.aspx) documento.


6. Para iniciar com uma nova aplicação de serviço de pesquisa, siga os passos abaixo.

    * Este método assume que está disponível uma cópia de segurança da base de dados "Administração de pesquisa" no site de DR.
    * Uma vez que não são replicadas as outras bases de dados aplicação de serviço de pesquisa, eles precisam de ser recriadas. Para tal, navegue para a Administração Central e eliminar a aplicação de serviço de pesquisa. Em todos os servidores que alojam o índice de pesquisa, elimine os ficheiros de índice.
    * Voltar a criar a aplicação de serviço de pesquisa e isso cria as bases de dados novamente. É recomendado ter um script preparado, que cria novamente esta aplicação de serviço, uma vez que não é possível efetuar todas as ações através da GUI. Por exemplo, definir o local de unidade de índice e configurar a topologia de pesquisa são só possíveis utilizando cmdlets do PowerShell do SharePoint. Utilize o cmdlet do Windows PowerShell SPEnterpriseSearchServiceApplication de restauro e especifique o replicados com envio de log e administração de pesquisa da base de dados, Search_Service__DB. Este cmdlet permite que a configuração de pesquisa, esquema, propriedades gerenciadas, regras e origens e cria um conjunto predefinido dos outros componentes.
    * Assim que a aplicação de serviço de pesquisa tem de ser recriadas, tem de iniciar uma pesquisa completa para cada origem de conteúdo restaurar o serviço de pesquisa. Perder algumas informações de análise do farm no local, por exemplo, recomendações de pesquisa.

7. Depois de todas as etapas são concluídas, guardar o plano de recuperação e o plano de recuperação final será semelhante a seguinte.

    ![RP guardado](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Efetuar uma ativação pós-falha de teste
Siga [esta orientação](site-recovery-test-failover-to-azure.md) para fazer uma ativação pós-falha de teste.

1.  Aceda ao portal do Azure e selecione o Cofre de serviço de recuperação.
2.  Clique no plano de recuperação criado para a aplicação do SharePoint.
3.  Clique em "Ativação pós-falha de teste".
4.  Selecione o ponto de recuperação e de rede virtual do Azure para iniciar o processo de ativação pós-falha de teste.
5.  Após o ambiente secundário está ativo, pode realizar as validações.
6.  Depois que as validações forem concluídas, pode clicar em "Ativação pós-falha de teste" no plano de recuperação e o ambiente de ativação pós-falha de teste sejam limpos.

Para obter orientações sobre a fazer a ativação pós-falha de teste para o AD e DNS, consulte [considerações de ativação pós-falha do AD de teste e DNS](site-recovery-active-directory.md#test-failover-considerations) documento.

Para obter orientações sobre como fazer a ativação pós-falha de teste para o SQL Always em grupos de disponibilidade, consulte [fazendo testar a ativação pós-falha para o SQL Server Always On](site-recovery-sql.md#steps-to-do-a-test-failover) documento.

## <a name="doing-a-failover"></a>Efetuar uma ativação pós-falha
Siga [esta orientação](site-recovery-failover.md) para efetuar uma ativação pós-falha.

1.  Aceda ao portal do Azure e selecione o Cofre de serviços de recuperação.
2.  Clique no plano de recuperação criado para a aplicação do SharePoint.
3.  Clique em "Ativação pós-falha".
4.  Selecione o ponto de recuperação para iniciar o processo de ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes
Pode saber mais sobre [replicar outros aplicativos](site-recovery-workload.md) com o Site Recovery.
