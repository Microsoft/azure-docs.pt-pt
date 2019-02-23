---
title: Mainframe realojamento em máquinas virtuais do Azure | Documentos da Microsoft
description: Realojar suas cargas de trabalho de mainframe como sistemas baseados no IBM Z utilizar máquinas virtuais (VMs) no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 067ab7538924f4aef7c48731d10fa7e68855214a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739769"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe realojamento em máquinas virtuais do Azure

Migrar cargas de trabalho desde o mainframe o ambientes na cloud permite-lhe modernize a sua infraestrutura e, muitas vezes, poupar nos custos. Muitas cargas de trabalho podem ser transferidas para o Azure com apenas alterações de código pequenas, como atualizar os nomes das bases de dados.

O termo *mainframe* geralmente se refere a um sistema de computador grande, mas a grande maioria implementada no momento são servidores de sistema Z da IBM ou sistemas plug-compatible IBM executar MVS, DOS, VSE, os/390 ou z/OS.

Uma máquina virtual do Azure (VM) é utilizada para isolar e gerir os recursos para uma aplicação específica numa única instância. Mainframes IBM z/OS, como o utilizam partições lógicas (LPARS) para esta finalidade. Um mainframe poderá utilizar um LPAR para uma região do CICS com programas de COBOL associados e um LPAR separado para a base de dados Db2 da IBM. Um típico [aplicação de n camadas no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implementa as VMs do Azure numa rede virtual que pode ser segmentada em sub-redes para cada camada.

VMs do Azure podem executar ambientes de emulação de mainframe e compiladores que suportam cenários de migração lift-and-shift. Desenvolvimento e teste, muitas vezes, estão entre as cargas de trabalho de primeira para migrar de um mainframe para um ambiente de dev/test do Azure. Componentes de servidor comuns que poderá emular incluem o processo de transações online (OLTP), batch e os sistemas de ingestão de dados, como mostra a figura seguinte.

![](media/01-overview.png)

Algumas cargas de trabalho de mainframe podem ser migradas para o Azure com relativa facilidade, enquanto outros podem ser rehosted no Azure através de uma solução de parceiro. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, o [Centro de migração de Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Configurar o ambiente de desenvolvimento/teste com uma plataforma de rehosting Micro Focus

Micro Focus Enterprise Server é uma das maior mainframe realojamento plataformas disponíveis. Pode usá-lo a executar cargas de trabalho z/OS num x86 mais barato plataforma no Azure.

Para começar a utilizar, veja os artigos seguintes:

- [Instalar o Micro Focus Enterprise Server 4.0 e o Enterprise Developer 4.0 no Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Configurar a Micro foco CICS BankDemo para Micro foco Enterprise Developer 4.0 no Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame no Azure

TmaxSoft OpenFrame é um mainframe popular realojamento solução que torna mais fácil para os recursos existentes do mainframe de lift- and -shift-los para o Azure. Um ambiente de OpenFrame no Azure é adequado para o desenvolvimento, demonstrações, teste ou cargas de trabalho de produção.

Para começar a utilizar, transfira o [instalar o TmaxSoft OpenFrame no Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) ebook.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Configurar um ambiente de desenvolvimento/teste com o IBM Z Dev/Test 12.0

Desenvolvimento do IBM Z e de ambiente de teste (IBM zD & T) configura um ambiente de não produção no Azure que pode utilizar para o desenvolvimento, teste e demonstrações de aplicativos baseados em z/OS.

O ambiente de emulação no Azure pode alojar uma variedade de instâncias de Z por meio do aplicativo que os desenvolvedores controlado distribuições (ADCDs). Pode executar zD & T Personal Edition, zD Sysplex paralela de T e zD & T Enterprise Edition no Azure e no Azure Stack.

Para começar a utilizar, veja os artigos seguintes:

-   [Configurar a IBM zD & T 12.0 no Azure](./ibm/install-ibm-z-environment.md)
-   [Configurar ADCD no zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>Migrar o IBM DB2 pureScale para o Azure

O ambiente de pureScale IBM DB2 fornece um cluster de base de dados para o Azure com elevada disponibilidade e escalabilidade em sistemas operativos Linux. Embora não é idêntico para o ambiente original, IBM DB2 pureScale no Linux fornece semelhante para a elevada disponibilidade e recursos de escalabilidade como IBM DB2 para z/sistema operacional em execução numa configuração Sysplex paralela no mainframe.

Para começar a utilizar, veja [pureScale IBM DB2 no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerações

Quando migra cargas de trabalho de mainframe para a infraestrutura do Azure como um serviço (IaaS), pode escolher entre vários tipos de recursos informáticos a pedido e dimensionáveis, incluindo as VMs do Azure. O Azure oferece diversas [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) VMs.

### <a name="high-availability-and-failover"></a>Elevada disponibilidade e ativação pós-falha

O Azure oferece alocação com base em contratos de nível de serviço (SLAs), onde vários 9s disponibilidade é o predefinido, otimizado com a replicação local ou baseadas na geografia dos serviços. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Para o IaaS do Azure, como VMs, ativação pós-falha baseia-se na funcionalidade de sistema específico, como instâncias de clustering de ativação pós-falha e [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). Quando utiliza a plataforma do Azure como recursos de serviço (PaaS), tal como [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) e [do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), a plataforma lida com as ativações pós-falha automaticamente.

### <a name="scalability"></a>Escalabilidade

Normalmente, mainframes aumentar verticalmente, ao escalamento horizontal de ambiente de cloud. O Azure oferece diversas [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) tamanhos para atender às suas necessidades. A cloud também aumenta ou para baixo para especificações de utilizador exata de correspondência. Capacidade de computação, armazenamento e serviços [dimensionamento](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) a pedido num modelo de faturação baseada na utilização.

### <a name="storage"></a>Armazenamento

Na nuvem, tem uma variedade de opções de armazenamento flexível e escalável e paga apenas aquilo de que precisa. [O armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) oferece um armazenamento de objetos altamente dimensionável para objetos de dados, um serviço de sistema de ficheiros para a cloud, um arquivo de mensagens confiável e um arquivo de NoSQL. Para as VMs, discos geridos e fornecem armazenamento de disco persistentes e seguros.

### <a name="backup-and-recovery"></a>Cópia de segurança e recuperação

Manter o seu próprio site de recuperação após desastre pode ser uma proposta cara. Opções de fácil implementação e econômicas para o Azure tem [cópia de segurança](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), e [redundância](https://docs.microsoft.com/azure/storage/common/storage-redundancy) em níveis de locais ou regionais, ou através de redundância geográfica.

## <a name="azure-government-for-mainframe-migrations"></a>O Azure Government para migrações de mainframe

Muitas entidades do setor público muito mover seus aplicativos de mainframe para uma plataforma mais moderno e flexível. O Microsoft Azure Government é uma instância fisicamente separada dos serviços de cloud com base na plataforma Microsoft Azure global mas empacotados para sistemas governamentais federais, estaduais e locais. Ele fornece segurança de classe mundial, proteção e os serviços de conformidade especificamente para agências governamentais dos Estados Unidos e respetivos parceiros.

O Azure Government de obter uma autoridade provisória para operar (P-ATO) por FedRAMP elevado impacto para os sistemas que necessitam deste tipo de ambiente. 

Para começar a utilizar, transfira [cloud do Microsoft Azure Government para aplicações de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Saiba mais

Se estiver a considerar uma migração de mainframe, nossos abrangente [parceiro](partner-workloads.md) ecossistema está disponível para ajudá-lo. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte a [Alliance de modernização de plataforma](https://www.platformmodernization.org/pages/mainframe.aspx).

Veja também:

-   [Migração de mainframe](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Resolução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Desmistificando do mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
