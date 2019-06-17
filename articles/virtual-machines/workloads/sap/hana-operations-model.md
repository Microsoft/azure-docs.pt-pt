---
title: Modelo de operações do SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Modelo de operações do SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36a648e2d46cce96a8ff663f45ccf45326898a84
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60477890"
---
# <a name="operations-model-and-responsibilities"></a>Modelo e responsabilidades de operações

O serviço fornecido com o SAP HANA no Azure (instâncias grandes) é alinhado com os serviços de IaaS do Azure. Obtém uma instância de uma instância grande do HANA com um sistema operativo instalado que está otimizado para o SAP HANA. Como com as VMs de IaaS do Azure, a maioria das tarefas de sistema de proteção do sistema operacional, instalar software adicional, instalar o HANA, operar o sistema operacional e HANA e a atualizar o sistema operacional e HANA é sua responsabilidade. Microsoft não força a atualizações do SO ou atualizações do HANA com.

![Responsabilidades do SAP HANA no Azure (instâncias grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Conforme mostrado no diagrama, o SAP HANA no Azure (instâncias grandes) é um multi-inquilino que oferecem IaaS. Na maior parte, a divisão de responsabilidade é no limite a infraestrutura de sistema operacional. A Microsoft é responsável por todos os aspectos do serviço abaixo da linha do sistema operativo. É responsável por todos os aspectos do serviço acima da linha. O sistema operacional é sua responsabilidade. Pode continuar a utilizar mais atual no local métodos que pode ser empregadas para conformidade, segurança, gestão de aplicações, base e gerenciamento de sistema operacional. Os sistemas aparecem como se eles estão em sua rede no que respeita todas.

Este serviço está otimizado para o SAP HANA, pelo que existem áreas em que precisa trabalhar com a Microsoft para usar os recursos de infraestrutura subjacente para obter melhores resultados.

A lista seguinte fornece mais detalhes sobre cada uma das camadas e as suas responsabilidades:

**Funcionamento em rede**: Todas as redes internas para o carimbo de instância grande execução do SAP HANA. Sua responsabilidade inclui acesso a armazenamento, conectividade entre o instâncias (para Escalamento horizontal e outras funções), a conectividade para o cenário e a conectividade para o Azure onde está alojada a camada de aplicação SAP em VMs. Ele também inclui a conectividade WAN entre centros de dados do Azure para a replicação de objetivos de recuperação após desastre. Todas as redes estão particionadas por inquilino e tem a qualidade de serviço aplicado.

**Armazenamento**: O virtualizado divididos em partições para todos os volumes necessários para os servidores de SAP HANA, bem como para instantâneos de armazenamento. 

**Servidores**: Os servidores físicos dedicados para executar o bds SAP HANA atribuído aos inquilinos. Os servidores do tipo a classe de SKUs são abstraído do hardware. Com esses tipos de servidores, a configuração do servidor é recolhida e mantida em perfis, que podem ser movidos um hardware físico para outro hardware físico. Tal (manual) mover de um perfil por operações pode ser em comparação com um pouco para recuperação do serviço do Azure. Os servidores dos SKUs de classe de tipo II não oferecem esse recurso.

**SDDC**: O software de gestão que é utilizado para gerir dados centra-se como entidades definidas por software. Ele permite que a Microsoft para recursos de conjunto de dimensionamento, disponibilidade e motivos de desempenho.

**O/S**: O sistema operacional é escolher (SUSE Linux ou Red Hat Linux) que está em execução nos servidores. As imagens de sistema operacional que são fornecidos com foram fornecidas pelo fornecedor do Linux individual para a Microsoft para a execução do SAP HANA. Tem de ter uma subscrição com o fornecedor do Linux para a imagem específica otimizadas para SAP HANA. É responsável por registar as imagens com o fornecedor do sistema operacional. 

Do ponto de entregar direito de permanência pela Microsoft, é responsável por qualquer ainda mais patches do sistema operativo Linux. Esta aplicação de patches inclui pacotes adicionais que poderá ser necessário para uma instalação de SAP HANA com êxito e que não estavam incluídas pelo fornecedor do Linux específico no seu SAP HANA com otimização de imagens do sistema operacional. (Para obter mais informações, consulte a documentação de instalação do HANA e anotações de SAP do SAP.) 

É responsável por aplicação de patches de SO devido avarias ou a otimização do sistema operacional e seus drivers em relação ao hardware de servidor específico. É também responsável por segurança ou a aplicação de patches funcional do sistema operacional. 

Sua responsabilidade também inclui a monitorização e planeamento da capacidade do:

- Consumo de recursos de CPU.
- Consumo de memória.
- Volumes de disco relacionados com espaço livre, IOPS e latência.
- Tráfego de volume de rede entre a instância grande do HANA e a camada de aplicação SAP.

A infraestrutura subjacente da instância grande do HANA fornece funcionalidade de cópia de segurança e restauro do volume de sistema operacional. Usar essa funcionalidade também é sua responsabilidade.

**Middleware**: O SAP HANA de instância, principalmente. Administração, as operações e monitorização são da responsabilidade do cliente. Pode utilizar a funcionalidade fornecida para utilizar instantâneos de armazenamento para fins de recuperação e cópia de segurança e restauro. Estas capacidades são fornecidas pela infraestrutura. As suas responsabilidades também incluem o design de elevada disponibilidade ou recuperação após desastre com esses recursos, tirando partido-los e a monitorização para determinar se os instantâneos de armazenamento é executado com êxito.

**Dados**: Os dados geridos pelo SAP HANA e outros dados, como cópias de segurança de partilham de ficheiros localizados em volumes ou ficheiros. As suas responsabilidades incluem a monitorizar o espaço livre em disco e a gerir o conteúdo nos volumes. É também responsável por monitorizar a execução bem-sucedida de cópias de segurança de volumes de discos e instantâneos de armazenamento. Execução bem-sucedida de replicação de dados para sites de recuperação após desastre é da responsabilidade da Microsoft.

**Aplicações:** As instâncias de aplicações SAP ou, no caso de aplicações não pertencentes à SAP, a camada de aplicativo desses aplicativos. As suas responsabilidades incluem a implementação, administração, operações e monitorização desses aplicativos. É responsável por planeamento da capacidade do consumo de recursos de CPU, o consumo de memória, o consumo de armazenamento do Azure e o consumo de largura de banda de rede nas redes virtuais. É também responsável por planeamento da capacidade do consumo de recursos de redes virtuais para o SAP HANA no Azure (instâncias grandes).

**WANs**: As ligações que estabelecer no local para implementações do Azure para cargas de trabalho. Todos os clientes com a instância grande do HANA utilizam o Azure ExpressRoute para conectividade. Esta ligação não faz parte do SAP HANA na solução do Azure (instâncias grandes). É responsável pela configuração desta ligação.

**Arquivo**: Poderá preferir arquivar as cópias dos dados ao utilizar os seus próprios métodos nas contas de armazenamento. Arquivamento necessita de operações, os custos, conformidade e gestão. É responsável por gerar cópias do arquivo e as cópias de segurança no Azure e armazená-los de uma forma em conformidade.

Consulte a [SLA para o SAP HANA no Azure (instâncias grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Passos seguintes?**
- Consulte [arquitetura do SAP HANA (instâncias grandes) no Azure](hana-architecture.md)
