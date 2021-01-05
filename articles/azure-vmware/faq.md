---
title: Perguntas mais frequentes
description: Fornece respostas a algumas das perguntas comuns sobre a Azure VMware Solution.
ms.topic: conceptual
ms.date: 1/4/2020
ms.openlocfilehash: d0228739a6ee4eac20d2fa04e47cf86b6fddb84e
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881230"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Perguntas frequentes sobre Azure VMware Solution

Neste artigo, responderemos a perguntas frequentes sobre a Azure VMware Solution.

## <a name="general"></a>Geral

#### <a name="what-is-azure-vmware-solution"></a>O que é o Azure VMware Solution?

À medida que as empresas prosseguem estratégias de modernização de TI para melhorar a agilidade do negócio, reduzir custos e acelerar a inovação, as plataformas híbridas cloud surgiram como principais facilitadores da transformação digital dos clientes. A Azure VMware Solution combina o software Software-Defined Data Center (SDDC) da VMware com o ecossistema global de serviços de nuvem Azure da Microsoft. A Azure VMware Solution é gerida para atender aos requisitos de desempenho, disponibilidade, segurança e conformidade.

## <a name="azure-vmware-solution-service"></a>Serviço de Solução Azure VMware

#### <a name="where-is-azure-vmware-solution-available-today"></a>Onde está a Solução VMware Azure disponível hoje?

O serviço está continuamente a ser adicionado a novas regiões, por isso veja as [últimas informações de disponibilidade de serviços](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) para mais detalhes. 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>As cargas de trabalho em execução numa Solução VMware Azure podem consumir ou integrar-se com os serviços Azure?

Todos os serviços da Azure estarão disponíveis para os clientes da Azure VMware Solution. As limitações de desempenho e disponibilidade de serviços específicos terão de ser abordadas caso a caso.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Uso as mesmas ferramentas que uso agora para gerir recursos privados em nuvem?

Yes. O portal Azure é utilizado para implantação e várias operações de gestão. vCenter e NSX Manager são usados para gerir recursos vSphere e NSX-T.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Posso gerir uma nuvem privada com o meu vCenter no local?

No lançamento, a Azure VMware Solution não suportará uma única experiência de gestão em ambientes de nuvem e local. Os aglomerados de nuvem privada serão geridos com vCenter e NSX Manager local para uma nuvem privada.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Posso usar a vRealize Suite a funcionar no local? 

Integrações específicas e casos de utilização podem ser avaliados caso a caso.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Posso migrar vSphere VMs de ambientes no local para nuvens privadas Azure VMware Solution?

Yes. A migração vM e vMotion podem ser usadas para mover VMs para uma nuvem privada se os requisitos padrão de vCenter [vCenter vMotion forem cumpridos.](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952)

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>É necessária uma versão específica da vSphere em ambientes no local?

Todos os ambientes em nuvem vêm com VMware HCX, vSphere 5.5, ou mais tarde em ambientes no local para vMotion.

#### <a name="what-does-the-change-control-process-look-like"></a>Como é o processo de controlo de mudança?

As atualizações feitas ao próprio serviço seguem o processo padrão de gestão de alterações da Microsoft Azure. Os clientes são responsáveis por quaisquer tarefas de administração da carga de trabalho e pelos processos de gestão de alterações associados.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Como é que isto é diferente da Solução VMware Azure da CloudSimple?

Com a nova Azure VMware Solution, a Microsoft e a VMware têm uma parceria direta com fornecedores de nuvem. A nova solução é totalmente projetada, construída e suportada pela Microsoft, e endossada pela VMware. Em termos arquitetónicos, as soluções são consistentes, com a pilha de tecnologia VMware a funcionar numa infraestrutura dedicada Azure.

#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Os VMS de Solução VMware Azure podem ser geridos pela VMRC?
Yes. Desde que o sistema instalado possa aceder ao vCenter de nuvem privada e esteja a utilizar DNS público para resolver os hostnames ESXi.

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Existem instruções especiais para instalar e utilizar VMRC com VMS de Solução VMware Azure?
N.º Para cumprir os pré-requisitos VM siga as [instruções fornecidas pela VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html). 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>A VMware HCX é suportada em VPNs?
Não, por causa dos requisitos de largura de banda e latência.

#### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>O Azure Bastion pode ser utilizado para a ligação aos VMs da Solução VMware Azure?
Azure Bastion é o serviço recomendado para ligar à caixa de salto para evitar expor a Solução Azure VMware à internet. Não é possível utilizar o Azure Bastion para ligar aos VMs da Solução VMware Azure, uma vez que não são objetos Azure IaaS.

#### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>O Azure Load Balancer interno pode ser utilizado para VMs de Solução VMware Azure?
N.º Azure Load Balancer suporta apenas VMs Azure IaaS. O Azure Load Balancer não suporta piscinas de backend baseadas em IP; apenas VMs Azure ou conjuntos de balança de máquina virtual em que VMs de Solução VMware Azure não são objetos Azure.

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Pode ser utilizado um Gateway ExpressRoute existente para ligar à Solução VMware Azure?
Yes. Utilize um Gateway ExpressRoute existente para ligar à Solução VMware Azure desde que não exceda o limite de quatro circuitos ExpressRoute por rede virtual. Para aceder à Azure VMware Solution a partir das instalações através do ExpressRoute, tem de ter o ExpressRoute Global Reach uma vez que o Portal ExpressRoute não fornece um encaminhamento transitório entre os seus circuitos conectados.

## <a name="compute-network-storage-and-backup"></a>Computação, rede, armazenamento e backup

#### <a name="is-there-more-than-one-type-of-host-available"></a>Há mais de um tipo de hospedeiro disponível?

Só há um tipo de hospedeiro disponível.

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

####  <a name="what-independent-software-vendors-isvs-backup-solutions-work-with-azure-vmware-solution"></a>Que soluções de backup de fornecedores de software independentes (ISVs) funcionam com a Azure VMware Solution?

Commvault, Veritas e Veeam alargaram as suas soluções de backup para trabalhar com a Azure VMware Solution.  No entanto, qualquer solução de backup que utilize vMware VADP com o modo de transporte HotAdd funcionaria logo a partir da caixa na Azure VMware Solution.

#### <a name="what-about-support-for-isv-backup-solutions"></a>E o suporte para soluções de backup ISV?

Uma vez que estas soluções de backup são instaladas e geridas pelos clientes, podem contactar o respetivo ISV para apoio. 

#### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Qual é a política de armazenamento correta para a configuração do dedupe?

Utilize a política de armazenamento *thin_provision* para o seu modelo VM.  O padrão é *thick_provision*.

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>Os registos de infraestruturas do SNMP são partilhados?

N.º

## <a name="hosts-clusters-and-private-clouds"></a>Anfitriões, aglomerados e nuvens privadas

#### <a name="is-the-underlying-infrastructure-shared"></a>A infraestrutura subjacente é partilhada?

Não, os anfitriões e aglomerados de nuvens privadas são dedicados e apagados de forma segura antes e depois da utilização.

#### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Quais são o número mínimo e máximo de hospedeiros por agrupamento?

Os clusters podem escalar entre 3 e 16 anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>Posso escalar os meus aglomerados de nuvens privadas?

Sim, os aglomerados escalam entre o número mínimo e o número máximo de anfitriões ESXi. Os agrupamentos de ensaios estão limitados a três anfitriões.

#### <a name="what-are-trial-clusters"></a>O que são aglomerados de ensaios?

Os clusters de ensaio são três aglomerados de hospedeiros usados para avaliações de um mês de nuvens privadas Azure VMware Solution.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Posso usar hospedeiros de alta qualidade para agrupamentos de ensaios?

N.º Os anfitriões ESXi de gama alta estão reservados para uso em clusters de produção.

## <a name="azure-vmware-solution-and-vmware-software"></a>Solução Azure VMware e software VMware

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Que versões do software VMware são usadas em nuvens privadas?

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


#### <a name="do-private-clouds-use-vmware-nsx"></a>Nuvens privadas usam VMware NSX?

Sim, o NSX-T 2.5 é utilizado para a rede definida pelo software em nuvens privadas Azure VMware Solution.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Posso usar VMware NSX-V numa nuvem privada?

N.º NSX-T é a única versão suportada do NSX.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>O NSX é necessário em ambientes ou redes no local que se ligam a uma nuvem privada?

Não, não é obrigado a usar NSX no local.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Qual é o calendário de atualização e atualização do software VMware numa nuvem privada?

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software lançado a partir de VMware. As versões privadas de software em nuvem podem diferir das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN).

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

