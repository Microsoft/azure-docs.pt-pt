---
title: Elevada disponibilidade e recuperação após desastre do SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4d60f6752bf369e875c350823f76854408fcb806
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000604"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Instâncias grandes do SAP HANA elevada disponibilidade e recuperação após desastre no Azure 

>[!IMPORTANT]
>Esta documentação não é nenhuma substituição da documentação de administração do SAP HANA ou SAP Notes. Espera-se que o leitor tenha uma compreensão sólida do e experiência em administração de SAP HANA e operações, especialmente com os tópicos de cópia de segurança, restauro, elevada disponibilidade e recuperação após desastre.

É importante que exerce o etapas e os processos executados no seu ambiente e com as suas versões do HANA e versões. Alguns processos descritos nesta documentação são simplificados para uma melhor compreensão geral e não se destinam a ser utilizado como obter os passos detalhados para handbooks operação eventual. Se quiser criar handbooks de operação para suas configurações, terá de testar e testar seus processos e os processos relacionados com suas configurações específicas de documentos. 


Elevada disponibilidade e recuperação após desastre (DR) são aspectos cruciais da execução do seu SAP HANA de missão crítica no servidor do Azure (instâncias grandes). É importante trabalhar com o SAP, o integrador de sistemas ou Microsoft corretamente arquitetar e implementar o direito de elevada disponibilidade e estratégias de recuperação após desastre. Também é importante considerar o objetivo de ponto de recuperação (RPO) e o objetivo de tempo de recuperação, que são específicas do ambiente.

A Microsoft suporta alguns recursos de alta disponibilidade do SAP HANA com instâncias grandes do HANA. Estas funcionalidades incluem:

- **Replicação de armazenamento**: Capacidade do sistema de armazenamento para replicar todos os dados para outro carimbo de instância grande do HANA noutra região do Azure. SAP HANA funciona independentemente neste método. Esta funcionalidade é o mecanismo de recuperação de desastres do padrão oferecido para as instâncias grandes do HANA.
- **Replicação do sistema HANA**: O [replicação de todos os dados no SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) para um sistema separado do SAP HANA. O objetivo de tempo de recuperação é minimizado através da replicação de dados em intervalos regulares. SAP HANA suporta assíncronos e síncronos modos dentro da memória e síncronos. Modo síncrono é utilizado apenas para sistemas de SAP HANA que estão dentro do mesmo datacenter ou inferior a 100 km de distância. Com o design atual de carimbos de instância grande do HANA, a replicação do sistema HANA pode ser utilizada para elevada disponibilidade numa região apenas. Replicação do sistema HANA requer um proxy inverso de terceiros ou o componente de roteamento para configurações de recuperação após desastre para outra região do Azure. 
- **Ativação pós-falha automática do anfitrião**: Uma solução de recuperação de falha local para SAP HANA, que é uma alternativa para a replicação de sistema do HANA. Se o nó principal ficar indisponível, configure um ou mais nós de SAP HANA em espera no modo de escalamento horizontal e SAP HANA automaticamente efetua a ativação pós-falha para um nó em modo de espera.

SAP HANA no Azure (instâncias grandes) é disponibilizado em duas regiões do Azure em quatro áreas geopolíticas (dos EUA, Austrália, Europa e Japão). Duas regiões dentro de uma área geopolítica que alojam os carimbos de data / instância grande do HANA estão ligados aos circuitos de rede dedicado separado. Estes são utilizados para replicar os instantâneos de armazenamento para fornecer métodos de recuperação de desastres. A replicação não for estabelecida por predefinição, mas é configurada para os clientes que adquirirem a funcionalidade de recuperação após desastre. Replicação de armazenamento é dependente da utilização de instantâneos de armazenamento para instâncias grandes do HANA. Não é possível escolher uma região do Azure como uma região de DR é uma área geopolíticas diferentes. 

A tabela seguinte mostra as combinações e atualmente elevados disponibilidade e após desastre recuperação métodos suportados:

| Cenário suportado no HANA nas instâncias grandes | Opção de elevada disponibilidade | Opção de recuperação após desastre | Comentários |
| --- | --- | --- | --- |
| Nó único | Não disponível. | Configuração de DR dedicada.<br /> Configuração de Multipurpose DR. | |
| Anfitrião ativação pós-falha automática: Escalamento horizontal (com ou sem o modo de espera)<br /> incluindo 1 + 1 | Possível com o modo de espera que a função do Active Directory.<br /> HANA controla o comutador de função. | Configuração de DR dedicada.<br /> Configuração de Multipurpose DR.<br /> Sincronização de DR através de replicação de armazenamento. | São anexados a conjuntos de volume do HANA para todos os nós.<br /> Site de DR tem de ter o mesmo número de nós. |
| Replicação do sistema HANA | Possível com a configuração primária ou secundária.<br /> Secundário se move para a função primária num caso de ativação pós-falha.<br /> Replicação do sistema HANA e o sistema operacional controla ativação pós-falha. | Configuração de DR dedicada.<br /> Configuração de Multipurpose DR.<br /> Sincronização de DR através de replicação de armazenamento.<br /> DR através de replicação de sistema HANA ainda não é possível sem componentes de terceiros. | Um conjunto separado de volumes de disco são anexados a cada nó.<br /> Apenas os volumes de disco de réplica secundária no site de produção são replicados para a localização de DR.<br /> Um conjunto de volumes é necessário no site de DR. | 

