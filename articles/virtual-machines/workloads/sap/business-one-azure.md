---
title: SAP Business One em máquinas virtuais do Azure | Microsoft Docs
description: SAP Business One no Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18409f93ab50f7d031ec78a55b9eaf8ad1b85a49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101419"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One em Máquinas Virtuais do Azure
Este documento fornece orientação para implantar o SAP Business One em máquinas virtuais do Azure. A documentação não é uma substituição para a documentação de instalação do Business One para SAP. A documentação deve abranger as diretrizes básicas de planejamento e implantação da infraestrutura do Azure para executar um aplicativo de negócios.

A Business One dá suporte a dois bancos de dados diferentes:
- SQL Server-consulte [SAP Note #928839 – planejamento de lançamentos para Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA-para a matriz de suporte do SAP Business One exata para SAP HANA, verifique a [matriz de disponibilidade do produto SAP](https://support.sap.com/pam)

Em relação à SQL Server, as considerações básicas de implantação, conforme documentado na [implantação de DBMS das máquinas virtuais do Azure para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) , se aplicam. por SAP HANA, as considerações são mencionadas neste documento.

## <a name="prerequisites"></a>Pré-requisitos
Para usar este guia, você precisa de conhecimento básico dos seguintes componentes do Azure:

- [Máquinas virtuais do Azure no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Máquinas virtuais do Azure no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Gerenciamento de redes virtuais e rede do Azure com o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Rede do Azure e redes virtuais com a CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Gerenciar discos do Azure com o CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Mesmo que você esteja interessado apenas na empresa, o [planejamento e a implementação de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) podem ser uma boa fonte de informações.

A suposição é que você como a instância que implanta o SAP Business One é:

- Familiarize-se com a instalação do SAP HANA em uma determinada infraestrutura, como uma VM
- Familiarização da instalação do aplicativo SAP Business One em uma infraestrutura como VMs do Azure
- Familiarize-se com o SAP Business One e o sistema DBMS escolhidos
- Familiarizado com a implantação de infraestrutura no Azure

Todas essas áreas não serão abordadas neste documento.

Além da documentação do Azure, você deve estar ciente das principais notas SAP, que se referem à empresa uma ou que são observações centrais do SAP for Business:

- [528296-visão geral geral nota para versões do SAP Business One e produtos relacionados](https://launchpad.support.sap.com/#/notes/528296)
- [2216195-nota de atualizações de versão para o SAP Business One 9,2, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583-nota central para o SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615-nota de atualizações de versão para SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595-nota coletiva para problemas gerais do SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458-observação de Consultoria coletiva para tópicos relacionados a SAP HANA do SAP Business One, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Arquitetura de negócios um
Business One é um aplicativo que tem duas camadas:

- Uma camada de cliente com um cliente ' Fat '
- Uma camada de banco de dados que contém o esquema de banco de dados para um locatário

Uma visão geral melhor de quais componentes estão sendo executados na parte do cliente e quais partes estão sendo executadas na parte do servidor está documentada no [Guia do administrador do SAP Business One](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Como há uma interação crítica de latência intensa entre a camada do cliente e a camada do DBMS, ambas as camadas precisam estar localizadas no Azure durante a implantação no Azure. é comum que os usuários, em seguida, o RDS em uma ou várias VMs que executam um serviço RDS para os componentes de cliente Business One.

### <a name="sizing-vms-for-sap-business-one"></a>Dimensionando VMs para o SAP Business One

Em relação ao dimensionamento das VMs do cliente, os requisitos de recursos são documentados pela SAP no documento [Guia de requisitos de hardware do SAP Business One](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, você precisa se concentrar e calcular com os requisitos indicados no capítulo 2,4 do documento.

Como máquinas virtuais do Azure para hospedar os componentes de negócios de um cliente e o host do DBMS, somente as VMs com suporte do SAP NetWeaver são permitidas. Para encontrar a lista de VMs do Azure com suporte do SAP NetWeaver, leia [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Executando SAP HANA como um back-end do DBMS para negócios One, somente as VMs, que são listadas para negócios no HANA na [lista de plataformas IaaS certifeid do Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) , têm suporte para Hana. Os componentes de cliente corporativo um não são afetados por essa restrição mais forte para o SAP HANA como sistema DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Versões do sistema operacional a serem usadas para o SAP Business One

Em princípio, é sempre melhor usar as versões mais recentes do sistema operacional. Especialmente no espaço do Linux, a nova funcionalidade do Azure foi introduzida com diferentes versões secundárias mais recentes do SUSE e do Red Hat. No lado do Windows, é altamente recomendável usar o Windows Server 2016.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Implantando a infraestrutura no Azure para SAP Business One
Nos próximos capítulos, as partes de infraestrutura importantes para a implantação do SAP.

### <a name="azure-network-infrastructure"></a>Infraestrutura de rede do Azure
A infraestrutura de rede que você precisa implantar no Azure depende se você implanta um único sistema de negócios para si mesmo. Ou se você é um hoster que hospeda dezenas de sistemas comerciais um para os clientes. Também pode haver pequenas alterações no design se você se conectar ao Azure. Passando por diferentes possibilidades, um design onde você tem uma conectividade VPN no Azure e onde você estende seu Active Directory por meio de [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para o Azure.

![Configuração de rede simples com Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

A configuração simplificada apresentada apresenta várias instâncias de segurança que permitem controlar e limitar o roteamento. Ele começa com 

- O roteador/firewall no lado do cliente local.
- A próxima instância é o [grupo de segurança de rede do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) que você pode usar para introduzir as regras de roteamento e segurança para a VNet do Azure na qual você executa sua configuração de negócios do SAP.
- Para evitar que os usuários do Business One Client também possam ver o servidor que executa o servidor de negócios um, que executa o banco de dados, você deve separar a VM que hospeda o cliente Business One e o servidor Business One em duas sub-redes diferentes na VNet.
- Você usaria o Azure NSG atribuído às duas sub-redes diferentes novamente para limitar o acesso ao servidor de negócios um.

Uma versão mais sofisticada de uma configuração de rede do Azure baseia-se nas práticas recomendadas documentadas do Azure [da arquitetura Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). O padrão de arquitetura do Hub e do spoke alteraria a primeira configuração simplificada para uma da seguinte maneira:


![Configuração de Hub e spoke com Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Para casos em que os usuários estão se conectando pela Internet sem nenhuma conectividade privada no Azure, o design da rede no Azure deve ser alinhado aos princípios documentados na arquitetura de referência do Azure para [DMZ entre o Azure e o Internet](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Servidor de banco de dados corporativo One
Para o tipo de banco de dados, SQL Server e SAP HANA estão disponíveis. Independentemente do DBMS, você deve ler as [Considerações sobre o documento para a implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) para obter uma compreensão geral das implantações de DBMS em VMs do Azure e os tópicos de rede e armazenamento relacionados.

Embora tenha sido enfatizado nos documentos de banco de dados específicos e genéricos, você deve se familiarizar com:

- [Gerenciar a disponibilidade de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [gerenciar a disponibilidade de máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Esses documentos devem ajudá-lo a decidir a seleção de tipos de armazenamento e a configuração de alta disponibilidade.

Em princípio, você deve:

- Use o SSDs Premium em HDDs padrão. Para saber mais sobre os tipos de disco disponíveis, consulte nosso artigo [selecionar um tipo de disco](../../windows/disks-types.md)
- Usar o Azure Managed disks em discos não gerenciados
- Verifique se você tem IOPS suficientes e taxa de transferência de e/s configurada com a configuração de disco
- Combine o volume/Hana/data e/Hana/log para ter uma configuração de armazenamento com custo eficiente


#### <a name="sql-server-as-dbms"></a>SQL Server como DBMS
Para implantar SQL Server como DBMS para negócios One, vá ao documento [SQL Server implantação de DBMS de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Estimativas de dimensionamento aproximado para o lado do DBMS para SQL Server são:

| Número de utilizadores | vCPUs | Memória | Exemplos de tipos de VM |
| --- | --- | --- | --- |
| até 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| até 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| até 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| até 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

O dimensionamento listado acima deve dar uma ideia de onde começar. Pode ser que você precise de menos ou mais recursos, caso em que uma adaptação no Azure é fácil. Uma alteração entre os tipos de VM é possível com apenas uma reinicialização da VM.

#### <a name="sap-hana-as-dbms"></a>SAP HANA como DBMS
Usando SAP HANA como DBMS as seções a seguir, você deve seguir as considerações do documento [SAP Hana no guia de operações do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

Para configurações de alta disponibilidade e recuperação de desastres em relação a SAP HANA como banco de dados para empresas um no Azure, você deve ler a documentação [SAP Hana alta disponibilidade para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) e a documentação apontada do documento .

Para SAP HANA estratégias de backup e restauração, leia o guia de [backup do documento para SAP Hana em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) e a documentação apontada a partir desse documento.

 
### <a name="business-one-client-server"></a>Servidor cliente corporativo um
Para esses componentes, as considerações de armazenamento não são a principal preocupação. no entanto, você deseja ter uma plataforma confiável. Portanto, você deve usar o armazenamento Premium do Azure para essa VM, mesmo para o VHD de base. Dimensionando a VM, com os dados fornecidos no [Guia de requisitos de hardware do SAP Business One](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, você precisa se concentrar e calcular com os requisitos indicados no capítulo 2,4 do documento. Ao calcular os requisitos, você precisa compará-los com os documentos a seguir para encontrar a VM ideal para você:

- [Tamanhos de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [Observação SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Compare o número de CPUs e da memória necessária para o que está documentado pela Microsoft. Além disso, mantenha a taxa de transferência de rede em mente ao escolher as VMs.








