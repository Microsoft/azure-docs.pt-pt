---
title: Configurar a recuperação de desastres para uma aplicação web DoI usando a Recuperação do Site Azure
description: Aprenda a replicar máquinas virtuais da quinta web IIS utilizando a Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: dfed398124ca20771e169f6f9e7d08d4d799ee1e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478283"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Configurar a recuperação de desastres para uma aplicação web baseada em IIS de vários níveis

O software de aplicação é o motor da produtividade do negócio numa organização. Várias aplicações web podem servir diferentes propósitos numa organização. Algumas aplicações, como aplicações utilizadas para processamento de folha de pagamento, aplicações financeiras e websites voltados para o cliente, podem ser cruciais para uma organização. Para evitar a perda de produtividade, é importante que a organização tenha estas aplicações continuamente em funcionamento. Mais importante ainda, ter estas aplicações consistentemente disponíveis pode ajudar a prevenir danos à marca ou imagem da organização.

As aplicações web críticas são normalmente configuradas como aplicações de vários níveis: a web, a base de dados e a aplicação estão em diferentes níveis. Além de serem distribuídas por vários níveis, as aplicações também podem usar vários servidores em cada nível para carregar o equilíbrio do tráfego. Além disso, os mapeamentos entre vários níveis e no servidor web podem ser baseados em endereços IP estáticos. No failover, alguns destes mapeamentos precisam de ser atualizados, especialmente se vários websites estiverem configurados no servidor web. Se as aplicações web utilizarem TLS, deve atualizar as encadernações do certificado.

Os métodos tradicionais de recuperação que não se baseiam na replicação envolvem o backup de vários ficheiros de configuração, definições de registo, encadernações, componentes personalizados (COM ou .NET), conteúdo e certificados. Os ficheiros são recuperados através de um conjunto de passos manuais. Os métodos tradicionais de recuperação de ficheiros de backup e recuperação manual são pesados, propensos a erros e não escaláveis. Por exemplo, pode facilmente esquecer-se de fazer back-up de certificados. Depois do fracasso, não tem escolha a não ser comprar novos certificados para o servidor.

Uma boa solução de recuperação de desastres apoia a modelação de planos de recuperação para arquiteturas de aplicações complexas. Você também deve ser capaz de adicionar passos personalizados ao plano de recuperação para lidar com mapeamentos de aplicações entre níveis. Se houver um desastre, os mapeamentos de aplicações fornecem uma solução de um clique e tiro certeiro que ajuda a levar a um RTO mais baixo.

Este artigo descreve como proteger uma aplicação web baseada em Serviços de Informação da Internet (IIS) utilizando a Recuperação do [Site Azure](site-recovery-overview.md). O artigo abrange as melhores práticas para replicar uma aplicação web baseada em três níveis do IIS para o Azure, como fazer um exercício de recuperação de desastres e como falhar com a aplicação ao Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que sabe como fazer as seguintes tarefas:

* [Replicar uma máquina virtual para Azure](vmware-azure-tutorial.md)
* [Conceber uma rede de recuperação](site-recovery-network-design.md)
* [Faça um teste falhado para Azure](site-recovery-test-failover-to-azure.md)
* [Faça uma falha com Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicação do SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação
Uma aplicação web baseada no IIS segue tipicamente um dos seguintes padrões de implementação:

**Padrão de implantação 1**

Uma quinta web baseada em IIS com O Encaminhamento de Pedidos de Pedido de Aplicação (ARR), um servidor IIS e Servidor SQL.

![Diagrama de uma quinta web baseada em IIS que tem três níveis](./media/site-recovery-iis/deployment-pattern1.png)

**Padrão de implantação 2**

Uma quinta web baseada em IIS com ARR, um servidor IIS, um servidor de aplicações e SQL Server.

![Diagrama de uma quinta web baseada em IIS que tem quatro níveis](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para os exemplos deste artigo, utilizamos máquinas virtuais VMware com IIS 7.5 no Windows Server 2012 R2 Enterprise. Como a replicação da Recuperação do Site não é específica da aplicação, espera-se que as recomendações neste artigo se apliquem nos cenários listados na tabela seguinte e em diferentes versões do IIS.

### <a name="source-and-target"></a>Fonte e alvo

Cenário | Para um site secundário | Para o Azure
--- | --- | ---
Hyper-V | Sim | Sim
VMware | Sim | Sim
Servidor físico | Não | Sim
Azure|ND|Sim

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para começar a replicar todas as máquinas virtuais da quinta web IIS para o Azure, siga a orientação em [Teste para o Azure em Recuperação do Local](site-recovery-test-failover-to-azure.md).

Se estiver a utilizar um endereço IP estático, pode especificar o endereço IP que pretende que a máquina virtual leve. Para definir o endereço IP, vá às >  **definições de Computação e Rede**TARGET**IP**.

![Screenshot que mostra como definir o IP alvo no painel de recuperação do site e da rede](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação apoia a sequenciação de vários níveis numa aplicação de vários níveis durante uma falha. A sequenciação ajuda a manter a consistência da aplicação. Quando criar um plano de recuperação para uma aplicação web de vários níveis, complete os passos descritos no Create a recovery utilizando a [Recuperação](site-recovery-create-recovery-plans.md)do Site .

### <a name="add-virtual-machines-to-failover-groups"></a>Adicione máquinas virtuais a grupos de failover
Uma aplicação web típica de vários níveis IIS consiste nos seguintes componentes:
* Um nível de base de dados que tem máquinas virtuais SQL.
* O nível web, que consiste num servidor IIS e num nível de aplicação. 

Adicione máquinas virtuais a diferentes grupos com base no nível:

1. Criar um plano de recuperação. Adicione as máquinas virtuais de nível de base de dados no grupo 1. Isto garante que as máquinas virtuais de nível de base de dados são desligadas por último e criadas primeiro.
1. Adicione as máquinas virtuais do nível de aplicação no grupo 2. Isto garante que as máquinas virtuais do nível de aplicação são criadas após a criação do nível de base de dados.
1. Adicione as máquinas virtuais de nível web no Grupo 3. Isto garante que as máquinas virtuais de nível web são criadas após a criação do nível de aplicação.
1. Adicione máquinas virtuais de equilíbrio de carga no Grupo 4. Isto garante que as máquinas virtuais de equilíbrio de carga são criadas após a criação do nível web.

Para mais informações, consulte Personalizar o plano de [recuperação.](site-recovery-runbook-automation.md#customize-the-recovery-plan)


### <a name="add-a-script-to-the-recovery-plan"></a>Adicione um script ao plano de recuperação
Para que a quinta web IIS funcione corretamente, poderá ser necessário efetuar algumas operações nas máquinas virtuais Azure após a falha ou durante uma falha de teste. Pode automatizar algumas operações pós-falha. Por exemplo, pode atualizar a entrada de DNS, alterar uma ligação do site ou alterar uma cadeia de ligação adicionando scripts correspondentes ao plano de recuperação. [Adicione um script VMM a um plano de recuperação](site-recovery-how-to-add-vmmscript.md) descreve como configurar tarefas automatizadas utilizando um script.

#### <a name="dns-update"></a>Atualização dNS
Se o DNS estiver configurado para uma atualização dinâmica do DNS, as máquinas virtuais normalmente atualizam o DNS com o novo endereço IP quando começam. Se pretender adicionar um passo explícito para atualizar o DNS com os novos endereços IP das máquinas virtuais, adicione um [script para atualizar o IP em DNS](https://aka.ms/asr-dns-update) como uma ação pós-falha nos grupos de planos de recuperação.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadeia de ligação na web.config de uma aplicação
A cadeia de ligação especifica a base de dados com a que o website comunica. Se a cadeia de ligação tiver o nome da máquina virtual da base de dados, não são necessários mais passos após a falha. A aplicação pode comunicar automaticamente com a base de dados. Além disso, se o endereço IP da máquina virtual da base de dados for mantido, não precisa de atualizar a cadeia de ligação. 

Se a cadeia de ligação se referir à máquina virtual da base de dados utilizando um endereço IP, esta deve ser atualizada após a falha. Por exemplo, as seguintes faixas de ligação apontam para a base de dados com o endereço IP 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Para atualizar a cadeia de ligação no nível web, adicione um script de atualização de [ligação IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) após o Grupo 3 no plano de recuperação.

#### <a name="site-bindings-for-the-application"></a>Encadernações do site para a aplicação
Cada site consiste em informações vinculativas. As informações vinculativas incluem o tipo de encadernação, o endereço IP no qual o servidor IIS ouve os pedidos para o site, o número da porta e os nomes de anfitrião do site. Durante a falha, poderá ter de atualizar estas encadernações se houver uma alteração no endereço IP que esteja associada a elas.

> [!NOTE]
>
> Se definir o site vinculado a **Tudo não atribuído,** não precisa de atualizar este pós-falha vinculativo. Além disso, se o endereço IP associado a um site não for alterado após a falha, não precisa de atualizar a ligação do site. (A manutenção do endereço IP depende da arquitetura da rede e das subredes atribuídas aos locais primários e de recuperação. Atualizá-los pode não ser viável para a sua organização.)

![Screenshot que mostra a definição da ligação TLS/SSL](./media/site-recovery-iis/sslbinding.png)

Se associar o endereço IP a um site, atualize todas as ligações do site com o novo endereço IP. Para alterar as ligações do site, adicione um script de [atualização de nível web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) após o Grupo 3 no plano de recuperação.

#### <a name="update-the-load-balancer-ip-address"></a>Atualizar o endereço IP do equilibrador de carga
Se tiver uma máquina virtual ARR, para atualizar o endereço IP, adicione um [script de failover IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) após o Grupo 4.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>Ligação de certificado TLS/SSL para uma ligação HTTPS
Um site pode ter um certificado TLS/SSL associado que ajuda a garantir uma comunicação segura entre o servidor web e o navegador do utilizador. Se o website tiver uma ligação HTTPS, e também tiver um site HTTPS associado vinculando-se ao endereço IP do servidor IIS com uma ligação de certificado TLS/SSL, deve adicionar um novo link de site para o certificado com o endereço IP da pós-falha da máquina virtual IIS.

O certificado TLS/SSL pode ser emitido contra estes componentes:

* O nome de domínio totalmente qualificado do site.
* O nome do servidor.
* Um certificado wildcard para o nome de domínio.  
* Um endereço IP. Se o certificado TLS/SSL for emitido contra o endereço IP do servidor IIS, outro certificado TLS/SSL tem de ser emitido contra o endereço IP do servidor IIS no site do Azure. É necessário criar uma ligação adicional de TLS para este certificado. Por isso, recomendamos que não utilize um certificado TLS/SSL emitido contra o endereço IP. Esta opção é menos utilizada e em breve será depreciada de acordo com as novas alterações da autoridade de certificados/fórum do navegador.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Atualizar a dependência entre o nível web e o nível de aplicação
Se tiver uma dependência específica da aplicação baseada no endereço IP das máquinas virtuais, deve atualizar esta dependência após a falha.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. No portal Azure, selecione o seu cofre de Serviços de Recuperação.
2. Selecione o plano de recuperação que criou para a quinta web iIS.
3. Selecione **Ativação Pós-falha de Teste**.
4. Para iniciar o processo de failover do teste, selecione o ponto de recuperação e a rede virtual Azure.
5. Quando o ambiente secundário acabar, pode realizar validações.
6. Quando as validações estiverem completas, para limpar o ambiente de failover do teste, selecione **Validações completas**.

Para mais informações, consulte [Test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1. No portal Azure, selecione o seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que criou para a quinta web iIS.
1. Selecione **Ativação pós-falha**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para mais informações, consulte [Failover na Recuperação](site-recovery-failover.md)do Site .

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a replicação de [outras aplicações](site-recovery-workload.md) utilizando a Recuperação do Site.
