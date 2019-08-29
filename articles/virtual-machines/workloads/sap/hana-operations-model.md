---
title: Modelo de operações do SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Modelo de operações do SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a8ea845dd53048766abc337a1351a408ea7f1bb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099700"
---
# <a name="operations-model-and-responsibilities"></a>Modelo e responsabilidades de operações

O serviço fornecido com SAP HANA no Azure (instâncias grandes) está alinhado com os serviços IaaS do Azure. Você Obtém uma instância de uma instância grande do HANA com um sistema operacional instalado que é otimizado para SAP HANA. Assim como nas VMs de IaaS do Azure, a maioria das tarefas de proteção do sistema operacional, instalação de software adicional, instalação do HANA, operação do sistema operacional e HANA e atualização do sistema operacional e HANA é sua responsabilidade. A Microsoft não força atualizações do sistema operacional ou atualizações do HANA para você.

![Responsabilidades de SAP HANA no Azure (instâncias grandes)](./media/hana-overview-architecture/image2-responsibilities.png)

Conforme mostrado no diagrama, SAP HANA no Azure (instâncias grandes) é uma oferta IaaS multilocatário. Na maior parte do tempo, a divisão de responsabilidade está no limite da infraestrutura do sistema operacional. A Microsoft é responsável por todos os aspectos do serviço abaixo da linha do sistema operacional. Você é responsável por todos os aspectos do serviço acima da linha. O sistema operacional é sua responsabilidade. Você pode continuar a usar os métodos locais mais atuais que você pode empregar para conformidade, segurança, gerenciamento de aplicativos, base e gerenciamento de so. Os sistemas aparecem como se estivessem em sua rede em todos os aspectos.

Esse serviço é otimizado para SAP HANA, portanto, há áreas em que você precisa trabalhar com a Microsoft para usar os recursos de infraestrutura subjacentes para obter melhores resultados.

A lista a seguir fornece mais detalhes sobre cada uma das camadas e suas responsabilidades:

**Rede**: Todas as redes internas para o carimbo de instância grande em execução SAP HANA. Sua responsabilidade inclui acesso ao armazenamento, conectividade entre as instâncias (para expansão e outras funções), conectividade com o cenário e conectividade com o Azure, onde a camada de aplicativo SAP está hospedada em VMs. Ele também inclui conectividade de WAN entre data centers do Azure para fins de recuperação de desastres. Todas as redes são particionadas pelo locatário e têm a qualidade de serviço aplicada.

**Armazenamento**: O armazenamento particionado virtualizado para todos os volumes necessários para os servidores SAP HANA, bem como para instantâneos. 

**Servidores**: Os servidores físicos dedicados para executar os bancos de SAP HANA atribuídos aos locatários. Os servidores da classe do tipo I de SKUs são abstratos pelo hardware. Com esses tipos de servidores, a configuração do servidor é coletada e mantida em perfis, que podem ser movidos de um hardware físico para outro hardware físico. Uma movimentação desse tipo (manual) de um perfil por operações pode ser comparada um pouco à recuperação de serviço do Azure. Os servidores dos SKUs de classe do tipo II não oferecem tal recurso.

**SDDC**: O software de gerenciamento usado para gerenciar data centers como entidades definidas pelo software. Ele permite que a Microsoft pool recursos para fins de escala, disponibilidade e desempenho.

**O/S**: O sistema operacional escolhido (SUSE Linux ou Red Hat Linux) em execução nos servidores. As imagens do sistema operacional que você forneceu foram fornecidas pelo fornecedor individual do Linux para a Microsoft para execução de SAP HANA. Você deve ter uma assinatura com o fornecedor do Linux para a imagem específica com otimização de SAP HANA. Você é responsável por registrar as imagens com o fornecedor do sistema operacional. 

Do ponto de transferência da Microsoft, você é responsável por qualquer aplicação de patch no sistema operacional Linux. Essa aplicação de patch inclui pacotes adicionais que podem ser necessários para uma instalação de SAP HANA bem-sucedida e que não foram incluídos pelo fornecedor específico do Linux em suas SAP HANA imagens do sistema operacional otimizadas. (Para obter mais informações, consulte documentação de instalação do HANA do SAP e notas SAP.) 

Você é responsável pela aplicação de patch do sistema operacional em devido de mau funcionamento ou otimização do sistema operacional e de seus drivers em relação ao hardware de servidor específico. Você também é responsável pela aplicação de patches de segurança ou funcional do sistema operacional. 

Sua responsabilidade também inclui monitoramento e planejamento de capacidade de:

- Consumo de recursos de CPU.
- Consumo de memória.
- Volumes de disco relacionados a espaço livre, IOPS e latência.
- Tráfego de volume de rede entre a instância grande do HANA e a camada de aplicativo SAP.

A infraestrutura subjacente do HANA em instâncias grandes fornece a funcionalidade de backup e restauração do volume do sistema operacional. O uso dessa funcionalidade também é de sua responsabilidade.

**Middleware**: A instância do SAP HANA, principalmente. Administração, operações e monitoramento são sua responsabilidade. Você pode usar a funcionalidade fornecida para usar instantâneos de armazenamento para fins de backup e restauração e recuperação de desastre. Esses recursos são fornecidos pela infraestrutura. Suas responsabilidades também incluem a criação de alta disponibilidade ou recuperação de desastres com esses recursos, o aproveitamento e o monitoramento para determinar se os instantâneos de armazenamento foram executados com êxito.

**Dados**: Seus dados gerenciados por SAP HANA e outros dados, como arquivos de backup localizados em volumes ou compartilhamentos de arquivos. Suas responsabilidades incluem o monitoramento de espaço livre em disco e o gerenciamento do conteúdo nos volumes. Você também é responsável por monitorar a execução bem-sucedida de backups de volumes de disco e instantâneos de armazenamento. A execução bem-sucedida da replicação de dados para sites de recuperação de desastre é de responsabilidade da Microsoft.

**Aplicativos** As instâncias do aplicativo SAP ou, no caso de aplicativos não SAP, a camada de aplicativo desses aplicativos. Suas responsabilidades incluem implantação, administração, operações e monitoramento desses aplicativos. Você é responsável pelo planejamento de capacidade de consumo de recursos de CPU, consumo de memória, consumo de armazenamento do Azure e consumo de largura de banda de rede em redes virtuais. Você também é responsável pelo planejamento de capacidade para consumo de recursos de redes virtuais para SAP HANA no Azure (instâncias grandes).

**WANs**: As conexões que você estabelece de locais para implantações do Azure para cargas de trabalho. Todos os clientes com o SAP HANA em instâncias grandes usam o Azure ExpressRoute para conectividade. Essa conexão não faz parte do SAP HANA na solução do Azure (instâncias grandes). Você é responsável pela configuração desta conexão.

**Arquivo morto**: Você pode preferir arquivar cópias de dados usando seus próprios métodos em contas de armazenamento. O arquivamento requer gerenciamento, conformidade, custos e operações. Você é responsável por gerar cópias de arquivamento e backups no Azure e armazená-los de forma compatível.

Consulte o [SLA para SAP Hana no Azure (instâncias grandes)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Passos seguintes?**
- Consulte a [arquitetura SAP Hana (instâncias grandes) no Azure](hana-architecture.md)
