---
title: SAP Business One em Azure Virtual Machines / Microsoft Docs
description: SAP Business One em Azure.
author: msjuergent
ms.service: virtual-machines
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: caabaa6fbf65787f90c0033a06af26996c2388f2
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994103"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One em Máquinas Virtuais do Azure
Este documento fornece orientações para implantar o SAP Business One em Azure Virtual Machines. A documentação não substitui a documentação de instalação do Negócios um para a SAP. A documentação deve abranger as diretrizes básicas de planeamento e implantação para a infraestrutura Azure para executar aplicações do Business One.

O Business One suporta duas bases de dados diferentes:
- SQL Server - ver [#928839 de nota SAP - Release Planning for Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - para uma matriz de suporte exato SAP Business One para SAP HANA, check-out da [Matriz de Disponibilidade de Produto SAP](https://support.sap.com/pam)

No que diz respeito ao SQL Server, aplicam-se as considerações básicas de implementação documentadas na [implementação DBMS das Máquinas Virtuais Azure para o SAP NetWeaver.](./dbms_guide_general.md) para a SAP HANA, as considerações são mencionadas neste documento.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este guia, precisa de conhecimentos básicos dos seguintes componentes Azure:

- [Máquinas virtuais Azure no Windows](../../windows/tutorial-manage-vm.md)
- [Máquinas virtuais Azure em Linux](../../linux/tutorial-manage-vm.md)
- [Gestão de redes Azure e redes virtuais com PowerShell](../../windows/tutorial-virtual-network.md)
- [Redes de rede Azure e redes virtuais com CLI](../../linux/tutorial-virtual-network.md)
- [Gerir discos do Azure com a CLI do Azure](../../linux/tutorial-manage-disks.md)

Mesmo que esteja interessado apenas no negócio Um, o documento [Azure Virtual Machines planejamento e implementação para SAP NetWeaver](./planning-guide.md) pode ser uma boa fonte de informação.

O pressuposto é que você como o caso de implantação do SAP Business One são:

- Familiar com a instalação do SAP HANA numa determinada infraestrutura como um VM
- Instalação familiar da aplicação SAP Business One numa infraestrutura como a Azure VMs
- Familiarizado com a operação SAP Business One e o sistema DBMS escolhido
- Familiarizar-se com a implantação de infraestruturas em Azure

Todas estas áreas não serão abrangidas pelo presente documento.

Além da documentação do Azure, deve estar atento às principais notas sapais, que se referem ao Business One ou que são notas centrais da SAP para o negócio Um:

- [528296 - Nota geral geral para lançamentos e produtos relacionados com negócios SAP](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Atualizações de Lançamento Nota para SAP Business One 9.2, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Nota central para SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Nota de atualizações de lançamento para SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Nota coletiva para o SAP Business One 9.3 Emissões Gerais](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Nota de Consultoria Coletiva para OSE HANA-Related Tópicos do SAP Business One, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Business One Architecture
O Business One é uma aplicação que tem dois níveis:

- Um nível de cliente com um cliente 'gordo'
- Um nível de base de dados que contém o esquema da base de dados para um inquilino

Uma melhor visão geral dos componentes que estão a ser executando na parte do cliente e quais as peças que estão a ser executando na parte do servidor está documentada no [Guia do Administrador do SAP Business One](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) 

Uma vez que existe uma forte interação crítica de latência entre o nível de cliente e o nível DBMS, ambos os níveis precisam de ser localizados em Azure ao serem implantados em Azure. é habitual que os utilizadores, em seguida, RDS em um ou vários VMs executando um serviço RDS para os componentes do cliente Business One.

### <a name="sizing-vms-for-sap-business-one"></a>VMs de tamanho para SAP Business One

No que diz respeito ao dimensionamento dos VM(s) clientes, os requisitos de recursos são documentados pela SAP no documento [SAP Business One Hardware Requirements Guide](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, é necessário concentrar-se e calcular com os requisitos indicados no capítulo 2.4 do documento.

Como máquinas virtuais Azure para hospedar os componentes do cliente Business One e o anfitrião DBMS, apenas são permitidos VMs que são suportados pela SAP NetWeaver. Para encontrar a lista de VMs suportados pela SAP NetWeaver, leia [a NOTA SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Executando SAP HANA como backend DBMS para o Business One, apenas VMs, que estão listados para Negócios em HANA na [lista de plataformas iaas certificadas hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) são suportados para HANA. Os componentes do cliente Business One não são afetados por esta restrição mais forte para o sistema SAP HANA como sistema DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Desbloqueios do sistema operativo para utilizar para SAP Business One

Em princípio, é sempre melhor utilizar as mais recentes versões do sistema operativo. Especialmente no espaço Linux, a nova funcionalidade Azure foi introduzida com diferentes lançamentos menores mais recentes de Suse e Red Hat. No lado do Windows, a utilização do Windows Server 2016 é altamente recomendada.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Implantação de infraestruturas em Azure para SAP Business One
Nos próximos capítulos, as peças de infraestrutura que importam para a implantação do SAP.

### <a name="azure-network-infrastructure"></a>Infraestrutura de rede Azure
A infraestrutura de rede que precisa de implementar em Azure depende se implementa um único sistema Business One para si. Ou se é um anfitrião que acolhe dezenas de sistemas Do Business One para clientes. Também pode haver ligeiras alterações no design sobre se se conecta ao Azure. Passando por diferentes possibilidades, um design onde você tem uma conectividade VPN em Azure e onde você estende seu Ative Directory através [de VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../../../expressroute/expressroute-introduction.md) para Azure.

![Configuração de rede simples com Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

A configuração simplificada apresentada introduz várias instâncias de segurança que permitem controlar e limitar o encaminhamento. Começa com 

- O router/firewall no lado do cliente no local.
- Segue-se o [Grupo de Segurança da Rede Azure](../../../virtual-network/network-security-groups-overview.md) que pode utilizar para introduzir regras de encaminhamento e segurança para o Azure VNet em que executou a configuração sap Business one.
- Para evitar que os utilizadores do cliente Business One possam também ver o servidor que executa o servidor Business One, que executa a base de dados, deve separar o VM que hospeda o cliente Business one e o servidor de negócios em duas sub-redes diferentes dentro do VNet.
- Voltaria a utilizar o Azure NSG atribuído às duas sub-redes diferentes para limitar o acesso ao servidor Business one.

Uma versão mais sofisticada de uma configuração de rede Azure baseia-se nas [melhores práticas documentadas do hub e da arquitetura falada.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) O padrão de arquitetura do hub e da fala mudaria a primeira configuração simplificada para uma como esta:


![Hub e configuração de falou com o Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Nos casos em que os utilizadores se estão a ligar através da internet sem qualquer conectividade privada ao Azure, o desenho da rede em Azure deve ser alinhado com os princípios documentados na arquitetura de referência azul para [a DMZ entre o Azure e a Internet.](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)

### <a name="business-one-database-server"></a>Servidor de base de dados Business One
Para o tipo de base de dados, SQL Server e SAP HANA estão disponíveis. Independentemente do DBMS, deve ler o documento [Considerações para a implementação de DBMS de máquinas virtuais Azure para obter](./dbms_guide_general.md) uma compreensão geral das implementações de DBMS em VMs Azure e os tópicos de networking e armazenamento relacionados.

Embora já salientado nos documentos específicos e genéricos da base de dados, deve familiarizar-se com:

- [Gerir a disponibilidade de máquinas virtuais do Windows em Azure](../../manage-availability.md) e [Gerir a disponibilidade de máquinas virtuais Linux em Azure](../../manage-availability.md)
- [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Estes documentos devem ajudá-lo a decidir sobre a seleção de tipos de armazenamento e configuração de alta disponibilidade.

Em princípio, deve:

- Utilize SSDs Premium em vez de HDDs standard. Para saber mais sobre os tipos de disco disponíveis, consulte o nosso artigo [Selecione um tipo de disco](../../disks-types.md)
- Utilize discos geridos Azure sobre discos não geridos
- Certifique-se de que tem uma produção de IOPS e I/O suficiente configurada com a configuração do disco
- Combine /hana/data e /hana/log volume de modo a ter uma configuração de armazenamento eficiente em termos de custos


#### <a name="sql-server-as-dbms"></a>SqL Server como DBMS
Para implementar o SQL Server como DBMS para o Business One, acompanhe o documento [SQL Server Azure Virtual Machines DBMS para a implementação do SAP NetWeaver](./dbms_guide_sqlserver.md). 

Estimativas de dimensionamento áspero para o lado DBMS para o SQL Server são:

| Número de utilizadores | vCPUs | Memória | Tipos de VM de exemplo |
| --- | --- | --- | --- |
| até 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| até 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| até 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| até 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

O tamanho acima indicado deve dar uma ideia de onde começar. Pode ser que precise de menos ou mais recursos, caso em que uma adaptação no Azure é fácil. Uma mudança entre os tipos de VM é possível com apenas um reinício do VM.

#### <a name="sap-hana-as-dbms"></a>SAP HANA como DBMS
Utilizando o SAP HANA como DBMS as seguintes secções deve seguir as considerações do documento SAP HANA no guia de [operações da Azure](./hana-vm-operations.md).

Para configurações de alta disponibilidade e recuperação de desastres em torno do SAP HANA como base de dados para o Business One em Azure, deve ler a documentação [SAP HANA alta disponibilidade para máquinas virtuais Azure](./sap-hana-availability-overview.md) e a documentação apontada a partir desse documento.

Para o backup e restauro de estratégias de backback SAP HANA, deve ler o guia de backup do documento [PARA SAP HANA em Azure Virtual Machines](./sap-hana-backup-guide.md) e a documentação apontada a partir desse documento.

 
### <a name="business-one-client-server"></a>Servidor de clientes Business One
Para estes componentes, as considerações de armazenamento não são a principal preocupação. no entanto, quer ter uma plataforma confiável. Portanto, deve utilizar o Azure Premium Storage para este VM, mesmo para o VHD base. Dimensionamento do VM, com os dados dados no [GUIA de Requisitos de Hardware SAP Business One](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, é necessário concentrar-se e calcular com os requisitos indicados no capítulo 2.4 do documento. Ao calcular os requisitos, precisa compará-los com os seguintes documentos para encontrar o VM ideal para si:

- [Tamanhos de máquinas virtuais do Windows no Azure](../../sizes.md)
- [Nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Compare o número de CPUs e memória necessária ao que é documentado pela Microsoft. Tenha também em mente o rendimento da rede ao escolher os VMs.
