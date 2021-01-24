---
title: Configuração DE ANF de máquina virtual SAP HANA Azure | Microsoft Docs
description: Recomendações de armazenamento de ficheiros Azure NetApp para SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, snapshot
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 906879c44a2d7a3248f3d3ac0c9fec7ced7f2a4f
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746548"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>Volumes NFS v4.1 no Azure NetApp Files para SAP HANA

O Azure NetApp Files fornece ações NFS nativas que podem ser usadas para **volumes /hana/shared,** **/hana/data**, e **/hana/log** volumes. A utilização de ações NFS baseadas em ANF para os volumes **/hana/data** e **/hana/log** requer a utilização do protocolo v4.1 NFS. O protocolo NFS v3 não é suportado para a utilização de **/hana/data** e **/hana/log** volumes ao basear as ações na ANF. 


> [!IMPORTANT]
> O protocolo NFS v3 implementado nos Ficheiros Azure NetApp **não** é suportado para ser utilizado para **/hana/dados** e **/hana/log**. A utilização do NFS 4.1 é obrigatória para **volumes /hana/dados** e **/hana/log** do ponto de vista funcional. Considerando que para o volume **/hana/partilhado** o NFS v3 ou o protocolo NFS v4.1 podem ser utilizados de um ponto de vista funcional.

## <a name="important-considerations"></a>Considerações importantes

Ao considerar os ficheiros Azure NetApp para o SAP Netweaver e o SAP HANA, esteja ciente das seguintes considerações importantes:

- A capacidade mínima é de 4 TiB  
- O tamanho mínimo do volume é 100 GiB
- Os ficheiros Azure NetApp e todas as máquinas virtuais, onde os volumes dos Ficheiros Azure NetApp são montados, devem estar na mesma Rede Virtual Azure ou em [redes virtuais na](../../../virtual-network/virtual-network-peering-overview.md) mesma região
- É importante ter as máquinas virtuais implantadas nas proximidades do armazenamento do Azure NetApp para baixa latência.  
- A rede virtual selecionada deve ter uma sub-rede, delegada nos Ficheiros Azure NetApp
- Certifique-se de que a latência do servidor de base de dados para o volume ANF é medida e inferior a 1 milissegundo
- O rendimento de um volume Azure NetApp é uma função da quota de volume e do nível de Serviço, conforme documentado no [nível de Serviço para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Ao dimensionar os volumes HANA Azure NetApp, certifique-se de que a produção resultante satisfaz os requisitos do sistema HANA
- Tente "consolidar" volumes para obter mais desempenho num volume maior, por exemplo, use um volume para /sapmnt, /usr/seiva/trans, ... se possível  
- O Azure NetApp Files oferece [uma política de exportação:](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)pode controlar os clientes autorizados, o tipo de acesso (Ler&Escrever, Ler Apenas, etc.). 
- A funcionalidade Azure NetApp Files ainda não está ciente da zona. Atualmente, a funcionalidade Azure NetApp Files não está implantada em todas as zonas de Disponibilidade numa região do Azure. Esteja ciente das potenciais implicações de latência em algumas regiões de Azure.   
- O ID do Utilizador para <b>sid</b>adm e o ID do Grupo `sapsys` para as máquinas virtuais devem corresponder à configuração em Ficheiros Azure NetApp. 

> [!IMPORTANT]
> Para as cargas de trabalho da SAP HANA, a baixa latência é fundamental. Trabalhe com o seu representante da Microsoft para garantir que as máquinas virtuais e os volumes dos Ficheiros Azure NetApp são implantados em proximidade.  

> [!IMPORTANT]
> Se houver um desfasamento entre o ID do utilizador para o <b>sid</b>adm e o ID do Grupo `sapsys` entre a máquina virtual e a configuração Azure NetApp, as permissões para ficheiros nos volumes Azure NetApp, montados no VM, serão apresentadas como `nobody` . Certifique-se de especificar o ID do utilizador correto para <b>sid</b>adm e o ID do grupo para `sapsys` , ao embarcar um novo [sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) para Azure NetApp Files.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento para base de dados HANA em Ficheiros Azure NetApp

O rendimento de um volume Azure NetApp é uma função do tamanho do volume e do nível de Serviço, conforme documentado no [nível de Serviço para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Importante entender é a relação de desempenho do tamanho e que existem limites físicos para um LIF (Interface Lógica) do SVM (Máquina Virtual de Armazenamento).

O quadro abaixo demonstra que poderia fazer sentido criar um grande volume "Standard" para armazenar backups e que não faz sentido criar um volume "Ultra" superior a 12 TB, uma vez que a capacidade de largura de banda física de um único LIF seria ultrapassada. 

A produção máxima para um LIF e uma única sessão de Linux é entre 1,2 e 1,4 GB/s. Se necessitar de mais produção para /hana/dados, pode utilizar a partição do volume de dados SAP HANA para riscar a atividade de E/S durante a recarga de dados ou os pontos de poupança HANA em vários ficheiros de dados HANA que estão localizados em várias partilhas de NFS. Para mais detalhes sobre o volume de dados da HANA, leia estes artigos:

- [Guia do Administrador HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog sobre SAP HANA – Volumes de Dados de Partição](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [#2400005 de nota SAP](https://launchpad.support.sap.com/#/notes/2400005)
- [Nota SAP #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Tamanho  | Padrão de produção | Prémio de Produção | Produção Ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/seg | 64 MB/seg | 128 MB/seg |
| 2 TB | 32 MB/seg | 128 MB/seg | 256 MB/seg |
| 4 TB | 64 MB/seg | 256 MB/seg | 512 MB/seg |
| 10 TB | 160 MB/seg | 640 MB/seg | 1.280 MB/seg |
| 15 TB | 240 MB/seg | 960 MB/seg | 1.400 MB/seg |
| 20 TB | 320 MB/seg | 1.280 MB/seg | 1.400 MB/seg |
| 40 TB | 640 MB/seg | 1.400 MB/seg | 1.400 MB/seg |

É importante entender que os dados são escritos para os mesmos SSDs no backend de armazenamento. A quota de desempenho do pool de capacidade foi criada para poder gerir o ambiente.
Os KPI's de armazenamento são iguais para todos os tamanhos da base de dados HANA. Em quase todos os casos, esta suposição não reflete a realidade e a expectativa do cliente. O tamanho da HANA Systems não significa necessariamente que um pequeno sistema requer baixa produção de armazenamento – e um grande sistema requer alta produção de armazenamento. Mas, em geral, podemos esperar requisitos de produção mais elevados para casos de bases de dados HANA maiores. Como resultado das regras de dimensionamento da SAP para o hardware subjacente, tais instâncias HANA maiores também fornecem mais recursos de CPU e maior paralelismo em tarefas como o carregamento de dados após o reinício de instâncias. Como resultado, os tamanhos de volume devem ser adotados de acordo com as expectativas e requisitos do cliente. E não só impulsionado por requisitos de capacidade pura.

Ao conceber a infraestrutura para SAP em Azure, deve estar ciente de alguns requisitos mínimos de produção (para sistemas de produção) pela SAP, que se traduzem em características mínimas de produção de:

| Tipo de volume e tipo de E/S | KPI mínimo exigido pela SAP | Nível de serviço premium | Nível de serviço ultra |
| --- | --- | --- | --- |
| Escrita de volume de registo | 250 MB/seg | 4 TB | 2 TB |
| Escrita de volume de dados | 250 MB/seg | 4 TB | 2 TB |
| Leitura do volume de dados | 400 MB/seg | 6.3 TB | 3.2 TB |

Uma vez que os três KPI's são exigidos, o volume **/hana/dados** precisa de ser dimensionado para uma maior capacidade para satisfazer os requisitos mínimos de leitura.

Para os sistemas HANA, que não necessitam de alta largura de banda, os tamanhos de volume ANF podem ser menores. E no caso de um sistema HANA necessitar de mais produção, o volume poderia ser adaptado redimensionando a capacidade online. Não são definidos KPI's para volumes de backup. No entanto, a produção de volume de backup é essencial para um ambiente de bom desempenho. Log – e o desempenho do volume de dados deve ser projetado para as expectativas do cliente.

> [!IMPORTANT]
> Independentemente da capacidade que se implanta num único volume NFS, espera-se que a potência seja plana na gama de largura de banda de 1,2-1,4 GB/seg alavancada por um consumidor numa máquina virtual. Isto tem a ver com a arquitetura subjacente da oferta da ANF e os limites de sessão linux relacionados em torno da NFS. Os números de desempenho e de produção, tal como documentados no artigo Os resultados dos [testes de benchmark de desempenho para a Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) foram realizados contra um volume NFS partilhado com VMs de vários clientes e como resultado de múltiplas sessões. Este cenário é diferente do cenário que medimos no SAP. Onde medimos a produção de um único VM contra um volume NFS. Hospedado na ANF.

Para satisfazer os requisitos mínimos de produção de dados e registos do SAP, e de acordo com as diretrizes para **/hana/shared,** os tamanhos recomendados seriam como:

| Volume | Tamanho<br /> Nível de armazenamento premium | Tamanho<br /> Nível de armazenamento ultra | Protocolo NFS apoiado |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/dados | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/escala partilhada | Min(1 TB, 1 x RAM)  | Min(1 TB, 1 x RAM) | v3 ou v4.1 |
| /hana/escala partilhada | 1 x RAM do nó do trabalhador<br /> por 4 nóns operários  | 1 x RAM do nó do trabalhador<br /> por 4 nóns operários  | v3 ou v4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 ou v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 ou v4.1 |

Para todos os volumes, NFS v4.1 é altamente recomendado

Os tamanhos para os volumes de reserva são estimativas. Os requisitos exatos devem ser definidos com base na carga de trabalho e nos processos de funcionamento. Para cópias de segurança, pode consolidar muitos volumes para diferentes casos SAP HANA para um (ou dois) volumes maiores, que poderiam ter um nível de serviço mais baixo de ANF.

> [!NOTE]
> Os Ficheiros Azure NetApp, recomendações de dimensionamento indicadas neste documento, visam os requisitos mínimos que a SAP expressa para os seus fornecedores de infraestruturas. Em verdadeiros cenários de colocação de clientes e de carga de trabalho, isso pode não ser suficiente. Utilize estas recomendações como ponto de partida e adapte-se, com base nos requisitos da sua carga de trabalho específica.  

Por isso, pode considerar a implementação de uma produção semelhante para os volumes ANF listados para armazenamento ultra disco já. Considere também os tamanhos para os tamanhos listados para os volumes para os diferentes SKUs VM como feito nas tabelas de disco ultra já.

> [!TIP]
> Pode redimensionar os volumes Azure NetApp Files dinamicamente, sem necessidade `unmount` de volumes, parar as máquinas virtuais ou parar o SAP HANA. Isso permite flexibilidade para atender a sua aplicação, tanto as exigências de produção esperadas como imprevistas.

A documentação sobre como implantar uma configuração de escala SAP HANA com nó de espera utilizando volumes NFS v4.1 que são hospedados na ANF é publicada em [escala SAP HANA com nó de standby em Azure VMs com Ficheiros Azure NetApp no SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Disponibilidade
As atualizações e atualizações do sistema ANF são aplicadas sem afetar o ambiente do cliente. O [SLA definido é de 99,99%.](https://azure.microsoft.com/support/legal/sla/netapp/)


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Volumes e endereços IP e piscinas de capacidade
Com a ANF, é importante compreender como é construída a infraestrutura subjacente. Um pool de capacidade é apenas uma estrutura, o que torna mais simples a criação de um modelo de faturação para a ANF. Um pool de capacidade não tem qualquer relação física com a infraestrutura subjacente. Se criar uma piscina de capacidade, apenas é criada uma concha que pode ser carregada, não mais. Quando cria um volume, o primeiro SVM (Máquina Virtual de Armazenamento) é criado num conjunto de vários sistemas NetApp. É criado um único IP para que este SVM aceda ao volume. Se criar vários volumes, todos os volumes são distribuídos neste SVM sobre este cluster NetApp multi-controlador. Mesmo que obtenha apenas um IP, os dados são distribuídos por vários controladores. A ANF tem uma lógica que distribui automaticamente as cargas de trabalho dos clientes uma vez que os volumes ou/e capacidade do armazenamento configurado atinge um nível interno pré-definido. Pode notar tais casos porque um novo endereço IP é atribuído para aceder aos volumes.

##<a name="log-volume-and-log-backup-volume"></a>Volume de registo e volume de backup de registo
O "volume de registo"**(/hana/log)** é utilizado para escrever o registo de redo online. Assim, existem ficheiros abertos localizados neste volume e não faz sentido fazer uma imagem deste volume. Os registos de redo online são arquivados ou cópias de segurança até ao volume de backup de registos uma vez que o ficheiro de registo de redo on-line está completo ou uma cópia de segurança de redo é executada. Para proporcionar um desempenho de backup razoável, o volume de backup de registo requer uma boa produção. Para otimizar os custos de armazenamento, pode fazer sentido consolidar o volume de backup de registo de várias instâncias HANA. Para que vários casos de HANA aproveitem o mesmo volume e escrevam os seus backups em diferentes diretórios. Usando tal consolidação, você pode obter mais produção com uma vez que você precisa fazer o volume um pouco maior. 

O mesmo se aplica ao volume a que utiliza, escrever cópias de dados HANA completas.  
 

## <a name="backup"></a>Backup
Além de cópias de segurança de streaming e serviço Azure Back a cópia de segurança das bases de dados SAP HANA, conforme descrito no guia de backup do [artigo Backup para SAP HANA em Máquinas Virtuais Azure,](./sap-hana-backup-guide.md)o Azure NetApp Files abre a possibilidade de realizar cópias de segurança instantâneas baseadas em armazenamento. 

O SAP HANA suporta:

- Backups instantâneos baseados em armazenamento de SAP HANA 1.0 SPS7 em
- Suporte de backup instantâneo baseado em armazenamento para ambientes HANA de Multi Database Container (MDC) de SAP HANA 2.0 SPS4 em


Criar cópias de segurança instantâneas baseadas em armazenamento é um procedimento simples de quatro etapas, 
1. Criação de uma foto de base de dados HANA (interna) - uma atividade que você ou ferramentas precisam realizar 
1. SAP HANA escreve dados para os ficheiros de dados para criar um estado consistente no armazenamento - HANA executa este passo como resultado da criação de um instantâneo HANA
1. Crie uma imagem instantânea no volume **/hana/dados** no armazenamento - um passo que você ou ferramentas precisam de executar. Não há necessidade de realizar uma instantâneo no volume **/hana/log**
1. Elimine o instantâneo da base de dados HANA (interna) e retome o funcionamento normal - um passo que você ou ferramentas precisam de realizar

> [!WARNING]
> Perder o último passo ou não realizar o último passo tem um impacto severo na procura de memória da SAP HANA e pode levar a uma paragem do SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![AnF snapshot backup para SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![AnF snapshot backup para SAP HANA parte2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Este procedimento de backup instantâneo pode ser gerido de várias maneiras, usando várias ferramentas. Um exemplo é o script python "ntaphana_azure.py" disponível no GitHub [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) Este é o código de amostra, desde que "as-is" sem qualquer manutenção ou suporte.



> [!CAUTION]
> Um instantâneo por si só não é uma cópia de segurança protegida, uma vez que está localizado no mesmo armazenamento físico que o volume de que acabou de tirar uma foto. É obrigatório "proteger" pelo menos um instantâneo por dia para um local diferente. Isto pode ser feito no mesmo ambiente, numa região remota de Azure ou no armazenamento Azure Blob.


Para os utilizadores de produtos de backup commvault, uma segunda opção é Commvault IntelliSnap V.11.21 e mais tarde. Estas ou posteriores versões da Commvault oferecem suporte a ficheiros Azure NetApp. O artigo [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) fornece mais informações.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Ressarça o instantâneo usando o armazenamento de bolhas Azure
O armazenamento de blob de volta a Azure é um método eficaz e rápido para salvar cópias de segurança de armazenamento de armazenamento de dados HANA baseadas em ANF. Para guardar as imagens para o armazenamento da Azure Blob, a ferramenta de azcopia é preferida. Descarregue a versão mais recente desta ferramenta e instale-a, por exemplo, no diretório de contentores onde está instalado o script python do GitHub.
Descarregue a mais recente ferramenta de azcopy:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

A característica mais avançada é a opção SYNC. Se utilizar a opção SYNC, a azcopia mantém a origem e o diretório de destino sincronizados. A utilização do parâmetro **--excluir-destino** é importante. Sem este parâmetro, a azcopia não está a apagar ficheiros no local de destino e a utilização do espaço no lado do destino cresceria. Crie um recipiente Block Blob na sua conta de armazenamento Azure. Em seguida, crie a chave SAS para o recipiente blob e sincronize a pasta instantânea para o recipiente Azure Blob.

Por exemplo, se uma imagem diária deve ser sincronizada no recipiente de bolhas Azure para proteger os dados. E apenas que uma foto deve ser mantida, o comando abaixo pode ser usado.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Próximos passos
Leia o artigo:

- [SAP HANA alta disponibilidade para máquinas virtuais Azure](./sap-hana-availability-overview.md)