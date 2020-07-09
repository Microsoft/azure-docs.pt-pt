---
title: Perguntas mais frequentes
description: Fornece respostas a algumas das perguntas comuns sobre Azure VMware Solution (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 1649b5649bd18b7ab53f3cc0196d7dff0f6f5b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84112690"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Perguntas frequentes sobre pré-visualização da Solução VMware Azure (AVS)

Respostas para perguntas frequentes sobre Azure VMware Solution (AVS).

## <a name="general"></a>Geral

**O que é Azure VMware Solution (AVS)?**

À medida que as empresas prosseguem estratégias de modernização de TI para melhorar a agilidade do negócio, reduzir custos e acelerar a inovação, as plataformas híbridas cloud surgiram como principais facilitadores da transformação digital dos clientes. O AVS combina o software do Software Defined Data Center (SDDC) da VMware com o ecossistema global de serviços de nuvem do Microsoft Azure. A solução AVS é gerida para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="avs-service"></a>Serviço AVS

**Onde é que o AVS está disponível hoje?**

Durante a pré-estreia, está disponível nos EUA Leste na América do Norte e em Amesterdão na Europa Ocidental.

**As cargas de trabalho que executam numa Solução VMware Azure (AVS) podem consumir ou integrar-se com os serviços Azure?**

Todos os serviços da Azure estarão disponíveis para clientes de solução AVS. As limitações de desempenho e disponibilidade de serviços específicos terão de ser abordadas caso a caso.

**Uso as mesmas ferramentas que uso agora para gerir recursos privados em nuvem?**

Sim. O portal Azure é utilizado para implantação e várias operações de gestão. vCenter e NSX Manager são usados para gerir recursos vSphere e NSX-T.

**Posso gerir uma nuvem privada com o meu vCenter no local?**

No lançamento, a AVS não apoiará uma única experiência de gestão em ambientes de nuvem privada e no local. Os aglomerados de nuvem privada serão geridos com vCenter e NSX Manager local para uma nuvem privada.

**Posso usar a vRealize Suite a funcionar no local?** 

Integrações específicas e casos de utilização podem ser avaliados caso a caso.

**Posso migrar vSphere VMs de ambientes no local para nuvens privadas AVS?**

Sim. A migração vM e vMotion podem ser usadas para mover VMs para uma nuvem privada se o vCenter padrão cruzado [requisitos vMotion][ https://kb.vmware.com/s/article/210695 forem cumpridos.

**É necessária uma versão específica da vSphere em ambientes no local?**

Uma vez que todos os ambientes em nuvem vêm com HCX, vSphere 5.5 ou mais tarde em ambientes no local para vMotion.

**Como é o processo de controlo de mudança?**

As atualizações feitas ao próprio serviço seguirão o processo padrão de gestão de alterações da Microsoft Azure. Os clientes são responsáveis por quaisquer tarefas de administração da carga de trabalho e pelos processos de gestão de alterações associados.

**Como é que isto é diferente da Solução VMware Azure da CloudSimple?**

Com a nova Azure VMware Solution, a Microsoft e a VMware têm uma parceria direta com fornecedores de nuvem. A nova solução é totalmente projetada, construída e suportada pela Microsoft, e endossada pela VMware. Em termos arquitetónicos, as soluções são consistentes, com a pilha de tecnologia VMware a funcionar numa infraestrutura dedicada ao Azure.

**Se sou um cliente existente da Azure VMware Solution, o que significa esta pré-visualização para mim?**

Não há qualquer alteração à solução Azure VMware da CloudSimple. Continuamos a apoiar a solução sobre o Azure. A Azure VMware Solution é apoiada pelo nosso acordo de nível de serviço [(SLA)](https://aka.ms/CSVMwareSLA). Os clientes devem continuar a utilizar o serviço para cargas de trabalho de produção; esta é uma solução disponível regida pelos [termos de serviço da Microsoft.](https://azure.microsoft.com/support/legal/)

**Posso migrar da Azure VMware Solution by CloudSimple para esta nova solução?**

Sim, a Azure VMware Solution suporta a migração utilizando ferramentas VMware familiares como o HCX. Para os clientes interessados em migrar para a nova solução, por favor, trabalhe com a sua equipa de conta microsoft para explorar opções e suporte disponível.



## <a name="compute-network-and-storage"></a>Computação, rede e armazenamento

**Há mais de um tipo de hospedeiro disponível?**

Há apenas um tipo de hospedeiro disponível.

**Quais são as especificações do CPU em cada tipo de hospedeiro?**

Os servidores têm um núcleo duplo de 18 núcleos 2.3 GHz Intel CPUs.

**Quanta memória há em cada hospedeiro?**

Os servidores têm 576 GB de RAM.

**Qual é a capacidade de armazenamento de cada hospedeiro?**

Cada anfitrião ESXi tem dois grupos de discos VSAN com um nível de capacidade de 15.2 TB e um nível de cache 3.2 TB NVMe (1.6 TB em cada grupo de discos).

**Qual a largura de banda disponível em cada anfitrião ESXi?**

Os anfitriões ESXi suportam largura de banda de conectividade até 25 Gbps.

**Os dados armazenados nas datas-lojas VSAN são encriptados em repouso?**

Sim, todos os dados vsan são encriptados por padrão usando chaves armazenadas no Cofre de Chaves Azure.

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

**A infraestrutura subjacente é partilhada?**

Não, os anfitriões e aglomerados de nuvens privadas são dedicados e apagados de forma segura antes e depois da utilização.

**Quais são o número mínimo e máximo de hospedeiros por agrupamento?**

Os clusters podem escalar entre 3 e 16 anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

**Posso escalar os meus aglomerados de nuvens privadas?**

Sim, os aglomerados escalam entre o número mínimo e máximo de anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

**O que são aglomerados de ensaios?**

Os aglomerados experimentais são três aglomerados hospedeiros usados para avaliações de um mês de nuvens privadas AVS.

**Posso usar hospedeiros de alta qualidade para agrupamentos de ensaios?**

Não. Os anfitriões ESXi de gama alta estão reservados para uso em clusters de produção.

## <a name="avs-and-vmware-software"></a>Software AVS e VMware

**Que versões do software VMware são usadas em nuvens privadas?**

Nuvens privadas usam vSphere 6.7, vSAN 6.7, HCX, e versão 2.5 de NSX-T.  

**Nuvens privadas usam VMware NSX?**

Sim, o NSX-T 2.5 é utilizado para o software definido em rede em nuvens privadas AVS.

**Posso usar VMware NSX-V numa nuvem privada?**

Não. NSX-T é a única versão suportada do NSX.

**O NSX é necessário em ambientes ou redes no local que se conectam a uma nuvem privada.**

Não, não é obrigado a usar NSX no local.

**Qual é o calendário de atualização e atualização do software VMware numa nuvem privada?**

As atualizações do pacote de software de nuvem privada são feitas para manter o software dentro de uma versão da versão mais recente do pacote de software da VMware. As versões privadas de software em nuvem podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, VSAN).

**Com que frequência a pilha de software de nuvem privada será atualizada?**

O software de nuvem privada é atualizado numa programação que acompanha o lançamento do pacote de software da VMware. A sua nuvem privada não requer tempo de inatividade para atualizações.

## <a name="connectivity"></a>Conectividade

**Que planeamento de endereços IP de rede é necessário para incorporar nuvens privadas com ambientes no local?**

É necessário um espaço de endereço de rede privada /22 para implantar uma nuvem privada AVS. Este espaço de endereço privado não deve sobrepor-se a outras redes virtuais numa subscrição ou com redes no local.
 
**Como posso ligar-me dos ambientes no local a uma nuvem privada AVS?**

Pode ligar-se ao serviço num de dois métodos: 

- Com um gateway VM ou aplicação implantado numa rede virtual Azure que é espreitada através do ExpressRoute para a nuvem privada.
- Através do ExpressRoute Global Reach do seu datacenter no local para um circuito Azure ExpressRoute.

**Como posso ligar um VM de carga de trabalho à internet ou um ponto final de serviço Azure?**

No portal Azure, permita a conectividade da internet para uma nuvem privada. Com o gestor NSX-T, crie um router NSX-T T1 e um interruptor lógico. Em seguida, utilize o vCenter para implantar um VM no segmento de rede definido pelo interruptor lógico. Que a VM terá acesso à rede à internet e aos serviços Azure.

**Preciso de restringir o acesso da internet a VMs em redes lógicas numa nuvem privada?**

Não. O tráfego de rede a entrar da internet diretamente para nuvens privadas não é permitido.

**Preciso de restringir o acesso à Internet de VMs em redes lógicas para a internet?**

Sim. Terá de usar o gestor NSX-T para criar uma firewall que restringe o acesso de VM à internet.

## <a name="accounts-and-privileges"></a>Contas e privilégios

**Que contas e privilégios vou ter com a minha nova nuvem privada avs?**

É-lhe fornecidas credenciais para um utilizador de cloudadmin no vCenter e acesso administrativo no NSX-T Manager. Há também um grupo CloudAdmin que pode ser usado para incorporar o Azure Ative Directory. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

**Pode ter acesso de administrador aos anfitriões ESXi?**

Não, o acesso do administrador à ESXi está restrito ao cumprimento dos requisitos de segurança da solução.

**Que privilégios e permissões terei no vCenter?**

Terá privilégios de grupo CloudAdmin. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

**Que privilégios e permissões terei sobre o gerente da NSX-T?**

Você terá privilégios completos de administrador em NSX-T e poderá gerir o controlo de acesso baseado em funções como faria com o NSX-T Data Center no local. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

> [!NOTE]
> Um router T0 é criado e configurado como parte de uma implementação de nuvem privada. Qualquer modificação nesse router lógico ou nos VMs de nó de borda NSX-T pode afetar a conectividade à sua nuvem privada.

## <a name="billing-and-support"></a>Faturação e Apoio

**Como vou ser cobrado durante a pré-estreia do AVS**

A faturação de AVS durante a pré-visualização é mensal numa base pay-as-you-go. Opções adicionais estarão disponíveis por disponibilidade geral.

**Como será estruturado o preço durante a pré-visualização do AVS?**

Para questões gerais sobre preços consulte a página [de preços](https://azure.microsoft.com/pricing/details/azure-vmware) da Solução VMware Azure. Os preços de pré-visualização estão disponíveis a pedido, contacte a sua equipa de conta ou siga o link na página de preços para contactar as vendas.

**Quem apoia o AVS?**

O suporte para AVS é entregue pela Microsoft. Por favor, note, de acordo com as nossas diretrizes de pré-visualização, forneceremos suporte durante as 21:00 horas úteis do PST de segunda a sexta-feira. Você pode levantar um bilhete de apoio a partir [deste link](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

**Que contas preciso para criar uma nuvem privada AVS?**

Você precisará de uma conta Azure numa subscrição do Azure.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Como solicito um aumento da quota de anfitrião para a Azure VMware Solution?**

Pode solicitar um aumento de quota [através da apresentação de um pedido de apoio.](..\azure-portal\supportability\how-to-create-azure-support-request.md) A equipa de Gestão de Quotas avalia o pedido e aprova-o no prazo de três dias úteis.  

> [!IMPORTANT]
> Antes de solicitar um aumento de quota, certifique-se de que regista o fornecedor de recursos **Microsoft.AVS** no portal Azure.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

1. No seu portal Azure, no âmbito **da Ajuda + Suporte,** crie um **novo pedido de suporte** e forneça as seguintes informações para o bilhete:
   - **Tipo de emissão:** Técnico
   - **Assinatura:** O seu ID de subscrição
   - **Serviço:**  Solução Azure VMware 
   - **Resumo:** Aumento das quotas
   - **Tipo de problema:** Questões de Gestão de Capacidades
   - **Subtipo de problema:** Pedido de cliente para quota/capacidade adicional do anfitrião

1. Na Descrição do bilhete de apoio, no separador Detalhes, forneça o seguinte:
   - Número de nós adicionais   
   - Nó SKU
   - Região

   > [!NOTE] 
   > Por defeito, será concedido um mínimo de quatro nós.

1. Clique **em 'Rever + Criar'** para submeter o pedido.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
