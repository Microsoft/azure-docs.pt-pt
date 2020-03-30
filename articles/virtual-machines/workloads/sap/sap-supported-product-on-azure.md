---
title: 'SAP on Azure: Que software SAP é suportado no Azure'
description: Explica o que o software SAP é suportado para ser implantado no Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0aaa13ff4d3331378cc17cd0cde29be43822397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460794"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>O software SAP é suportado para implementações do Azure
Este artigo descreve como pode descobrir qual o software SAP suportado para implementações do Azure e quais são as versões necessárias do sistema operativo ou lançamentos dbmS.

Avaliando se o seu software SAP atual é suportado e quais as versões DE Os e DBMS suportadas com o seu software SAP em Azure, você precisará de acesso a:

- Notas de suporte SAP
- Matriz de disponibilidade do produto SAP



## <a name="general-restrictions-for-sap-workload"></a>Restrições gerais para a carga de trabalho do SAP
Os serviços Azure IaaS que podem ser utilizados para carga de trabalho SAP estão limitados a hardware x86-64 ou x64. Não existem ofertas baseadas em Sparc ou Power CPU que se apliquem à carga de trabalho SAP. Os clientes que executam as suas aplicações em sistemas operativos proprietários de arquiteturas de hardware como o mainframe IBM ou AS400, ou onde os sistemas operativos HP-UX, Solaris ou AIX estão em uso, precisam de alterar as suas aplicações SAP, incluindo DBMS para um dos seguintes sistemas operativos:

- Servidor windows 64bit para a plataforma x86-64
- SUSE linux 64bit para a plataforma x86-64
- Chapéu vermelho Linux 64Bit para a plataforma x86-64
- Oracle Linux 64bit para a plataforma x86-64

Em combinação com o software SAP, não são suportados outros lançamentos de SO ou distribuições linux. Os detalhes exatos sobre versões e casos específicos são documentados mais tarde no documento.


## <a name="you-start-here"></a>Começa por aqui.
O ponto de partida para si é a [nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). À medida que passa por esta nota SAP de cima para baixo, várias áreas de software suportado e VMs são mostradas

A primeira secção enumera os requisitos mínimos para as libertações operacionais que são suportadas com software SAP em VMs Azure em geral. Se não estiver a atingir esses requisitos mínimos e executar lançamentos mais antigos destes sistemas operativos, precisa de atualizar a sua versão de OS para uma versão mínima ou ainda mais recentes. É correto que o Azure em geral apoie libertações mais antigas de alguns desses sistemas operativos. Mas as restrições ou lançamentos mínimos listados baseiam-se em testes e qualificações executadas e não serão alargadas mais para trás. 


> [!NOTE]
>Existem alguns tipos de VM específicos, hana grandes instâncias ou cargas de trabalho SAP que vão exigir mais lançamentos de SO mais recentes. Casos como este serão mencionados em todo o documento. Casos como este estão claramente documentados quer em notas SAP quer noutras publicações sap.

A secção que acompanha as plataformas gerais do SAP que são suportadas com as versões que são suportadas e mais importantes são os núcleos SAP que são suportados. Ele lista pilhas NetWeaver/ABAP ou Java que são suportadas e, que precisam de libertações mínimas de kernel. As pilhas ABAP mais recentes são suportadas no Azure, mas não precisam de lançamentos mínimos de kernel desde que as mudanças para o Azure foram implementadas desde o início do desenvolvimento das pilhas mais recentes

Precisa verificar:

- Se as aplicações SAP que está a executar, estão cobertas pelas versões mínimas indicadas. Caso contrário, é necessário definir uma nova versão do alvo, verificar na Matriz de Disponibilidade de Produto SAP, que sistema operativo constrói e as combinações DEDBMS são suportadas com a nova versão-alvo. Para que possa escolher a libertação do sistema operativo certo e a libertação de DBMS
- Se precisa de atualizar os seus núcleos SAP numa mudança para o Azure
- Se precisa atualizar os pacotes de suporte sAP. Especialmente pacotes de suporte base que podem ser necessários para casos em que você é obrigado a mudar-se para uma versão DBMS mais recente


A próxima secção vai mais detalhes sobre outros produtos SAP e lançamentos DBMS que são suportados pela SAP em Azure para Windows e Linux. 

> [!NOTE]
> As libertações mínimas do dbms diferente são cuidadosamente escolhidas e podem nem sempre refletir todo o espectro de DBMS liberta o suporte dos diferentes fornecedores dbmS no Azure em geral. Foram tidas em conta muitas considerações relacionadas com a carga de trabalho do SAP para definir essas libertações mínimas. Não há nenhum esforço para testar e qualificar as libertações de DBMS mais antigas. 

