---
title: Perguntas mais frequentes
description: Fornece respostas a algumas das perguntas comuns sobre a Azure VMware Solution.
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: dikamath
ms.openlocfilehash: 2d71c30d2fb08629674c352ae812d8890463abfe
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695106"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Perguntas frequentes sobre Azure VMware Solution

Respostas para perguntas frequentes sobre Azure VMware Solution.

## <a name="general"></a>Geral

#### <a name="what-is-azure-vmware-solution"></a>O que é o Azure VMware Solution?

À medida que as empresas prosseguem estratégias de modernização de TI para melhorar a agilidade do negócio, reduzir custos e acelerar a inovação, as plataformas híbridas cloud surgiram como principais facilitadores da transformação digital dos clientes. A Azure VMware Solution combina o software Software-Defined Data Center (SDDC) da VMware com o ecossistema global de serviços de nuvem do Microsoft Azure. A Azure VMware Solution é gerida para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="azure-vmware-solution-service"></a>Serviço de Solução Azure VMware

#### <a name="where-is-azure-vmware-solution-available-today"></a>Onde está a Solução VMware Azure disponível hoje?

O serviço está continuamente a ser adicionado a novas regiões, por isso veja as [últimas informações de disponibilidade de serviços](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) para mais detalhes. 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>As cargas de trabalho em execução numa Solução VMware Azure podem consumir ou integrar-se com os serviços Azure?

Todos os serviços da Azure estarão disponíveis para os clientes da Azure VMware Solution. As limitações de desempenho e disponibilidade de serviços específicos terão de ser abordadas caso a caso.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Uso as mesmas ferramentas que uso agora para gerir recursos privados em nuvem?

Sim. O portal Azure é utilizado para implantação e várias operações de gestão. vCenter e NSX Manager são usados para gerir recursos vSphere e NSX-T.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Posso gerir uma nuvem privada com o meu vCenter no local?

No lançamento, a Azure VMware Solution não suportará uma única experiência de gestão em ambientes de nuvem e local. Os aglomerados de nuvem privada serão geridos com vCenter e NSX Manager local para uma nuvem privada.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Posso usar a vRealize Suite a funcionar no local? 

Integrações específicas e casos de utilização podem ser avaliados caso a caso.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Posso migrar vSphere VMs de ambientes no local para nuvens privadas Azure VMware Solution?

Sim. A migração vM e vMotion podem ser usadas para mover VMs para uma nuvem privada se os requisitos padrão de vCenter [vCenter vMotion forem cumpridos.](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952)

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>É necessária uma versão específica da vSphere em ambientes no local?

Todos os ambientes em nuvem vêm com VMware HCX, vSphere 5.5 ou mais tarde em ambientes no local para vMotion.

#### <a name="what-does-the-change-control-process-look-like"></a>Como é o processo de controlo de mudança?

As atualizações feitas ao próprio serviço seguirão o processo padrão de gestão de alterações da Microsoft Azure. Os clientes são responsáveis por quaisquer tarefas de administração da carga de trabalho e pelos processos de gestão de alterações associados.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Como é que isto é diferente da Solução VMware Azure da CloudSimple?

Com a nova Azure VMware Solution, a Microsoft e a VMware têm uma parceria direta com fornecedores de nuvem. A nova solução é totalmente projetada, construída e suportada pela Microsoft, e endossada pela VMware. Em termos arquitetónicos, as soluções são consistentes, com a pilha de tecnologia VMware a funcionar numa infraestrutura dedicada Azure.



#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Os VMS de Solução VMware Azure podem ser geridos pela VMRC?
Sim, desde que o sistema em que esteja instalado possa aceder ao vCenter de nuvem privada e esteja a utilizar DNS público para resolver os hostnames ESXi.

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Existem instruções especiais para instalar e utilizar VMRC com VMS de Solução VMware Azure?
Não, utilize as [instruções fornecidas pela VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html) e cumpra os pré-requisitos VM especificados nessas instruções. 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>A VMware HCX é suportada em VPNs?
Não, por causa dos requisitos de largura de banda e latência.

#### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>O Azure Bastion pode ser utilizado para a ligação aos VMs da Solução VMware Azure?
Azure Bastion é o serviço recomendado para ligar à caixa de salto para evitar expor a Solução Azure VMware à internet. Não é possível utilizar o Azure Bastion para ligar aos VMs da Solução VMware Azure, uma vez que não são objetos Azure IaaS.

#### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>O Azure Load Balancer interno pode ser utilizado para VMs de Solução VMware Azure?
Não. Azure Load Balancer interno suporta apenas VMs Azure IaaS. O Azure Load Balancer não suporta piscinas de backend baseadas em IP; apenas VMs Azure ou conjuntos de balança de máquina virtual em que VMs de Solução VMware Azure não são objetos Azure.

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Pode ser utilizado um Gateway ExpressRoute existente para ligar à Solução VMware Azure?
Sim, pode utilizar um Gateway ExpressRoute existente para ligar à Azure VMware Solution desde que não exceda o limite de quatro circuitos ExpressRoute por rede virtual.  No entanto, para aceder à Azure VMware Solution a partir das instalações através do ExpressRoute, deve ter ExpressRoute Global Reach uma vez que o gateway ExpressRoute não fornece encaminhamento transitório entre os seus circuitos conectados.

## <a name="compute-network-storage-and-backup"></a>Computação, rede, armazenamento e backup

#### <a name="is-there-more-than-one-type-of-host-available"></a>Há mais de um tipo de hospedeiro disponível?

Há apenas um tipo de hospedeiro disponível.

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Quais são as especificações do CPU em cada tipo de hospedeiro?

Os servidores têm um núcleo duplo de 18 núcleos 2.3 GHz Intel CPUs.

#### <a name="how-much-memory-is-in-each-host"></a>Quanta memória há em cada hospedeiro?

Os servidores têm 576 GB de RAM.

#### <a name="what-is-the-storage-capacity-of-each-host"></a>Qual é a capacidade de armazenamento de cada hospedeiro?

Cada anfitrião ESXi tem dois grupos de discos vSAN com um nível de capacidade de 15.2 TB e um nível de cache de 3.2-TB NVMe (1.6 TB em cada grupo de discos).

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Qual a largura de banda disponível em cada anfitrião ESXi?

Cada anfitrião ESXi na Azure VMware Solution está configurado com quatro NICs de 25 Gbps, dois NICs previstos para o tráfego do sistema ESXi, e dois NICs previstos para o tráfego de carga de trabalho. 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Os dados armazenados nas datas-tores vSAN são encriptados em repouso?

Sim, todos os dados vSAN são encriptados por padrão usando chaves armazenadas no Cofre de Chaves Azure.

#### <a name="you-document-that-commvault-veritas-and-veeam-have-extended-their-backup-solutions-to-work-with-azure-vmware-solution-what-about-other-independent-software-vendor-isv-backup-solutions"></a>Documenta que a Commvault, a Veritas e a Veeam alargaram as suas soluções de backup para trabalharem com a Azure VMware Solution. E outras soluções de backup de fornecedores de software independentes (ISV) ?

Tanto quanto sabemos, qualquer solução de backup que utilize o VMware VADP com o modo de transporte HotAdd deve funcionar fora da caixa na Azure VMware Solution.

#### <a name="what-about-support-for-isv-backup-solutions"></a>E o suporte para soluções de backup ISV?

Uma vez que estas soluções de backup são instaladas e geridas pelos clientes, podem contactar o respetivo ISV para apoio. 

#### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Qual é a política de armazenamento correta para a configuração do dedupe?

Utilize a política de armazenamento *thin_provision* para o seu modelo VM.  O padrão é *thick_provision*.

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>Os registos de infraestruturas do SNMP são partilhados?

Não.

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

#### <a name="is-the-underlying-infrastructure-shared"></a>A infraestrutura subjacente é partilhada?

Não, os anfitriões e aglomerados de nuvens privadas são dedicados e apagados de forma segura antes e depois da utilização.

#### <a name="what-are-the-minimum-and-the-maximum-number-of-hosts-per-cluster"></a>Quais são os números mínimos e máximo de hospedeiros por agrupamento?

Os clusters podem escalar entre 3 e 16 anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>Posso escalar os meus aglomerados de nuvens privadas?

Sim, os aglomerados escalam entre o número mínimo e o número máximo de anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

#### <a name="what-are-trial-clusters"></a>O que são aglomerados de ensaios?

Os clusters de ensaio são três aglomerados de hospedeiros usados para avaliações de um mês de nuvens privadas Azure VMware Solution.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Posso usar hospedeiros de alta qualidade para agrupamentos de ensaios?

