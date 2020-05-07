---
title: Perguntas mais frequentes
description: Fornece respostas a algumas das questões comuns sobre a Azure VMware Solution (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 6d8e87dd52871b82109ccc794af04244efe95b06
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854596"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Perguntas frequentes sobre a pré-visualização da Azure VMware Solution (AVS)

Respostas para perguntas frequentes sobre a Azure VMware Solution (AVS).

## <a name="general"></a>Geral

**O que é a Solução Azure VMware (AVS)?**

À medida que as empresas prosseguem estratégias de modernização de TI para melhorar a agilidade do negócio, reduzir custos e acelerar a inovação, as plataformas híbridas de nuvem surgiram como principais facilitadores da transformação digital dos clientes. O AVS combina o software Software Defined Data Center (SDDC) da VMware com o ecossistema global de serviços em nuvem do Microsoft Azure. A solução AVS é conseguida para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="avs-service"></a>Serviço AVS

**Onde está a AVS disponível hoje?**

Durante a pré-estreia, está disponível no Us East na América do Norte e em Amesterdão na Europa Ocidental.

**As cargas de trabalho em funcionamento numa instância Azure VMware Solution (AVS) podem consumir ou integrar-se com os serviços Azure?**

Todos os serviços azure estarão disponíveis para clientes de solução AVS. As limitações de desempenho e disponibilidade para serviços específicos terão de ser abordadas caso a caso.

**Uso as mesmas ferramentas que uso agora para gerir recursos privados em nuvem?**

Sim. O portal Azure é utilizado para a implantação e uma série de operações de gestão. vCenter e NSX Manager são usados para gerir os recursos vSphere e NSX-T.

**Posso gerir uma nuvem privada com o meu vCenter no local?**

No lançamento, a AVS não apoiará uma única experiência de gestão em ambientes de nuvem privada. Os clusters privados de nuvem serão geridos com vCenter e NSX Manager local para uma nuvem privada.

**Posso usar a VRealize Suite correndo no local?** 

As integrações específicas e os casos de utilização podem ser avaliados caso a caso.

**Posso migrar vSphere VMs de ambientes no local para nuvens privadas AVS?**

Sim. A migração vM e a vMotion podem ser usadas para mover VMs parahttps://kb.vmware.com/s/article/210695uma nuvem privada se o vCenter transversal padrão [requisitos vMotion][ ] for cumprido.

**É necessária uma versão específica da vSphere em ambientes no local?**

Uma vez que todos os ambientes em nuvem vêm com HCX, vSphere 5.5 ou mais tarde em ambientes no local para vMotion.

**Como é o processo de controlo de mudança?**

As atualizações feitas ao próprio serviço seguirão o processo de gestão de mudanças padrão do Microsoft Azure. Os clientes são responsáveis por quaisquer tarefas de administração de carga de trabalho e os processos de gestão de mudanças associados.

**Como é que isto é diferente da Solução Azure VMware da CloudSimple?**

Com a nova Solução Azure VMware, a Microsoft e a VMware têm uma parceria direta com fornecedores de nuvem. A nova solução é totalmente projetada, construída e apoiada pela Microsoft, e apoiada pela VMware. Em termos arquitetónicos, as soluções são consistentes, com a pilha de tecnologia VMware a funcionar numa infraestrutura dedicada azure.

**Se sou um cliente da Azure VMware Solution existente, o que significa esta pré-visualização para mim?**

Não existe qualquer alteração à atual Solução Azure VMware pela CloudSimple. Continuamos a apoiar a solução no Azure. A Azure VMware Solution é apoiada pelo nosso acordo de nível de serviço [(SLA)](https://aka.ms/CSVMwareSLA). Os clientes devem continuar a utilizar o serviço para cargas de trabalho de produção; esta é uma solução disponível regida pelos [termos de serviço da Microsoft.](https://azure.microsoft.com/support/legal/)

**Posso migrar da Azure VMware Solution by CloudSimple para esta nova solução?**

Sim, a Azure VMware Solution suporta a migração utilizando ferramentas VMware familiares como o HCX. Para os clientes interessados em migrar para a nova solução, por favor trabalhe com a sua equipa de conta Microsoft para explorar opções e suporte disponível.

## <a name="compute-network-and-storage"></a>Computação, rede e armazenamento

**Há mais de um tipo de hospedeiro disponível?**

Só há um tipo de hospedeiro disponível.

**Quais são as especificações da CPU em cada tipo de hospedeiro?**

Os servidores têm cpUs intel de 2,3 GHz de 2,3 GHz.

**Quanta memória tem em cada hospedeiro?**

Os servidores têm 576 GB de RAM.

**Qual é a capacidade de armazenamento de cada hospedeiro?**

Cada anfitrião ESXi tem dois grupos de discos VSAN com um nível de capacidade de 15,2 TB e um nº de cache NVMe de 3,2 TB (1,6 TB em cada grupo de disco).

**Quanto largura de banda de rede está disponível em cada anfitrião ESXi?**

EsXi acolhe suporte de largura de banda de conectividade até 25 Gbps.

**Os dados armazenados nas lojas de dados VSAN estão encriptados em repouso?**

Sim, todos os dados VSAN são encriptados por padrão usando chaves armazenadas no Cofre de Chaves Azure.

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

**A infraestrutura subjacente é partilhada?**

Não, os anfitriões e aglomerados privados de nuvem são dedicados e apagados de forma segura antes e depois da utilização.

**Qual é o número mínimo e máximo de hospedeiros por agrupamento?**

Os clusters podem escalar entre 3 e 16 anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

**Posso escalar os meus aglomerados privados de nuvens?**

Sim, a escala de aglomerados entre o número mínimo e máximo de anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

**O que são agrupamentos de ensaios?**

Os agrupamentos de ensaios são três aglomerados hospedeiros usados para avaliações de um mês de nuvens privadas AVS.

**Posso usar anfitriões de alta qualidade para grupos de ensaio?**

Não. Os anfitriões ESXi de alta qualidade são reservados para uso em clusters de produção.

## <a name="avs-and-vmware-software"></a>Software AVS e VMware

**Que versões do software VMware são usadas em nuvens privadas?**

Nuvens privadas usam vSphere 6.7, vSAN 6.7, HCX e versão 2.5 de NSX-T.  

**As nuvens privadas usam O NSX VMware?**

Sim, NSX-T 2.5 é usado para o software definido networking em nuvens privadas AVS.

**Posso usar VMware NSX-V numa nuvem privada?**

Não. NSX-T é a única versão suportada do NSX.

**É necessário nsx em ambientes ou redes no local que se ligam a uma nuvem privada.**

Não, não é obrigado a usar NSX no local.

**Qual é a programação de atualização e atualização do software VMware numa nuvem privada?**

As atualizações privadas do pacote de software em nuvem são feitas para manter o software dentro de uma versão do mais recente lançamento do formulário de software VMware. As versões privadas de software em nuvem podem ser diferentes das versões mais recentes dos componentes individuais do software (ESXi, NSX-T, vCenter, VSAN).

**Quantas vezes a pilha privada de software em nuvem será atualizada?**

O software privado de cloud é atualizado numa programação que acompanha com o lançamento do pacote de software da VMware. A sua nuvem privada não requer tempo de descanso para upgrades.

## <a name="connectivity"></a>Conectividade

**Que planeamento de endereços IP da rede é necessário para incorporar nuvens privadas com ambientes no local?**

É necessário um espaço de endereço seletiva /22 para implantar uma nuvem privada AVS. Este espaço de endereços privados não deve sobrepor-se a outras redes virtuais numa subscrição, ou com redes no local.
 
**Como posso ligar de ambientes no local a uma nuvem privada AVS?**

Pode ligar-se ao serviço num de dois métodos: 

- Com um VM ou portal de aplicações implantado numa rede virtual Azure que é espreitada através do ExpressRoute para a nuvem privada.
- Através do ExpressRoute Global Reach do seu centro de dados no local para um circuito Azure ExpressRoute.

**Como posso ligar um VM de carga de trabalho à internet ou a um ponto final de serviço Azure?**

No portal Azure, permita a conectividade da Internet para uma nuvem privada. Com o gestor NSX-T, crie um router NSX-T T1 e um interruptor lógico. Em seguida, utilize o vCenter para implantar um VM no segmento de rede definido pelo interruptor lógico. Essa VM terá acesso à rede à internet e aos serviços Azure.

**Preciso restringir o acesso da internet a VMs em redes lógicas numa nuvem privada?**

Não. O tráfego de rede que entra diretamente da internet para nuvens privadas não é permitido.

**Preciso de restringir o acesso à Internet de VMs em redes lógicas para a internet?**

Sim. Você precisará usar o gestor NSX-T para criar uma firewall que restringe o acesso vM à internet.

## <a name="accounts-and-privileges"></a>Contas e privilégios

**Que contas e privilégios vou ter com a minha nova nuvem privada AVS?**

São-lhe fornecidas credenciais para um utilizador de cloudadmin no vCenter e acesso administrativo no NSX-T Manager. Há também um grupo CloudAdmin que pode ser usado para incorporar o Diretório Ativo Azure. Para mais informações, consulte Conceitos de [Acesso e Identidade.](concepts-identity.md)

**Pode ter acesso a administradores aos anfitriões esXi?**

Não, o acesso do administrador à ESXi limita-se a satisfazer os requisitos de segurança da solução.

**Que privilégios e permissões terei no vCenter?**

Terá privilégios de grupo CloudAdmin. Para mais informações, consulte Conceitos de [Acesso e Identidade.](concepts-identity.md)

**Que privilégios e permissões terei no gerente da NSX-T?**

Terá privilégios de administrador completos no NSX-T e poderá gerir o controlo de acesso baseado em papéis, como faria com o NSX-T Data Center no local. Para mais informações, consulte Conceitos de [Acesso e Identidade.](concepts-identity.md)

> [!NOTE]
> Um router T0 é criado e configurado como parte de uma implantação privada em nuvem. Qualquer modificação a esse router lógico ou aos VMs do nó de borda NSX-T pode afetar a conectividade com a sua nuvem privada.

## <a name="billing-and-support"></a>Faturação e Apoio

**Como vou ser cobrado durante a pré-visualização do AVS**

A faturação para AVS durante a pré-visualização é mensal mente paga. Opções adicionais estarão disponíveis na disponibilidade geral.

**Como é que os preços serão estruturados durante a pré-visualização do AVS?**

Para questões gerais sobre preços consulte a página de [preços](https://azure.microsoft.com/pricing/details/azure-vmware) da Azure VMware Solution. Os preços de pré-visualização estão disponíveis mediante solicitação, contacte a sua equipa de conta ou siga o link na página de preços para contactar as vendas.

**Quem apoia a AVS?**

O suporte para AVS é entregue pela Microsoft. Por favor, note, de acordo com as nossas diretrizes de pré-visualização, forneceremos apoio durante as 9 às 17 horas de trabalho do PST de segunda a sexta-feira. Você pode levantar um bilhete de suporte a partir [deste link](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

**Que contas preciso para criar uma nuvem privada AVS?**

Você precisará de uma conta Azure numa subscrição Azure.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
