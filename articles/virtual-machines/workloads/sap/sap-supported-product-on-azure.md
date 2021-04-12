---
title: 'SAP on Azure: O software SAP é suportado no Azure'
description: Explica o que o software SAP é suportado para ser implantado no Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673537"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Qual o software SAP suportado para implementações do Azure
Este artigo descreve como pode descobrir o que o software SAP é suportado para implementações Azure e quais são as libertações necessárias do sistema operativo ou as versões DBMS.

Avaliando, se o seu software SAP atual é suportado e quais as versões OS e DBMS suportadas com o seu software SAP em Azure, vai precisar de acesso a:

- Notas de suporte SAP
- Matriz de disponibilidade de produto SAP



## <a name="general-restrictions-for-sap-workload"></a>Restrições gerais para a carga de trabalho SAP
Os serviços Azure IaaS que podem ser utilizados para a carga de trabalho SAP estão limitados a hardware x86-64 ou x64. Não existe ofertas baseadas em Sparc ou Power CPU que se apliquem à carga de trabalho SAP. Os clientes que executam as suas aplicações em sistemas operativos proprietários de arquiteturas de hardware como o mainframe IBM ou AS400, ou onde os sistemas operativos HP-UX, Solaris ou AIX estão a ser utilizados, precisam de alterar as suas aplicações SAP, incluindo dBMS para um dos seguintes sistemas operativos:

- Servidor Windows 64bit para a plataforma x86-64
- SUSE linux 64bit para a plataforma x86-64
- Chapéu vermelho Linux 64Bit para a plataforma x86-64
- Oracle Linux 64bit para a plataforma x86-64

Em combinação com o software SAP, não são suportadas outras versões de SO ou de distribuição Linux. Os detalhes exatos sobre versões e casos específicos são documentados mais tarde no documento.


