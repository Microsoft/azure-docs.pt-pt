---
title: Guia de backup para SAP HANA em Azure Virtual Machines | Microsoft Docs
description: O guia de backup para SAP HANA oferece duas grandes possibilidades de backup para SAP HANA em máquinas virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0004afb5895d7549e5db8ad5e53b52fa17991520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667916"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de backup para SAP HANA em Azure Virtual Machines

## <a name="getting-started"></a>Introdução

O guia de backup para SAP HANA em execução em Azure virtual Machines apenas descreverá tópicos específicos do Azure. Para obter itens relacionados com cópias de segurança gerais SAP HANA, verifique a documentação SAP HANA. Esperamos que esteja familiarizado com as estratégias de backup da base de dados de princípios, as razões e motivações para ter uma estratégia de backup sólida e válida, e estamos cientes dos requisitos que a sua empresa tem para o procedimento de backup, período de retenção de backups e procedimento de restauro.

O SAP HANA é oficialmente suportado em vários tipos de VM Azure, como a Azure M-Series. Para obter uma lista completa das unidades Azure VMs certificadas da SAP HANA e HANA Large Instance, consulte [as plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). O Microsoft Azure oferece uma série de unidades onde o SAP HANA funciona não virtualizado em servidores físicos. Este serviço chama-se [HANA Large Instances](hana-overview-architecture.md). Este guia não abrange os processos e ferramentas de backup para HANA Large Instances. Mas vai limitar-se às máquinas virtuais Azure. Para mais detalhes sobre processos de backup/restauro com HANA Grandes Instâncias, leia o artigo [HLI Backup e Restauro](./hana-backup-restore.md).

O foco deste artigo está em três possibilidades de backup para SAP HANA em máquinas virtuais Azure:

- Apoio hana através dos [Serviços de Backup da Azure](../../../backup/backup-overview.md) 
- HANA backup para o sistema de ficheiros numa Máquina Virtual Azure Linux (ver [SAP HANA Azure Backup no nível de ficheiros](sap-hana-backup-file-level.md))
- Cópia de segurança HANA com base em instantâneos de armazenamento utilizando o recurso de imagem de bolha de armazenamento Azure manualmente ou serviço de backup Azure


O SAP HANA oferece uma API de reserva, que permite que ferramentas de backup de terceiros se integrem diretamente com o SAP HANA. Produtos como o serviço Azure Backup ou [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) estão a usar esta interface proprietária para ativar a base de dados SAP HANA ou refazer cópias de segurança de registo. 


