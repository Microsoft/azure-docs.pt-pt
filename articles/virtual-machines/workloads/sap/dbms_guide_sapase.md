---
title: Instalação DBMS de Máquinas Virtuais SAP ASE Azure para carga de trabalho SAP Microsoft Docs
description: Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce13c3bce7cdeb0f3e6dcf1f731be22d93a65587
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654604"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP

Neste documento, abrange várias áreas diferentes a ter em conta na implantação do SAP ASE em Azure IaaS. Como condição prévia para este documento, deveria ter lido o documento [Considerações para a implementação de DBMS de máquinas virtuais Azure para a carga de trabalho SAP](dbms_guide_general.md) e outros guias na carga de trabalho SAP sobre [documentação Azure](./get-started.md). Este documento abrange o SAP ASE em funcionamento no Linux e nos Sistemas Operativos Windows. A libertação mínima suportada no Azure é SAP ASE 16.0.02 (Release 16 Support Pack 2). Recomenda-se a implementação da versão mais recente do SAP e do mais recente Patch Level.  Como mínimo, recomenda-se o SAP ASE 16.0.03.07 (Release 16 Support Pack 3 Patch Level 7).  A versão mais recente do SAP pode ser encontrada na [Lista de Lançamentos 16.0 e na lista de CR.](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)

São encontradas informações adicionais sobre suporte de libertação com aplicações SAP ou localização de suporte de suporte de instalação, além da Matriz de Disponibilidade de Produto SAP nestes locais:

- [Nota de suporte sap #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Nota de suporte SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Nota de suporte sap #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Nota de suporte SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Observação: Ao longo da documentação dentro e fora do mundo SAP, o nome do produto é referenciado como Sybase ASE ou SAP ASE ou em alguns casos ambos. Para se manter consistente, utilizamos o nome **SAP ASE** nesta documentação.

## <a name="operating-system-support"></a>Suporte ao sistema operativo
A Matriz de Disponibilidade de Produto SAP contém as combinações suportadas do Sistema Operativo e do Kernel SAP para cada aplicação SAP.  As distribuições Linux SUSE 12.x, SUSE 15.x, Red Hat 7.x estão totalmente suportadas.  O Oracle Linux como sistema operativo para SAP ASE não é suportado.  Recomenda-se a utilização das versões Linux mais recentes disponíveis. Os clientes do Windows devem utilizar as versões windows Server 2016 ou Windows Server 2019.  As versões mais antigas do Windows, como o Windows 2012, são tecnicamente suportadas, mas a versão mais recente do Windows é sempre recomendada.


## <a name="specifics-to-sap-ase-on-windows"></a>Especificidades para SAP ASE no Windows
A partir do Microsoft Azure, pode migrar as suas aplicações SAP ASE existentes para Azure Virtual Machines. O SAP ASE numa Máquina Virtual Azure permite-lhe reduzir o custo total de propriedade de aplicações de implementação, gestão e manutenção de aplicações de amplitude empresarial, migrando facilmente estas aplicações para o Microsoft Azure. Com o SAP ASE numa Máquina Virtual Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis no local.

O Microsoft Azure oferece inúmeros tipos de máquinas virtuais diferentes que lhe permitem executar sistemas e paisagens SAP mais pequenos até grandes sistemas e paisagens SAP com milhares de utilizadores. O tamanho SAPS números dos diferentes SKUs certificados da SAP é fornecido na [nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

A documentação para instalar o SAP ASE no Windows pode ser encontrada no [Guia de Instalação SAP ASE para windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Bloquear páginas na memória é uma definição que evitará que o tampão de base de dados SAP ASE seja paged.  Esta definição é útil para grandes sistemas ocupados com muita memória. Contacte bc-DB-SYB para mais informações. 


## <a name="linux-operating-system-specific-settings"></a>Definições específicas do sistema operativo Linux
Em VMs Linux, executado `saptune` com perfil SAP-ASE Linux Grandes Páginas devem ser ativados por padrão e podem ser verificados com comando  

`cat /proc/meminfo` 

O tamanho da página é tipicamente 2048 KB. Para mais detalhes veja o artigo [Páginas Enormes no Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Recomendações sobre vm e estrutura de disco para implementações SAP ASE

O SAP ASE para aplicações SAP NetWeaver é suportado em qualquer tipo VM listado na [nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533) tipos típicos de VM utilizados para servidores de base de dados SAP ASE de tamanho médio incluem Esv3.  Grandes bases de dados multi-terabytes podem alavancar tipos de VM da série M. O desempenho do disco de gravação de registo de transações SAP ASE pode ser melhorado permitindo o Acelerador de Escrita da série M. O Acelerador de Escrita deve ser testado cuidadosamente com SAP ASE devido à forma como o SAP ASE executa as gravações de log.  [Reveja a nota de suporte da SAP #2816580](../../how-to-enable-write-accelerator.md) e considere realizar um teste de desempenho.  
O Write Accelerator foi concebido apenas para o disco de registo de transações. A cache de nível do disco deve ser definida para NENHUM. Não se surpreenda se o Acelerador de Escrita Azure não apresentar melhorias semelhantes às de outros DBMS. Com base na forma como o SAP ASE escreve no registo de transações, pode ser que haja pouca ou nenhuma aceleração por parte do Acelerador de Escrita Azure.
São recomendados discos separados para dispositivos de dados e dispositivos de registo.  As bases de dados do sistema sybsecurity e `saptools` não requerem discos dedicados e podem ser colocados nos discos que contêm os dados da base de dados SAP e dispositivos de registo 

![Configuração de armazenamento para SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Sistemas de ficheiros, tamanho de listras & equilíbrio IO 
A SAP ASE escreve dados sequencialmente em dispositivos de armazenamento de discos, salvo configuração em contrário. Isto significa que uma base de dados SAP ASE vazia com quatro dispositivos irá escrever dados apenas no primeiro dispositivo.  Os outros dispositivos de disco só serão escritos quando o primeiro dispositivo estiver cheio.  A quantidade de IO DE LER e ESCREVER para cada dispositivo SAP ASE é provável que seja diferente. Para equilibrar o disco IO em todos os discos Azure disponíveis, quer os Espaços de Armazenamento do Windows, quer o Linux LVM2, tem de ser utilizado. No Linux, recomenda-se a utilização do sistema de ficheiros XFS para formatar os discos. O tamanho da risca LVM deve ser testado com um teste de desempenho. O tamanho das listras KB de 128 É um bom ponto de partida. No Windows, o tamanho da unidade de atribuição NTFS (AUS) deve ser testado. 64 KB pode ser usado como um valor inicial. 

Recomenda-se configurar a expansão automática da base de dados, conforme descrito no artigo [Configurar a expansão automática do espaço de base de dados em servidor adaptativo SAP Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)  e [nota de suporte SAP #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Amostra SAP ASE em configurações de máquina virtual Azure, disco e sistema de ficheiros 
Os modelos abaixo mostram configurações de amostra tanto para Linux como para Windows. Antes de confirmar a configuração da máquina virtual e do disco, certifique-se de que as quotas de largura de banda de rede e armazenamento do VM individual são suficientes para satisfazer os requisitos de negócio. Tenha também em mente que diferentes tipos de VM Azure têm diferentes números máximos de discos que podem ser ligados ao VM. Por exemplo, um E4s_v3 VM tem um limite de armazenamento de 48 MB/seg de produção IO. Se o rendimento de armazenamento exigido pela atividade de backup da base de dados exigir mais de 48 MB/seg, então um tipo VM maior com mais largura de banda de armazenamento é inevitável. Ao configurar o armazenamento Azure, também deve ter em mente que especialmente com o [armazenamento Azure Premium](../../windows/premium-storage-performance.md) a produção e IOPS por GB de capacidade mudam. Veja mais sobre este tópico no artigo [Que tipos de disco estão disponíveis no Azure?](../../disks-types.md) As quotas para tipos específicos de VM Azure estão documentadas no artigo [Memória otimizado tamanhos](../../sizes-memory.md) de máquinas virtuais e artigos ligados a ele. 

> [!NOTE]
>  Se um sistema DBMS estiver a ser transferido do local para o Azure, recomenda-se a realização de monitorização no VM e a avaliação do CPU, memória, IOPS e produção de armazenamento. Compare os valores máximos observados com os limites de quota VM documentados nos artigos acima mencionados

Os exemplos a seguir dados são para fins ilustrativos e podem ser modificados com base nas necessidades individuais. Devido à conceção do SAP ASE, o número de dispositivos de dados não é tão crítico como em outras bases de dados. O número de dispositivos de dados detalhados neste documento é apenas um guia. 

Um exemplo de uma configuração para um pequeno Servidor DB SAP ASE com um tamanho de base de dados entre 50 GB – 250 GB, como o SAP solution Manager, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 4 | 4 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 2 x P10 (RAID0) | Armazenamento premium: 2 x P10 (RAID0)| Cache = Ler apenas |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |


Um exemplo de uma configuração para um servidor DB DB SAP ASE médio com um tamanho de base de dados entre 250 GB – 750 GB, como um sistema SAP Business Suite mais pequeno, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 8 | 8 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4 x P20 (RAID0) | Armazenamento premium: 4 x P20 (RAID0)| Cache = Ler apenas |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |

Um exemplo de uma configuração para um pequeno SERVIDOR SAP ASE DB com um tamanho de base de dados entre 750 GB – 2000 GB, como um sistema sap business suite maior, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 16 | 16 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4 x P30 (RAID0) | Armazenamento premium: 4 x P30 (RAID0)| Cache = Ler apenas |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 4 | 4| --- |
| # e tipo de discos de backup | 1 | 1 | --- |


Um exemplo de uma configuração para um pequeno SERVIDOR SAP ASE DB com um tamanho de base de dados de 2 TB+, como um sistema SAP Business Suite maior globalmente utilizado, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | M-Series (1.0 a 4.0 TB RAM)  | M-Series (1.0 a 4.0 TB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 32 | 32 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação de discos | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4+ x P30 (RAID0) | Armazenamento premium: 4+ x P30 (RAID0)| Cache = Leia apenas, considere O disco Azure Ultra |
| # e tipo de discos de log | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM, Considere O Disco Azure Ultra |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 16 | 16 | --- |
| # e tipo de discos de backup | 4 | 4 | Utilizar espaços LVM2/Armazenamento |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Backup & restaurar considerações para SAP ASE em Azure
Aumentar o número de dados e dispositivos de backup aumenta a cópia de segurança e restaura o desempenho. Recomenda-se riscar os discos Azure que estão a hospedar o dispositivo de backup SAP ASE, como mostram nas tabelas mostradas anteriormente. Deve-se ter cuidado para equilibrar o número de dispositivos de backup e discos e garantir que a produção de backup não deve exceder 40%-50% da quota total de produção de VM. Recomenda-se a utilização da compressão de backup SAP como padrão. Mais detalhes podem ser encontrados nos artigos:

- [Nota de suporte sap #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Nota de suporte SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Nota de suporte SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Não utilize a unidade D:\ ou /espaço temporário como base de dados ou destino de despejo de registo.

### <a name="impact-of-database-compression"></a>Impacto da compressão da base de dados
Em configurações onde a largura de banda de I/S pode tornar-se um fator limitativo, medidas que reduzem o IOPS podem ajudar a esticar a carga de trabalho que se pode correr num cenário iaas como o Azure. Por conseguinte, recomenda-se que se certifique de que a compressão SAP ASE é utilizada antes de enviar uma base de dados SAP existente para Azure.

A recomendação de aplicação da compressão antes do upload para a Azure é dada por várias razões:

* A quantidade de dados a enviar para a Azure é menor
* A duração da execução da compressão é mais curta assumindo que se pode usar hardware mais forte com mais CPUs ou maior largura de banda de I/O ou menos latência de I/O no local
* Tamanhos de base de dados mais pequenos podem levar a menos custos para a atribuição de discos

Data-e LOB-Compression funcionam num VM hospedado em Azure Virtual Machines como faz no local. Para obter mais informações sobre como verificar se a compressão já está a ser utilizada numa base de dados SAP ASE existente, consulte a [nota de suporte sap 1750510](https://launchpad.support.sap.com/#/notes/1750510). Para mais detalhes sobre a compressão da base de dados SAP ASE verifique a [nota de suporte da SAP #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Alta disponibilidade de SAP ASE em Azure 
O Guia de Utilizadores HADR detalha a configuração e configuração de uma solução "Always-on" de 2 nó SAP ASE.  Além disso, um terceiro nó de recuperação de desastres também é apoiado. O SAP ASE suporta muitas configurações disponíveis, incluindo o disco partilhado e o agrupamento de SO nativo (IP flutuante). A única configuração suportada no Azure é a utilização do Gestor de Falhas sem IP flutuante.  O método de endereço IP flutuante não funcionará no Azure.  O KERNEL SAP é uma aplicação "HA Aware" e conhece os servidores ASE primários e secundários do SAP. Não existem integrações estreitas entre o SAP ASE e o Azure, o equilibrador de carga interna Azure não é utilizado. Portanto, a documentação padrão SAP ASE deve ser seguida a partir do Guia de [Utilizadores SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> A única configuração suportada no Azure é a utilização do Gestor de Falhas sem IP flutuante.  O método de endereço IP flutuante não funcionará no Azure. 

### <a name="third-node-for-disaster-recovery"></a>Terceiro nó para recuperação de desastres
Além de utilizar o Always-On SAP ASE para alta disponibilidade local, é possível que queira alargar a configuração a um nó assíncronamente replicado noutra região de Azure. A documentação para tal cenário pode ser consultada [aqui.](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

## <a name="sap-ase-database-encryption--ssl"></a>Encriptação da base de dados SAP ASE & SSL 
O GESTOR DE Provisioning software SAP (SWPM) está a dar uma opção para encriptar a base de dados durante a instalação.  Se pretender utilizar encriptação, é aconselhável utilizar a Encriptação de Base de Dados Completa SAP.  Consulte os detalhes documentados em:

- [Nota de suporte da SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Nota de suporte SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Nota de suporte SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Nota de suporte SAP #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Se uma base de dados SAP ASE for encriptada, então a compressão de despejo de backup não funcionará. Consulte também [a nota de suporte da SAP #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP ASE na lista de verificação de implantação da Azure
 
- Implementar SAP ASE 16.0.03.07 ou superior
- Atualização para a versão mais recente e patches de FaultManager e SAPHostAgent
- Implementar nos mais recentes SISTEMAS certificados disponíveis como Windows 2019, Suse 15.1 ou Redhat 7.6 ou superior
- Utilização VMs certificados SAP – SKUs VM de alta memória, tais como Es_v3 ou para os skus VM da série M-Series X de grande porte, são recomendados
- Combine o disco IOPS e a quota total de produção agregada de VM do VM com o design do disco.  Implementar um número suficiente de discos
- Discos agregados que usam espaços de armazenamento do Windows ou Linux LVM2 com tamanho de listras correto e sistema de ficheiros
- Criar um número suficiente de dispositivos para fins de dados, registos, temperaturas e backup
- Considere usar o UltraDisk para sistemas x-grandes 
- Executar `saptune` SAP-ASE no Linux OS 
- Proteja a base de dados com encriptação DB – guarde manualmente as chaves no Cofre da Chave Azure 
- Complete o [SAP na Lista de Verificação Azure](./sap-deployment-checklist.md) 
- Configurar backup de registo e backup completo 
- Teste HA/DR, backup e restauro e execute teste de & de volume de stress 
- Confirme que a extensão automática da base de dados está a funcionar 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilização do DBACockpit para monitorizar as instâncias da base de dados
Para os sistemas SAP, que estão a usar o SAP ASE como plataforma de base de dados, o DBACockpit está acessível como janelas de navegador incorporadas na transação DBACockpit ou como Webdynpro. No entanto, a funcionalidade completa de monitorização e administração da base de dados está disponível apenas na implementação Webdynpro do DBACockpit.

Tal como acontece com os sistemas no local, são necessários vários passos para permitir todas as funcionalidades SAP NetWeaver utilizadas pela implementação Webdynpro do DBACockpit. Siga [a nota de suporte SAP #1245200](https://launchpad.support.sap.com/#/notes/1245200) para permitir a utilização de webdynpros e gerar os necessários. Ao seguir as instruções nas notas acima, também configura o Gestor de Comunicação da Internet , `ICM` juntamente com as portas a utilizar para ligações http e https. A definição predefinição para http parece:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e as ligações geradas na transação DBACockpit são semelhantes a:

> https: \/ / \<fullyqualifiedhostname> :44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> :8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dependendo da forma como a Máquina Virtual Azure que hospeda o sistema SAP está ligada ao seu AD e DNS, tem de se certificar de que o ICM está a utilizar um nome de hospedeiro totalmente qualificado que pode ser resolvido na máquina onde está a abrir o DBACockpit. Consulte [a nota de suporte SAP #773830](https://launchpad.support.sap.com/#/notes/773830) para entender como o ICM determina o nome de anfitrião totalmente qualificado com base nos parâmetros de perfil e definir o parâmetro icm/host_name_full explicitamente se necessário.

Se implementou o VM num cenário Cloud-Only sem conectividade entre as instalações e o Azure, tem de definir um endereço IP público e um `domainlabel` . O formato do nome DNS público do VM parece:

> `<custom domainlabel`>. `<azure region`>.cloudapp.azure.com
> 
> 

Mais detalhes relacionados com o nome DNS podem ser encontrados [aqui][virtual-machines-azurerm-versus-azuresm].

Definindo o parâmetro de perfil SAP icm/host_name_full ao nome DNS do Azure VM a ligação pode parecer semelhante a:

> https: \/ /mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Neste caso, tem de se certificar de:

* Adicione as regras de entrada ao Grupo de Segurança da Rede no portal Azure para as portas TCP/IP utilizadas para comunicar com o ICM
* Adicione as regras de entrada à configuração do Windows Firewall para as portas TCP/IP utilizadas para comunicar com o ICM

Para uma importação automatizada de todas as correções disponíveis, recomenda-se aplicar periodicamente a coleção de correção SAP Nota aplicável à sua versão SAP:

* [Nota de suporte sap #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Nota de suporte SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Nota de suporte SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Mais informações sobre o Cockpit DBA para SAP ASE podem ser encontradas nas seguintes notas SAP:

* [Nota de suporte SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Nota de suporte SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Nota de suporte SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Nota de suporte SAP #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Nota de suporte SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Nota de suporte SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Nota de suporte SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Nota de suporte SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Links úteis, notas & brancos para SAP ASE
A página inicial do [SAP ASE 16.0.03.07 A documentação](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) dá ligações a vários documentos dos quais os documentos de:

- SAP ASE Learning Journey - Administration & Monitoring
- SAP ASE Learning Journey - Instalação & Upgrade

são úteis. Outro documento útil é [as aplicações SAP em SAP Adaptive Server Enterprise Best Practices for Migration and Runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Outras notas de suporte úteis da SAP são:

- [Nota de suporte sap #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Nota de suporte SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Nota de suporte SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Nota de suporte SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Nota de suporte SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Nota de suporte SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Nota de suporte SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Nota de suporte SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Nota de suporte SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Nota de suporte sap #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Nota de suporte SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota de suporte sap #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Outras informações são publicadas em 

- [Aplicações SAP na EMPRESA de Servidor Adaptativo SAP](https://community.sap.com/topics/applications-on-ase)
- [Infocenter SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Always-on com 3ª Configuração do Nó DR](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Uma newsletter mensal é publicada através da [nota de apoio da SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Passos seguintes
Verifique o artigo [Cargas de trabalho SAP no Azure: lista de verificação de planeamento e implantação](./sap-deployment-checklist.md)