No portal Azure, permita a conectividade da internet para uma nuvem privada. Com o gestor NSX-T, crie um router NSX-T T1 e um interruptor lógico. Em seguida, utilize o vCenter para implantar um VM no segmento de rede definido pelo interruptor lógico. A VM terá acesso à rede à internet e aos serviços Azure.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Preciso de restringir o acesso da internet a VMs em redes lógicas numa nuvem privada?

N.º O tráfego de rede que está a chegar da internet diretamente para as nuvens privadas não é permitido por defeito.  No entanto, é possível expor VMs de Solução VMware Azure à internet através da opção [IP pública](public-ip-usage.md) no seu portal Azure para a sua nuvem privada Azure VMware Solution.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Preciso de restringir o acesso à Internet de VMs em redes lógicas para a internet?

Yes. Terá de usar o gestor NSX-T para criar uma firewall para restringir o acesso de VM à internet.


#### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>A Azure VMware Solution pode usar Azure Virtual WAN hospedado ExpressRoute Gateways?
Yes.

#### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Pode a conectividade de trânsito ser estabelecida entre as instalações e a Solução Azure VMware através do Azure Virtual WAN sobre o ExpressRoute Global Reach?
AZure Virtual WAN não fornece encaminhamento transitório entre dois circuitos ExpressRoute conectados e o GATEWAY WAN ExpressRoute não virtual. A utilização do ExpressRoute Global Reach permite a conectividade entre as instalações e a Azure VMware Solution, mas passa pela rede global da Microsoft em vez do Virtual WAN Hub.

#### <a name="could-i-use-hcx-through-public-internet-communications-as-a-workaround-for-the-non-supportability-of-hcx-when-using-vpn-s2s-with-vwan-for-on-premises-communications"></a>Poderia utilizar o HCX através das comunicações públicas na Internet como uma solução alternativa para a não-suporte do HCX ao utilizar o VPN S2S com vWAN para comunicações no local?

Atualmente, o único método suportado para VMware HCX é através do ExpressRoute.

## <a name="accounts-and-privileges"></a>Contas e privilégios

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Que contas e privilégios vou ter com a minha nova nuvem privada Azure VMware Solution?

É-lhe fornecidas credenciais para um utilizador de cloudadmin no vCenter e acesso administrativo no NSX-T Manager. Há também um grupo CloudAdmin que pode ser usado para incorporar o Azure Ative Directory. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>Pode ter acesso de administrador aos anfitriões ESXi?

Não, o acesso do administrador à ESXi está restrito ao cumprimento dos requisitos de segurança da solução.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Que privilégios e permissões terei no vCenter?

Terá privilégios de grupo CloudAdmin. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Que privilégios e permissões terei sobre o gerente da NSX-T?

Você terá privilégios completos de administrador em NSX-T e pode gerir o controlo de acesso baseado em funções vSphere como você faria com o NSX-T Data Center no local. Para mais informações, consulte [Os Conceitos de Acesso e Identidade.](concepts-identity.md)

> [!NOTE]
> Um router T0 é criado e configurado como parte de uma implementação de nuvem privada. Qualquer modificação nesse router lógico ou nos VMs de nó de borda NSX-T pode afetar a conectividade à sua nuvem privada.

## <a name="billing-and-support"></a>Faturação e Apoio

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>Como será estruturado o preço para a Azure VMware Solution?