Informações sobre como você pode encontrar o software SAP suportado no Azure pode ser encontrado no artigo [O software SAP é suportado para implementações Azure](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Serviço de Backup Azure

O primeiro cenário mostrado é um cenário em que o Azure Backup Service está a utilizar a interface SAP HANA `backint` para realizar uma cópia de segurança de streaming a partir de uma base de dados SAP HANA. Ou usa uma capacidade mais genérica do serviço de Backup Azure para criar uma imagem de disco consistente de aplicação e transferir essa para o serviço de Backup Azure.

O Azure Backup integra-se e é certificado como solução de backup para SAP HANA utilizando a interface PRÓPRIA SAP HANA chamada [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Para obter mais detalhes sobre a solução, as suas capacidades e as regiões Azure onde está disponível, leia o artigo [Matriz de suporte para cópia de segurança das bases de dados SAP HANA em VMs Azure](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Para mais detalhes e princípios sobre o serviço de backup Azure para HANA, leia o artigo [Sobre a base de dados SAP HANA em VMs Azure](../../../backup/sap-hana-db-about.md). 

A segunda possibilidade de alavancar o serviço Azure Backup é criar uma cópia de segurança consistente da aplicação utilizando imagens de disco do Azure Premium Storage. Outros armazenamentos Azure certificados pela HANA, como [o disco Azure Ultra](../../disks-enable-ultra-ssd.md) e os [Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/) não estão a suportar este tipo de instantâneo através do serviço Azure Backup. Ler estes artigos:

- [Planear a sua infraestrutura de cópias de segurança de VMs no Azure](../../../backup/backup-azure-vms-introduction.md)
- [Cópia de segurança consistente com aplicações de VMs Linux do Azure](../../../backup/backup-azure-linux-app-consistent.md) 

esta sequência de atividade emerge:

- A Azure Backup precisa executar um script pré-instantâneo que coloca a aplicação, neste caso SAP HANA, em um estado consistente
- Como este estado consistente é confirmado, Azure Backup executará os instantâneos do disco
- Depois de terminar as fotos, a Azure Backup irá desfazer a atividade que fez no script pré-instantâneo
- Após a execução bem sucedida, a Azure Backup transmitirá os dados para o cofre de reserva

No caso do SAP HANA, a maioria dos clientes está a utilizar o Acelerador de Escrita Azure para os volumes que contêm o registo de redo SAP HANA. O serviço de backup Azure excluirá automaticamente estes volumes das imagens. Esta exclusão não prejudica a capacidade de HANA de restaurar. Embora bloqueasse a capacidade de restaurar com quase todos os outros DBMS suportados pela SAP.

O lado negativo desta possibilidade é o facto de precisares de desenvolver o teu próprio script pré e pós-instantâneo. O script pré-instantâneo precisa de criar um instantâneo HANA e lidar com eventuais casos de exceção. Enquanto o script pós-instantâneo precisa de apagar novamente a imagem HANA. Para obter mais detalhes sobre a lógica necessária, comece com [a nota de suporte do SAP #2039883](https://launchpad.support.sap.com/#/notes/2039883). As considerações da secção "consistência dos dados SAP HANA ao tirar snapshots de armazenamento" neste artigo aplicam-se plenamente a este tipo de cópia de segurança.

> [!NOTE]
> Cópias de segurança baseadas em instantâneos em disco para SAP HANA em implementações onde são utilizados vários contentores de base de dados, requerem uma libertação mínima de HANA 2.0 SP04
> 

Consulte os detalhes sobre as fotos de armazenamento mais tarde neste documento.

![Este número mostra duas possibilidades para salvar o estado atual de VM](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Outros métodos de backup HANA
Existem três outros métodos ou caminhos de backup que podem ser considerados:

- Recuando contra uma participação da NFS que se baseia em Ficheiros Azure NetApp (ANF). A ANF tem novamente a capacidade de criar instantâneos dos volumes em que armazena cópias de segurança. Dada a produção que eventualmente necessita para escrever os backups, esta solução pode tornar-se um método caro. Embora seja fácil de estabelecer já que HANA pode escrever os backups diretamente na parte nativa do Azure NFS
- Executar a Cópia de Segurança HANA contra discos VM anexados de Standard SSD ou Azure Premium Storage. Como próximo passo, pode copiar os ficheiros de backup contra o armazenamento da Azure Blob. Esta estratégia pode ser atrativa em termos de preço
- Executar a Cópia de Segurança HANA contra discos VM anexados de Standard SSD ou Azure Premium Storage. À medida que o próximo passo o disco é instantâneo regularmente. Após o primeiro instantâneo, instantâneos incrementais podem ser usados para reduzir custos

![Este número mostra opções para fazer uma cópia de segurança de ficheiro SAP HANA dentro do VM](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Este número mostra opções para fazer uma cópia de segurança de ficheiros SAP HANA dentro do VM e, em seguida, armazenar ficheiros de backup HANA em outro lugar usando diferentes ferramentas. No entanto, todas as soluções que não envolvam um serviço de backup de terceiros ou o serviço Azure Backup têm vários obstáculos em comum. Alguns deles podem ser listados, como a administração de retenção, o processo de restauro automático e a prestação de recuperação automática pontual como o serviço Azure Backup ou outras suítes e serviços especializados de backup de terceiros. Muitos desses serviços de terceiros podem funcionar em Azure. 


## <a name="sap-resources-for-hana-backup"></a>Recursos SAP para apoio hana

### <a name="sap-hana-backup-documentation"></a>Documentação de backup SAP HANA

- [Introdução à Administração SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planejando a sua estratégia de backup e recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar hana Backup usando ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Agendar backups de dados (COCKPIT SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- FAQ sobre o backup SAP HANA na [Nota SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- FAQ sobre base de dados SAP HANA e instantâneos de armazenamento no [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de ficheiros de rede inadequados para backup e recuperação na [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a correção do backup SAP HANA
Independentemente do seu método de backup, executar um teste restaurado contra um sistema diferente é uma necessidade absoluta. Esta abordagem fornece uma forma de garantir que um backup está correto, e processos internos para o backup e restaurar o trabalho como esperado. Embora a restauração de backups possa ser um obstáculo no local devido à sua necessidade de infraestrutura, é muito mais fácil de realizar na nuvem, fornecendo recursos necessários temporariamente para o efeito. É correto que existam ferramentas fornecidas com HANA que podem verificar ficheiros de backup sobre a capacidade de restaurar. No entanto, o objetivo dos exercícios de restauro frequentes é testar o processo de restauro de uma base de dados e treinar esse processo com o pessoal de operações.

Tenha em mente que fazer uma simples restauração e verificar se hana está em funcionamento não é suficiente. Deve verificar a consistência da mesa para ter a certeza de que a base de dados restaurada está bem. A SAP HANA oferece vários tipos de verificações de consistência descritas na [Nota SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

As informações sobre a verificação de consistência da tabela também podem ser encontradas no site da SAP no [Table and Catalog Consistency Checks](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Prós e contras de backup HANA versus instantâneo de armazenamento

O SAP não&#39;dar preferência a qualquer cópia de segurança HANA versus instantâneo de armazenamento. Ele lista os seus prós e contras, para que se possa determinar qual usar dependendo da situação e da tecnologia de armazenamento disponível (ver [Planeamento da Sua Estratégia de Backup e Recuperação).](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)

No Azure, esteja ciente de que a função de snapshot blob Azure não&#39;fornecer consistência do sistema de ficheiros em vários discos (ver [Utilização de instantâneos blob com PowerShell).](/archive/blogs/cie/using-blob-snapshots-with-powershell) 

Além disso, é preciso compreender as implicações da faturação quando se trabalha frequentemente com instantâneos blob como descrito neste artigo: [Compreender como os Snapshots Accrue Charges](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— não é&#39;tão óbvio como usar discos virtuais Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Consistência dos dados SAP HANA ao tirar fotos de armazenamento

Como documentado anteriormente, descrever as capacidades de backup instantânea do Azure Backup, sistema de ficheiros e consistência da aplicação é obrigatório ao tirar fotos de armazenamento. A maneira mais fácil de evitar problemas seria desligar o SAP HANA, ou talvez até toda a máquina virtual. Algo que não é viável para um caso de produção.

> [!NOTE]
> Cópias de segurança baseadas em instantâneos em disco para SAP HANA em implementações onde são utilizados vários contentores de base de dados, requerem uma libertação mínima de HANA 2.0 SP04
> 

O armazenamento Azure, não fornece consistência do sistema de ficheiros em vários discos ou volumes que estão ligados a um VM durante o processo de instantâneo. Isto significa que a consistência da aplicação durante o instantâneo precisa de ser entregue pelo pedido, neste caso, o próprio SAP HANA. [SAP Nota 2039883](https://launchpad.support.sap.com/#/notes/2039883) tem informações importantes sobre backups SAP HANA através de instantâneos de armazenamento. Por exemplo, com sistemas de ficheiros XFS, é necessário executar **xfs \_ congelar** antes de iniciar uma imagem de armazenamento para fornecer consistência da aplicação (ver [xfs \_ freeze(8) - página do homem Linux](https://linux.die.net/man/8/xfs_freeze) para obter detalhes sobre **o \_ congelamento de xfs).**

Assumindo que existe um sistema de ficheiros XFS abrangendo quatro discos virtuais Azure, os seguintes passos fornecem um instantâneo consistente que representa a área de dados HANA:

1. Criar instantâneo de dados HANA preparar
1. Congelar os sistemas de ficheiros de todos os discos/volumes (por exemplo, utilizar **xfs \_ congelar)**
1. Crie todos os instantâneos de bolhas necessários em Azure
1. Descongele o sistema de ficheiros
1. Confirme o instantâneo de dados HANA (apagará o instantâneo)

Ao utilizar a capacidade do Azure Backup para executar cópias de segurança instantâneas consistentes da aplicação, os passos #1 precisam de ser codificados/scripts por si para o script pré-instantâneo. O serviço de backup Azure executará passos #2 e #3. Os passos #4 e #5 precisam de ser novamente fornecidos pelo seu código no script pós-instantâneo. Se não estiver a utilizar o serviço de backup Azure, também precisa de codificar/script #2 e #3 por conta própria.
Mais informações sobre a criação de instantâneos de dados HANA podem ser encontradas nestes artigos:

- [Instantâneos de dados HANA] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Mais detalhes para executar passo #1 pode ser encontrado em artigo [Criar um Data Snapshot (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Os detalhes para confirmar/eliminar as imagens de dados HANA como necessário em #5 podem ser encontrados no artigo [Criar um Snapshot de dados (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

É importante confirmar o instantâneo HANA. Devido à &quot; cópia-on-write, &quot; o SAP HANA pode não necessitar de espaço adicional em disco durante este modo de preparação instantânea. Também&#39;não é possível iniciar novos backups até que o instantâneo SAP HANA seja confirmado.


### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de backup SAP HANA

O artigo DA SAP HANA [que planeia a sua Estratégia de Backup e Recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) estabelece um plano básico para fazer backups. Confie na documentação do SAP em torno da HANA e das suas experiências com outros DBMS na definição da estratégia e processo de backup/restauro para SAP HANA. A sequência de diferentes tipos de backups e o período de retenção são altamente dependentes dos SLAs que precisa fornecer.


### <a name="sap-hana-backup-encryption"></a>Encriptação de backup SAP HANA

A SAP HANA oferece encriptação de dados e registo. Se os dados e o registo SAP HANA não forem encriptados, então as cópias de segurança não são encriptadas por padrão. No entanto, o SAP HANA oferece uma encriptação de backup separada, como documentado na [Encriptação de Backup SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Se estiver a executar versões mais antigas do SAP HANA, poderá ter de verificar se a encriptação de backup já fazia parte da funcionalidade fornecida.  


## <a name="next-steps"></a>Passos seguintes
* [SAP HANA Azure Backup no nível](sap-hana-backup-file-level.md) de ficheiro descreve a opção de backup baseada em ficheiros.
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em Azure (grandes instâncias), ver [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md).
