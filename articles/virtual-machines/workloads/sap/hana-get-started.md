---
title: Instalação de SAP HANA em máquinas virtuais Azure | Microsoft Docs'
description: Guia para a instalação do SAP HANA em VMs Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 54bfd7c6e691efbd754e9ea2b804e16b3e07ebbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670970"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalação de SAP HANA em máquinas virtuais Azure
## <a name="introduction"></a>Introdução
Este guia ajuda-o a apontar os recursos certos para implantar HANA em máquinas virtuais Azure com sucesso. Este guia vai indicar-lhe recursos documentais que precisa de verificar antes de instalar o SAP HANA num Azure VM. Assim, que é capaz de executar os passos certos para terminar com uma configuração suportada de SAP HANA em VMs Azure.  

> [!NOTE]
> Este guia descreve a implantação de SAP HANA em VMs Azure. Para obter informações sobre como implantar o SAP HANA em grandes instâncias HANA, consulte [Como instalar e configurar o SAP HANA (Grandes Instâncias) em Azure](./hana-installation.md).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia também assume que está familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Como instalar e operar as instâncias de aplicação SAP HANA e SAP no Azure.
* Os conceitos e procedimentos documentados em:
   * Planejamento para implantação de SAP em Azure, que inclui o planeamento da Rede Virtual Azure e o uso do Armazenamento Azure. Ver [SAP NetWeaver em Azure Virtual Machines - Guia de planeamento e implementação](./planning-guide.md)
   * Princípios de implantação e formas de implantação de VMs em Azure. Ver [implementação de Máquinas Virtuais Azure para SAP](./deployment-guide.md)
   * Conceitos de alta disponibilidade para SAP HANA como documentado em [SAP HANA alta disponibilidade para máquinas virtuais Azure](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Passo a passo antes de ser implantado
Nesta secção, estão listados os diferentes passos que é necessário executar antes de começar com a instalação do SAP HANA numa máquina virtual Azure. A ordem é enumerada e, como tal, deve ser seguida como enumerada:

1. Nem todos os cenários possíveis de implantação são suportados no Azure. Portanto, deve verificar a carga de trabalho do documento [SAP em cenários suportados pela máquina virtual Azure](./sap-planning-supported-configurations.md) para o cenário que tem em mente com a sua implementação SAP HANA. Se o cenário não estiver listado, tem de assumir que não foi testado e, consequentemente, não é apoiado
2. Assumindo que tem uma ideia aproximada sobre o seu requisito de memória para a sua implantação SAP HANA, precisa de encontrar um Azure VM adequado. Nem todos os VMs certificados para SAP NetWeaver, como documentado na [nota de suporte SAP #1928533,](https://launchpad.support.sap.com/#/notes/1928533)são certificados SAP HANA. A fonte da verdade para os VMs certificados da SAP HANA é o site [SAP HANA hardware directy](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). As unidades que começam com **S** são [unidades HANA Large Instances](./hana-overview-architecture.md) e não VMs Azure.
3. Diferentes tipos de VM Azure têm diferentes versões mínimas do sistema operativo para SUSE Linux ou Red Hat Linux. No site [SAP HANA hardware diretório,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)você precisa clicar numa entrada na lista de unidades certificadas SAP HANA para obter dados detalhados desta unidade. Além da carga de trabalho hana suportada, os lançamentos de SO que são suportados com essas unidades para SAP HANA estão listados
4. A partir das versões do sistema operativo, é necessário considerar determinadas versões mínimas do núcleo. Estas versões mínimas estão documentadas nestas notas de suporte SAP:
    - [Nota de suporte SAP #2814271 SAP HANA Backup falha no Azure com Erro de Verificação](https://launchpad.support.sap.com/#/notes/2814271)
    - [Nota de suporte SAP #2753418 Potencial Degradação do Desempenho Devido a Queda do Temporizador](https://launchpad.support.sap.com/#/notes/2753418)
    - [Nota de suporte SAP #2791572 degradação do desempenho por causa do suporte vdso em falta para hiper-V em Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Com base na versão OS suportada para o tipo de escolha da máquina virtual, é necessário verificar se a sua libertação SAP HANA desejada é suportada com essa versão do sistema operativo. Leia [a nota de suporte DO SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) para uma matriz de suporte de lançamentos SAP HANA com as diferentes versões do Sistema Operativo.
5. Como pode ter encontrado uma combinação válida do tipo Azure VM, libertação do sistema operativo e libertação SAP HANA, tem de verificar a Matriz de Disponibilidade de Produto SAP. Na Matriz de Disponibilidade SAP, pode descobrir se o produto SAP que pretende executar contra a base de dados SAP HANA é suportado.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Considerações passo a passo em VM e os so convidados
Nesta fase, é necessário passar pelos passos que implementam os VM(s) para instalar o HANA e, eventualmente, otimizar o sistema operativo escolhido após a instalação.

1. Escolheu a imagem base da galeria Azure. Se quiser construir a sua própria imagem do sistema operativo para o SAP HANA, precisa de conhecer todos os diferentes pacotes necessários para uma instalação SAP HANA bem sucedida. Caso contrário, recomenda-se a utilização das imagens SUSE e Red Hat para SAP ou SAP HANA da galeria de imagens Azure. Estas imagens incluem os pacotes necessários para uma instalação HANA bem sucedida. Com base no seu contrato de suporte com o fornecedor do sistema operativo, tem de escolher uma imagem onde traz a sua própria licença. Ou você escolhe uma imagem de SO que inclui suporte
2. Se escolheu uma imagem de SO de hóspede que requer que tenha trazido a sua própria licença, tem de registar a imagem do SO com a sua subscrição, para que possa descarregar e aplicar os patches mais recentes. Este passo vai exigir acesso público à Internet. A menos que tenha configurado o seu exemplo privado de, por exemplo, um servidor SMT em Azure.
3. Decida a configuração de rede do VM. Pode ler mais informações no documento [CONFIGURAÇÕES e operações de infraestrutura SAP HANA em Azure](./hana-vm-operations.md). Tenha em mente que não existem quotas de produção de rede que possa atribuir a cartões de rede virtuais em Azure. Como resultado, o único objetivo de direcionar o tráfego através de diferentes vNICs baseia-se em considerações de segurança. Confiamos que encontre um compromisso favorável entre a complexidade do encaminhamento de tráfego através de múltiplos vNICs e os requisitos aplicados por aspetos de segurança.
3. Aplique os remendos mais recentes no sistema operativo assim que o VM estiver implantado e registado. Registado com a sua própria assinatura. Ou caso tenha escolhido uma imagem que inclua suporte ao sistema operativo, o VM já deve ter acesso aos patches. 
4. Aplique as melodias necessárias para o SAP HANA. Estas melodias estão listadas nestas notas de suporte SAP:

    - [Nota de suporte DA SAP #2694118 - Red Hat Enterprise Linux HA Add-On em Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Nota de suporte SAP #1984787 - SUSE LINUX Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Nota de suporte SAP #2578899 - SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
    - [Nota de suporte SAP #2002167 - Red Hat Enterprise Linux 7.x: Instalação e atualização](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Nota de suporte SAP #2292690 - SAP HANA DB: Definições recomendadas de OS para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Nota de suporte SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalação e Configuração](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Nota de suporte SAP #2777782 - SAP HANA DB: Definições recomendadas de OS para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Nota de suporte SAP #2455582 - Linux: Aplicações SAP em execução compiladas com GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Nota de suporte SAP #2382421 - Otimização da Configuração da Rede no Nível HANA e OS](https://launchpad.support.sap.com/#/notes/2382421)

1. Selecione o tipo de armazenamento Azure para SAP HANA. Neste passo, você precisa decidir sobre o layout de armazenamento para a instalação SAP HANA. Você vai usar discos Azure anexados ou ações nativas da Azure NFS. Os tipos de armazenamento Azure que ou suportados e combinações de diferentes tipos de armazenamento Azure que podem ser usados, são documentados em [configurações de armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md). Tome as configurações documentadas como ponto de partida. Para sistemas de não produção, poderá configurar uma produção mais baixa ou iOPS. Para efeitos de produção, poderá ser necessário configurar um pouco mais de produção e IOPS.
2. Certifique-se de que configura o [Acelerador de Escrita Azure](../../how-to-enable-write-accelerator.md) para os seus volumes que contêm os registos de transações DBMS ou registos de redo quando estiver a utilizar M-Series ou VMs Mv2-Series. Esteja ciente das limitações para o Acelerador de Escrita, conforme documentado.
2. Verifique se [o Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado nos VM(s) implantados.

> [!NOTE]
> Nem todos os comandos nos diferentes perfis de sefina ou como descrito nas notas podem ser executados com sucesso no Azure. Os comandos que manipulariam o modo de potência dos VMs normalmente regressam com um erro, uma vez que o modo de alimentação do hardware do anfitrião Azure subjacente não pode ser manipulado.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Preparações passo a passo específicas das máquinas virtuais Azure
Uma das especificidades do Azure é a instalação de uma extensão Azure VM que fornece dados de monitorização para o Agente Anfitrião SAP. Os detalhes sobre a instalação desta extensão de monitorização estão documentados em:

-  [SAP Nota 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) discute monitorização melhorada da SAP com Os VMs linux em Azure 
-  [SAP Nota 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) discute informação sobre SAPOSCOL no Linux 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) discute métricas-chave de monitorização para SAP no Microsoft Azure
-  [Implementação de máquinas virtuais Azure para SAP NetWeaver](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalação SAP HANA
Com as máquinas virtuais Azure implantadas e os sistemas operativos registados e configurados, pode instalar o SAP HANA de acordo com a instalação SAP. Como um bom começo para chegar a esta documentação, comece com este site SAP [recursos HANA](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd)

Para configurações de escala SAP HANA utilizando discos ligados diretos de Azure Premium Storage ou disco Ultra, leia as especificidades no documento [Configurações e operações de infraestrutura SAP HANA em Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Recursos adicionais para backup SAP HANA
Para obter informações sobre como fazer o back bases de dados SAP HANA em VMs Azure, consulte:
* [Guia de backup para SAP HANA em Azure Virtual Machines](./sap-hana-backup-guide.md)
* [SAP HANA Azure Backup no nível de ficheiro](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Passos seguintes
Leia a documentação:

- [Configurações e operações de infraestrutura do SAP HANA no Azure](./hana-vm-operations.md)
- [Configurações de armazenamento da máquina virtual do Azure do SAP HANA](./hana-vm-operations-storage.md)
