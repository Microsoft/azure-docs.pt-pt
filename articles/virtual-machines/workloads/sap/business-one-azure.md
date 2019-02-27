---
title: SAP Business um em máquinas virtuais do Azure | Documentos da Microsoft
description: SAP Business um no Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 101710b5a57faa37be77ff4b059fa0d494f4e617
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56878069"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business um em máquinas virtuais do Azure
Este documento fornece orientação para implementar o SAP Business One em máquinas de virtuais do Azure. A documentação não é um substituto para documentação de instalação do negócio, um para SAP. A documentação deve abranger as orientações básicas sobre o planeamento e implementação para a infraestrutura do Azure executar aplicações de Business One.

Um negócio suporta dois diferentes bancos de dados:
- SQL Server - consulte [SAP Note #928839 - planejamento de lançamentos para o Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - para exata matriz de suporte para o SAP HANA, SAP Business One Check-Out do [matriz de disponibilidade do produto SAP](https://support.sap.com/pam)

Sobre as considerações de implementação básica do SQL Server, conforme documentado no [implementação de DBMS de máquinas virtuais do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) aplica-se. para o SAP HANA, as considerações são mencionadas neste documento.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este guia, tem um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas virtuais do Azure no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Máquinas virtuais do Azure no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure virtual de rede e a redes de gestão com o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Redes do Azure e redes virtuais com a CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Gerir discos do Azure com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Mesmo se estiver interessado em negócios um só, o documento [máquinas virtuais do Azure de planeamento e implementação para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) pode ser uma boa fonte de informações.

A suposição é que, como a instância que implementar o SAP Business One é:

- Familiarizado com a instalação de SAP HANA numa infraestrutura determinada como uma VM
- Familiarizar-se de instalar o aplicativo SAP Business One numa infra-estrutura como VMs do Azure
- Familiarizado com o SAP Business One e o sistema do DBMS escolhido a funcionar
- Familiarizado com a implantação de infraestrutura no Azure

Todas estas áreas não serão abordadas neste documento.

Além de documentação do Azure deve estar ciente das notas de SAP principal, que se referem às empresas uma ou que são notas centrais do SAP para a empresa um:

- [528296 - nota de descrição geral de geral para versões de um SAP Business e produtos relacionados](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - nota de atualizações de versão para o SAP Business um 9.2, versão para o SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - nota central para SAP Business um 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - nota de atualizações de versão para o SAP Business um 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - nota coletiva para SAP Business problemas gerais de um 9.3](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - observação de consultoria coletiva para SAP HANA-Related tópicos de SAP Business One, versão para o SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Arquitetura de uma empresa
Um negócio é uma aplicação que tem dois escalões:

- Uma camada de cliente com um cliente de 'fat'
- Uma camada de base de dados que contém o esquema de banco de dados para um inquilino

Uma visão geral melhor que componentes estão em execução na parte cliente e que partes estão em execução na parte servidor está documentada em [Guia do administrador um Business SAP](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Como há interação crítica de latência pesada entre a camada de cliente e a camada do DBMS, ambas as camadas tem de estar localizado no Azure ao implementar no Azure. é comum que os utilizadores, em seguida, RDS numa ou várias VMs em execução um RDS de serviço para os componentes de cliente Business One.

### <a name="sizing-vms-for-sap-business-one"></a>O dimensionamento de VMs para as empresas SAP um

Sobre o dimensionamento de VM (s) de cliente, os requisitos de recursos estão documentados pelo SAP no documento [guia de requisitos de Hardware um do SAP Business](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, precisa concentrar-se e calcular com os requisitos indicados no capítulo 2.4 do documento.

Como máquinas virtuais do Azure para hospedar os componentes do cliente Business One e o anfitrião do DBMS, apenas as VMs que são suportados do SAP NetWeaver são permitidas. Para encontrar a lista de SAP NetWeaver suportadas VMs do Azure, leia [SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Execução do SAP HANA como back-end do DBMS Business One, apenas as VMs, que estão listadas para empresas em HANA para o [lista de plataformas de IaaS do HANA certifeid](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) são suportadas para HANA. Os componentes do cliente Business One não são afetados por esta restrição mais forte para o SAP HANA como sistema do DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Versões do sistema operativo a utilizar para o SAP Business One

Em princípio, é sempre melhor usar as versões mais recentes do sistema operativo. Especialmente no espaço de Linux, a nova funcionalidade do Azure foi introduzida com diferentes versões de pequenas mais recentes do Suse e Red Hat. No lado do Windows, com o Windows Server 2016 é altamente recomendado.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Implementar infraestrutura no Azure para SAP Business One
Nos capítulos alguns seguintes, a infraestrutura de peças caso para a implementação de SAP.

### <a name="azure-network-infrastructure"></a>Infraestrutura de rede do Azure
A infraestrutura de rede que necessita para implementar no Azure depende de se implementar um único sistema Business One por conta própria. Ou, se for um fornecedor de alojamento que aloja a dezenas de sistemas comerciais um para os clientes. Também pode haver pequenas alterações no design no se como ligar ao Azure. Passar por possibilidades diferentes, um design em que tem uma conectividade VPN para o Azure e onde é estender o Active Directory por meio [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para o Azure.

![Configuração de rede simples com Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

A configuração simplificada apresentada introduz várias instâncias de segurança que permitir que o controle e roteamento de limite. Ele começa com 

- O router/firewall do lado do cliente no local.
- A instância seguinte é o [grupo de segurança de rede do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) que pode utilizar para apresentar as regras de roteamento e segurança para que executar a configuração de um SAP Business na VNet do Azure.
- Para evitar que os usuários de negócios de um cliente também pode ver o servidor que executa o servidor Business One, o que é executado na base de dados, deve separar a VM que aloja o cliente de um negócio e o servidor de uma de negócios em duas sub-redes diferentes dentro da VNet.
- Usaria NSG do Azure atribuído para as duas sub-redes diferentes novamente para limitar o acesso ao servidor de um negócio.

Uma versão mais sofisticada de uma configuração de rede do Azure baseia-se do Azure [documentado melhores práticas de arquitetura hub- and -spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). O padrão de arquitetura de hub- and -spoke alteraria a primeira configuração simplificada para um como este:


![Configuração de hub- and -spoke com Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Nos casos em que os utilizadores estão a ligar através da internet sem nenhuma conectividade privada para o Azure, o design da rede no Azure deve ser alinhado com os princípios documentados na arquitetura de referência do Azure para [DMZ entre o Azure e a Internet](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Servidor de uma base de dados de negócio
Para o tipo de base de dados, SQL Server e SAP HANA estão disponíveis. Independentemente do DBMS, deve ler o documento [considerações para a implementação de DBMS de máquinas virtuais do Azure para a carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) para obter uma compreensão geral de implementações do DBMS em VMs do azure e o sistema de rede relacionados e o armazenamento tópicos.

Embora enfatizadas já nos documentos específicos e genérico da base de dados, deve tornar-se familiarizado com:

- [Gerir a disponibilidade das máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [gerir a disponibilidade das máquinas virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [SLA para máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Estes documentos devem ajudá-lo a decidir sobre a seleção dos tipos de armazenamento e configuração de elevada disponibilidade.

Em princípio, deve:

- Utilize Premium SSDs em HDDs padrão. Para saber mais sobre os tipos de disco disponível, consulte nosso artigo [selecione um tipo de disco](../../windows/disks-types.md)
- Utilizar Managed disks do Azure através de discos não geridos
- Certifique-se de que tenha um débito IOPS e e/s suficiente configurado com a configuração do disco
- Combinar/hana/dados e /hana/log volume para ter uma configuração de um armazenamento eficiente de custo


#### <a name="sql-server-as-dbms"></a>SQL Server como DBMS
Para implementar o SQL Server como DBMS para Business One, aceda ao longo do documento [implementação de DBMS de máquinas virtuais do SQL Server do Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

As estimativas de dimensionamento aproximada para o lado do DBMS para o SQL Server são:

| Número de utilizadores | vCPUs | Memória | Tipos de VM de exemplo |
| --- | --- | --- | --- |
| até 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| até 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| até 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| até 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

O dimensionamento listado acima deve dar uma ideia onde para começar. Pode ser que precisa menos ou mais recursos, caso em que uma adaptação no azure é fácil. Uma alteração entre tipos VM é possível com apenas um reinício da VM.

#### <a name="sap-hana-as-dbms"></a>SAP HANA como DBMS
Utilizar o SAP HANA DBMS as seguintes secções que deve seguir as considerações do documento [SAP HANA no guia de operações do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

Para elevada disponibilidade e configurações de recuperação após desastre em torno do SAP HANA como uma base de dados para o Business One no Azure, leia a documentação [elevada disponibilidade de SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) e a documentação apontado para a partir desse documento.

Para o SAP HANA de cópia de segurança e restaurar estratégias, deve ler o documento [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) e a documentação apontado para a partir desse documento.

 
### <a name="business-one-client-server"></a>Servidor de um cliente de negócios
Para esses componentes considerações de armazenamento não são a principal preocupação. No entanto, deseja ter uma plataforma confiável. Portanto, deve usar o armazenamento Premium do Azure para esta VM, mesmo para o VHD base. O dimensionamento da VM, com os dados fornecidos na [guia de requisitos de Hardware um do SAP Business](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, precisa concentrar-se e calcular com os requisitos indicados no capítulo 2.4 do documento. Como calcular os requisitos, é necessário compará-los nos seguintes documentos para localizar a VM ideal para:

- [Tamanhos de máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [Nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Compare o número de CPUs e memória necessários para o que é descrito pela Microsoft. Tenha também o débito de rede em mente ao escolher as VMs.








