---
title: Elevada disponibilidade e recuperação de desastres da SAP HANA em Azure (Grandes Instâncias) [ Microsoft Docs
description: Estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP HANA em Azure (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0585c1251ba18e1390f3eee28a989edee6eb8591
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616937"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA Grandes Instâncias alta disponibilidade e recuperação de desastres em Azure 

>[!IMPORTANT]
>Esta documentação não substitui a documentação da administração SAP HANA ou as Notas SAP. Espera-se que o leitor tenha uma compreensão sólida e experiência na administração e operações da SAP HANA, especialmente com os tópicos de backup, restauro, alta disponibilidade e recuperação de desastres.

É importante que exerça os passos e processos tomados no seu ambiente e com as suas versões e lançamentos HANA. Alguns processos descritos nesta documentação são simplificados para uma melhor compreensão geral e não devem ser usados como passos detalhados para eventuais manuais de operação. Se pretender criar manualde funcionamento para as suas configurações, precisa de testar e exercitar os seus processos e documentar os processos relacionados com as suas configurações específicas. 


A elevada disponibilidade e recuperação de desastres (DR) são aspetos cruciais para executar o seu sap HANA crítico da missão no servidor Azure (Grandes Instâncias). É importante trabalhar com o SAP, o seu integrador de sistemas ou a Microsoft para arquiteto e implementar as estratégias de alta disponibilidade e recuperação de desastres certas. Também é importante considerar o objetivo do ponto de recuperação (RPO) e o objetivo de tempo de recuperação, que são específicos do seu ambiente.

A Microsoft suporta algumas capacidades de alta disponibilidade sAP HANA com as grandes instâncias HANA. Estas funcionalidades incluem:

- **Replicação de armazenamento**: A capacidade do sistema de armazenamento de replicar todos os dados para outro carimbo HANA Large Instance em outra região de Azure. A SAP HANA opera independentemente deste método. Esta funcionalidade é o mecanismo padrão de recuperação de desastres oferecido para as grandes instâncias HANA.
- **Replicação**do sistema HANA : A [replicação de todos os dados em SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) para um sistema Separado SAP HANA. O objetivo do tempo de recuperação é minimizado através da replicação de dados em intervalos regulares. O SAP HANA suporta modos assíncronos, sincronizados de memória e sincronizados. O modo sincronizado é utilizado apenas para sistemas SAP HANA que estão dentro do mesmo datacenter ou com menos de 100 km de distância. Com o design atual de selos HANA Large Instance, a replicação do sistema HANA pode ser usada para alta disponibilidade dentro de uma região apenas. A replicação do sistema HANA requer um componente de procuração ou encaminhamento invertido de terceiros para configurações de recuperação de desastres noutra região do Azure. 
- **Reprovação automática**do anfitrião : Uma solução local de recuperação de falhas para o SAP HANA que é uma alternativa à replicação do sistema HANA. Se o nó principal ficar indisponível, configura rumimos um ou mais nós de espera SAP HANA em modo de escala e sAP HANA falha automaticamente num nó de espera.

O SAP HANA on Azure (Grandes Instâncias) é oferecido em duas regiões azure em quatro áreas geopolíticas (EUA, Austrália, Europa e Japão). Duas regiões dentro de uma área geopolítica que acolhem selos HANA Large Instance estão ligados a circuitos de rede dedicados separados. Estes são utilizados para replicar instantâneos de armazenamento para fornecer métodos de recuperação de desastres. A replicação não é estabelecida por padrão, mas está configurada para clientes que encomendam funcionalidade de recuperação de desastres. A replicação do armazenamento depende da utilização de instantâneos de armazenamento para as grandes instâncias HANA. Não é possível escolher uma região azure como uma região de DR que se encontra numa área geopolítica diferente. 

O quadro que se segue mostra os métodos e combinações de elevada disponibilidade e recuperação de desastres atualmente suportados:

| Cenário apoiado em grandes instâncias HANA | Opção de alta disponibilidade | Opção de recuperação de desastres | Comentários |
| --- | --- | --- | --- |
| Nó único | Não disponível. | Configuração de DR dedicada.<br /> Configuração de DR multiusos. | |
| Auto-failover do anfitrião: Scale-out (com ou sem standby)<br /> incluindo 1+1 | Possível com o standby assumindo o papel ativo.<br /> Hana controla o interruptor de funções. | Configuração de DR dedicada.<br /> Configuração de DR multiusos.<br /> Sincronização DR utilizando a replicação do armazenamento. | Os conjuntos de volume HANA estão ligados a todos os nós.<br /> O site dr deve ter o mesmo número de nós. |
| Replicação do sistema HANA | Possível com configuração primária ou secundária.<br /> Secundário passa para o papel principal num caso de falha.<br /> A replicação do sistema HANA e o controlo do SISTEMA falham. | Configuração de DR dedicada.<br /> Configuração de DR multiusos.<br /> Sincronização DR utilizando a replicação do armazenamento.<br /> Dr usando a replicação do sistema HANA ainda não é possível sem componentes de terceiros. | Um conjunto separado de volumes de disco são ligados a cada nó.<br /> Apenas volumes de disco de réplica secundária no local de produção são replicados para a localização DR.<br /> Um conjunto de volumes é necessário no local da DR. | 

Uma configuração DR dedicada é onde a unidade HANA Large Instance no local DR não é utilizada para executar qualquer outro sistema de carga de trabalho ou não-produção. A unidade é passiva e só é implantada se um desastre falhar. No entanto, esta configuração não é uma escolha preferida para muitos clientes.

Consulte [cenários apoiados pelo HLI](hana-supported-scenario.md) para aprender o layout de armazenamento e detalhes do ethernet para a sua arquitetura.

> [!NOTE]
> [SAP HANA MCOD implantações](https://launchpad.support.sap.com/#/notes/1681092) (múltiplas instâncias HANA numa unidade) como cenários de sobreposição funcionam com os métodos HA e DR listados na tabela. Uma exceção é a utilização da Replicação do Sistema HANA com um cluster de failover automático baseado no Pacemaker. Tal caso só suporta uma instância HANA por unidade. Para as implementações do [SAP HANA MDC,](https://launchpad.support.sap.com/#/notes/2096000) apenas os métodos HA e DR não baseados em armazenamento funcionam se forem implantados mais de um inquilino. Com um inquilino destacado, todos os métodos listados são válidos.  

Uma configuração DR multiusos é onde a unidade HANA Large Instance no site DR executa uma carga de trabalho não-produção. Em caso de catástrofe, desligue o sistema de não produção, monte os conjuntos de volume (adicionais) replicados pelo armazenamento e, em seguida, inicie a ocorrência DE HANA de produção. A maioria dos clientes que utilizam a funcionalidade de recuperação de desastres HANA Large Instance utilizam esta configuração. 


Pode encontrar mais informações sobre a alta disponibilidade do SAP HANA nos seguintes artigos da SAP: 

- [Branco de alta disponibilidade SAP HANA](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de Administração SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo da Academia SAP HANA na replicação do sistema SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de suporte SAP #1999880 – FAQ na replicação do sistema SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [Nota de suporte SAP #2165547 – SAP HANA Back up and Restore in SAP HANA System Replication Environment](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Nota de suporte SAP #1984882 – Utilizando replicação do sistema SAP HANA para troca de hardware com tempo mínimo/zero de paragem](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerações de rede para recuperação de desastres com grandes instâncias HANA

Para aproveitar a funcionalidade de recuperação de desastres de HANA Large Instances, você precisa projetar conectividade de rede para as duas regiões Azure. Você precisa de uma ligação de circuito Azure ExpressRoute a partir de instalações na sua região principal de Azure, e outra ligação de circuito de instalações para a sua região de recuperação de desastres. Esta medida cobre uma situação em que há um problema numa região do Azure, incluindo uma localização do Microsoft Enterprise Edge Router (MSEE).

Como segunda medida, pode ligar todas as redes virtuais Azure que ligam ao SAP HANA em Azure (Grandes Instâncias) numa região a um circuito ExpressRoute que liga as grandes instâncias da HANA na outra região. Com esta *ligação cruzada,* os serviços que estão a funcionar numa rede virtual Azure na Região 1 podem ligar-se às unidades HANA Large Instance na Região 2, e ao contrário. Esta medida aborda um caso em que apenas uma das localizações do MSEE que se liga à sua localização no local com o Azure fica offline.

O gráfico seguinte ilustra uma configuração resiliente para casos de recuperação de desastres:

![Configuração ideal para recuperação de desastres](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Outros requisitos com a replicação de armazenamento de grandes instâncias HANA para recuperação de desastres

Para além dos requisitos anteriores para uma instalação de recuperação de desastres com as grandes instâncias hana, deve:

- Encomende SAP HANA em SKUs Azure (Grandes Instâncias) do mesmo tamanho que as suas SKUs de produção e desemprepreça-as na região de recuperação de desastres. Nas atuais implementações de clientes, estes casos são utilizados para executar instâncias HANA não-produtivas. Estas configurações são referidas como *configurações DR multiusos*.   
- Encomende armazenamento adicional no site dr para cada um dos seus SAP HANA em Azure (Grandes Instâncias) SKUs que pretende recuperar no local de recuperação de desastres. Comprar armazenamento adicional permite-lhe alocar os volumes de armazenamento. Você pode alocar os volumes que são alvo da replicação de armazenamento da sua produção da região Azure para a região de recuperação de desastres Azure.
- No caso, onde tiver configuração hSR nas primárias, e configurar replicação baseada em armazenamento no site DR, deve adquirir armazenamento adicional no site dr para que os dados dos nós primários e secundários sejam replicados para o site DR.

  **Passos seguintes**
- Consulte [a Cópia de Segurança e restaure](hana-backup-restore.md).













