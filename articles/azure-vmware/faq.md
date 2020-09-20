---
title: Perguntas mais frequentes
description: Fornece respostas a algumas das perguntas comuns sobre a Azure VMware Solution.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 7b4abc2d711a3da6a6df125854759e083d7e04a7
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817841"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Perguntas frequentes sobre pré-visualização da Solução VMware Azure

Respostas para perguntas frequentes sobre Azure VMware Solution.

## <a name="general"></a>Geral

### <a name="what-is-azure-vmware-solution"></a>O que é o Azure VMware Solution?

À medida que as empresas prosseguem estratégias de modernização de TI para melhorar a agilidade do negócio, reduzir custos e acelerar a inovação, as plataformas híbridas cloud surgiram como principais facilitadores da transformação digital dos clientes. A Azure VMware Solution combina o software do Software Defined Data Center (SDDC) da VMware com o ecossistema global de serviços de nuvem do Microsoft Azure. A Azure VMware Solution é gerida para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="azure-vmware-solution-service"></a>Serviço de Solução Azure VMware

### <a name="where-is-azure-vmware-solution-available-today"></a>Onde está a Solução VMware Azure disponível hoje?

O serviço está continuamente a ser adicionado a novas regiões, por isso veja as [últimas informações de disponibilidade de serviços](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) para mais detalhes. 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>As cargas de trabalho em execução numa Solução VMware Azure podem consumir ou integrar-se com os serviços Azure?

Todos os serviços da Azure estarão disponíveis para os clientes da Azure VMware Solution. As limitações de desempenho e disponibilidade de serviços específicos terão de ser abordadas caso a caso.

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Uso as mesmas ferramentas que uso agora para gerir recursos privados em nuvem?

Sim. O portal Azure é utilizado para implantação e várias operações de gestão. vCenter e NSX Manager são usados para gerir recursos vSphere e NSX-T.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Posso gerir uma nuvem privada com o meu vCenter no local?

No lançamento, a Azure VMware Solution não suportará uma única experiência de gestão em ambientes de nuvem e local. Os aglomerados de nuvem privada serão geridos com vCenter e NSX Manager local para uma nuvem privada.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Posso usar a vRealize Suite a funcionar no local? 

Integrações específicas e casos de utilização podem ser avaliados caso a caso.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Posso migrar vSphere VMs de ambientes no local para nuvens privadas Azure VMware Solution?

