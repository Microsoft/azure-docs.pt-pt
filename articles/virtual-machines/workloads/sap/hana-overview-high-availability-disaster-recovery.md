---
title: Alta disponibilidade e recuperação de desastres da SAP HANA em Azure (Grandes Instâncias) Microsoft Docs
description: Estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em Azure (Grandes Instâncias)
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77616937"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA Grandes Instâncias alta disponibilidade e recuperação de desastres em Azure 

>[!IMPORTANT]
>Esta documentação não substitui a documentação da administração SAP HANA ou as Notas SAP. Espera-se que o leitor tenha uma compreensão sólida e experiência na administração e operações da SAP HANA, especialmente com os tópicos de backup, restauro, alta disponibilidade e recuperação de desastres.

É importante que exerça passos e processos tomados no seu ambiente e com as suas versões e lançamentos HANA. Alguns processos descritos nesta documentação são simplificados para uma melhor compreensão geral e não devem ser usados como passos detalhados para eventuais manuais de operação. Se pretender criar manuais de operação para as suas configurações, tem de testar e exercitar os seus processos e documentar os processos relacionados com as suas configurações específicas. 


A alta disponibilidade e recuperação de desastres (DR) são aspetos cruciais para executar o seu SAP HANA crítico da missão no servidor Azure (Grandes Instâncias). É importante trabalhar com o SAP, o seu integrador de sistemas, ou a Microsoft para arquiteto e implementar adequadamente as estratégias certas de alta disponibilidade e recuperação de desastres. Também é importante considerar o objetivo do ponto de recuperação (RPO) e o objetivo do tempo de recuperação, que são específicos do seu ambiente.

A Microsoft suporta algumas capacidades de alta disponibilidade SAP HANA com HANA Large Instances. Estas funcionalidades incluem:

- **Replicação do armazenamento**: A capacidade do sistema de armazenamento de replicar todos os dados para outro carimbo HANA Large Instance em outra região de Azure. A SAP HANA funciona independentemente deste método. Esta funcionalidade é o mecanismo de recuperação de desastres padrão oferecido para HANA Large Instances.
- **Replicação do sistema HANA**: A [replicação de todos os dados em SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) para um sistema SAP HANA separado. O objetivo do tempo de recuperação é minimizado através da replicação de dados em intervalos regulares. O SAP HANA suporta modos assíncronos, sincronizados e sincronizados. O modo sincronizado é utilizado apenas para sistemas SAP HANA que se encontram dentro do mesmo datacenter ou com menos de 100 km de distância. Com o design atual de selos HANA Large Instance, a replicação do sistema HANA pode ser usada para alta disponibilidade apenas dentro de uma região. A replicação do sistema HANA requer um componente de procuração inversa ou encaminhamento de terceiros para configurações de recuperação de desastres para outra região do Azure. 
- **Host auto-failover**: Uma solução local de recuperação de falhas para SAP HANA que é uma alternativa à replicação do sistema HANA. Se o nó principal ficar indisponível, configura um ou mais nós DE SAP HANA em modo de escala e o SAP HANA falha automaticamente num nó de espera.

O SAP HANA on Azure (Grandes Instâncias) é oferecido em duas regiões de Azure em quatro áreas geopolíticas (EUA, Austrália, Europa e Japão). Duas regiões dentro de uma área geopolítica que acolhe selos HANA Large Instance estão ligadas a circuitos de rede dedicados separados. Estes são usados para replicar instantâneos de armazenamento para fornecer métodos de recuperação de desastres. A replicação não é estabelecida por padrão, mas é criada para clientes que encomendam funcionalidade de recuperação de desastres. A replicação do armazenamento depende da utilização de instantâneos de armazenamento para HANA Large Instances. Não é possível escolher uma região de Azure como uma região dr que está em uma área geopolítica diferente. 

O quadro a seguir mostra os métodos e combinações de alta disponibilidade e recuperação de desastres atualmente suportados:

| Cenário apoiado em Grandes Instâncias HANA | Opção de alta disponibilidade | Opção de recuperação de desastres | Comentários |
| --- | --- | --- | --- |
| Nó único | Não disponível. | Configuração dedicada dr.<br /> Configuração dr multiusos. | |
| Acolá automática do anfitrião: Scale-out (com ou sem standby)<br /> incluindo 1+1 | Possível com o standby assumindo o papel ativo.<br /> Hana controla o interruptor de função. | Configuração dedicada dr.<br /> Configuração dr multiusos.<br /> Sincronização dr utilizando a replicação de armazenamento. | Os conjuntos de volume HANA estão ligados a todos os nós.<br /> O site DR deve ter o mesmo número de nós. |
| Replicação do sistema HANA | Possível com configuração primária ou secundária.<br /> O secundário passa para o papel principal num caso de falência.<br /> Replicação do sistema HANA e falha no controlo do sistema OPERATIVO. | Configuração dedicada dr.<br /> Configuração dr multiusos.<br /> Sincronização dr utilizando a replicação de armazenamento.<br /> Dr usando a replicação do sistema HANA ainda não é possível sem componentes de terceiros. | São fixados conjuntos separados de volumes de disco a cada nó.<br /> Apenas volumes de discos de réplica secundária no local de produção são replicados para a localização DR.<br /> Um conjunto de volumes é necessário no local dr. | 

