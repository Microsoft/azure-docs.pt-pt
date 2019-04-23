---
title: Mainframe realojamento em máquinas virtuais do Azure
description: Realojar suas cargas de trabalho de mainframe como sistemas baseados no IBM Z utilizar máquinas virtuais (VMs) no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004419"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe realojamento em máquinas virtuais do Azure

Migrar cargas de trabalho desde o mainframe o ambientes na cloud permite-lhe modernize a sua infraestrutura e, muitas vezes, poupar nos custos. Muitas cargas de trabalho podem ser transferidas para o Azure com apenas alterações de código pequenas, como atualizar os nomes das bases de dados.

Em termos gerais, o termo *mainframe* significa que um sistema de computador grandes. Especificamente, a grande maioria atualmente em utilização são servidores de sistema Z da IBM ou sistemas de plug-compatible do IBM que executem MVS, DOS, VSE, os/390 ou z/OS.

Uma máquina virtual do Azure (VM) é utilizada para isolar e gerir os recursos para uma aplicação específica numa única instância. Mainframes IBM z/OS, como o utilizam partições lógicas (LPARS) para esta finalidade. Um mainframe poderá utilizar um LPAR para uma região do CICS com programas de COBOL associados e um LPAR separado para a base de dados Db2 da IBM. Um típico [aplicação de n camadas no Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) implementa as VMs do Azure numa rede virtual que pode ser segmentada em sub-redes para cada camada.

VMs do Azure podem executar ambientes de emulação de mainframe e compiladores que suportam cenários de migração lift-and-shift. Desenvolvimento e teste, muitas vezes, estão entre as cargas de trabalho de primeira para migrar de um mainframe para um ambiente de dev/test do Azure. Componentes de servidor comuns que poderá emular incluem o processo de transações online (OLTP), batch e os sistemas de ingestão de dados, como mostra a figura seguinte.

![Ambientes de emulação no Azure permitem-lhe executar sistemas baseados em z/OS.](media/01-overview.png)

Algumas cargas de trabalho de mainframe podem ser migradas para o Azure com relativa facilidade, enquanto outros podem ser rehosted no Azure através de uma solução de parceiro. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, o [Centro de migração de Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) pode ajudar.

## <a name="mainframe-migration"></a>Migração de mainframe

Realojar, recompilar, substituir ou extinguir? IaaS ou PaaS? Para determinar a estratégia de migração de direita para a sua aplicação de mainframe, consulte a [migração de Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) guia no Centro de arquitetura do Azure.

## <a name="micro-focus-rehosting-platform"></a>Plataforma de realojamento micro de foco

Micro Focus Enterprise Server é uma das maior mainframe realojamento plataformas disponíveis. Pode usá-lo a executar cargas de trabalho z/OS num x86 mais barato plataforma no Azure.

Para começar:

- [Instalar o servidor de Enterprise e Enterprise Developer no Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configurar a CICS BankDemo para programador empresarial no Azure](./microfocus/demo.md)
- [Executar o servidor empresarial num contentor do Docker no Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame no Azure

TmaxSoft OpenFrame é uma solução de rehosting de popular mainframe utilizada em cenários de migração lift-and-shift. Um ambiente de OpenFrame no Azure é adequado para o desenvolvimento, demonstrações, teste ou cargas de trabalho de produção.

Para começar:

- [Introdução ao TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Transferir o ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD & T 12.0

Desenvolvimento do IBM Z e de ambiente de teste (IBM zD & T) configura um ambiente de não produção no Azure que pode utilizar para o desenvolvimento, teste e demonstrações de aplicativos baseados em z/OS.

O ambiente de emulação no Azure pode alojar diferentes tipos de instâncias de Z por meio do aplicativo que os desenvolvedores controlado distribuições (ADCDs). Pode executar zD & T Personal Edition, zD Sysplex paralela de T e zD & T Enterprise Edition no Azure e no Azure Stack.

Para começar:

- [Configurar a IBM zD & T 12.0 no Azure](./ibm/install-ibm-z-environment.md)
- [Configurar ADCD no zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale no Azure

O ambiente de pureScale IBM DB2 fornece um cluster de base de dados para o Azure. Não é idêntico para o ambiente original, mas fornece disponibilidade e dimensionamento semelhantes como IBM DB2 para z/sistema operacional em execução numa configuração de Sysplex paralela.

Para começar a utilizar, veja [pureScale IBM DB2 no Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerações

Quando migra cargas de trabalho de mainframe para a infraestrutura do Azure como um serviço (IaaS), pode escolher entre vários tipos de recursos informáticos a pedido e dimensionáveis, incluindo as VMs do Azure. O Azure oferece diversas [Linux](/azure/virtual-machines/linux/overview) e [Windows](/azure/virtual-machines/windows/overview) VMs.

### <a name="compute"></a>Computação

Poder de computação do Azure se compara de maneira favorável à capacidade de um mainframe. Se estiver pensando em mudar uma carga de trabalho de mainframe para o Azure, compare a métrica de mainframe de instruções de um milhão por segundo (MIPS) para as CPUs virtuais. 

Saiba como [mover a computação de mainframe para o Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Elevada disponibilidade e ativação pós-falha

O Azure oferece alocação com base em contratos de nível de serviço (SLAs). Disponibilidade de vários nines é a predefinição e SLAs podem ser otimizados com a replicação do local ou baseadas na geografia dos serviços. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

Com IaaS do Azure, como uma VM, as funções de sistema específico fornecem suporte de ativação pós-falha — por exemplo, clustering de ativação pós-falha instâncias e [conjuntos de disponibilidade](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Quando utiliza a plataforma do Azure como um recursos de serviço (PaaS), a plataforma lida com ativação pós-falha automaticamente. Os exemplos incluem [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) e [do Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Escalabilidade

Normalmente, mainframes aumentar verticalmente, ao escalamento horizontal de ambientes de cloud. O Azure oferece diversas [Linux](/azure/virtual-machines/linux/sizes) e [Windows](/azure/virtual-machines/windows/sizes) tamanhos para atender às suas necessidades. A cloud também aumenta ou para baixo para especificações de utilizador exata de correspondência. Capacidade de computação, armazenamento e serviços [dimensionamento](/azure/architecture/best-practices/auto-scaling) a pedido num modelo de faturação baseada na utilização.

### <a name="storage"></a>Armazenamento

Na nuvem, tem uma variedade de opções de armazenamento flexível e escalável e paga apenas aquilo de que precisa. [O armazenamento do Azure](/azure/storage/common/storage-introduction) oferece um armazenamento de objetos altamente dimensionável para objetos de dados, um serviço de sistema de ficheiros para a cloud, um arquivo de mensagens confiável e um arquivo de NoSQL. Para as VMs, discos geridos e fornecem armazenamento de disco persistentes e seguros.

Saiba como [mover o armazenamento de mainframe para o Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Cópia de segurança e recuperação

Manter o seu próprio site de recuperação após desastre pode ser uma proposta cara. Opções de fácil implementação e econômicas para o Azure tem [cópia de segurança](/azure/backup/backup-introduction-to-azure-backup), [recuperação](/azure/site-recovery/site-recovery-overview), e [redundância](/azure/storage/common/storage-redundancy) em níveis de locais ou regionais, ou através de redundância geográfica.

## <a name="azure-government-for-mainframe-migrations"></a>O Azure Government para migrações de mainframe

Muitas entidades do setor público muito mover seus aplicativos de mainframe para uma plataforma mais moderno e flexível. O Microsoft Azure Government é uma instância fisicamente separada da plataforma Microsoft Azure global — empacotados para sistemas governamentais federais, estaduais e locais. Ele fornece segurança de classe mundial, proteção e os serviços de conformidade especificamente para agências governamentais dos Estados Unidos e respetivos parceiros.

O Azure Government de obter uma autoridade provisória para operar (P-ATO) por FedRAMP elevado impacto para os sistemas que necessitam deste tipo de ambiente.

Para começar a utilizar, transfira [cloud do Microsoft Azure Government para aplicações de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Passos Seguintes

Peça ao nosso [parceiros](partner-workloads.md) para ajudar a migrar ou realojar aplicações de mainframe. Para obter orientações detalhadas sobre como escolher uma solução de parceiro, consulte a [Alliance de modernização de plataforma](https://www.platformmodernization.org/pages/mainframe.aspx) Web site.

Veja também:

- [White papers sobre tópicos de mainframe](mainframe-white-papers.md)
- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Resolução de problemas](/azure/virtual-machines/troubleshooting/)
- [Desmistificando do mainframe para a migração do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