Não. Os anfitriões ESXi de gama alta estão reservados para uso em clusters de produção.

## <a name="azure-vmware-solution-and-vmware-software"></a>Solução Azure VMware e software VMware

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Que versões do software VMware são usadas em nuvens privadas?

Nuvens privadas usam vSphere 6.7, vSAN 6.7, VMware HCX e versão 2.5 de NSX-T.  

#### <a name="do-private-clouds-use-vmware-nsx"></a>Nuvens privadas usam VMware NSX?

Sim, o NSX-T 2.5 é utilizado para a rede definida pelo software em nuvens privadas Azure VMware Solution.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Posso usar VMware NSX-V numa nuvem privada?

Não. NSX-T é a única versão suportada do NSX.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>O NSX é necessário em ambientes ou redes no local que se ligam a uma nuvem privada?

Não, não é obrigado a usar NSX no local.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Qual é o calendário de atualização e atualização do software VMware numa nuvem privada?

As atualizações do pacote de software de nuvem privada são feitas para manter o software dentro de uma versão da versão mais recente do pacote de software lançado a partir de VMware. As versões privadas de software em nuvem podem ser diferentes das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN).

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Com que frequência a pilha de software de nuvem privada será atualizada?

O software de nuvem privada é atualizado numa programação que acompanha a versão do pacote de software a partir de VMware. A sua nuvem privada não requer tempo de inatividade para atualizações.

## <a name="connectivity"></a>Conectividade

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Que planeamento de endereços IP de rede é necessário para incorporar nuvens privadas com ambientes no local?

É necessário um espaço de endereço de rede privada /22 para implantar uma nuvem privada Azure VMware Solution. Este espaço de endereço privado não deve sobrepor-se a outras redes virtuais numa subscrição ou com redes no local.
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Como posso ligar-me dos ambientes no local a uma nuvem privada Azure VMware Solution?

Pode ligar-se ao serviço num de dois métodos: 

- Com um gateway VM ou aplicação implantado numa rede virtual Azure que é espreitada através do ExpressRoute para a nuvem privada.
- Através do ExpressRoute Global Reach do seu centro de dados no local para um circuito Azure ExpressRoute.

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Como posso ligar um VM de carga de trabalho à internet ou um ponto final de serviço Azure?

No portal Azure, permita a conectividade da internet para uma nuvem privada. Com o gestor NSX-T, crie um router NSX-T T1 e um interruptor lógico. Em seguida, utilize o vCenter para implantar um VM no segmento de rede definido pelo interruptor lógico. Que a VM terá acesso à rede à internet e aos serviços Azure.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Preciso de restringir o acesso da internet a VMs em redes lógicas numa nuvem privada?

Não. O tráfego de rede a entrar da internet diretamente para nuvens privadas não é permitido.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Preciso de restringir o acesso à Internet de VMs em redes lógicas para a internet?

Sim. Terá de usar o gestor NSX-T para criar uma firewall que restringe o acesso de VM à internet.


#### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>A Azure VMware Solution pode usar Azure Virtual WAN hospedado ExpressRoute Gateways?
Sim.

#### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Pode a conectividade de trânsito ser estabelecida entre as instalações e a Solução Azure VMware através do Azure Virtual WAN sobre o ExpressRoute Global Reach?
AZure Virtual WAN não fornece encaminhamento transitório entre dois circuitos ExpressRoute conectados e o GATEWAY WAN ExpressRoute não virtual. A utilização do ExpressRoute Global Reach permite a conectividade entre as instalações e a Azure VMware Solution, mas passa pela rede global da Microsoft em vez do Virtual WAN Hub.


## <a name="accounts-and-privileges"></a>Contas e privilégios

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Que contas e privilégios vou ter com a minha nova nuvem privada Azure VMware Solution?

É-lhe fornecidas credenciais para um utilizador de cloudadmin no vCenter e acesso administrativo no NSX-T Manager. Há também um grupo CloudAdmin que pode ser usado para incorporar o Azure Ative Directory. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>Pode ter acesso de administrador aos anfitriões ESXi?

Não, o acesso do administrador à ESXi está restrito ao cumprimento dos requisitos de segurança da solução.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Que privilégios e permissões terei no vCenter?

Terá privilégios de grupo CloudAdmin. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Que privilégios e permissões terei sobre o gerente da NSX-T?