Sim. A migração vM e vMotion podem ser usadas para mover VMs para uma nuvem privada se os requisitos padrão de vCenter [vCenter vMotion forem cumpridos.](https://kb.vmware.com/s/article/210695)

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>É necessária uma versão específica da vSphere em ambientes no local?

Uma vez que todos os ambientes em nuvem vêm com HCX, vSphere 5.5 ou mais tarde em ambientes no local para vMotion.

### <a name="what-does-the-change-control-process-look-like"></a>Como é o processo de controlo de mudança?

As atualizações feitas ao próprio serviço seguirão o processo padrão de gestão de alterações da Microsoft Azure. Os clientes são responsáveis por quaisquer tarefas de administração da carga de trabalho e pelos processos de gestão de alterações associados.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Como é que isto é diferente da Solução VMware Azure da CloudSimple?

Com a nova Azure VMware Solution, a Microsoft e a VMware têm uma parceria direta com fornecedores de nuvem. A nova solução é totalmente projetada, construída e suportada pela Microsoft, e endossada pela VMware. Em termos arquitetónicos, as soluções são consistentes, com a pilha de tecnologia VMware a funcionar numa infraestrutura dedicada ao Azure.

### <a name="if-im-an-existing-azure-vmware-solution-customer-what-does-this-preview-mean-for-me"></a>Se sou um cliente existente da Azure VMware Solution, o que significa esta pré-visualização para mim?

Não há qualquer alteração à solução Azure VMware da CloudSimple. Continuamos a apoiar a solução sobre o Azure. A Azure VMware Solution é apoiada pelo nosso acordo de nível de serviço [(SLA)](https://aka.ms/CSVMwareSLA). Os clientes devem continuar a utilizar o serviço para cargas de trabalho de produção; esta é uma solução disponível regida pelos [termos de serviço da Microsoft.](https://azure.microsoft.com/support/legal/)

### <a name="can-i-migrate-from-azure-vmware-solution-by-cloudsimple-to-this-new-solution"></a>Posso migrar da Azure VMware Solution by CloudSimple para esta nova solução?

Sim, a Azure VMware Solution suporta a migração utilizando ferramentas VMware familiares como o HCX. Para os clientes interessados em migrar para a nova solução, trabalhe com a sua equipa de conta microsoft para explorar opções e suporte disponível.



## <a name="compute-network-and-storage"></a>Computação, rede e armazenamento

### <a name="is-there-more-than-one-type-of-host-available"></a>Há mais de um tipo de hospedeiro disponível?

Há apenas um tipo de hospedeiro disponível.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Quais são as especificações do CPU em cada tipo de hospedeiro?

Os servidores têm um núcleo duplo de 18 núcleos 2.3 GHz Intel CPUs.

### <a name="how-much-memory-is-in-each-host"></a>Quanta memória há em cada hospedeiro?

Os servidores têm 576 GB de RAM.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Qual é a capacidade de armazenamento de cada hospedeiro?

Cada anfitrião ESXi tem dois grupos de discos vSAN com um nível de capacidade de 15.2 TB e um nível de cache 3.2 TB NVMe (1.6 TB em cada grupo de discos).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Qual a largura de banda disponível em cada anfitrião ESXi?

Cada anfitrião ESXi é Azure VMware Solution está configurado com quatro NICs de 25 Gbps, com dois NICs previstos para o tráfego do sistema ESXi e dois NICs previstos para o tráfego de carga de trabalho. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Os dados armazenados nas datas-tores vSAN são encriptados em repouso?

Sim, todos os dados vSAN são encriptados por padrão usando chaves armazenadas no Cofre de Chaves Azure.

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

### <a name="is-the-underlying-infrastructure-shared"></a>A infraestrutura subjacente é partilhada?

Não, os anfitriões e aglomerados de nuvens privadas são dedicados e apagados de forma segura antes e depois da utilização.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Quais são o número mínimo e máximo de hospedeiros por agrupamento?

Os clusters podem escalar entre 3 e 16 anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Posso escalar os meus aglomerados de nuvens privadas?

Sim, os aglomerados escalam entre o número mínimo e máximo de anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

### <a name="what-are-trial-clusters"></a>O que são aglomerados de ensaios?

Os clusters de ensaio são três aglomerados de hospedeiros utilizados para avaliações de um mês de nuvens privadas Azure VMware Solution.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Posso usar hospedeiros de alta qualidade para agrupamentos de ensaios?

Não. Os anfitriões ESXi de gama alta estão reservados para uso em clusters de produção.

## <a name="azure-vmware-solution-and-vmware-software"></a>Solução Azure VMware e software VMware

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Que versões do software VMware são usadas em nuvens privadas?

Nuvens privadas usam vSphere 6.7, vSAN 6.7, HCX, e versão 2.5 de NSX-T.  

### <a name="do-private-clouds-use-vmware-nsx"></a>Nuvens privadas usam VMware NSX?

Sim, o NSX-T 2.5 é utilizado para a rede definida em nuvem privada Azure VMware Solution.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Posso usar VMware NSX-V numa nuvem privada?

Não. NSX-T é a única versão suportada do NSX.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>O NSX é necessário em ambientes ou redes no local que se ligam a uma nuvem privada?

Não, não é obrigado a usar NSX no local.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Qual é o calendário de atualização e atualização do software VMware numa nuvem privada?

As atualizações do pacote de software de nuvem privada são feitas para manter o software dentro de uma versão da versão mais recente do pacote de software da VMware. As versões privadas de software em nuvem podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Com que frequência a pilha de software de nuvem privada será atualizada?

O software de nuvem privada é atualizado numa programação que acompanha o lançamento do pacote de software da VMware. A sua nuvem privada não requer tempo de inatividade para atualizações.

## <a name="connectivity"></a>Conectividade

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Que planeamento de endereços IP de rede é necessário para incorporar nuvens privadas com ambientes no local?

É necessário um espaço de endereço de rede privada /22 para implantar uma nuvem privada Azure VMware Solution. Este espaço de endereço privado não deve sobrepor-se a outras redes virtuais numa subscrição ou com redes no local.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Como posso ligar-me dos ambientes no local a uma nuvem privada Azure VMware Solution?

Pode ligar-se ao serviço num de dois métodos: 

- Com um gateway VM ou aplicação implantado numa rede virtual Azure que é espreitada através do ExpressRoute para a nuvem privada.
- Através do ExpressRoute Global Reach do seu datacenter no local para um circuito Azure ExpressRoute.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Como posso ligar um VM de carga de trabalho à internet ou um ponto final de serviço Azure?

No portal Azure, permita a conectividade da internet para uma nuvem privada. Com o gestor NSX-T, crie um router NSX-T T1 e um interruptor lógico. Em seguida, utilize o vCenter para implantar um VM no segmento de rede definido pelo interruptor lógico. Que a VM terá acesso à rede à internet e aos serviços Azure.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Preciso de restringir o acesso da internet a VMs em redes lógicas numa nuvem privada?

Não. O tráfego de rede a entrar da internet diretamente para nuvens privadas não é permitido.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Preciso de restringir o acesso à Internet de VMs em redes lógicas para a internet?

Sim. Terá de usar o gestor NSX-T para criar uma firewall que restringe o acesso de VM à internet.

## <a name="accounts-and-privileges"></a>Contas e privilégios

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Que contas e privilégios vou ter com a minha nova nuvem privada Azure VMware Solution?

É-lhe fornecidas credenciais para um utilizador de cloudadmin no vCenter e acesso administrativo no NSX-T Manager. Há também um grupo CloudAdmin que pode ser usado para incorporar o Azure Ative Directory. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Pode ter acesso de administrador aos anfitriões ESXi?

Não, o acesso do administrador à ESXi está restrito ao cumprimento dos requisitos de segurança da solução.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Que privilégios e permissões terei no vCenter?

Terá privilégios de grupo CloudAdmin. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Que privilégios e permissões terei sobre o gerente da NSX-T?

Você terá privilégios completos de administrador em NSX-T e poderá gerir o controlo de acesso baseado em funções como faria com o NSX-T Data Center no local. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

> [!NOTE]
> Um router T0 é criado e configurado como parte de uma implementação de nuvem privada. Qualquer modificação nesse router lógico ou nos VMs de nó de borda NSX-T pode afetar a conectividade à sua nuvem privada.

## <a name="billing-and-support"></a>Faturação e Apoio

### <a name="how-will-i-be-billed-during-the-preview-of-azure-vmware-solution"></a>Como vou ser cobrado durante a pré-estreia da Azure VMware Solution?

A faturação da Azure VMware Solution durante a pré-visualização é mensal numa base pay-as-you-go. Opções adicionais estarão disponíveis por disponibilidade geral.

### <a name="how-will-pricing-be-structured-during-the-preview-of-azure-vmware-solution"></a>Como será estruturado o preço durante a pré-visualização da Solução VMware Azure?

Para questões gerais sobre preços consulte a página [de preços](https://azure.microsoft.com/pricing/details/azure-vmware) da Solução VMware Azure. Os preços de pré-visualização estão disponíveis a pedido, contactem a equipa da sua conta ou sigam o link na página de preços para contactar as vendas.

### <a name="who-supports-azure-vmware-solution"></a>Quem suporta a Azure VMware Solution?

O suporte para Azure VMware Solution é entregue pela Microsoft. Nota, de acordo com as nossas diretrizes de pré-visualização, forneceremos apoio durante as 21:00 horas úteis do PST de segunda a sexta-feira. Você pode levantar um bilhete de apoio a partir [deste link](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Que contas preciso para criar uma nuvem privada Azure VMware Solution?

Você precisará de uma conta Azure numa subscrição do Azure.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Como solicito um aumento da quota de anfitrião para a Azure VMware Solution?

* Vai precisar de um [Acordo de Empresa Azure (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) com a Microsoft.
* Você precisará de uma conta Azure numa subscrição do Azure.

Antes de criar o seu recurso Azure VMware Solution, terá de submeter um bilhete de apoio para que os seus nós tenham os seus nós atribuídos. Uma vez que a equipa de apoio recebe o seu pedido, leva até cinco dias úteis para confirmar o seu pedido e alocar os seus nós. Se tiver uma nuvem privada Azure VMware Solution e quiser mais nós atribuídos, passará pelo mesmo processo.


1. No seu portal Azure, no âmbito **da Ajuda + Suporte,** crie um **[novo pedido de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o bilhete:
   - **Tipo de emissão:** Técnico
   - **Assinatura:** Selecione a sua subscrição
   - **Serviço:** Todos os serviços > Solução VMware Azure
   - **Recurso:** Pergunta geral 
   - **Resumo:** Capacidade de necessidade
   - **Tipo de problema:** Questões de Gestão de Capacidades
   - **Subtipo de problema:** Pedido de cliente para quota/capacidade adicional do anfitrião

1. Na **Descrição** do bilhete de apoio, no separador **Detalhes,** forneça:

   - POC ou Produção 
   - Nome da Região
   - Número de nós
   - Quaisquer outros detalhes

   >[!NOTE]
   >A Azure VMware Solution recomenda um mínimo de três nós para girar a sua nuvem privada e para os nós N+1 de redundância. 

1. Selecione **Review + Criar** para submeter o pedido.

   Levará até cinco dias úteis para um representante de apoio confirmar o seu pedido.

   >[!IMPORTANT] 
   >Se já tem uma Solução Azure VMware existente, e está a solicitar nóns adicionais, por favor, note que precisamos de cinco dias úteis para alocar os nós. 

1. Antes de poder aprovisionar os seus nós, certifique-se de que regista o fornecedor de recursos **Microsoft.AVS** no portal Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para obter formas adicionais de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
