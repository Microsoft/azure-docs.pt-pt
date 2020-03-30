---
title: Instalação de SAP HANA em máquinas virtuais Azure [ Microsoft Docs'
description: Guia para instalação de SAP HANA em VMs Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123355"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalação de SAP HANA em máquinas virtuais Azure
## <a name="introduction"></a>Introdução
Este guia ajuda-o a apontar os recursos certos para implantar hana em máquinas virtuais Azure com sucesso. Este guia vai indicar-lhe os recursos de documentação que precisa de verificar antes de instalar o SAP HANA num VM Azure. Para que seja capaz de executar os passos certos para terminar com uma configuração suportada de SAP HANA em VMs Azure.  

> [!NOTE]
> Este guia descreve as implantações da SAP HANA em VMs Azure. Para obter informações sobre como implantar o SAP HANA em grandes instâncias HANA, consulte [Como instalar e configurar o SAP HANA (Grandes Instâncias) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation).
 
## <a name="prerequisites"></a>Pré-requisitos
Este guia também assume que está familiarizado com:
* SAP HANA e SAP NetWeaver e como instalá-los no local.
* Como instalar e operar instâncias de aplicação SAP HANA e SAP no Azure.
* Os conceitos e procedimentos documentados em:
   * Planejamento para implantação de SAP no Azure, que inclui planeamento de rede virtual Azure e utilização de Armazenamento Azure. Ver [SAP NetWeaver em Máquinas Virtuais Azure - Guia](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) de planeamento e implementação
   * Princípios e formas de implantação de VMs em Azure. Ver [implantação de Máquinas Virtuais Azure para SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Conceitos de alta disponibilidade para SAP HANA como documentado em [SAP HANA alta disponibilidade para máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Passo a passo antes de implantar
Nesta secção, os diferentes passos são listados que você precisa realizar antes de começar com a instalação de SAP HANA em uma máquina virtual Azure. A ordem é enumerada e, como tal, deve ser seguida como enumerada:

1. Nem todos os cenários de implantação possíveis são apoiados no Azure. Por isso, deve verificar o documento [de carga de trabalho SAP em cenários suportados por máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) para o cenário que tem em mente com a sua implementação SAP HANA. Se o cenário não estiver listado, tem de assumir que não foi testado e, consequentemente, não é suportado.
2. Assumindo que tem uma ideia difícil sobre o seu requisito de memória para a sua implantação SAP HANA, precisa encontrar um Azure VM adequado. Nem todos os VMs certificados para sap NetWeaver, conforme documentado na nota de [suporte SAP #1928533,](https://launchpad.support.sap.com/#/notes/1928533)são certificados pela SAP HANA. A fonte da verdade para a SAP HANA certificada Azure VMs é o [site SAP HANA hardware diretório](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). As unidades que começam com **S** são [unidades HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e não VMs Azure.
3. Diferentes tipos de VM Azure têm diferentes lançamentos mínimos de sistema operativo para SUSE Linux ou Red Hat Linux. No site [SAP HANA hardware diretório,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)você precisa clicar numa entrada na lista de unidades certificadas SAP HANA para obter dados detalhados desta unidade. Além da carga de trabalho apoiada pela HANA, os lançamentos de SO que são suportados com essas unidades para sap HANA estão listados
4. A partir das libertações do sistema operativo, é necessário considerar certas libertações mínimas de kernel. Estas versões mínimas estão documentadas nestas notas de suporte SAP:
    - [Nota de suporte sAP #2814271 SAP HANA Backup falha no Azure com erro de Verificação](https://launchpad.support.sap.com/#/notes/2814271)
    - [Nota de suporte SAP #2753418 potencial degradação do desempenho devido ao recuo do timer](https://launchpad.support.sap.com/#/notes/2753418)
    - [Nota de suporte SAP #2791572 degradação do desempenho devido ao apoio vDSO desaparecido para hiper-V em Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Com base na versão OS suportada para o tipo de escolha da máquina virtual, é necessário verificar se a sua versão SAP HANA desejada é suportada com a libertação do sistema operativo. Leia a nota de [suporte SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) para uma matriz de suporte das versões SAP HANA com as diferentes versões do Sistema Operativo.
5. Como pode ter encontrado uma combinação válida do tipo Azure VM, libertação do sistema operativo e libertação do SAP HANA, precisa de verificar a Matriz de Disponibilidade de Produto SAP. Na Matriz de Disponibilidade SAP, pode descobrir se o produto SAP que pretende executar contra a sua base de dados SAP HANA é suportado.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Implantação de VM passo a passo e considerações de osso convidado
Nesta fase, é necessário percorrer os passos de implantação do VM(s) para instalar o HANA e, eventualmente, otimizar o sistema operativo escolhido após a instalação.

1. Escolheu a imagem base da galeria Azure. Se quiser construir a sua própria imagem de sistema operativo para o SAP HANA, precisa de conhecer todos os diferentes pacotes necessários para uma instalação bem sucedida do SAP HANA. Caso contrário, recomenda-se a utilização das imagens SUSE e Red Hat para SAP ou SAP HANA da galeria de imagens Azure. Estas imagens incluem os pacotes necessários para uma instalação HANA bem sucedida. Com base no seu contrato de suporte com o fornecedor do sistema operativo, precisa de escolher uma imagem onde traga a sua própria licença. Ou escolhe uma imagem de OS que inclui suporte
2. Se escolheu uma imagem de OS de hóspedes que requer que você tenha a sua própria licença, você precisa registar a imagem de OS com a sua subscrição, para que possa descarregar e aplicar os patches mais recentes. Este passo vai exigir o acesso público à Internet. A menos que tenha configurado a sua instância privada de, por exemplo, um servidor SMT em Azure.
3. Decida a configuração da rede do VM. Pode ler mais informações no documento [Configurações e operações de infraestruturas SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Tenha em mente que não existem quotas de produção de rede que possa atribuir a cartões de rede virtuais em Azure. Como resultado, o único objetivo de direcionar o tráfego através de diferentes VNICs baseia-se em considerações de segurança. Confiamos em si para encontrar um compromisso supportável entre a complexidade do tráfego através de múltiplos vNICs e os requisitos aplicados por aspetos de segurança.
3. Aplique as últimas correções no sistema operativo assim que o VM estiver implantado e registado. Registrado com a sua própria subscrição. Ou caso escolha uma imagem que inclua suporte ao sistema operativo, o VM já deve ter acesso aos patches. 
4. Aplique as melodias necessárias para o SAP HANA. Estas melodias estão listadas nestas notas de suporte SAP:

    - [Nota de apoio SAP #2694118 - Red Hat Enterprise Linux HA Add-On on Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Nota de suporte SAP #1984787 - SUSE LINUX Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Nota de suporte SAP #2578899 - SUSE Linux Enterprise Server 15: Nota de instalação](https://launchpad.support.sap.com/#/notes/2578899)
    - [Nota de suporte SAP #2002167 - Red Hat Enterprise Linux 7.x: Instalação e Upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Nota de suporte SAP #2292690 - SAP HANA DB: Definições recomendadas de OS para RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Nota de suporte SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalação e Configuração](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Nota de suporte SAP #2777782 - SAP HANA DB: Definições recomendadas de OS para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Nota de suporte SAP #2455582 - Linux: Executar aplicações SAP compiladas com GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Nota de suporte SAP #2382421 - Otimização da Configuração da Rede em HANA- e Nível OS](https://launchpad.support.sap.com/#/notes/2382421)

1. Selecione o tipo de armazenamento Azure para SAP HANA. Neste passo, você precisa decidir sobre o layout de armazenamento para a instalação SAP HANA. Você vai usar discos Azure anexados ou ações nativas azure NFS. Os tipos de armazenamento Azure que ou suportados e combinações de diferentes tipos de armazenamento Azure que podem ser usados, estão documentados em configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Tome as configurações documentadas como ponto de partida. Para sistemas não produtivos, pode ser capaz de configurar a produção mais baixa ou o IOPS. Para fins de produção, poderá ser necessário configurar um pouco mais de produção e IOPS.
2. Certifique-se de que configurou o [Acelerador de Escrita Azure](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) para os seus volumes que contêm os registos de transações DBMS ou registos de redoe quando estiver a utilizar VMs de Série M ou Mv2. Esteja ciente das limitações para o Acelerador de Escrita como documentado.
2. Verifique se o [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado nos VM(s) implantados.

> [!NOTE]
> Nem todos os comandos nos diferentes perfis de sap-tune ou como descrito nas notas podem funcionar com sucesso em Azure. Os comandos que manipulam o modo de potência dos VMs geralmente regressam com um erro, uma vez que o modo de potência do hardware de hospedeiro Azure subjacente não pode ser manipulado.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Preparações passo a passo específicas das máquinas virtuais Azure
Uma das especificidades do Azure é a instalação de uma extensão Azure VM que fornece dados de monitorização para o Agente anfitrião sAP. Os detalhes sobre a instalação desta extensão de monitorização estão documentados em:

-  [SAP Nota 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) discute monitorização reforçada com VMs Linux em Azure 
-  [SAP Nota 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) discute informação sobre SAPOSCOL no Linux 
-  [SAP Nota 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) discute métricas de monitorização chave para SAP no Microsoft Azure
-  [Implantação de Máquinas Virtuais Azure para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalação SAP HANA
Com as máquinas virtuais Azure implantadas e os sistemas operativos registados e configurados, pode instalar o SAP HANA de acordo com a instalação do SAP. Como um bom começo para chegar a esta documentação, comece com este site SAP [HANA recursos](https://www.sap.com/products/hana/implementation/resources.html)

Para configurações de escala SAP HANA utilizando discos ligados diretos de Armazenamento Premium Azure ou disco Ultra, leia as especificidades do documento [Configurações e operações de infraestrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Recursos adicionais para backup SAP HANA
Para obter informações sobre como fazer o backup sap HANA bases de dados em VMs Azure, consulte:
* [Guia de backup para SAP HANA em Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Backup SAP HANA Azure no nível de ficheiro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Passos seguintes
Leia a documentação:

- [Configurações e operações de infraestrutura do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Configurações de armazenamento da máquina virtual do Azure do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