Uma configuração de DR dedicada é onde a unidade HANA Large Instance no site DR não é usada para executar qualquer outra carga de trabalho ou sistema de não produção. A unidade é passiva e é implantada apenas se um desastre falhar. No entanto, esta configuração não é uma escolha preferida para muitos clientes.

Consulte [cenários apoiados pelo HLI](hana-supported-scenario.md) para aprender o layout de armazenamento e detalhes ethernet para a sua arquitetura.

> [!NOTE]
> [As implementações DE SAP HANA MCOD (múltiplas](https://launchpad.support.sap.com/#/notes/1681092) instâncias HANA numa unidade) como cenários de sobreposição funcionam com os métodos HA e DR listados na tabela. Uma exceção é a utilização da Replicação do Sistema HANA com um cluster automático de failover baseado no Pacemaker. Tal caso só suporta uma instância HANA por unidade. Para as implementações [de MDC SAP HANA,](https://launchpad.support.sap.com/#/notes/2096000) apenas os métodos HA e DR não baseados em armazenamento funcionam se mais de um inquilino for implantado. Com um inquilino implantado, todos os métodos listados são válidos.  

Uma configuração de DR multiusos é onde a unidade HANA Large Instance no site DR executa uma carga de trabalho não-produção. Em caso de catástrofe, desligue o sistema de não produção, monte os conjuntos de volume (adicionais) replicados pelo armazenamento e, em seguida, inicie a produção da instância HANA. A maioria dos clientes que usam a funcionalidade de recuperação de desastres HANA Large Instance utilizam esta configuração. 


Pode encontrar mais informações sobre a alta disponibilidade da SAP HANA nos seguintes artigos DAA: 

- [Papel branco de alta disponibilidade SAP HANA](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de Administração SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo da Academia SAP HANA na replicação do sistema SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de suporte SAP #1999880 – FAQ na replicação do sistema SAP HANA](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [Nota de suporte SAP #2165547 – SAP HANA Back up e Restaurar dentro do Ambiente de Replicação do Sistema SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Nota de suporte SAP #1984882 – Utilização da replicação do sistema SAP HANA para troca de hardware com tempo de inatividade mínimo/zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerações de rede para recuperação de desastres com HANA Grandes Instâncias

Para tirar partido da funcionalidade de recuperação de desastres da HANA Large Instances, você precisa projetar a conectividade da rede para as duas regiões Azure. Você precisa de uma ligação do circuito Azure ExpressRoute a partir das instalações da sua principal região de Azure, e outra ligação de circuito desde as instalações até à sua região de recuperação de desastres. Esta medida abrange uma situação em que há um problema numa região de Azure, incluindo uma localização do Microsoft Enterprise Edge Router (MSEE).

Como segunda medida, pode ligar todas as redes virtuais Azure que se conectam ao SAP HANA em Azure (Grandes Instâncias) numa região a um circuito ExpressRoute que liga HANA Large Instances na outra região. Com esta *ligação cruzada,* os serviços em execução numa rede virtual Azure na Região 1 podem ligar-se às unidades HANA Large Instance na Região 2, e ao contrário. Esta medida aborda um caso em que apenas uma das localizações do MSEE que se conecta ao seu local de localização com o Azure fica offline.

O gráfico a seguir ilustra uma configuração resiliente para casos de recuperação de desastres:

![Configuração ideal para recuperação de desastres](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Outros requisitos com a replicação do armazenamento de grandes instâncias HANA para a recuperação de desastres

Para além dos requisitos anteriores para uma configuração de recuperação de desastres com HANA Large Instances, deve:

- Encomende SKUs SAP HANA em Azure (Grandes Instâncias) SKUs do mesmo tamanho que os SKUs de produção e implemente-os na região de recuperação de desastres. Nas atuais implementações de clientes, estas instâncias são utilizadas para executar instâncias HANA não-produção. Estas configurações são *referidas como configurações DR multiusos*.   
- Encomende armazenamento adicional no site DR para cada um dos seus SKUs SAP HANA em Azure (Grandes Instâncias) que pretende recuperar no local de recuperação de desastres. A compra de armazenamento adicional permite-lhe alocar os volumes de armazenamento. Você pode alocar os volumes que são alvo da replicação de armazenamento da sua produção Região Azure na região de Azure de recuperação de desastres.
- No caso de ter a configuração de HSR na primária e configurar a replicação baseada no armazenamento para o site DR, deve adquirir armazenamento adicional no site DR para que os dados dos nós primários e secundários sejam replicados no site DR.

  **Próximos passos**
- Consulte [a Cópia de Segurança e restaure](hana-backup-restore.md).













