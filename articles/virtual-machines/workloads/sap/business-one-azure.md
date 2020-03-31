---
title: SAP Business One em Máquinas Virtuais Azure [ Microsoft Docs
description: SAP Business One em Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101419"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One em Máquinas Virtuais do Azure
Este documento fornece orientações para a implementação do SAP Business One em Máquinas Virtuais Azure. A documentação não substitui a documentação de instalação do Business one para a SAP. A documentação deve abranger as diretrizes básicas de planeamento e implantação para a infraestrutura Azure para executar aplicações do Business One.

O Business One suporta duas bases de dados diferentes:
- SQL Server - ver [SAP Note #928839 - Planeamento de lançamento para microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - para a matriz de suporte sap business one exata para SAP HANA, checkout the [SAP Product Availability Matrix](https://support.sap.com/pam)

No que diz respeito ao SQL Server, aplicam-se as considerações básicas de implementação como documentadas na [implementação do DBMS das Máquinas Virtuais Azure para o SAP NetWeaver.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide) para a SAP HANA, as considerações são mencionadas neste documento.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este guia, necessita de conhecimentos básicos sobre os seguintes componentes Azure:

- [Máquinas virtuais azure no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Máquinas virtuais Azure em Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Gestão de redes de rede Azure e redes virtuais com a PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Redes de rede Azure e redes virtuais com CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Gerir discos do Azure com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Mesmo que esteja interessado apenas no negócio One, o documento De planeamento e implementação de [Máquinas Virtuais Azure para sAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) pode ser uma boa fonte de informação.

O pressuposto é que você, como a instância que implementa o SAP Business One, é:

- Familiarizado com a instalação do SAP HANA numa determinada infraestrutura como um VM
- Instalação familiar da aplicação SAP Business One numa infraestrutura como os VMs Azure
- Familiarizado com a operação SAP Business One e o sistema DBMS escolhido
- Familiarizado com a implantação de infraestruturas em Azure

Todas estas áreas não serão abrangidas por este documento.

Além da documentação azure deve estar ciente das principais notas SAP, que se referem ao Business One ou que são notas centrais da SAP para o negócio Um:

- [528296 - Nota geral geral para lançamentos e produtos relacionados sAP Business One](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - Atualizações de lançamento Nota para SAP Business One 9.2, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - Nota central para o SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - Atualizações de lançamento Nota para SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - Nota Coletiva para o SAP Business One 9.3 Questões Gerais](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - Nota de Consultoria Coletiva para Tópicos Relacionados SAP HANA do SAP HANA Business One, versão para SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Business One Architecture
O Business One é uma aplicação que tem dois níveis:

- Um nível de cliente com um cliente 'gordo'
- Um nível de base de dados que contém o esquema de base de dados para um inquilino

Uma melhor visão geral dos componentes que estão a ser recorridas na parte do cliente e quais as peças que estão a ser recorridas na parte do servidor está documentada no [Guia do Administrador do SAP Business One](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) 

Uma vez que existe uma forte interação crítica de latência entre o nível de cliente e o nível DBMS, ambos os níveis precisam de estar localizados em Azure quando se implantam em Azure. é habitual que os utilizadores, em seguida, RDS em um ou múltiplo VMs executando um serviço RDS para os componentes do cliente Business One.

### <a name="sizing-vms-for-sap-business-one"></a>Dimensionamento de VMs para SAP Business One

No que diz respeito ao dimensionamento do(s) cliente VM(s), os requisitos de recursos são documentados pela SAP no documento Guia de Requisitos de [Hardware SAP Business One](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, é necessário concentrar-se e calcular com os requisitos indicados no capítulo 2.4 do documento.

Como máquinas virtuais Azure para hospedar os componentes do cliente Business One e o anfitrião DBMS, apenas são permitidos VMs que são suportados sAP NetWeaver. Para encontrar a lista de VMs Azure suportados pela SAP NetWeaver, leia [a Nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Executar sAP HANA como backend DBMS para o Business One, apenas VMs, que estão listados para Negócios em HANA na lista de [plataformas hantifeid IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) são suportados para HANA. Os componentes do cliente Business One não são afetados por esta restrição mais forte para o sistema SAP HANA como DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Lançamentos do sistema operativo para utilização para o SAP Business One

Em princípio, é sempre melhor utilizar as mais recentes versões do sistema operativo. Especialmente no espaço Linux, a nova funcionalidade Azure foi introduzida com diferentes lançamentos menores mais recentes de Suse e Red Hat. Do lado do Windows, a utilização do Windows Server 2016 é altamente recomendada.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Implantação de infraestruturas em Azure para o SAP Business One
Nos próximos capítulos, as peças de infraestrutura que importam para a implantação do SAP.

### <a name="azure-network-infrastructure"></a>Infraestrutura de rede Azure
A infraestrutura de rede que precisa de implantar no Azure depende se implementa um único sistema Business One para si mesmo. Ou se é um anfitrião que acolhe dezenas de sistemas Business One para clientes. Também pode haver ligeiras alterações no design sobre se você se conecta ao Azure. Passando por diferentes possibilidades, um design onde você tem uma conectividade VPN em Azure e onde você estende o seu Diretório Ativo através [de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para Azure.

![Configuração de rede simples com o Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

A configuração simplificada apresentada introduz várias instâncias de segurança que permitem controlar e limitar o encaminhamento. Começa com 

- O router/firewall do lado do cliente no local.
- A próxima instância é o Grupo de [Segurança da Rede Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) que pode usar para introduzir regras de encaminhamento e segurança para o Azure VNet em que executa a sua configuração SAP Business one.
- Para evitar que os utilizadores do cliente Business One possam ver o servidor que gere o servidor Business One, que executa a base de dados, deve separar o VM que hospeda o cliente Business one e o business um servidor em duas subredes diferentes dentro do VNet.
- Utilizaria o Azure NSG atribuído novamente às duas subredes diferentes, a fim de limitar o acesso ao servidor Business one.

Uma versão mais sofisticada de uma configuração de rede Azure baseia-se no Azure [documentado boas práticas de hub e arquitetura falada.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) O padrão de arquitetura do hub e falado mudaria a primeira configuração simplificada para uma como esta:


![Configuração do hub e falou com o Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Para os casos em que os utilizadores estão a ligar-se através da internet sem qualquer conectividade privada com o Azure, o desenho da rede em Azure deve estar alinhado com os princípios documentados na arquitetura de referência Azure para [DMZ entre o Azure e a Internet.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)

### <a name="business-one-database-server"></a>Servidor de base de dados Business One
Para o tipo de base de dados, o SQL Server e o SAP HANA estão disponíveis. Independentemente do DBMS, deve ler o documento Considerações para a implantação de [DBMS de Máquinas Virtuais Azure para](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) a carga de trabalho sAP para obter uma compreensão geral das implementações de DBMS em VMs azul e os tópicos de networking e armazenamento relacionados.

Embora enfatizado nos documentos específicos e genéricos da base de dados, deve familiarizar-se com:

- [Gerencie a disponibilidade de máquinas virtuais windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [Gerencie a disponibilidade de máquinas virtuais Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [SLA para Máquinas Virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Estes documentos devem ajudá-lo a decidir sobre a seleção de tipos de armazenamento e configuração de alta disponibilidade.

Em princípio, deve:

- Utilize SSDs Premium sobre HDDs Standard. Para saber mais sobre os tipos de disco disponíveis, consulte o nosso artigo [Selecione um tipo](../../windows/disks-types.md) de disco
- Utilize discos geridos azure sobre discos não geridos
- Certifique-se de que tem uma entrada suficiente de IOPS e I/O configurada com a configuração do disco
- Combine /hana/data e /hana/volume de registo para ter uma configuração de armazenamento eficiente em termos de custos


#### <a name="sql-server-as-dbms"></a>Servidor SQL como DBMS
Para implementar o SQL Server como DBMS para o Business One, vá ao longo do documento [SQL Server Azure Virtual Machines DBMS implementação para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

Estimativas de tamanho bruto para o lado DBMS para o Servidor SQL são:

| Número de utilizadores | vCPUs | Memória | Exemplo tipos de VM |
| --- | --- | --- | --- |
| até 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| até 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| até 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| até 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

O tamanho acima listado deve dar uma ideia por onde começar. Pode ser que precise de menos ou mais recursos, caso em que uma adaptação no azure é fácil. Uma mudança entre os tipos vM é possível com apenas um reinício do VM.

#### <a name="sap-hana-as-dbms"></a>SAP HANA como DBMS
Utilização do SAP HANA como DBMS as seguintes secções deve seguir as considerações do documento [SAP HANA no guia de operações azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

Para configurações de alta disponibilidade e recuperação de desastres em torno do SAP HANA como base de dados para o Business One em Azure, deve ler a documentação [SAP HANA alta disponibilidade para máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) e a documentação apontada a partir desse documento.

Para a cópia de segurança e restauro da SAP HANA, deve ler o guia de backup do documento [Para SAP HANA em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) e a documentação apontada a partir desse documento.

 
### <a name="business-one-client-server"></a>Business One servidor de cliente
Para estes componentes, as considerações de armazenamento não são a principal preocupação. no entanto, quer ter uma plataforma fiável. Por isso, deverá utilizar o Armazenamento Azure Premium para este VM, mesmo para o VHD base. Dimensionamento do VM, com os dados fornecidos no Guia de Requisitos de [Hardware SAP Business One](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). Para o Azure, é necessário concentrar-se e calcular com os requisitos indicados no capítulo 2.4 do documento. À medida que calcula os requisitos, precisa compará-los com os seguintes documentos para encontrar o VM ideal para si:

- [Tamanhos de máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
- [Nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Compare o número de CPUs e memória necessária ao que é documentado pela Microsoft. Tenha também em mente a entrada da rede na hora de escolher os VMs.








