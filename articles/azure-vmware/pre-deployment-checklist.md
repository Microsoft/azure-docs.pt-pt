---
title: Lista de verificação pré-implantação da Solução VMware Azure
description: Utilize esta lista de verificação como parte do processo de planeamento de pré-implantação.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580024"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Lista de verificação pré-implantação da Solução VMware Azure
Utilizará esta lista de verificação de pré-implantação durante a [fase de planeamento.](production-ready-deployment-steps.md)

## <a name="success-criteria"></a>Critérios de sucesso
Defina os testes que vai fazer e o resultado esperado.

| Informação fundamental necessária | A sua resposta |
| ----------- | ------------- |
| Implementar SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar a rede virtual | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar caixa de salto | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar porta de rede virtual | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Connect ExpressRoute Global Reach | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Inicie sessão no NSX Manager e vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Informação primária necessária | A sua resposta |
| --------| --------------|
| Criar servidor DHCP | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar segmentos de rede | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Escala (adicionar ou eliminar nós) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Implementar VMware HCX | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar máquinas virtuais (VMs) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Ativar a internet | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Migração de VM de instalações para nuvem privada | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Informações adicionais necessárias | A sua resposta |
| --------| --------------|
| Operações de instantâneo VM | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Implementar o equilibrador de carga NSX-T | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Integração do Azure<br><ul><li>Biblioteca de conteúdos compartilhados</li><li>Integração de segurança</li><li>Upload ISO</li><li>Construir a partir da ISO</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Micro segmentação | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Encaminhamento | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Informações sobre a solução Azure VMware

#### <a name="azure-subscription"></a>Subscrição do Azure
Forneça o nome de subscrição e o ID de subscrição para Azure VMware Solution. A subscrição pode ser nova ou existente. Não utilize uma assinatura Dev/Teste.

| Informação necessária  | A sua resposta |
| ------------| ------------- |
| Assinatura e ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Assinatura com um EA | ☐ Sim &nbsp; &nbsp; ☐ Não  |
| Nome do Grupo de Recursos | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Localização | ☒ Leste dos EUA |
| Administrador Azure<br><br>Fornecer o nome e o contacto da administração<br>atribuído para ativar o serviço a partir do mercado.<br>Contribuinte é a função mínima necessária para <br>registar o fornecedor de [recursos Azure VMware Solution](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Informações sobre o VMware no local

| Informação necessária  | A sua resposta |
| ------------| --------------|
| versão vSphere | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| versão vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| administrador vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Extensão L2<br><br>Se alargar as redes L2 com VMware HCX,<br>fornecer a rede no local que irá estender. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>Forneça o tipo de vSwitch utilizado em todo o ambiente. | ☐ Standard &nbsp; &nbsp; ☐ Distribuído<br><br>Se utilizar o Standard, então o VMware HCX não está disponível. |
| DNS e DHCP<br><br>É necessária uma infraestrutura adequada de DNS e DHCP. <br>Recomenda-se a utilização do serviço DHCP incorporado <br>NSX ou use um servidor DHCP local na nuvem privada <br>em vez de encaminhamento difundido tráfego DHCP sobre o <br>WAN de volta ao local. Para mais informações, <br>ver [como criar e gerir o DHCP na Solução VMWare Azure](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Networking - Infraestrutura de Solução Azure VMware 
Os dados necessários são para ajudá-lo a satisfazer as necessidades de networking da Azure VMware Solution para testes de stand-up e rede iniciais. 

| Informação necessária | A sua resposta |
| ----------- | ------------- |
|Solução Azure VMware CIDR<br><br>Necessário para os anfitriões vSphere, vSAN e gestão <br>redes em Azure VMware Solution. Para mais <br>informação, ver [considerações de encaminhamento e sub-rede](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Carga de carga de solução Azure VMware CIDR (opcional)<br><br>Atribuir uma rede a ser utilizada no Azure VMware<br>Solução para testes iniciais. Máquinas virtuais<br>serão implementados para validar a conectividade da rede <br>de/para Azure VMware Solution. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Networking - ligue a Solução Azure VMware à Rede Virtual Azure
Os dados necessários para depois de o cluster Azure VMware Solution ser levantado podem ser ligados ao Azure através de um circuito ExpressRoute, que faz parte do serviço Azure VMware Solution.

| Informação necessária | A sua resposta |
| ------------------ | ------------- |
| Caixa de salto - Servidor windows | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar a rede virtual | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar sub-rede de gateway | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar porta de rede virtual<br><br>Para obter mais informações, consulte [Criar o gateway de rede virtual.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Networking - ligue a Solução Azure VMware a um datacenter no local

| Informação necessária | A sua resposta |
| ------------------ | ------------- |
| ExpressRoute espreitando CIDR<br><br>O `/29` bloco de endereços CIDR. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Chave de autorização ExpressRoute e ID de recursos<br><br>Fornecer uma chave de autorização e iD de recursos, <br>que é gerado a partir do ExpressRoute atual <br>circuito que se conecta ao datacenter no local.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Direção da rota padrão<br><br>Se as máquinas virtuais em Azure VMware Solution <br>aceder à internet via Azure VMware Solution fornecido <br>internet, ou voltar através do circuito ExpressRoute para <br>no local para a rota predefinido? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Portas de rede necessárias para comunicar com o serviço<br><br>Para obter mais informações, consulte [as portas de rede necessárias.](tutorial-network-checklist.md#required-network-ports) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Networking - VMware HCX

| Informação necessária | A sua resposta |
| ------------------ | ------------- |
| Portas de rede<br><br>Se houver uma firewall, certifique-se de que as portas de rede necessárias <br>são abertos entre as instalações e a Azure VMware Solution. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Para obter informações sobre a configuração do DNS, <br>ver [Rede - Infraestrutura de Solução VMware Azure](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX CIDRs<br><br>Você precisa de dois `/29` blocos CIDR, que são usados para <br>os componentes de infraestrutura VMware HCX no local.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

