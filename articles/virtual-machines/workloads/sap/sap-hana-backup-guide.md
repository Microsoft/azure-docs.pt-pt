---
title: Guia de backup para SAP HANA em Máquinas Virtuais Azure [ Microsoft Docs
description: Guia de backup para SAP HANA fornece duas grandes possibilidades de backup para SAP HANA em máquinas virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255242"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de backup para SAP HANA em Máquinas Virtuais Azure

## <a name="getting-started"></a>Introdução

O guia de backup para sap HANA em execução em Máquinas virtuais Azure só descreverá tópicos específicos de Azure. Para obter itens relacionados com cópias de segurança Gerais SAP HANA, consulte a documentação sap HANA. Esperamos que esteja familiarizado com as estratégias de backup da base de dados, as razões e motivações para ter uma estratégia de backup sólida e válida, e estamos cientes dos requisitos que a sua empresa tem para o procedimento de backup, período de retenção de backups e restauro procedimento.

O SAP HANA é oficialmente suportado em vários tipos de VM Azure, como o Azure M-Series. Para obter uma lista completa das unidades Azure VMs certificadas da SAP HANA e da HANA Large Instance, consulte as [Plataformas IaaS Certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)find Certified . O Microsoft Azure oferece uma série de unidades onde o SAP HANA funciona sem virtualizar em servidores físicos. Este serviço chama-se [HANA Large Instances](hana-overview-architecture.md). Este guia não abrange processos e ferramentas de backup para grandes instâncias HANA. Mas vai limitar-se às máquinas virtuais Azure. Para mais detalhes sobre processos de backup/restauro com as grandes instâncias HANA, leia o artigo [HLI Backup and Restore](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore).

O foco deste artigo está em três possibilidades de backup para sAP HANA em máquinas virtuais Azure:

- Backup HANA através de Serviços de [Backup Azure](https://docs.microsoft.com/azure/backup/backup-overview) 
- Backup HANA para o sistema de ficheiros numa Máquina Virtual Azure Linux (ver [Backup SAP HANA Azure no nível de ficheiro)](sap-hana-backup-file-level.md)
- Backup HANA baseado em instantâneos de armazenamento usando a função de instantâneo blob de armazenamento Azure manualmente ou serviço de backup Azure


O SAP HANA oferece uma API de reserva, que permite que ferramentas de backup de terceiros se integrem diretamente com o SAP HANA. Produtos como o serviço de backup Azure, ou [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) estão a usar esta interface proprietária para ativar a base de dados SAP HANA ou refazer cópias de segurança de registo. 


Informações sobre como pode encontrar o software SAP suportado no Azure podem ser encontradas no artigo [O software SAP é suportado para implementações Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure).

## <a name="azure-backup-service"></a>Serviço de Backup Azure

O primeiro cenário mostrado é um cenário em que o Serviço `backint` de Backup Azure está a usar a interface SAP HANA para realizar uma cópia de segurança de streaming com uma base de dados SAP HANA. Ou usa uma capacidade mais genérica do serviço De backup Azure para criar uma imagem de instantâneo de disco consistente e transferi-lo para o serviço de backup Azure.

O Azure Backup integra-se e é certificado como solução de backup para o SAP HANA utilizando a interface proprietária SAP HANA chamada [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Para mais detalhes sobre a solução, as suas capacidades e as regiões de Azure onde está disponível, leia o artigo [Matriz de suporte para backup das bases de dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). Para mais detalhes e princípios sobre o serviço de backup Azure para hana, leia o artigo Sobre a cópia de segurança da base de [dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-about). 

A segunda possibilidade de alavancar o serviço de backup Azure é criar uma cópia de segurança consistente utilizando instantâneos de disco do Armazenamento Premium Azure. Outros armazenamentos Azure certificados pela HANA, como [o disco Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd) e o [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) não estão a suportar este tipo de instantâneo através do serviço de backup Azure. Ler estes artigos:

- [Planear a sua infraestrutura de cópias de segurança de VMs no Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Backup consistente com aplicações de VMs Azure Linux](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

esta sequência de atividade emerge:

- O Azure Backup precisa de executar um guião pré-instantâneo que coloque a aplicação, neste caso SAP HANA, num estado consistente
- Como este estado consistente é confirmado, Azure Backup executará os instantâneos do disco
- Depois de terminar as fotos, o Azure Backup irá desfazer a atividade que fez no roteiro pré-instantâneo
- Após a execução bem sucedida, o Azure Backup irá transmitir os dados para o cofre de backup

No caso do SAP HANA, a maioria dos clientes está a utilizar o Acelerador De Escrita Azure para os volumes que contêm o registo de redodo do SAP HANA. O serviço de backup Azure excluirá automaticamente estes volumes dos instantâneos. Esta exclusão não prejudica a capacidade da HANA de restaurar. Embora bloqueasse a capacidade de restaurar com quase todos os outros DBMS suportados pela SAP.

O lado negativo desta possibilidade é o facto de precisares de desenvolver o teu próprio guião pré e pós-instantâneo. O script pré-instantâneo precisa de criar uma imagem instantânea hana e lidar com eventuais casos de exceção. Enquanto o script pós-instantâneo precisa de apagar novamente o instantâneo HANA. Para mais detalhes sobre a lógica necessária, comece com a nota de [suporte SAP #2039883](https://launchpad.support.sap.com/#/notes/2039883). As considerações da secção 'SAP HANA data consistência ao tirar fotografias de armazenamento' neste artigo aplicam-se plenamente a este tipo de cópia de segurança.

> [!NOTE]
> Backups baseados em instantâneo sap para SAP HANA em implementações onde vários recipientes de base de dados são usados, requerem uma libertação mínima de HANA 2.0 SP04
> 

Consulte detalhes sobre instantâneos de armazenamento mais tarde neste documento.

![Este número mostra duas possibilidades para salvar o estado vm atual](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Outros métodos de backup HANA
Existem três outros métodos de backup ou caminhos que podem ser considerados:

- Apoiar-se contra uma participação da NFS baseada em Ficheiros Azure NetApp (ANF). A ANF volta a ter a capacidade de criar instantâneos desses volumes em que armazena cópias de segurança. Dada a entrada que eventualmente necessita para escrever as cópias de segurança, esta solução pode tornar-se um método caro. Embora seja fácil de estabelecer uma vez que HANA pode escrever os backups diretamente na parte nFS nativa de Azure
- Executar a Cópia de Segurança HANA contra discos vm ligados de Armazenamento Standard SSD ou Azure Premium. Como próximo passo, pode copiar os ficheiros de reserva contra o armazenamento do Azure Blob. Esta estratégia pode ser atraente para os preços
- Executar a Cópia de Segurança HANA contra discos vm ligados de Armazenamento Standard SSD ou Azure Premium. Como próximo passo, o disco é instantâneo regularmente. Após o primeiro instantâneo, instantâneos incrementais podem ser usados para reduzir custos

![Este valor mostra opções para obter um backup de ficheiroS SAP HANA dentro do VM](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Esta figura mostra opções para tomar uma cópia de segurança de ficheiroS SAP HANA dentro do VM e, em seguida, armazená-lo ficheiros de backup HANA em outro lugar usando diferentes ferramentas. No entanto, todas as soluções que não envolvam um serviço de backup de terceiros ou o serviço de backup Azure têm vários obstáculos em comum. Alguns deles podem ser listados, como a administração de retenção, processo de restauro automático e fornecimento automático de recuperação ponto-a-tempo como serviço de backup Azure ou outras suites e serviços especializados de apoio de terceiros fornecer. Muitos desses serviços de terceiros podem funcionar no Azure. 


## <a name="sap-resources-for-hana-backup"></a>Recursos SAP para backup HANA

### <a name="sap-hana-backup-documentation"></a>Documentação de backup SAP HANA

- [Introdução à Administração SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planejando a sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar Backup HANA usando ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Agendar backups de dados (Cockpit SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- FAQ sobre backup SAP HANA em [Nota SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- FAQ sobre base de dados SAP HANA e fotos de armazenamento em [Nota SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de ficheiros de rede inadequados para backup e recuperação em [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a correção da cópia de segurança SAP HANA
Independentemente do seu método de backup, executar um teste restaurar contra um sistema diferente é uma necessidade absoluta. Esta abordagem fornece uma forma de garantir que um backup está correto, e os processos internos para backup e restauro de trabalho como esperado. Embora restaurar os backups possa ser um obstáculo no local devido à sua exigência de infraestrutura, é muito mais fácil de realizar na nuvem fornecendo recursos necessários temporariamente para este fim. É correto que existam ferramentas fornecidas com HANA que podem verificar ficheiros de backup sobre a capacidade de restaurar. No entanto, o objetivo dos exercícios frequentes de restauro é testar o processo de restauração de uma base de dados e treinar esse processo com o pessoal das operações.

Tenha em mente que fazer uma simples restauração e verificar se hana está a funcionar não é suficiente. Deve fazer uma verificação de consistência da tabela para se certificar de que a base de dados restaurada está boa. O SAP HANA oferece vários tipos de controlos de consistência descritos na [Nota SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

As informações sobre a verificação de consistência da tabela também podem ser encontradas no site da SAP em [Verificações](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)de Consistência de Tabela e Catálogo .

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Prós e contras de backup HANA versus instantâneo de armazenamento

O SAP não&#39;dar preferência a qualquer cópia de segurança HANA versus instantâneo de armazenamento. Ele lista os seus prós e contras, para que se possa determinar qual usar dependendo da situação e da tecnologia de armazenamento disponível (ver Planeamento Da Sua Estratégia de [Backup e Recuperação).](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)

No Azure, esteja ciente de que a função de instantâneo blob Azure não&#39;fornecer consistência do sistema de ficheiros em vários discos (ver [Utilizar imagens blob com PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). 

Além disso, é preciso compreender as implicações da faturação quando se trabalha frequentemente com imagens blob, como descrito neste artigo: [Compreender como os Snapshots Acumulam Encargos](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— não é&#39;tão óbvio como usar discos virtuais Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Consistência de dados do SAP HANA ao tirar fotos de armazenamento

Tal como documentado anteriormente, descrever as capacidades de backup instantânea seleções do Azure Backup, o sistema de ficheiros e a consistência da aplicação são obrigatórios ao tirar fotografias de armazenamento. A maneira mais fácil de evitar problemas seria desligar o SAP HANA, ou talvez até toda a máquina virtual. Algo que não é viável para um caso de produção.

> [!NOTE]
> Backups baseados em instantâneo sap para SAP HANA em implementações onde vários recipientes de base de dados são usados, requerem uma libertação mínima de HANA 2.0 SP04
> 

O armazenamento azure, não fornece consistência do sistema de ficheiros em vários discos ou volumes que estão ligados a um VM durante o processo de instantâneo. Isto significa que a consistência da aplicação durante o instantâneo tem de ser entregue pela aplicação, neste caso a própria SAP HANA. [O SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) tem informações importantes sobre cópias de segurança SAP HANA por instantâneos de armazenamento. Por exemplo, com sistemas de ficheiros XFS, é necessário executar o congelamento de **xfs\_** antes de iniciar um instantâneo de armazenamento para fornecer consistência da aplicação (ver [xfs\_freeze(8) - Página](https://linux.die.net/man/8/xfs_freeze) do homem linux para detalhes sobre o congelamento de **xfs\_**).

Assumindo que existe um sistema de ficheiros XFS que abrange quatro discos virtuais Azure, os seguintes passos fornecem uma imagem consistente que representa a área de dados hana:

1. Criar a preparação de instantâneos de dados da HANA
1. Congelar os sistemas de ficheiros de todos os discos/volumes (por exemplo, utilizar **xfs\_congelando)**
1. Crie todos os instantâneos de bolhas necessários no Azure
1. Descongelar o sistema de ficheiros
1. Confirme o instantâneo de dados da HANA (eliminará o instantâneo)

Ao utilizar a capacidade do Azure Backup para realizar cópias de segurança instantâneas consistentes da aplicação, os passos #1 precisam de ser codificados/scripts por si para o script pré-instantâneo. O serviço De reserva Azure executará passos #2 e #3. Os passos #4 e #5 precisam de ser novamente fornecidos pelo seu código no script pós-instantâneo. Se não estiver a utilizar o serviço de backup Azure, também precisa de codificar/script passo #2 e #3 por conta própria.
Mais informações sobre a criação de instantâneos de dados da HANA podem ser encontradas nestes artigos:

- [Imagens de dados da HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Mais detalhes para executar passo #1 pode ser encontrado no artigo Criar um Snapshot de [Dados (SQL nativo)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Detalhes para confirmar/eliminar instantâneos de dados da HANA como necessidade em passo #5 podem ser encontrados no artigo Criar um Snapshot de [Dados (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

É importante confirmar o instantâneo da HANA. Devido ao &quot;Copy-on-Write,&quot; o SAP HANA pode não necessitar de espaço adicional para o disco enquanto estiver neste modo de preparação de instantâneos. Também&#39;é possível iniciar novas cópias de segurança até que o instantâneo SAP HANA seja confirmado.


### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de backup SAP HANA

O artigo da SAP HANA que planeia a sua estratégia de [backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) indica um plano básico para fazer backups. Confie na documentação sap em torno da HANA e suas experiências com outros DBMS na definição da estratégia de backup/restauro e processo para SAP HANA. A sequência de diferentes tipos de backups, e o período de retenção são altamente dependentes dos SLAs que você precisa fornecer.


### <a name="sap-hana-backup-encryption"></a>Encriptação de backup SAP HANA

O SAP HANA oferece encriptação de dados e registos. Se os dados e o registo do SAP HANA não forem encriptados, as cópias de segurança não são encriptadas por predefinição. No entanto, o SAP HANA oferece uma encriptação de backup separada, tal como documentado na Encriptação de [Backup SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Se estiver a executar lançamentos mais antigos do SAP HANA, poderá ter de verificar se a encriptação de backup já fazia parte da funcionalidade fornecida.  


## <a name="next-steps"></a>Passos seguintes
* [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md) descreve a opção de backup baseada em ficheiros.
* Para aprender a estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP HANA em Azure (grandes instâncias), consulte a alta disponibilidade do [SAP HANA (grandes instâncias) e a recuperação de desastres em Azure.](hana-overview-high-availability-disaster-recovery.md)
