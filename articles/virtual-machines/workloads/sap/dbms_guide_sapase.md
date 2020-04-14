---
title: Implantação dbms das máquinas virtuais SAP ASE Azure para carga de trabalho SAP [ SAP ASE Azure Virtual Machines) para carga de trabalho SAP Microsoft Docs
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
ms.openlocfilehash: 25d911869c95baba6ac9db3b893292e702e9c0e9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273210"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP

Neste documento, abrange várias áreas diferentes a ter em conta na implementação da SAP ASE no Azure IaaS. Como condição prévia para este documento, deve ter lido o documento Considerações para a implantação de [DBMS de Máquinas Virtuais Azure para carga de trabalho SAP](dbms_guide_general.md) e outros guias na carga de [trabalho sap sobre documentação Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Este documento abrange a SAP ASE em execução no Linux e nos Sistemas Operativos Windows. A versão mínima suportada no Azure é SAP ASE 16.0.02 (Lançamento 16 Suporte Pack 2). Recomenda-se a implementação da versão mais recente do SAP e do mais recente Patch Level.  Como um SAP ASE mínimo 16.0.03.07 (Lançamento 16 Suporte Pack 3 Patch Level 7) é recomendado.  A versão mais recente do SAP pode ser encontrada na Agenda de Lançamento da [ASE 16.0 e na lista de CR Informações.](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)

São encontradas informações adicionais sobre o suporte de libertação com aplicações SAP ou localização de meios de instalação, além da Matriz de Disponibilidade de Produto SAP nestes locais:

- [Nota de apoio SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Nota de apoio SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Nota de apoio SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Nota de apoio SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Observação: Ao longo da documentação dentro e fora do mundo SAP, o nome do produto é referenciado como Sybase ASE ou SAP ASE ou em alguns casos ambos. Para nos mantermos consistentes, utilizamos o nome **SAP ASE** nesta documentação.

## <a name="operating-system-support"></a>Suporte do sistema operativo
A Matriz de Disponibilidade de Produto SAP contém o Sistema Operativo suportado e as combinações SAP Kernel para cada aplicação SAP.  Distribuição de linux SUSE 12.x, SUSE 15.x, Chapéu Vermelho 7.x estão totalmente suportados.  O Oracle Linux como sistema operativo para a SAP ASE não é suportado.  Recomenda-se a utilização dos mais recentes lançamentos linux disponíveis. Os clientes windows devem utilizar os lançamentos do Windows Server 2016 ou do Windows Server 2019.  As versões mais antigas do Windows, como o Windows 2012, são tecnicamente suportadas, mas a versão mais recente do Windows é sempre recomendada.


## <a name="specifics-to-sap-ase-on-windows"></a>Especificidades da SAP ASE no Windows
A partir do Microsoft Azure, pode migrar as suas aplicações SAP ASE existentes para máquinas virtuais Azure. A SAP ASE numa Máquina Virtual Azure permite-lhe reduzir o custo total de propriedade de implementação, gestão e manutenção de aplicações de amplitude empresarial, migrando facilmente estas aplicações para o Microsoft Azure. Com a SAP ASE numa Máquina Virtual Azure, administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis no local.

O Microsoft Azure oferece inúmeros tipos de máquinas virtuais diferentes que lhe permitem executar os mais pequenos sistemas e paisagens SAP até grandes sistemas e paisagens SAP com milhares de utilizadores. Os números SAPS de dimensionamento SAPS das diferentes VM SKUs certificadas sAP são fornecidos na nota de [suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Documentação para instalar ASE SAP no Windows pode ser encontrada no Guia de [Instalação Da ASE SAP para Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Lock Pages in Memory é uma definição que impedirá que o tampão de base de dados SAP ASE seja remepto.  Esta configuração é útil para grandes sistemas ocupados com muita memória. Contacte o BC-DB-SYB para obter mais informações. 


## <a name="linux-operating-system-specific-settings"></a>Definições específicas do sistema operativo Linux
Nos VMs Linux, executar `saptune` com perfil SAP-ASE Linux Páginas Enormes deve ser ativado por padrão e pode ser verificado com comando  

`cat /proc/meminfo` 

O tamanho da página é tipicamente 2048 KB. Para mais detalhes veja o artigo [Páginas Enormes no Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Recomendações sobre VM e estrutura de disco para implementações da ASE SAP

As aplicações SAP ASE para Aplicações SAP NetWeaver são suportadas em qualquer tipo VM listado na [nota de suporte SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533) tipos típicos de VM utilizados para servidores de base de dados SAP ASE de tamanho médio incluem Esv3.  Grandes bases de dados multiterabytes podem alavancar tipos vM da série M. O desempenho de escrita do disco de registo de transação SAP ASE pode ser melhorado permitindo o acelerador de escrita da série M. O Acelerador de Escrita deve ser testado cuidadosamente com a SAP ASE devido à forma como o SAP ASE executa log Writes.  Reveja a nota de [suporte do SAP #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) e considere executar um teste de desempenho.  
O Acelerador de Escrita foi concebido apenas para o disco de registo de transações. A cache de nível do disco deve ser definida para NENHUMA. Não se surpreenda se o Acelerador De Escrita Azure não apresentar melhorias semelhantes às de outros DBMS. Com base na forma como a SAP ASE escreve no registo de transações, pode ser que haja pouca ou nenhuma aceleração por parte do Acelerador de Escrita Azure.
São recomendados discos separados para dispositivos de dados e dispositivos de registo.  As bases de dados do `saptools` sistema sybsecurity e não requerem discos dedicados e podem ser colocadas nos discos que contêm os dados da base de dados SAP e dispositivos de registo 

![Configuração de armazenamento para SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Sistemas de ficheiros, tamanho de listra& equilíbrio IO 
A SAP ASE escreve dados sequencialmente em dispositivos de armazenamento de discos, a menos que estejam configurados de outra forma. Isto significa que uma base de dados SAP ASE vazia com quatro dispositivos irá escrever dados apenas no primeiro dispositivo.  Os outros dispositivos de disco só serão escritos quando o primeiro dispositivo estiver cheio.  A quantidade de READ e WRITE IO para cada dispositivo SAP ASE é provável que seja diferente. Para equilibrar o disco IO em todos os discos Azure disponíveis, quer os Espaços de Armazenamento do Windows quer o Linux LVM2 precisam de ser utilizados. No Linux, recomenda-se a utilização do sistema de ficheiros XFS para formatar os discos. O tamanho da risca LVM deve ser testado com um teste de desempenho. 128 KB tamanho de listra é um bom ponto de partida. No Windows, o tamanho da unidade de atribuição ntfs (AUS) deve ser testado. 64 KB pode ser usado como um valor inicial. 

Recomenda-se configurar a expansão automática da base de dados, tal como descrito no artigo Configurando a expansão automática do espaço da base de [dados em SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) e nota de suporte [SAP #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Amostra SAP ASE em configurações de máquina virtual Azure, disquete e sistema de ficheiros 
Os modelos abaixo mostram configurações de amostras tanto para Linux como Windows. Antes de confirmar a configuração virtual da máquina e do disco, certifique-se de que as quotas de largura de banda de rede e armazenamento do VM individual são suficientes para satisfazer os requisitos de negócio. Tenha também em mente que diferentes tipos de VM Azure têm diferentes números máximos de discos que podem ser ligados ao VM. Por exemplo, um E4s_v3 VM tem uma entrada de IO de armazenamento de 48 MB/seg. Se a entrada de armazenamento exigida pela atividade de backup da base de dados exigir mais de 48 MB/seg, então um tipo vm maior com mais largura de largura de armazenamento é inevitável. Ao configurar o armazenamento Azure, também precisa de ter em mente que, especialmente com o [armazenamento Azure Premium,](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) a entrada e o IOPS por GB de capacidade mudam. Veja mais sobre este tema no artigo Quais os tipos de [discos disponíveis no Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) As quotas para tipos de VM Azure específicos estão documentadas no artigo [Memória otimizada tamanhos](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) de máquinas virtuais e artigos ligados a ele. 

> [!NOTE]
>  Se um sistema DBMS estiver a ser transferido do local para o Azure, recomenda-se a realização de monitorização no VM e a avaliação do CPU, memória, IOPS e entrada de armazenamento. Comparar os valores máximos observados com os limites de quota VM documentados nos artigos acima mencionados

Os exemplos a seguir são para fins ilustrativos e podem ser modificados com base nas necessidades individuais. Devido ao desenho da SAP ASE, o número de dispositivos de dados não é tão crítico como com outras bases de dados. O número de dispositivos de dados detalhados neste documento é apenas um guia. 

Um exemplo de configuração para um pequeno SAP ASE DB Server com um tamanho de base de dados entre 50 GB e 250 GB, como o Gestor de Soluções SAP, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 4 | 4 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho sap BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação do disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 2 x P10 (RAID0) | Armazenamento premium: 2 x P10 (RAID0)| Cache = Ler Apenas |
| # e tipo de discos de registo | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 4 | 4| --- |
| # e tipo de discos de reserva | 1 | 1 | --- |


Um exemplo de configuração para um Servidor DB SAP ASE médio com um tamanho de base de dados entre 250 GB - 750 GB, como um sistema sAP Business Suite mais pequeno, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 8 | 8 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho sap BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação do disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4 x P20 (RAID0) | Armazenamento premium: 4 x P20 (RAID0)| Cache = Ler Apenas |
| # e tipo de discos de registo | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 4 | 4| --- |
| # e tipo de discos de reserva | 1 | 1 | --- |

Um exemplo de configuração para um pequeno SAP ASE DB Server com um tamanho de base de dados entre 750 GB e 2000 GB, como um maior sistema SAP Business Suite, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 16 | 16 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho sap BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação do disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4 x P30 (RAID0) | Armazenamento premium: 4 x P30 (RAID0)| Cache = Ler Apenas |
| # e tipo de discos de registo | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NENHUM |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 4 | 4| --- |
| # e tipo de discos de reserva | 1 | 1 | --- |


Um exemplo de uma configuração para um pequeno SAP ASE DB Server com uma base de dados de 2 TB+, como um sistema sAP Business Suite usado globalmente, poderia parecer

| Configuração | Windows | Linux | Comentários |
| --- | --- | --- | --- |
| Tipo VM | Série M (1.0 a 4.0 TB RAM)  | Série M (1.0 a 4.0 TB RAM) | --- |
| Redes Aceleradas | Ativar | Ativar | ---|
| Versão SAP ASE | 16.0.03.07 ou superior | 16.0.03.07 ou superior | --- |
| # de dispositivos de dados | 32 | 32 | ---|
| # de dispositivos de log | 1 | 1 | --- |
| # de dispositivos temporários | 1 | 1 | mais para a carga de trabalho sap BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 ou RHEL 7.6 | --- |
| Agregação do disco | Espaços de Armazenamento | LVM2 | --- |
| Sistema de ficheiros | NTFS | XFS |
| Tamanho do bloco de formato | precisa de testes de carga de trabalho | precisa de testes de carga de trabalho | --- |
| # e tipo de discos de dados | Armazenamento premium: 4+ x P30 (RAID0) | Armazenamento premium: 4+ x P30 (RAID0)| Cache = Ler Apenas, Considere o disco Azure Ultra |
| # e tipo de discos de registo | Armazenamento premium: 1 x P20  | Armazenamento premium: 1 x P20 | Cache = NONE, Considere o disco Azure Ultra |
| Parâmetro ASE MaxMemory | 90% da RAM Física | 90% da RAM Física | assumindo uma única instância |
| # de dispositivos de reserva | 16 | 16 | --- |
| # e tipo de discos de reserva | 4 | 4 | Utilizar espaços LVM2/Armazenamento |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Backup & restaurar considerações para ASE SAP em Azure
Aumentar o número de dados e dispositivos de backup aumenta a cópia de segurança e restaura o desempenho. Recomenda-se riscar os discos Azure que estão a alojar o dispositivo de backup SAP ASE como mostra nas tabelas anteriormente mostradas. Deve ser tomado o cuidado de equilibrar o número de dispositivos e discos de backup e garantir que a entrada de cópia de reserva não exceda 40%-50% do total da quota de produção vm. Recomenda-se utilizar a Compressão de Backup SAP como padrão. Mais detalhes podem ser encontrados nos artigos:

- [Nota de apoio SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Nota de apoio SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Nota de apoio SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Não utilize a unidade D:\ ou /espaço temporário como base de dados ou destino de despejo de registo.

### <a name="impact-of-database-compression"></a>Impacto da compressão da base de dados
Em configurações onde a largura de banda de I/S pode tornar-se um fator limitativo, medidas, que reduzem o IOPS podem ajudar a esticar a carga de trabalho que se pode executar num cenário iaaS como o Azure. Por isso, recomenda-se que se certifique de que a compressão SAP ASE é utilizada antes de enviar uma base de dados SAP existente para o Azure.

A recomendação de aplicação da compressão antes de enviar para o Azure é dada por várias razões:

* A quantidade de dados a ser enviado para o Azure é menor
* A duração da execução da compressão é mais curta assumindo que se pode usar hardware mais forte com mais CPUs ou largura de banda de I/S mais alta ou menos latência de E/S no local
* Tamanhos de base de dados menores podem levar a menos custos para a atribuição de discos

Os trabalhos de compressão de dados e LOB num VM hospedado em Máquinas Virtuais Azure como no local. Para obter mais detalhes sobre como verificar se a compressão já está a ser utilizada numa base de dados existente da ASE SAP, consulte a nota de [suporte SAP 1750510](https://launchpad.support.sap.com/#/notes/1750510). Para mais detalhes sobre a compressão da base de dados SAP ASE, consulte a [nota de suporte sap #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Alta disponibilidade de SAP ASE em Azure 
O Guia de Utilizadores HADR detalha a configuração e configuração de uma solução De 2 nósa SAP ASE "Always on".  Além disso, um terceiro nó de recuperação de desastres também é apoiado. A SAP ASE suporta muitas configurações disponíveis, incluindo o disco partilhado e o clustering de OS nativo (IP flutuante). A única configuração suportada no Azure é utilizar o Fault Manager sem IP flutuante.  O método de endereço IP flutuante não funcionará no Azure.  O SAP Kernel é uma aplicação "HA Aware" e conhece os servidores Primário e Secundário da ASE. Não existem integrações estreitas entre a SAP ASE e a Azure, o equilíbrio de carga sinuoso não é utilizado. Por isso, a documentação padrão da ASE SAP deve ser seguida a partir do Guia de [Utilizadores SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> A única configuração suportada no Azure é utilizar o Fault Manager sem IP flutuante.  O método de endereço IP flutuante não funcionará no Azure. 

### <a name="third-node-for-disaster-recovery"></a>Terceiro nó para recuperação de desastres
Além de utilizar o SAP ASE Always-On para alta disponibilidade local, é melhor que a configuração seja alargada a um nó assíncronamente replicado noutra região do Azure. A documentação para tal cenário pode ser consultada [aqui.](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

## <a name="sap-ase-database-encryption--ssl"></a>Encriptação da base de dados SAP ASE & SSL 
O Gestor de Fornecimento de Software SAP (SWPM) está a dar uma opção de encriptar a base de dados durante a instalação.  Se pretender utilizar encriptação, recomenda-se a utilização da encriptação De base de dados completa SAP.  Consulte os detalhes documentados em:

- [Nota de apoio SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Nota de apoio SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Nota de suporte SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Nota de apoio SAP #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Se uma base de dados SAP ASE estiver encriptada, a compressão de despejo de reserva não funcionará. Consulte também a nota de [suporte SAP #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP ASE na lista de verificação de implementação do Azure
 
- Implementar SAP ASE 16.0.03.07 ou superior
- Atualização para versão mais recente e patches de FaultManager e SAPHostAgent
- Implemente no os mais recente SISTEMA certificado disponível como Windows 2019, Suse 15.1 ou Redhat 7.6 ou superior
- Utilize VMs Certificados SAP – memória alta Azure VM SKUs tais como Es_v3 ou para sistemas de grandes dimensões M-Series VM SKUs são recomendados
- Combine o IOPS do disco e a quota total de entrada vm agregado do VM com o design do disco.  Implementar um número suficiente de discos
- Discos agregados que utilizam espaços de armazenamento do Windows ou Linux LVM2 com tamanho e sistema de ficheiros corretos
- Criar um número suficiente de dispositivos para fins de dados, registo, temporário e backup
- Considere usar o UltraDisk para sistemas x-grandes 
- Executar `saptune` SAP-ASE no Linux OS 
- Proteja a base de dados com encriptação DB – guarde manualmente as chaves no Cofre de Chaves Azure 
- Complete o SAP na Lista de [Verificação Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- Configure a cópia de segurança de registo e a cópia de segurança completa 
- Teste HA/DR, cópia de segurança e restauro e efetuar teste de volume & de stress 
- Confirmar que a extensão automática da base de dados está a funcionar 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilização do DBACockpit para monitorizar as instâncias da base de dados
Para os sistemas SAP, que estão a usar o SAP ASE como plataforma de base de dados, o DBACockpit está acessível como janelas de navegador incorporadas em transação DBACockpit ou como Webdynpro. No entanto, a funcionalidade completa para monitorizar e administrar a base de dados está disponível apenas na implementação webdynpro do DBACockpit.

Tal como acontece com os sistemas no local, são necessários vários passos para permitir a toda a funcionalidade SAP NetWeaver utilizada pela implementação webdynpro do DBACockpit. Siga a nota de [suporte SAP #1245200](https://launchpad.support.sap.com/#/notes/1245200) para permitir a utilização de webdynpros e gerar os necessários. Ao seguir as instruções nas notas acima, configura também`ICM`o Internet Communication Manager ( ) juntamente com as portas a utilizar para ligações http e https. A definição padrão para http parece:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> ICM/server_port_1 = PROT=HTTPS,PORT=443$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados na transação DBACockpit são semelhantes a:

> https:\//\<nome de anfitrião totalmente qualificado>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<nome de anfitrião totalmente qualificado>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dependendo de como a Máquina Virtual Azure que acolhe o sistema SAP está ligada ao seu AD e DNS, você precisa ter certeza de que o ICM está usando um nome de anfitrião totalmente qualificado que pode ser resolvido na máquina de onde está aabrir o DBACockpit. Consulte a nota de [suporte SAP #773830](https://launchpad.support.sap.com/#/notes/773830) para entender como o ICM determina o nome do hospedeiro totalmente qualificado com base em parâmetros de perfil e definir o parâmetro icm/host_name_full explicitamente, se necessário.

Se implementou o VM num cenário apenas em nuvem sem conectividade transversal entre as instalações e o `domainlabel`Azure, tem de definir um endereço IP público e um . O formato do nome público dNS do VM parece:

> `<custom domainlabel`>. `<azure region`>.cloudapp.azure.com
> 
> 

Mais detalhes relacionados com o nome DNS podem ser encontrados [aqui][máquinas virtuais-azurerm-versus-azuresm].

Definição do parâmetro de perfil SAP icm/host_name_full ao nome DNS do VM Azure, a ligação pode parecer semelhante a:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Neste caso, tem de se certificar de que:

* Adicione regras de entrada ao Grupo de Segurança da Rede no portal Azure para as portas TCP/IP utilizadas para comunicar com o ICM
* Adicione regras de entrada na configuração do Windows Firewall para as portas TCP/IP utilizadas para comunicar com o ICM

Para uma importação automatizada de todas as correções disponíveis, recomenda-se aplicar periodicamente a recolha de correção SAP Nota aplicável à sua versão SAP:

* [Nota de apoio SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Nota de apoio SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Nota de apoio SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Mais informações sobre o Cockpit dBA para ASE SAP podem ser encontradas nas seguintes notas SAP:

* [Nota de apoio SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Nota de apoio SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Nota de suporte SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Nota de apoio SAP #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Nota de apoio SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Nota de apoio SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Nota de apoio SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Nota de apoio SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Links úteis, notas & whitepapers para ASE SAP
A página de partida para [sap ASE 16.0.03.07 Documentação](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) dá ligações a vários documentos dos quais os documentos de:

- Jornada de Aprendizagem SAP ASE - Administração & Monitorização
- Jornada de Aprendizagem SAP ASE - Atualização de instalação &

são úteis. Outro documento útil é [aplicações SAP em SAP Adaptive Server Enterprise As Melhores Práticas para Migração e Tempo](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)de Execução .

Outras notas de suporte sap úteis são:

- [Nota de apoio SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Nota de apoio SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Nota de suporte SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Nota de apoio SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Nota de apoio SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Nota de apoio SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Nota de apoio SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota de apoio SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Nota de apoio SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Nota de apoio SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Nota de apoio SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Nota de apoio SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota de apoio SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Outras informações são publicadas em 

- [Aplicações SAP em Empresa de Servidor Adaptável SAP](https://community.sap.com/topics/applications-on-ase)
- [Infocentro SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Sempre ligado com 3ª Configuração do Nó DR](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Uma newsletter mensal é publicada através da nota de [suporte da SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Passos seguintes
Consulte as cargas de trabalho do [Artigo SAP no Azure: lista de verificação de planeamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

