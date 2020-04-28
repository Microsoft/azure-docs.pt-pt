---
title: Realojamento do mainframe em máquinas virtuais Azure
description: Realoja as suas cargas de trabalho principais, tais como sistemas baseados em IBM Z, utilizando máquinas virtuais (VMs) no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76289803"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Realojamento do mainframe em máquinas virtuais Azure

A migração de cargas de trabalho de ambientes mainframe para a nuvem permite-lhe modernizar a sua infraestrutura e muitas vezes economizar em custos. Muitas cargas de trabalho podem ser transferidas para o Azure apenas com pequenas alterações de código, tais como a atualização dos nomes das bases de dados.

De um modo geral, o termo *mainframe* significa um grande sistema informático. Especificamente, a grande maioria atualmente em uso são servidores IBM System Z ou sistemas compatíveis com plug-compatíveis COM plug-compatíveis COM A IBM que executam MVS, DOS, VSE, OS/390, ou z/OS.

Uma máquina virtual Azure (VM) é usada para isolar e gerir os recursos para uma aplicação específica numa única instância. Os principais quadros, como o IBM z/OS, utilizam partições lógicas (LPARS) para o efeito. Um computador principal pode usar um LPAR para uma região CICS com programas COBOL associados, e um LPAR separado para base de dados IBM Db2. Uma [aplicação típica n-tier no Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implanta VMs Azure numa rede virtual que pode ser segmentada em subredes para cada nível.

Os VMs azure podem executar ambientes de emulação e compiladores principais que suportam cenários de elevação e mudança. O desenvolvimento e os testes estão muitas vezes entre as primeiras cargas de trabalho a migrar de um computador principal para um ambiente azure dev/teste. Os componentes comuns do servidor que pode imitar incluem processo de transação on-line (OLTP), sistemas de lote e ingestão de dados, como mostra a figura seguinte.

![Ambientes de emulação no Azure permitem-lhe executar sistemas baseados em z/OS.](media/01-overview.png)

Algumas cargas de trabalho principais podem ser migradas para Azure com relativa facilidade, enquanto outras podem ser realojadas em Azure usando uma solução de parceiro. Para uma orientação detalhada sobre a escolha de uma solução parceira, o centro de [migração Azure Mainframe](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="mainframe-migration"></a>Migração de mainframe

Reanfitrião, reconstruir, substituir ou reformar-se? IaaS ou PaaS? Para determinar a estratégia de migração certa para a sua aplicação mainframe, consulte o guia de [migração Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) no Centro de Arquitetura Azure.

## <a name="micro-focus-rehosting-platform"></a>Plataforma de realojamento micro Focus

O Micro Focus Enterprise Server é uma das maiores plataformas de realojamento do mainframe disponíveis. Pode usá-lo executar as suas cargas de trabalho z/S numa plataforma x86 menos cara no Azure.

Para começar:

- [Instale o Enterprise Server e o Enterprise Developer no Azure](./microfocus/set-up-micro-focus-azure.md)
- [Criação do CICS BankDemo para promotor empresarial em Azure](./microfocus/demo.md)
- [Executar o Servidor Empresarial em um contentor docker em Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame em Azure

TmaxSoft OpenFrame é uma solução popular de realojamento do mainframe usada em cenários de elevação e mudança. Um ambiente OpenFrame em Azure é adequado para trabalhos de desenvolvimento, demonstrações, testes ou cargas de trabalho de produção.

Para começar:

- [Começar com o TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Descarregue o ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

A IBM Z Development and Test Environment (IBM zD&T) estabelece um ambiente de não produção no Azure que pode utilizar para desenvolvimento, teste e demonstrações de aplicações baseadas em z/OS.

O ambiente de emulação em Azure pode acolher diferentes tipos de casos De Z através de Distribuição Controlada de Desenvolvedores de Aplicações (ADCDs). Você pode executar zD&T Personal Edition, zD&T Parallel Sysplex, e zD&T Enterprise Edition em Azure e Azure Stack.

Para começar:

- [Configurar IBM zD&T 12.0 em Azure](./ibm/install-ibm-z-environment.md)
- [Configurar ADCD em zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale on Azure

O ambiente IBM DB2 pureScale fornece um cluster de base de dados para o Azure. Não é idêntico ao ambiente original, mas oferece disponibilidade e escala semelhantes à IBM DB2 para z/OS que funciona numa configuração paralela de Sysplex.

Para começar, consulte [IBM DB2 pureScale no Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerações

Ao migrar cargas de trabalho para a infraestrutura Azure como um serviço (IaaS), pode escolher entre vários tipos de recursos de computação a pedido e escaláveis, incluindo VMs Azure. O Azure oferece uma gama de [VMs Linux](/azure/virtual-machines/linux/overview) e [Windows.](/azure/virtual-machines/windows/overview)

### <a name="compute"></a>Computação

A potência da computação Azure compara-se favoravelmente à capacidade de um computador principal. Se estiver a pensar em mover uma carga de trabalho para o Azure, compare a métrica do computador principal de um milhão de instruções por segundo (MIPS) com CPUs virtuais. 

Aprenda a mover a [computação mainframe para O Azure.](./concepts/mainframe-compute-azure.md)

### <a name="high-availability-and-failover"></a>Alta disponibilidade e failover

A Azure oferece acordos de nível de serviço baseados em compromissos (SLAs). A disponibilidade de vários noveé o padrão, e sLAs podem ser otimizados com replicação local ou geo-baseada de serviços. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Com o Azure IaaS, como um VM, as funções específicas do sistema fornecem suporte de failover - por exemplo, casos de agrupamento de falhas e conjuntos de disponibilidade. Quando utiliza a plataforma Azure como recurso de serviço (PaaS), a plataforma trata de falhas automaticamente. Exemplos incluem Base de [Dados Azure SQL](/azure/sql-database/sql-database-technical-overview) e [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Escalabilidade

Os mainframes normalmente escalam-se, enquanto os ambientes de nuvem se esescalam. O Azure oferece uma gama de tamanhos [Linux](/azure/virtual-machines/linux/sizes) e [Windows](/azure/virtual-machines/windows/sizes) para atender às suas necessidades. A nuvem também escala para cima ou para baixo para corresponder às especificações exatas do utilizador. Calcular a potência, armazenamento e [a escala](/azure/architecture/best-practices/auto-scaling) de serviços à escala da procura ao abrigo de um modelo de faturação baseado no uso.

### <a name="storage"></a>Armazenamento

Na nuvem, você tem uma gama de opções de armazenamento flexíveis e escaláveis, e você paga apenas pelo que você precisa. [O Azure Storage](/azure/storage/common/storage-introduction) oferece uma loja de objetos massivamente escalável para objetos de dados, um serviço de sistema de ficheiros para a nuvem, uma loja de mensagens confiável e uma loja NoSQL. Para os VMs, os discos geridos e não geridos proporcionam armazenamento persistente e seguro do disco.

Aprenda a mover o [armazenamento do mainframe para o Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Cópia de segurança e recuperação

Manter o seu próprio local de recuperação de desastres pode ser uma proposta cara. O Azure tem opções fáceis de implementar e rentáveis para [backup,](/azure/backup/backup-introduction-to-azure-backup) [recuperação](/azure/site-recovery/site-recovery-overview)e [redundância](/azure/storage/common/storage-redundancy) a nível local ou regional, ou através de geo-redundância.

## <a name="azure-government-for-mainframe-migrations"></a>Governo azure para as migrações principais

Muitas entidades do sector público gostariam de mover as suas aplicações mainframe para uma plataforma mais moderna e flexível. O Governo do Microsoft Azure é um exemplo fisicamente separado da plataforma global Microsoft Azure - embalada para sistemas federais, estaduais e locais. Fornece serviços de segurança, proteção e conformidade de classe mundial especificamente para as agências governamentais dos Estados Unidos e os seus parceiros.

O Governo azure obteve uma Autoridade Provisória para Operar (P-ATO) para o Alto Impacto da FedRAMP para sistemas que precisam deste tipo de ambiente.

Para começar, baixe a [nuvem do Governo do Microsoft Azure para aplicações mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Passos seguintes

Peça aos [nossos parceiros](partner-workloads.md) que o ajudem a migrar ou realojar as suas aplicações mainframe. 

Veja também:

- [Papéis brancos sobre tópicos do mainframe](mainframe-white-papers.md)
- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Resolução de problemas](/azure/virtual-machines/troubleshooting/)
- [Desmistificando o mainframe para a migração de Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