Para questões gerais sobre preços, consulte a página [de preços](https://azure.microsoft.com/pricing/details/azure-vmware) da Solução VMware Azure. 

#### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>A Azure VMware Solution pode ser adquirida através de um Microsoft CSP?

Sim, os clientes podem implementar a Azure VMware Solution dentro de uma subscrição Azure gerida por um CSP.

#### <a name="who-supports-azure-vmware-solution"></a>Quem suporta a Azure VMware Solution?

A Microsoft fornece suporte para Azure VMware Solution. Pode submeter um pedido de [apoio.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 

Para as subscrições geridas pela CSP, o primeiro nível de suporte fornece ao Provedor de Solução da mesma forma que a CSP para outros serviços Azure.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Que contas preciso para criar uma nuvem privada Azure VMware Solution?

Você precisará de uma conta Azure numa subscrição do Azure.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>As soluções Red Hat são suportadas na Azure VMware Solution?

A Microsoft e a Red Hat partilham uma equipa de suporte integrada e de apoio que fornece um ponto de contacto unificado para os ecossistemas da Red Hat que estão a funcionar na plataforma Azure.  À semelhança de outros serviços da plataforma Azure que trabalham com a Red Hat Enterprise Linux, a Azure VMware Solution está sob o Cloud Access e guarda-chuva de suporte integrado. A Red Hat Enterprise Linux é suportada para correr em cima da Azure VMware Solution dentro do Azure.

#### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>A VMware HCX Enterprise está disponível e, em caso afirmativo, quanto custa?

A VMware HCX Enterprise está disponível com a Azure VMware Solution como função/serviço *de pré-visualização.* Enquanto a VMware HCX Enterprise for Azure VMware Solution está em Pré-visualização, é uma função/serviço gratuito e sujeita a termos e condições de serviço de pré-visualização. Assim que o serviço VMware HCX Enterprise for a GA, receberá um aviso de 30 dias de que a faturação irá mudar. Pode desligá-lo ou excluir o serviço.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Como solicito um aumento da quota de anfitrião para a Azure VMware Solution?

Para as subscrições geridas pela CSP, o cliente deve submeter o pedido ao parceiro. A equipa parceira envolve-se então com a Microsoft para que a quota aumente para a subscrição. Veja [como ativar o artigo de recurso Azure VMware Solution](enable-azure-vmware-solution.md) para os detalhes. 

Para as assinaturas EA, utilize o seguinte procedimento. Primeiro, vai precisar de:

* Um [Acordo de Empresa Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) com a Microsoft.
* Uma conta Azure numa subscrição do Azure.

Antes de poder criar o seu recurso Azure VMware Solution, submeterá um bilhete de apoio para que os seus anfitriões tenham sido atribuídos. Leva até cinco dias úteis para confirmar e cumprir o seu pedido. Se tiver uma nuvem privada Azure VMware Solution e quiser mais anfitriões, passará pelo mesmo processo.

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
   - Número de anfitriões
   - Quaisquer outros detalhes

   >[!NOTE]
   >A Azure VMware Solution recomenda um mínimo de três anfitriões para girar a sua nuvem privada e para anfitriões N+1 de redundância. 

1. Selecione **Review + Criar** para submeter o pedido.

   Levará até cinco dias úteis para um representante de apoio confirmar o seu pedido.

   >[!IMPORTANT] 
   >Se já tem uma Solução Azure VMware existente e solicite anfitriões adicionais, por favor, note que precisamos de cinco dias úteis para alocar os anfitriões. 

1. Antes de poder aprovisionar os seus anfitriões, certifique-se de que regista o fornecedor de recursos **Microsoft.AVS** no portal Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Para obter mais formas de registar o fornecedor de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md). 

#### <a name="are-reserved-instances-available-for-purchasing-through-the-cloud-solution-provider-csp-program"></a>Estão disponíveis instâncias reservadas para compra através do programa Cloud Solution Provider (CSP) ?

Yes. A CSP pode adquirir instâncias reservadas para os seus clientes. Para obter mais informações, consulte [Economize os custos com um caso reservado.](reserved-instance.md) 

#### <a name="does-azure-vmware-solution-offer-multi-tenancy-for-hosting-csp-partners"></a>A Azure VMware Solution oferece vários arrendamentos para hospedar parceiros CSP?

N.º Atualmente, a Azure VMware Solution não oferece vários arrendamentos.

#### <a name="will-traffic-between-on-premises-and-azure-vmware-solution-over-expressroute-incur-any-outbound-data-transfer-charge-in-the-metered-data-plan"></a>O tráfego entre as instalações e a Azure VMware Solution sobre o ExpressRoute incorrerá em qualquer taxa de transferência de dados de saída no plano de dados medido?

O tráfego no circuito Azure VMware Solution ExpressRoute não é medido de forma alguma. O tráfego do seu circuito ExpressRoute que liga ao seu local de paragem para Azure é cobrado de acordo com os planos de preços da ExpressRoute.


## <a name="customer-communication"></a>Comunicação ao cliente

#### <a name="how-can-i-receive-an-alert-when-azure-sends-service-health-notifications-to-my-azure-subscription"></a>Como posso receber um alerta quando o Azure envia notificações de saúde de serviço à minha assinatura Azure?

Problemas de serviço, manutenção planeada, avisos de saúde, notificações de avisos de segurança são publicados através do **Service Health** no portal Azure.  Pode tomar ações oportunas quando configurar alertas de registo de atividade para estas notificações. Para obter mais informações, consulte [Criar alertas de saúde de serviço utilizando o portal Azure.](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal)

:::image type="content" source="media/service-health.png" alt-text="Screenshot das notificações de saúde de serviço":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
