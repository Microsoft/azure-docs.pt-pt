---
title: Confiem da recuperação de desastres para uma aplicação web IIS utilizando a recuperação do site Azure
description: Aprenda a replicar máquinas virtuais de exploração web IIS usando a recuperação do site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 7a4408b54b663b2cd8abc22772ac1b799ea50de0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083774"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Configurar a recuperação após desastre para uma aplicação baseada em IIS de vários escalões

O software de aplicações é o motor da produtividade do negócio numa organização. Várias aplicações web podem servir diferentes propósitos numa organização. Algumas aplicações, como aplicações utilizadas para processamento de folha de pagamento, aplicações financeiras e websites voltados para o cliente, podem ser cruciais para uma organização. Para evitar a perda de produtividade, é importante que a organização tenha estas aplicações continuamente em funcionamento. Mais importante ainda, ter estas aplicações consistentemente disponíveis pode ajudar a prevenir danos à marca ou imagem da organização.

As aplicações web críticas são normalmente configuras como aplicações multi-camadas: a web, base de dados e aplicação estão em diferentes níveis. Além de serem distribuídos por vários níveis, as aplicações também podem usar vários servidores em cada nível para carregar o equilíbrio do tráfego. Além disso, os mapeamentos entre vários níveis e no servidor web podem ser baseados em endereços IP estáticos. No failover, alguns destes mapeamentos precisam de ser atualizados, especialmente se vários sites estiverem configurados no servidor web. Se as aplicações web utilizarem O S.TLS, tem de atualizar as ligações de certificados.

Os métodos tradicionais de recuperação que não são baseados na replicação envolvem o backup de vários ficheiros de configuração, configurações de registo, encadernações, componentes personalizados (COM ou .NET), conteúdo e certificados. Os ficheiros são recuperados através de um conjunto de passos manuais. Os métodos tradicionais de recuperação de ficheiros de backup e recuperação manual são pesados, propensos a erros e não escaláveis. Por exemplo, pode facilmente esquecer-se de fazer o back-up certificados. Depois do fracasso, não tem escolha a não ser comprar novos certificados para o servidor.

Uma boa solução de recuperação de desastres apoia a modelação de planos de recuperação para arquiteturas complexas de aplicações. Também deverá ser capaz de adicionar passos personalizados ao plano de recuperação para lidar com mapeamentos de aplicações entre níveis. Se houver um desastre, os mapeamentos de aplicações fornecem uma solução de um clique e um clique que ajuda a levar a um RTO mais baixo.

Este artigo descreve como proteger uma aplicação web baseada em Serviços de Informação da Internet (IIS) utilizando [a recuperação do site Azure.](site-recovery-overview.md) O artigo abrange as melhores práticas para replicar uma aplicação web baseada em três níveis para o Azure, como fazer um exercício de recuperação de desastres, e como falhar sobre a aplicação ao Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que sabe como fazer as seguintes tarefas:

* [Replicar uma máquina virtual para Azure](vmware-azure-tutorial.md)
* [Desenhe uma rede de recuperação](./concepts-on-premises-to-azure-networking.md)
* [Faça um teste de failover para Azure](site-recovery-test-failover-to-azure.md)
* [Faça um failover para Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicação do SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação
Uma aplicação web baseada no IIS segue normalmente um dos seguintes padrões de implementação:

**Padrão de implantação 1**

Uma fazenda web baseada no IIS com Encaminhamento de Pedido de Aplicação (ARR), um servidor IIS e SQL Server.

![Diagrama de uma fazenda web baseada no IIS que tem três níveis](./media/site-recovery-iis/deployment-pattern1.png)

**Padrão de implantação 2**

Uma fazenda web baseada no IIS com ARR, um servidor IIS, um servidor de aplicações e SQL Server.

![Diagrama de uma fazenda web baseada no IIS que tem quatro níveis](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para os exemplos deste artigo, utilizamos máquinas virtuais VMware com IIS 7.5 no Windows Server 2012 R2 Enterprise. Como a replicação da Recuperação do Site não é específica da aplicação, espera-se que as recomendações neste artigo sejam aplicadas nos cenários listados na tabela seguinte e para diferentes versões do IIS.

### <a name="source-and-target"></a>Fonte e alvo

Cenário | Para um site secundário | Para o Azure
--- | --- | ---
Hyper-V | Sim | Sim
VMware | Sim | Sim
Servidor físico | Não | Sim
Azure|ND|Sim

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para começar a replicar todas as máquinas virtuais da web do IIS para a Azure, siga as orientações em [Test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

Se estiver a utilizar um endereço IP estático, pode especificar o endereço IP que pretende que a máquina virtual possa ser tomada. Para definir o endereço IP, aceda às definições de **Compute e Network**TARGET  >  **IP**.

![Screenshot que mostra como definir o IP alvo no painel de recuperação do site e painel de rede](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação apoia a sequenciação de vários níveis numa aplicação multi-nível durante uma falha. A sequenciação ajuda a manter a consistência da aplicação. Quando criar um plano de recuperação para uma aplicação web multi-nível, complete os passos descritos na [Criar um plano de recuperação utilizando a Recuperação do Site](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adicione máquinas virtuais a grupos de failover
Uma aplicação web típica de vários níveis do IIS consiste nos seguintes componentes:
* Um nível de base de dados que tem máquinas virtuais SQL.
* O nível web, que consiste num servidor IIS e num nível de aplicação. 

Adicione máquinas virtuais a diferentes grupos com base no nível:

1. Criar um plano de recuperação. Adicione as máquinas virtuais de nível de base de dados no Grupo 1. Isto garante que as máquinas virtuais de nível de base de dados são fechadas por último e criadas primeiro.
1. Adicione as máquinas virtuais de nível de aplicação no Grupo 2. Isto garante que as máquinas virtuais de nível de aplicação são criadas após a criação do nível de base de dados.
1. Adicione as máquinas virtuais de nível web no Grupo 3. Isto garante que as máquinas virtuais de nível web são criadas após a criação do nível de aplicação.
1. Adicione máquinas virtuais de equilíbrio de carga no Grupo 4. Isto garante que as máquinas virtuais de equilíbrio de carga são criadas após a criação do nível web.

Para mais informações, consulte [Personalizar o plano de recuperação.](site-recovery-runbook-automation.md#customize-the-recovery-plan)


### <a name="add-a-script-to-the-recovery-plan"></a>Adicione um roteiro ao plano de recuperação
Para que a web farm IIS funcione corretamente, poderá ser necessário fazer algumas operações nas máquinas virtuais Azure pós-failover ou durante um teste de failover. Pode automatizar algumas operações pós-falha. Por exemplo, pode atualizar a entrada de DNS, alterar uma ligação do site ou alterar uma cadeia de ligação adicionando scripts correspondentes ao plano de recuperação. [Adicione um script VMM a um plano de recuperação](./hyper-v-vmm-recovery-script.md) descreve como configurar tarefas automatizadas usando um script.

#### <a name="dns-update"></a>Atualização dns
Se o DNS estiver configurado para uma atualização dinâmica do DNS, as máquinas virtuais normalmente atualizam o DNS com o novo endereço IP quando iniciam. Se pretender adicionar um passo explícito para atualizar o DNS com os novos endereços IP das máquinas virtuais, adicione um [script para atualizar IP em DNS](https://aka.ms/asr-dns-update) como uma ação pós-failover em grupos de plano de recuperação.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Cadeia de ligação na web.config de uma aplicação
A cadeia de ligação especifica a base de dados com a qual o website comunica. Se a cadeia de ligação transportar o nome da máquina virtual da base de dados, não são necessários mais passos após a falha. A aplicação pode comunicar automaticamente com a base de dados. Além disso, se o endereço IP da máquina virtual da base de dados for mantido, não será necessário atualizar a cadeia de ligação. 

Se o fio de ligação se referir à máquina virtual da base de dados utilizando um endereço IP, tem de ser atualizado após a falha. Por exemplo, o seguinte fio de ligação aponta para a base de dados com o endereço IP 127.0.1.2:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
<connectionStrings>
<add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
</connectionStrings>
</configuration>
```

Para atualizar a cadeia de ligação no nível web, adicione um [script de atualização de ligação IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) após o Grupo 3 no plano de recuperação.

#### <a name="site-bindings-for-the-application"></a>Encadernações do site para a aplicação
Cada site consiste em informações vinculativas. As informações vinculativas incluem o tipo de encadernação, o endereço IP no qual o servidor IIS ouve os pedidos do site, o número da porta e os nomes de anfitrião do site. Durante o failover, poderá ter de atualizar estas ligações se houver uma alteração no endereço IP que esteja associada a elas.

> [!NOTE]
>
> Se definir a ligação do site a **Todos os não atribuídos,** não precisa de atualizar este pós-failover de ligação. Além disso, se o endereço IP associado a um site não for alterado após o failover, não precisa de atualizar a ligação do site. (A conservação do endereço IP depende da arquitetura da rede e das sub-redes atribuídas aos locais primários e de recuperação. Atualizá-los pode não ser viável para a sua organização.)

![Screenshot que mostra a definição da ligação TLS/SSL](./media/site-recovery-iis/sslbinding.png)

Se associar o endereço IP a um site, atualize todas as ligações do site com o novo endereço IP. Para alterar as ligações do site, adicione um [script de atualização de nível web IIS](https://aka.ms/asr-web-tier-update-runbook-classic) após o Grupo 3 no plano de recuperação.

#### <a name="update-the-load-balancer-ip-address"></a>Atualizar o endereço IP do balançador de carga
Se tiver uma máquina virtual ARR para atualizar o endereço IP, adicione um [script de falha do IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) após o Grupo 4.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>Encadernação de certificado TLS/SSL para uma ligação HTTPS
Um site pode ter um certificado TLS/SSL associado que ajuda a garantir uma comunicação segura entre o servidor web e o navegador do utilizador. Se o website tiver uma ligação HTTPS, e tiver também uma ligação https associada ao endereço IP do servidor IIS com uma ligação de certificado TLS/SSL, deve adicionar uma nova ligação de site para o certificado com o endereço IP da máquina virtual IIS pós-falha.

O certificado TLS/SSL pode ser emitido contra estes componentes:

* O nome de domínio totalmente qualificado do site.
* O nome do servidor.
* Um certificado wildcard para o nome de domínio.  
* Um endereço IP. Se o certificado TLS/SSL for emitido contra o endereço IP do servidor IIS, é necessário emitir outro certificado TLS/SSL contra o endereço IP do servidor IIS no site Azure. É necessário criar uma ligação adicional do TLS para este certificado. Por isso, recomendamos não utilizar um certificado TLS/SSL emitido contra o endereço IP. Esta opção é menos utilizada e em breve será depreciada de acordo com as alterações da autoridade de certificados/fórum do navegador.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Atualizar a dependência entre o nível web e o nível de aplicação
Se tiver uma dependência específica da aplicação baseada no endereço IP das máquinas virtuais, tem de atualizar esta dependência pós-falha.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. No portal Azure, selecione o cofre dos Serviços de Recuperação.
2. Selecione o plano de recuperação que criou para a web farm IIS.
3. Selecione **Ativação Pós-falha de Teste**.
4. Para iniciar o processo de failover do teste, selecione o ponto de recuperação e a rede virtual Azure.
5. Quando o ambiente secundário estiver em cima, pode efetuar validações.
6. Quando as validações estiverem completas, para limpar o ambiente de failover de teste, selecione **Validações completas**.

Para obter mais informações, consulte [test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1. No portal Azure, selecione o cofre dos Serviços de Recuperação.
1. Selecione o plano de recuperação que criou para a web farm IIS.
1. Selecione **Ativação pós-falha**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover na Recuperação do Local.](site-recovery-failover.md)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a replicação de outras aplicações](site-recovery-workload.md) utilizando a Recuperação do Site.