> [!NOTE]
> As versões mínimas listadas representam a versão mais antiga dos sistemas operativos e das versões de bases de dados. Encorajamos vivamente a utilização das mais recentes versões de sistemas operativos e de bases de dados. Em muitos casos, o sistema operativo mais recente e as publicações de bases de dados levaram em consideração o caso de utilização na nuvem pública e código adaptado para otimizar para correr em nuvem pública ou mais especificamente Azure

## <a name="oracle-dbms-support"></a>Suporte oracle DBMS
O sistema operativo, os lançamentos oracle DBMS e a funcionalidade Oracle suportadas no Azure estão especificamente listados na nota de [suporte SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). A essência dessa nota pode ser resumida como:

- A versão mínima da Oracle suportada em VMs Azure certificados para NetWeaver é Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Como sistemas operativos convidados, apenas o Windows e o Oracle Linux se qualificam. As libertações exatas do SO e as versões mínimas relacionadas com o DBMS estão listadas na nota
- O apoio da Oracle Linux estende-se também ao cliente da Oracle DBMS. Isto significa que todos os componentes SAP, como casos de diálogo do ABAP ou do Java Stack, também precisam de ser executados no Oracle Linux. Apenas componentes SAP dentro de um sistema SAP que não se ligaria ao Oracle DBMS seriam autorizados a executar um sistema operativo Linux diferente
- Oracle RAC não é suportado 
- A Oracle ASM é apoiada para alguns dos casos. Os detalhes estão listados na nota
- Os sistemas SAP não Unicode são suportados apenas com servidores de aplicações em funcionamento com o OS convidado do Windows. O sistema operativo convidado do DBMS pode ser o Oracle Linux ou windows. A razão para esta restrição é evidente ao verificar a Matriz de Disponibilidade do Produto SAP (PAM). Para a Oracle Linux, a SAP nunca lançou núcleos SAP não-Unicode

Conhecendo as versões DBMS que são suportadas com a infraestrutura Azure direcionada, você precisa verificar a Matriz de Disponibilidade de Produto SAP sobre se as libertações de OS e DBMS necessárias são suportadas com as suas versões de produto SAP que pretendia executar. 


## <a name="sap-hana-support"></a>Suporte SAP HANA
No Azure existem dois serviços, que podem ser usados para gerir a base de dados HANA:

- Máquinas Virtuais do Azure
- [Grandes instâncias hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Para executar o SAP HANA, a SAP tem condições mais e mais fortes que a infraestrutura precisa de satisfazer do que para executar a NetWeaver ou outras aplicações SAP e DBMS. Como resultado, um número menor de VMs Azure qualificam-se para executar o SAP HANA DBMS. A lista de infraestruturas azure suportadas suportadas para o SAP HANA pode ser encontrada no chamado diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> As unidades que começam com a letra 'S' são [unidades HANA Large Instances.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 

> [!NOTE]
> A SAP não tem nenhuma certificação específica dependente das principais versões SAP HANA. Contrariamente à opinião comum, o cenário de **certificação** da coluna nas [plataformas IaaS certificadas pela HANA,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)a coluna não faz qualquer declaração sobre a versão importante ou menor certificada da **HANA.** Deve assumir que todas as unidades listadas que podem ser utilizadas para HANA 1.0 e HANA 2.0, desde que o sistema operativo certificado seja suportado também por lançamentos HANA 1.0. 

Para a utilização do SAP HANA, podem ser aplicadas diferentes libertações mínimas de SO Do que para os casos gerais da NetWeaver. É necessário verificar individualmente os sistemas operativos suportados para cada unidade, uma vez que estes podem variar. Fá-lo clicando em cada unidade. Mais detalhes aparecerão. Um dos detalhes listados são os diferentes sistemas operativos suportados para esta unidade específica.

> [!NOTE]
> As unidades azure HANA Large Instance são mais restritivas com sistemas operativos suportados em comparação com os VMs Azure. Por outro lado, os VMs Azure podem impor lançamentos operacionais mais recentes como lançamentos mínimos. Isto é especialmente verdade para algumas das maiores unidades VM que exigiram alterações aos núcleos linux

Conhecendo o SISTEMA suportado para a infraestrutura Azure, você precisa verificar a nota de [suporte SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) para os lançamentos e níveis de patch exatos do SAP HANA que são suportados com as unidades Azure que você está a visar. 

> [!IMPORTANT]
> O passo de verificar os lançamentos exatos do SAP HANA e os níveis de remendos suportados é muito importante. Em muitos casos, o apoio a uma certa libertação de SO depende de um nível específico de patch dos executáveis SAP HANA.

Como sabe, as versões específicas da HANA que pode executar na infraestrutura Azure direcionada, precisa de verificar a Matriz de Disponibilidade de Produto SAP para saber se existem restrições com as versões do produto SAP que suportam as libertações do HANA que filtraste


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>VMs Azure certificados e unidades de grande instância HANA e entrada de transações comerciais
Além de avaliar as versões suportadas do sistema operativo, lançamentos dbmS e lançamentos de software sAP de suporte dependente para unidades de infraestrutura Azure, você tem a necessidade de qualificar estas unidades através de uma entrada de transação de negócios, que é expressa na unidade 'SAP' por SAP. Todo o tamanho sAP depende dos cálculos do SAPS. Avaliando os sistemas SAP existentes, normalmente pode, com a ajuda do seu fornecedor de infraestruturas, calcular o SAPS das unidades. Para a camada DBMS, bem como para a camada de aplicação. Noutros casos em que é criada uma nova funcionalidade, um exercício de dimensionamento com SAP pode revelar os números SAPS necessários para a camada de aplicação e a camada DBMS. Como fornecedor de infraestruturas, a Microsoft é obrigada a fornecer a caracterização de entrada sap das diferentes unidades que são certificadas pela NetWeaver e/ou HANA.

Para os VMs Azure, estes números de entrada SAPS estão documentados na nota de [suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Para as unidades de grande instância Azure HANA, os números de entrada sAPS são documentados na nota de [suporte SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Olhando para a nota de [suporte sap #1928533,](https://launchpad.support.sap.com/#/notes/1928533)aplicam-se as seguintes observações:

- **Para VMs M-Series Azure e Mv2-Series Azure VMs, aplicam-se diferentes lançamentos mínimos de SO do que para outros tipos de VM Azure**. A exigência de lançamentos mais recentes do OS baseia-se em alterações que os diferentes fornecedores do sistema operativo tiveram de fornecer nas suas versões do sistema operativo para permitir que os seus sistemas operativos funcionassem nos tipos de VM do Azure específicos ou otimizassem o desempenho e e otimizarem o desempenho e e o timotam o desempenho e o timotam o desempenho e o desempenho e o timotam o desempenho e o desempenho e o timotam o desempenho e o desempenho e o timotam o desempenho e o desempenho e o timotam o desempenho e entrada de carga de trabalho SAP nesses tipos vM
- Existem duas tabelas que especificam diferentes tipos de VM. A segunda tabela especifica a entrada sAPS para tipos De VM Azure que suportam apenas o Armazenamento padrão Azure. A implantação do DBMS nas unidades especificadas na segunda tabela da nota não é suportada


## <a name="other-sap-products-supported-on-azure"></a>Outros produtos SAP suportados no Azure
Em geral, o pressuposto é que, com o estado de nuvens hiperdimensionais como o Azure, a maioria do software SAP deve funcionar sem problemas funcionais em Azure. No entanto, e em oposição à visualização privada em nuvem, a SAP ainda expressa apoio aos diferentes produtos SAP explicitamente para os diferentes fornecedores de nuvem de hierpscale. Como resultado, existem diferentes notas de suporte SAP que indicam suporte para o Azure para diferentes produtos SAP. 

Para a plataforma Business Objects BI, a nota de [suporte sap #2145537](https://launchpad.support.sap.com/#/notes/2145537) apresenta uma lista de produtos SAP Business Objects suportados no Azure. Se houver questões em torno de componentes ou combinações de lançamentos de software e lançamentos de SO que parecem não estar listados ou suportados e que são mais recentes do que os lançamentos mínimos listados, você precisa abrir um pedido de suporte SAP contra o componente que você inquiri apoio para.

No caso dos Serviços de Dados de Objetos Empresariais, [a nota de suporte da SAP #22288344](https://launchpad.support.sap.com/#/notes/2288344) explica o apoio mínimo dos Serviços de Dados SAP em funcionamento no Azure. 

> [!NOTE]
> Como indicado na nota de suporte do SAP, é necessário verificar o SAP PAM para identificar o nível correto do pacote de suporte a suportar no Azure

O suporte SAP Datahub/Vora nos Serviços Azure Kubernetes (AKS) é detalhado na nota de [suporte da SAP #2464722](https://launchpad.support.sap.com/#/notes/2464722)

O suporte para SAP BPC 10.1 SP08 é descrito na nota de [suporte SAP #2451795](https://launchpad.support.sap.com/#/notes/2451795)

O suporte para a Plataforma de Comércio SAP Hybris 5.x e 6.x em Azure é detalhado no [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud)


## <a name="next-steps"></a>Passos Seguintes
Leia os próximos passos no planeamento e implementação de [Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