Uma configuração de DR dedicada é onde a unidade de instância grande do HANA no site de DR não é utilizada para executar qualquer carga de trabalho ou sistema de não produção. A unidade é passiva e é implementada apenas se for executada uma ativação pós-falha de desastre. No entanto, esta configuração não é uma opção preferencial para muitos clientes.

Consultar [cenários suportados de HLI](hana-supported-scenario.md) para obter detalhes de ethernet e esquema de armazenamento para a sua arquitetura.

> [!NOTE]
> [Implementações do SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (várias instâncias de HANA numa só unidade) como a sobreposição de cenários de trabalho com o HA e DR métodos listados na tabela. Uma exceção é o uso do HANA System Replication com um cluster de ativação pós-falha automática com base em Pacemaker. Nesse caso suporta apenas uma instância HANA por unidade. Para [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) implementações, apenas métodos HA e DR com base no armazenamento funcionam se mais do que um inquilino é implementado. Com um inquilino implementado, todos os métodos listados são válidos.  

Uma configuração de DR multipurpose é onde a unidade de instância grande do HANA no site de DR é executada uma carga de trabalho de não produção. Em caso de desastre, encerrar o sistema de não produção, montar os conjuntos de armazenamento replicado volume (adicionais) e, em seguida, inicie a instância do HANA de produção. A maioria dos clientes que utilizam a funcionalidade de recuperação de desastres de instância grande do HANA Utilize esta configuração. 


Pode encontrar mais informações sobre o SAP HANA de elevada disponibilidade nos seguintes artigos SAP: 

- [Documento técnico de alta disponibilidade do SAP HANA](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de administração do SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo de SAP HANA Academy a replicação do sistema do SAP HANA](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de suporte #1999880 – perguntas frequentes sobre o SAP HANA System Replication de SAP](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP suporte Nota 2165547 # – SAP HANA novamente a cópia de segurança e restauro num ambiente de replicação de sistema do SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP suporte Nota #1984882 – utilizar o SAP HANA System Replication para o Exchange de Hardware com o tempo de inatividade mínimo/Zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerações de rede para recuperação após desastre com instâncias grandes do HANA

Para tirar partido da funcionalidade de recuperação após desastre do HANA nas instâncias grandes, terá de criar a conectividade de rede para as duas regiões do Azure. Precisa de uma ligação do circuito do ExpressRoute do Azure no local na sua região do Azure principal e outra ligação do circuito no local para a sua região de recuperação após desastre. Esta medida abrange uma situação em que há um problema com uma região do Azure, incluindo uma localização de Router de periferia Enterprise (MSEE) da Microsoft.

Como uma segunda medida, pode ligar-se todas as redes virtuais do Azure que se ligam ao SAP HANA no Azure (instâncias grandes) numa única região para um circuito do ExpressRoute que se liga instâncias grandes do HANA na outra região. Com isso *cruzada ligar*serviços em execução no Azure, rede virtual na região 1 pode ligar a unidades de instância grande do HANA região 2 e o caminho inverso. Esta medida aborda um caso em que apenas uma das localizações MSEE que se liga a sua localização no local com o Azure fica offline.

O gráfico seguinte ilustra uma configuração resiliente para casos de recuperação de desastres:

![Configuração ideal para recuperação após desastre](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Outros requisitos com a replicação de armazenamento de instâncias grandes do HANA para recuperação após desastre

Além dos requisitos anteriores para uma configuração de recuperação após desastre com instâncias grandes do HANA, tem de:

- Solicitar o SAP HANA em SKUs de Azure (instâncias grandes) do mesmo tamanho como seu SKUs de produção e implementá-las na região de recuperação após desastre. Nas implementações de cliente atual, estas instâncias são utilizadas para executar as instâncias do HANA de não produção. Estas configurações são denominadas *multipurpose configurações de DR*.   
- Solicitar armazenamento adicional no site de DR para cada um dos seus SAP HANA no Azure (instâncias grandes) SKUs que pretende recuperar no site de recuperação após desastre. Compra de armazenamento adicional permite-lhe alocar os volumes de armazenamento. Pode alocar os volumes que são o destino da replicação de armazenamento da sua região do Azure de produção para a recuperação após desastre de região do Azure.
- No caso, em que tiver configurado HSR primário e configurar a replicação de armazenamento com base no site de DR, tem de comprar armazenamento adicional no site de DR por isso, os dois principais e os dados de nós secundários obtém replicados para o site de DR.

  **Passos seguintes?**
- Consultar [Backup e restauração](hana-backup-restore.md).