Você terá privilégios completos de administrador em NSX-T e poderá gerir o controlo de acesso baseado em funções como faria com o NSX-T Data Center no local. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

> [!NOTE]
> Um router T0 é criado e configurado como parte de uma implementação de nuvem privada. Qualquer modificação nesse router lógico ou nos VMs de nó de borda NSX-T pode afetar a conectividade à sua nuvem privada.

## <a name="billing-and-support"></a>Faturação e Apoio

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Como será estruturado o preço para a Azure VMware Solution?

Para questões gerais sobre preços, consulte a página [de preços](https://azure.microsoft.com/pricing/details/azure-vmware) da Solução VMware Azure. 

#### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>A Azure VMware Solution pode ser adquirida através de um Microsoft CSP?

Sim, os clientes podem implementar a Azure VMware Solution dentro de uma subscrição Azure gerida por um CSP.

#### <a name="who-supports-azure-vmware-solution"></a>Quem suporta a Azure VMware Solution?

A Microsoft fornece suporte para Azure VMware Solution. Pode submeter um pedido de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 

Para as subscrições geridas pela CSP, o primeiro nível de suporte será fornecido pelo Provedor de Solução da mesma forma que a CSP para outros serviços da Azure.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Que contas preciso para criar uma nuvem privada Azure VMware Solution?

Você precisará de uma conta Azure numa subscrição do Azure.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>As soluções Red Hat são suportadas na Azure VMware Solution?

A Microsoft e a Red Hat partilham uma equipa de suporte integrada e de apoio que fornece um ponto de contacto unificado para os ecossistemas da Red Hat que estão a funcionar na plataforma Azure.  À semelhança de outros serviços da plataforma Azure que trabalham com a Red Hat Enterprise Linux, a Azure VMware Solution está sob o Cloud Access e o guarda-chuva de suporte integrado, e a Red Hat Enterprise Linux é suportada para correr em cima da Azure VMware Solution dentro do Azure.

#### <a name="is-vmware-hcx-enterprise-edition-available-and-if-so-how-much-does-it-cost"></a>A VMware HCX Enterprise Edition está disponível e, em caso afirmativo, quanto custa?

VMware HCX Enterprise Edition (EE) está disponível com a Azure VMware Solution como uma função/serviço *de pré-visualização.* Enquanto vMware HCX EE para Azure VMware Solution está em Pré-visualização, é uma função/serviço gratuito e sujeito a termos e condições de serviço de pré-visualização. Assim que o serviço VMware HCX EE for classificado, receberá um aviso de 30 dias de que a faturação irá mudar. Pode desligar ou optar por sair do serviço.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Como solicito um aumento da quota de anfitrião para a Azure VMware Solution?

Para as subscrições geridas pela CSP, o cliente deve realizar o pedido ao parceiro e a equipa parceira irá envolver-se com a Microsoft para que o contingente seja aumentado para a subscrição. Para obter mais informações, consulte Como ativar o [artigo de recurso Azure VMware Solution](enable-azure-vmware-solution.md) para obter os detalhes. 

Para as assinaturas EA, o cliente deve seguir este procedimento.

* Vai precisar de um [Acordo de Empresa Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
* Você precisará de uma conta Azure numa subscrição do Azure.

Antes de criar o seu recurso Azure VMware Solution, tem de enviar um bilhete de apoio para alocar os seus nós. Leva até cinco dias úteis para confirmar o seu pedido e alocar os seus nós. Se tiver uma nuvem privada Azure VMware Solution e quiser mais nós atribuídos, passará pelo mesmo processo.


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

   For additional ways to register the resource provider, see [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md). 

#### Are Reserved Instances available for purchasing through the Cloud Solution Provider (CSP) program?

Yes. CSP can purchase reserved instances on behalf of their customers, see [Save costs with a reserved instance](reserved-instance.md) article. 

#### Does Azure VMware Solution offer multi-tenancy for hosting CSP partners?

No. Currently Azure VMware Solution does not offer multi-tenancy.


## Customer communication

#### How can I receive an alert when Azure sends service health notifications to my Azure subscription?

Service issues, planned maintenance, health advisories, security advisories notifications are published through **Service Health** in the Azure portal.  You can take timely actions when you set up activity log alerts for these notifications. For more information, see [Create service health alerts using the Azure portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Screenshot of Service Health notifications":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
