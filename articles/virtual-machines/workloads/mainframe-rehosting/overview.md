---
title: Rehosagem de mainframe em máquinas virtuais Azure
description: Reenquadrar as suas cargas de trabalho de computador principal, como sistemas baseados em IBM Z, utilizando máquinas virtuais (VMs) no Microsoft Azure.
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.openlocfilehash: 1baba0afc08c6a4df63c38cafe9231411ecf8f1f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048855"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Rehosagem de mainframe em máquinas virtuais Azure

A migração de cargas de trabalho dos ambientes do computador principal para a nuvem permite-lhe modernizar a sua infraestrutura e muitas vezes economizar em custos. Muitas cargas de trabalho podem ser transferidas para o Azure apenas com pequenas alterações de código, tais como a atualização dos nomes das bases de dados.

De um modo geral, o termo *mainframe* significa um grande sistema informático. Especificamente, a grande maioria atualmente em uso são servidores IBM System Z ou sistemas compatíveis com plug-in IBM que executam MVS, DOS, VSE, OS/390 ou z/OS.

Uma máquina virtual Azure (VM) é usada para isolar e gerir os recursos para uma aplicação específica num único caso. Os mainframes, tais como o IBM z/OS, utilizam partições lógicas (LPARS) para este fim. Um computador central pode usar um LPAR para uma região do CICS com programas COBOL associados, e um LPAR separado para a base de dados IBM Db2. Uma [aplicação típica de n-tier no Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implanta VMs Azure numa rede virtual que pode ser segmentada em sub-redes para cada nível.

Os VMs do Azure podem executar ambientes de emulação de computador principal e compiladores que suportam cenários de elevação e mudança. O desenvolvimento e os testes estão frequentemente entre as primeiras cargas de trabalho a migrar de um computador central para um ambiente dev/teste Azure. Os componentes comuns do servidor que pode emular incluem o processo de transação online (OLTP), o lote e os sistemas de ingestão de dados, como mostra o seguinte número.

![Os ambientes de emulação em Azure permitem-lhe executar sistemas baseados em z/OS.](media/01-overview.png)

Algumas cargas de trabalho do mainframe podem ser migradas para Azure com relativa facilidade, enquanto outras podem ser rehosted em Azure usando uma solução parceira. Para uma orientação detalhada sobre a escolha de uma solução de parceiro, o [Centro de Migração do Azure Mainframe](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="mainframe-migration"></a>Migração de mainframe

Rehost, reconstruir, substituir ou aposentar-se? IaaS ou PaaS? Para determinar a estratégia de migração certa para a sua aplicação mainframe, consulte o guia [de migração mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  no Centro de Arquitetura Azure.

## <a name="micro-focus-rehosting-platform"></a>Micro Focus plataforma de rehosting

O Micro Focus Enterprise Server é uma das maiores plataformas de rehosting do mainframe disponíveis. Pode usá-lo executar as suas cargas de trabalho z/OS numa plataforma x86 mais barata em Azure.

Para começar:

- [Instalar servidor empresarial e desenvolvedor de empresas no Azure](./microfocus/set-up-micro-focus-azure.md)
- [Criação do CICS BankDemo para Construtor Empresarial em Azure](./microfocus/demo.md)
- [Executar o servidor da empresa num contentor de estivador em Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame em Azure

TmaxSoft OpenFrame é uma solução de rehosting de mainframe popular usada em cenários de elevação e mudança. Um ambiente OpenFrame em Azure é adequado para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção.

Para começar:

- [Começar com o TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Faça o download do ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

O IBM Z Development and Test Environment (IBM zD&T) cria um ambiente de não produção no Azure que pode utilizar para desenvolvimento, teste e demonstrações de aplicações baseadas em z/OS.

O ambiente de emulação em Azure pode hospedar diferentes tipos de instâncias Z através de Distribuições Controladas de Desenvolvedores de Aplicações (ADCDs). Você pode executar zD&T Personal Edition, zD&T Parallel Sysplex, e zD&T Enterprise Edition em Azure e Azure Stack.

Para começar:

- [Configurar o IBM zD&T 12.0 em Azure](./ibm/install-ibm-z-environment.md)
- [Configurar a ADCD no zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale em Azure

O ambiente IBM DB2 pureScale fornece um cluster de base de dados para o Azure. Não é idêntico ao ambiente original, mas oferece disponibilidade e escala semelhantes à IBM DB2 para z/SO em execução numa configuração Paralela Sysplex.

Para começar, consulte [a IBM DB2 pureScale em Azure.](.//ibm/ibm-db2-purescale-azure.md)

## <a name="considerations"></a>Considerações

Ao migrar cargas de trabalho do mainframe para a infraestrutura Azure como um serviço (IaaS), pode escolher entre vários tipos de recursos de computação a pedido e escaláveis, incluindo VMs Azure. A Azure oferece uma gama de [VMs Linux](../../linux/overview.md) e [Windows.](../../windows/overview.md)

### <a name="compute"></a>Computação

A potência de computação Azure compara-se favoravelmente à capacidade de um computador central. Se estiver a pensar em mover uma carga de trabalho do computador principal para Azure, compare a métrica do computador principal de um milhão de instruções por segundo (MIPS) com CPUs virtuais. 

Saiba como mover o [computador principal para Azure.](./concepts/mainframe-compute-azure.md)

### <a name="high-availability-and-failover"></a>Alta disponibilidade e failover

A Azure oferece acordos de nível de serviço baseados em compromissos (SLAs). A disponibilidade de múltiplos noves é o padrão, e as SLAs podem ser otimizadas com a replicação local ou geo-baseada de serviços. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Com o Azure IaaS, como um VM, as funções específicas do sistema fornecem suporte de failover — por exemplo, instâncias de agrupamento de falhas e conjuntos de disponibilidade. Quando utiliza a plataforma Azure como um recurso de serviço (PaaS), a plataforma lida com falhas automaticamente. Exemplos incluem [Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) e [Azure Cosmos DB](../../../cosmos-db/introduction.md).

### <a name="scalability"></a>Escalabilidade

Os mainframes normalmente escalam, enquanto os ambientes em nuvem escalam. O Azure oferece uma gama de tamanhos [Linux](../../sizes.md) e [Windows](../../sizes.md) para atender às suas necessidades. A nuvem também escala para cima ou para baixo para corresponder às especificações exatas do utilizador. A [escala](/azure/architecture/best-practices/auto-scaling) de potência, armazenamento e serviços de cálculo a pedido sob um modelo de faturação baseado no uso.

### <a name="storage"></a>Armazenamento

Na nuvem, você tem uma gama de opções de armazenamento flexível e escalável, e você paga apenas pelo que você precisa. [O Azure Storage](../../../storage/common/storage-introduction.md) oferece uma loja de objetos massivamente escalável para objetos de dados, um serviço de sistema de ficheiros para a nuvem, uma loja de mensagens confiável e uma loja NoSQL. Para os VMs, os discos geridos e não geridos proporcionam um armazenamento de disco persistente e seguro.

Saiba como mover o [armazenamento do computador principal para Azure.](./concepts/mainframe-storage-azure.md)

### <a name="backup-and-recovery"></a>Cópia de segurança e recuperação

Manter o seu próprio local de recuperação de desastres pode ser uma proposta cara. O Azure tem opções fáceis de implementar e rentáveis para [o backup,](../../../backup/backup-overview.md) [recuperação](../../../site-recovery/site-recovery-overview.md)e [redundância](../../../storage/common/storage-redundancy.md) a nível local ou regional, ou através de geo-redundância.

## <a name="azure-government-for-mainframe-migrations"></a>Governo de Azure para as migrações no mainframe

Muitas entidades do sector público gostariam de mover as suas aplicações mainframe para uma plataforma mais moderna e flexível. O Microsoft Azure Government é um caso fisicamente separado da plataforma global Microsoft Azure , embalada para sistemas federais, estaduais e governamentais locais. Fornece serviços de segurança, proteção e conformidade de classe mundial especificamente para as agências governamentais dos Estados Unidos e seus parceiros.

O Governo de Azure obteve uma Autoridade Provisória para Operar (P-ATO) para o Alto Impacto da FedRAMP para sistemas que precisam deste tipo de ambiente.

Para começar, baixe a [nuvem do Governo do Microsoft Azure para aplicações mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Passos seguintes

Peça aos [nossos parceiros](partner-workloads.md) que o ajudem a migrar ou a reenquadrar as suas aplicações mainframe. 

Veja também:

- [Livros brancos sobre temas do mainframe](mainframe-white-papers.md)
- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Resolução de problemas](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [Desmistificar o mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md