## <a name="you-start-here"></a>Começa aqui.
O ponto de partida para si é [a nota de apoio sap #1928533](https://launchpad.support.sap.com/#/notes/1928533). Ao passar por esta nota SAP de cima para baixo, são mostradas várias áreas de software suportado e VMs

A primeira secção enumera os requisitos mínimos para as versões de funcionamento suportadas com o software SAP em VMs Azure em geral. Se não estiver a atingir esses requisitos mínimos e executar versões mais antigas destes sistemas operativos, tem de atualizar a sua versão de SO para uma libertação mínima ou ainda mais recente. É correto que o Azure, em geral, apoie as libertações mais antigas de alguns desses sistemas operativos. Mas as restrições ou libertações mínimas, tal como listadas, baseiam-se em testes e qualificações executados e não serão alargadas mais para trás. 


> [!NOTE]
>Existem alguns tipos específicos de VM, HANA Large Instances ou cargas de trabalho SAP que vão exigir lançamentos de SO mais recentes. Casos como este serão mencionados em todo o documento. Casos como este estão claramente documentados quer em notas SAP, quer noutras publicações SAP.

A secção seguinte lista as plataformas GERAIS SAP que são suportadas com os lançamentos suportados e mais importantes os núcleos SAP que são suportados. Ele lista pilhas NetWeaver/ABAP ou Java que são suportadas E, que precisam de lançamentos mínimos de kernel. As pilhas ABAP mais recentes são suportadas no Azure, mas não precisam de lançamentos mínimos de kernel uma vez que as alterações para o Azure foram implementadas desde o início do desenvolvimento das pilhas mais recentes

Tem de verificar:

- Quer as aplicações SAP que está a executar, estão cobertas pelos lançamentos mínimos indicados. Caso contrário, tem de definir um novo lançamento-alvo, verificar na Matriz de Disponibilidade de Produto SAP, quais as construções do sistema operativo e as combinações DBMS são suportadas com a nova versão do alvo. Para que possa escolher a versão certa do sistema operativo e a versão DBMS
- Se precisa atualizar os seus núcleos SAP numa mudança para Azure
- Se precisa de atualizar pacotes de suporte SAP. Pacotes de suporte especialmente base que podem ser necessários para os casos em que você é obrigado a mudar para uma versão DBMS mais recente


A próxima secção vai para mais detalhes sobre outros produtos SAP e lançamentos DBMS que são suportados pela SAP on Azure para Windows e Linux. 

> [!NOTE]
> As libertações mínimas dos diferentes DBMS são cuidadosamente escolhidas e podem nem sempre refletir todo o espectro de DBMS que liberta o suporte dos diferentes fornecedores DBMS sobre o Azure em geral. Foram tomadas em consideração muitas considerações relacionadas com a carga de trabalho da SAP para definir essas libertações mínimas. Não há nenhum esforço para testar e qualificar lançamentos DBMS mais antigos. 

> [!NOTE]
> As versões mínimas listadas representam a versão mais antiga dos sistemas operativos e as versões de bases de dados. Encorajamos vivamente a utilização das mais recentes versões do sistema operativo e das versões de bases de dados. Em muitos casos, as versões mais recentes do sistema operativo e da base de dados levaram em consideração o caso de funcionamento em nuvem pública e código adaptado para otimizar para correr em nuvem pública ou mais especificamente em Azure

## <a name="oracle-dbms-support"></a>Suporte da Oracle DBMS
O sistema operativo, as versões Da Oracle DBMS e a funcionalidade Oracle suportada no Azure estão especificamente listadas na [nota de suporte SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). A essência dessa nota pode resumir-se como:

- Lançamento mínimo da Oracle suportado em VMs Azure certificados para NetWeaver é Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Como sistemas operativos de hóspedes apenas o Windows e o Oracle Linux se qualificam. As versões exatas do SISTEMA e das versões mínimas de DBMS estão listadas na nota
- O apoio da Oracle Linux estende-se também ao cliente Da Oracle DBMS. Isto significa que todos os componentes SAP, como instâncias de diálogo da ABAP ou Java Stack, também precisam de funcionar no Oracle Linux. Apenas componentes SAP dentro de um sistema SAP que não se ligariam ao DBMS do Oracle seriam autorizados a executar um sistema operativo Linux diferente
- Oracle RAC não é apoiado 
- A Oracle ASM é apoiada em alguns dos casos. Os detalhes estão listados na nota
- Os sistemas SAP não-Unicode são suportados apenas com servidores de aplicações em execução com o SISTEMA convidado do Windows. O sistema operativo de hóspedes do DBMS pode ser Oracle Linux ou Windows. A razão desta restrição é aparente ao verificar a Matriz de Disponibilidade de Produto SAP (PAM). Para a Oracle Linux, a SAP nunca lançou núcleos SAP não-Unicode

Conhecendo as versões DBMS suportadas com a infraestrutura Azure direcionada, é necessário verificar a Matriz de Disponibilidade de Produto SAP sobre se as versões de OS e DBMS necessárias são suportadas com as suas versões de produto SAP que pretendia executar. 

## <a name="oracle-linux"></a>Oracle Linux
A pergunta mais proeminente em torno da Oracle Linux é se o SAP suporta o núcleo do Chapéu Vermelho que também faz parte integrante do Oracle Linux. Para mais detalhes, leia [a nota de suporte da SAP #1565179](https://launchpad.support.sap.com/#/notes/1565179).

## <a name="other-database-than-sap-hana"></a>Outra base de dados que não a SAP HANA
O suporte de bases de dados não HANA para a carga de trabalho SAP está documentado na [nota de suporte da SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).


## <a name="sap-hana-support"></a>Suporte SAP HANA
Em Azure existem dois serviços, que podem ser usados para executar a base de dados HANA:

- Máquinas Virtuais do Microsoft Azure
- [Grandes Instâncias HANA](./hana-overview-architecture.md)

Para executar SAP HANA, a SAP tem condições mais e mais fortes que a infraestrutura precisa de satisfazer do que para executar aplicações NetWeaver ou outras aplicações SAP e DBMS. Como resultado, um número menor de VMs Azure qualificam-se para executar o SAP HANA DBMS. A lista de infraestruturas Azure suportadas apoiadas para SAP HANA pode ser encontrada no chamado [diretório de hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> As unidades que começam com a letra 'S' são [unidades HANA Large Instances.](./hana-overview-architecture.md) 

> [!NOTE]
> A SAP não tem certificação específica dependente das principais versões SAP HANA. Contrariamente à opinião comum, o **cenário de certificação** da coluna nas [plataformas iaaS certificadas pela HANA,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)a coluna não faz **qualquer declaração sobre o HANA maior ou menor lançamento certificado.** Tem de assumir que todas as unidades listadas que podem ser utilizadas para HANA 1.0 e HANA 2.0, desde que as versões certificadas do sistema operativo para as unidades específicas sejam suportadas também por versões HANA 1.0. 

Para a utilização de SAP HANA, podem ser aplicadas diferentes versões de SO mínimas do que para os casos gerais do NetWeaver. É necessário verificar individualmente os sistemas operativos suportados por cada unidade, uma vez que estes podem variar. Fá-lo clicando em cada unidade. Mais detalhes aparecerão. Um dos detalhes listados são os diferentes sistemas operativos suportados para esta unidade específica.

> [!NOTE]
> As unidades Azure HANA Large Instance são mais restritivas com sistemas operativos suportados em comparação com os VMs Azure. Por outro lado, os VM da Azure podem impor as versões operacionais mais recentes como libertações mínimas. Isto é especialmente verdade para algumas das maiores unidades de VM que exigiam alterações nos núcleos linux

Conhecendo o SISTEMA suportado para a infraestrutura Azure, é necessário verificar a [nota de suporte da SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) para as versões e níveis de remendos exatos do SAP HANA que são suportados com as unidades Azure que está a ser alvo. 

> [!IMPORTANT]
> O passo de verificar os lançamentos e os níveis de remendos exatos do SAP HANA suportados é muito importante. Em muitos casos, o suporte de uma determinada libertação de SO depende de um nível específico de correção dos executáveis SAP HANA.

Como sabe as versões específicas da HANA que pode executar na infraestrutura Azure direcionada, precisa de verificar na Matriz de Disponibilidade de Produto SAP para saber se existem restrições com as versões do produto SAP que suportam as libertações HANA que filtraste


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Azure VMs certificados e unidades de grande instância HANA e produção de transações comerciais
Além de avaliar as versões suportadas do sistema operativo, as libertações de DBMS e o suporte dependente lançamentos de software SAP para unidades de infraestrutura Azure, tem a necessidade de qualificar estas unidades por produção de transações comerciais, que é expressa na unidade 'SAP' da SAP. Todo o tamanho sap depende dos cálculos SAPS. Avaliando os sistemas SAP existentes, normalmente pode, com a ajuda do seu fornecedor de infraestruturas, calcular o SAPS das unidades. Para a camada DBMS, bem como para a camada de aplicação. Noutros casos em que é criada uma nova funcionalidade, um exercício de dimensionamento com SAP pode revelar os números SAPS necessários para a camada de aplicação e a camada DBMS. Como fornecedor de infraestrutura, a Microsoft é obrigada a fornecer a caracterização de produção SAP das diferentes unidades que são certificadas pela NetWeaver e/ou HANA.

Para os VMs Azure, estes números de produção SAPS estão documentados na [nota de suporte sap #1928533](https://launchpad.support.sap.com/#/notes/1928533). Para as unidades de Grande Instância Azure HANA, os números de produção DO SAPS são documentados na [nota de suporte sap #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Analisando a [nota de apoio da SAP #1928533,](https://launchpad.support.sap.com/#/notes/1928533)aplicam-se as seguintes observações:

- **Para os VMs M-Series Azure e Mv2-Series VMs Azure, aplicam-se diferentes versões de SO mínimas do que para outros tipos de VM Azure**. A exigência de versões de OS mais recentes baseia-se em alterações que os diferentes fornecedores do sistema operativo tiveram de fornecer nas suas versões do sistema operativo para permitir que os seus sistemas operativos funcionassem nos tipos específicos de VM do Azure ou otimizassem o desempenho e a produção da carga de trabalho SAP nesses tipos de VM
- Existem duas tabelas que especificam diferentes tipos de VM. O segundo quadro especifica a produção DE SAPS para os tipos Azure VM que suportam apenas o armazenamento padrão Azure. A implantação de DBMS nas unidades especificadas na segunda tabela da nota não é suportada


## <a name="other-sap-products-supported-on-azure"></a>Outros produtos SAP apoiados no Azure
Em geral, o pressuposto é que com o estado de nuvens de hiperescala como o Azure, a maioria do software SAP deve funcionar sem problemas funcionais em Azure. No entanto, e em frente à visualização em nuvem privada, a SAP ainda expressa o apoio aos diferentes produtos SAP explicitamente para os diferentes fornecedores de nuvem hyerpscale. Como resultado, existem diferentes notas de suporte SAP que indicam suporte para a Azure para diferentes produtos SAP. 

Para a plataforma Business Objects BI, [a nota de suporte da SAP #2145537](https://launchpad.support.sap.com/#/notes/2145537) apresenta uma lista de produtos SAP Business Objects suportados no Azure. Se houver questões em torno de componentes ou combinações de lançamentos de software e lançamentos de SO que parecem não estar listados ou suportados e que são mais recentes do que as versões mínimas listadas, precisa de abrir um pedido de suporte SAP contra o componente para o qual solicita apoio.

Para os Serviços de Dados de Objetos Empresariais, [a nota de suporte da SAP #22288344](https://launchpad.support.sap.com/#/notes/2288344) explica o apoio mínimo dos Serviços de Dados SAP em execução no Azure. 

> [!NOTE]
> Como indicado na nota de suporte da SAP, é necessário consultar o SAP PAM para identificar o nível correto do pacote de suporte a apoiar no Azure

O suporte SAP Datahub/Vora nos Serviços Azure Kubernetes (AKS) é detalhado na [nota de suporte da SAP #2464722](https://launchpad.support.sap.com/#/notes/2464722)

O suporte para SAP BPC 10.1 SP08 é descrito na [nota de suporte da SAP #2451795](https://launchpad.support.sap.com/#/notes/2451795)

O suporte para a Plataforma de Comércio de Hybris SAP em Azure é detalhado na [Documentação Hybris.](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) A partir de DBMS suportado para a Plataforma de Comércio de Hybris SAP, ele lista como:

- SQL Server e Oracle na plataforma do sistema operativo Windows. Aplicam-se as mesmas libertações mínimas que para o SAP NetWeaver. Consulte [a nota de apoio da SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533) para mais detalhes
- SAP HANA em Chapéu Vermelho e SUSE Linux. Os tipos de VM certificados DA SAP HANA são necessários como documentados anteriormente [neste documento](#sap-hana-support). A Plataforma de Comércio SAP (Hybris) é considerada carga de trabalho OLTP
- SQL Azure DB a partir da versão 1811 da Plataforma de Comércio SAP (Hybris)




## <a name="next-steps"></a>Passos Seguintes
Leia os próximos passos no planeamento e implementação de [Máquinas Virtuais Azure para o SAP NetWeaver](./planning-guide.md)
