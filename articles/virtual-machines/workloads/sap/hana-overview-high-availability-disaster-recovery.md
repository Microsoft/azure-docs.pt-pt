---
title: Alta disponibilidade e recuperação de desastre de SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Estabeleça alta disponibilidade e planeje a recuperação de desastre de SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d0150aeace3960d075bbf61c1dd0bba4865aaf2b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099714"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA em Instâncias Grandes alta disponibilidade e recuperação de desastres no Azure 

>[!IMPORTANT]
>Esta documentação não substitui a documentação de administração do SAP HANA ou as notas SAP. Espera-se que o leitor tenha uma compreensão sólida e experiência em SAP HANA administração e operações, especialmente com os tópicos de backup, restauração, alta disponibilidade e recuperação de desastres.

É importante que você exerça as etapas e os processos feitos em seu ambiente e com as versões e os lançamentos do HANA. Alguns processos descritos nesta documentação são simplificados para um melhor entendimento geral e não devem ser usados como etapas detalhadas para manuais de operação eventual. Se você quiser criar manuais de operação para suas configurações, será necessário testar e exercitar seus processos e documentar os processos relacionados às suas configurações específicas. 


A alta disponibilidade e a recuperação de desastres (DR) são aspectos cruciais da execução de SAP HANA de missão crítica no servidor do Azure (instâncias grandes). É importante trabalhar com o SAP, o integrador de sistemas ou a Microsoft para arquitetar e implementar adequadamente as estratégias de alta disponibilidade e recuperação de desastres corretas. Também é importante considerar o RPO (objetivo de ponto de recuperação) e o objetivo de tempo de recuperação, que são específicos do seu ambiente.

A Microsoft dá suporte a alguns SAP HANA recursos de alta disponibilidade com instâncias grandes do HANA. Esses recursos incluem:

- **Replicação de armazenamento**: A capacidade do sistema de armazenamento de replicar todos os dados para outro carimbo de instância grande do HANA em outra região do Azure. SAP HANA funciona independentemente desse método. Essa funcionalidade é o mecanismo de recuperação de desastres padrão oferecido para instâncias grandes do HANA.
- **Replicação do sistema Hana**: A [replicação de todos os dados no SAP Hana](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) para um sistema de SAP Hana separado. O objetivo de tempo de recuperação é minimizado por meio da replicação de dados em intervalos regulares. O SAP HANA dá suporte a modos assíncronos, síncronos na memória e síncronos. O modo síncrono é usado somente para sistemas SAP HANA que estão dentro do mesmo datacenter ou menos de 100 km de distância. Com o design atual de carimbos de instância grande do HANA, a replicação do sistema HANA pode ser usada para alta disponibilidade em apenas uma região. A replicação do sistema HANA requer um proxy reverso ou componente de roteamento de terceiros para configurações de recuperação de desastre em outra região do Azure. 
- **Failover automático do host**: Uma solução local de recuperação de falhas para SAP HANA que é uma alternativa à replicação do sistema HANA. Se o nó mestre ficar indisponível, você configurará um ou mais nós de SAP HANA em espera no modo de expansão e SAP HANA fará o failover automaticamente para um nó em espera.

SAP HANA no Azure (instâncias grandes) é oferecido em duas regiões do Azure em quatro áreas geopolíticas (EUA, Austrália, Europa e Japão). Duas regiões em uma área geopolítica que hospedam carimbos de instância grande do HANA estão conectadas a circuitos de rede dedicados separados. Eles são usados para replicar instantâneos de armazenamento para fornecer métodos de recuperação de desastres. A replicação não é estabelecida por padrão, mas é configurada para clientes que solicitam a funcionalidade de recuperação de desastre. A replicação de armazenamento depende do uso de instantâneos de armazenamento para instâncias grandes do HANA. Não é possível escolher uma região do Azure como uma região de DR que esteja em uma área geopolítica diferente. 

A tabela a seguir mostra os métodos e combinações de alta disponibilidade e recuperação de desastres com suporte no momento:

| Cenário com suporte em instâncias grandes HANA | Opção de alta disponibilidade | Opção de recuperação de desastre | Comentários |
| --- | --- | --- | --- |
| Nó único | Não disponível. | Configuração de DR dedicada.<br /> Configuração de DR multipropósito. | |
| Failover automático do host: Escalar horizontalmente (com ou sem espera)<br /> incluindo 1 + 1 | Possível com o modo de espera usando a função ativa.<br /> O HANA controla a opção de função. | Configuração de DR dedicada.<br /> Configuração de DR multipropósito.<br /> Sincronização de DR usando a replicação de armazenamento. | Os conjuntos de volumes do HANA são anexados a todos os nós.<br /> O local de DR deve ter o mesmo número de nós. |
| Replicação do sistema HANA | Possível com a configuração primária ou secundária.<br /> O secundário se move para a função primária em um caso de failover.<br /> Replicação de sistema do HANA e failover de controle do so. | Configuração de DR dedicada.<br /> Configuração de DR multipropósito.<br /> Sincronização de DR usando a replicação de armazenamento.<br /> A recuperação de desastre usando a replicação do sistema HANA ainda não é possível sem componentes de terceiros. | Um conjunto separado de volumes de disco é anexado a cada nó.<br /> Somente os volumes de disco da réplica secundária no site de produção são replicados para o local de recuperação de desastre.<br /> Um conjunto de volumes é necessário no local de recuperação de desastre. | 

Uma configuração de DR dedicada é onde a unidade de instância grande do HANA no site de DR não é usada para executar qualquer outra carga de trabalho ou sistema de não produção. A unidade é passiva e é implantada somente se um failover de desastre for executado. No entanto, essa configuração não é uma opção preferida para muitos clientes.

Consulte [cenários compatíveis](hana-supported-scenario.md) com a HLI para aprender o layout de armazenamento e detalhes de Ethernet para sua arquitetura.

> [!NOTE]
> Implantações do [SAP Hana MCOD](https://launchpad.support.sap.com/#/notes/1681092) (várias instâncias do HANA em uma unidade) como cenários de sobreposição funcionam com os métodos de HA e DR listados na tabela. Uma exceção é o uso da replicação de sistema do HANA com um cluster de failover automático baseado em pacemaker. Esse caso só dá suporte a uma instância do HANA por unidade. Para implantações [SAP Hana MDC](https://launchpad.support.sap.com/#/notes/2096000) , somente os métodos de ha e Dr não baseados em armazenamento funcionam se mais de um locatário for implantado. Com um locatário implantado, todos os métodos listados são válidos.  

Uma configuração de DR multipropósito é onde a unidade de instância grande do HANA no site de recuperação de desastres executa uma carga de trabalho de não produção. Em caso de desastre, desligue o sistema de não produção, monte os conjuntos de volumes replicados do armazenamento (adicionais) e, em seguida, inicie a instância do HANA de produção. A maioria dos clientes que usam a funcionalidade de recuperação de desastre de instância grande do HANA usa essa configuração. 


Você pode encontrar mais informações sobre SAP HANA alta disponibilidade nos seguintes artigos SAP: 

- [Whitepaper de alta disponibilidade SAP HANA](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de administração do SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo do SAP HANA Academy na replicação do sistema SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de suporte da SAP #1999880 – perguntas frequentes sobre a replicação do sistema SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [Nota de suporte da SAP #2165547 – SAP HANA backup e restauração dentro do ambiente de replicação do sistema SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [#1984882 de observação de suporte SAP – usando a replicação de sistema SAP HANA para troca de hardware com tempo de inatividade mínimo/zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerações de rede para recuperação de desastre com instâncias grandes HANA

Para aproveitar a funcionalidade de recuperação de desastres do HANA em instâncias grandes, você precisa criar conectividade de rede para as duas regiões do Azure. Você precisa de uma conexão de circuito do Azure ExpressRoute local em sua região principal do Azure e outra conexão de circuito do local para a sua região de recuperação de desastre. Essa medida aborda uma situação em que há um problema em uma região do Azure, incluindo um local do MSEE (Microsoft Enterprise Edge router).

Como uma segunda medida, você pode conectar todas as redes virtuais do Azure que se conectam ao SAP HANA no Azure (instâncias grandes) em uma região para um circuito do ExpressRoute que conecta as instâncias grandes do HANA na outra região. Com esse *Cross connect*, os serviços em execução em uma rede virtual do Azure na região 1 podem se conectar às unidades de instância grande do Hana na região 2 e o contrário. Essa medida aborda um caso em que apenas um dos locais MSEE que se conecta à sua localização local com o Azure fica offline.

O gráfico a seguir ilustra uma configuração resiliente para casos de recuperação de desastre:

![Configuração ideal para recuperação de desastres](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Outros requisitos com a replicação de armazenamento de instâncias grandes do HANA para recuperação de desastre

Além dos requisitos anteriores para uma configuração de recuperação de desastre com instâncias grandes HANA, você deve:

- Order SAP HANA em SKUs do Azure (instâncias grandes) do mesmo tamanho que os SKUs de produção e implantá-los na região de recuperação de desastres. Nas implantações atuais do cliente, essas instâncias são usadas para executar instâncias do HANA de não produção. Essas configurações são chamadas de configurações de *Dr multipropósito*.   
- Solicite armazenamento adicional no site de DR para cada um dos seus SAP HANA em SKUs do Azure (instâncias grandes) que você deseja recuperar no site de recuperação de desastre. A compra de armazenamento adicional permite alocar os volumes de armazenamento. Você pode alocar os volumes que são o destino da replicação de armazenamento de sua região do Azure de produção para a região de recuperação de desastre do Azure.
- No caso em que você tem a instalação do HSR no primário e configura a replicação baseada em armazenamento para o local de recuperação de desastre, você deve adquirir armazenamento adicional no local de recuperação de desastres, de modo que os dados dos nós primário e secundário sejam replicados para o site de recuperação de desastre.

  **Passos seguintes?**
- Consulte [backup e restauração](hana-backup-restore.md).













