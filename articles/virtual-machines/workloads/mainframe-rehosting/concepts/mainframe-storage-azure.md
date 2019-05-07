---
title: Mover o armazenamento de mainframe para armazenamento do Azure
description: Recursos de armazenamento do Azure e escaláveis em massa podem ajudar organizações baseados em mainframe, migrar e modernizar aplicativos de z14 da IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: dc78f87d9b47745119da91b8ed1f8f6c8572968c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190448"
---
# <a name="move-mainframe-storage-to-azure"></a>Mover o armazenamento de mainframe para o Azure

Para executar cargas de trabalho de mainframe no Microsoft Azure, precisa saber como se compara as capacidades de sua mainframe para o Azure. Os recursos de armazenamento e escaláveis em massa podem ajudar as organizações a começar a modernizar sem ter de abandonar as aplicações que dependem.

O Azure fornece recursos semelhantes de mainframe e capacidade de armazenamento é comparável para sistemas baseados em z14 da IBM (o modelo mais atual até ao presente momento). Este artigo mostra-lhe como obter resultados comparáveis no Azure.

## <a name="mainframe-storage-at-a-glance"></a>Armazenamento de mainframe rapidamente

IBM mainframe caracteriza armazenamento de duas formas. A primeira é um dispositivo de armazenamento de acesso direto (DASD). O segundo é o armazenamento seqüencial. Para gerir o armazenamento, o mainframe fornece o subsistema de gestão do dados recurso de armazenamento (DFSMS). Ele gerencia o acesso aos dados em vários dispositivos de armazenamento.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refere-se a um dispositivo separado para armazenamento secundário (não na memória) que permite que um endereço exclusivo a utilizar para o acesso direto aos dados. Originalmente, o termo DASD aplicada a rotação de discos, tambores magnéticas ou células de dados. No entanto, agora o termo também pode aplicar a dispositivos de armazenamento de estado sólido (SSDs), redes de armazenamento (SANs), anexado de rede (NAS) de armazenamento e unidades óticas. Para efeitos deste documento, DASD refere-se a rotação de discos, SANs e SSDs.

Ao contrário do armazenamento de DASD, armazenamento sequencial no seu próprio mainframe refere-se a dispositivos, como unidades de banda em que o dados são acedidos a partir de um ponto de partida, em seguida, lidos ou gravados numa linha.

Dispositivos de armazenamento estão normalmente ligados através de uma conexão de fibra (FICON) ou que são acedidos diretamente sobre a utilização de barramento de e/s do mainframe [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), uma tecnologia de IBM para comunicações de alta velocidade entre partições num servidor com um hipervisor.

A maioria dos sistemas de mainframe armazenamento separado em dois tipos:

- *Armazenamento online* (também conhecido como armazenamento de acesso frequente) é necessária para as operações diárias. Armazenamento de DASD geralmente é utilizado para esta finalidade. No entanto, o armazenamento sequencial, tais como banda cópias de segurança diárias (lógicos ou físicos), também pode ser utilizado para esta finalidade.

- *Armazenamento de arquivo* (também conhecido como armazenamento de frio) não é garantido de ser montado num determinado momento. Em vez disso, ele é montado e acedido conforme necessário. Armazenamento de arquivo, muitas vezes, é implementado com cópias de segurança de banda seqüencial (lógicas ou físicas) para armazenamento.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe em comparação com a latência de e/s e IOPS

Mainframes, muitas vezes, são utilizados para aplicações que necessitam de e/s de alto desempenho e baixa latência de e/s. Eles podem fazer isso usando as ligações de FICON HiperSockets e dispositivos de e/s. Quando HiperSockets são utilizadas para ligar aplicações e dispositivos diretamente ao canal de e/s de um mainframe, pode ser obtida a latência nos microssegundos.

## <a name="azure-storage-at-a-glance"></a>Armazenamento do Azure rapidamente

Azure infraestrutura-como-serviço ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) opções de armazenamento fornecem a capacidade de mainframe comparável.

A Microsoft oferece o valor de petabytes de armazenamento para aplicações alojadas no Azure e tem várias opções de armazenamento. Estas variam desde o armazenamento SSD de elevado desempenho para o armazenamento de BLOBs de baixo custo para armazenamento em massa e arquivos mortos. Além disso, o Azure fornece uma opção de redundância de dados para armazenamento — algo que um pouco mais para configurar a num ambiente de mainframe.

O armazenamento do Azure está disponível como [discos do Azure](/azure/virtual-machines/windows/managed-disks-overview), [ficheiros do Azure](/azure/storage/files/storage-files-introduction), e [Blobs do Azure](/azure/storage/blobs/storage-blobs-overview) como a tabela seguinte resume. Saiba mais sobre [quando usar cada](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Descrição</th><th>Utilize para:</th></tr>
</thead>
<tbody>
<tr><td>Ficheiros do Azure
</td>
<td>
Fornece uma interface SMB, bibliotecas de cliente e um <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> interface que permite o acesso em qualquer lugar para ficheiros armazenados.
</td>
<td><ul>
<li>Lift- and -shift de uma aplicação para a cloud quando o aplicativo usa as APIs do sistema de arquivo nativo para partilhar dados entre os mesmos e outras aplicações em execução no Azure.</li>
<li>Store de desenvolvimento e ferramentas que precisam de ser acedidos a partir do número de VMs de depuração.</li>
</ul>
</td>
</tr>
<tr><td>Blobs do Azure
</td>
<td>Dispõe de bibliotecas de cliente e um <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> interface que permite que os dados não estruturados ser armazenados e acedidos em grande escala nos blobs de blocos. Também suporta <a href="/azure/storage/blobs/data-lake-storage-introduction">geração 2 de armazenamento do Azure Data Lake</a> para soluções de análise de grandes volumes de dados empresariais.
</td>
<td><ul>
<li>Suporta cenários de transmissão em fluxo e de acesso aleatório num aplicativo.</li>
<li>Tem acesso aos dados da aplicação em qualquer lugar.</li>
<li>Crie um lake de dados empresariais no Azure e realizar análises de macrodados.</li>
</ul></td>
</tr>
<tr><td>Discos do Azure
</td>
<td>Dispõe de bibliotecas de cliente e um <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> interface que permite que os dados de forma permanente ser armazenados e acedidos a partir de um disco rígido virtual anexado.
</td>
<td><ul>
<li>Lift- and -shift de aplicações que utilizam APIs de sistema de ficheiros de nativo para ler e gravar dados em discos persistentes.</li>
<li>Store dados que não é necessários seja acessado de fora da VM à qual o disco está ligado.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure acesso frequente (online) e de armazenamento amovível (arquivo)

O tipo de armazenamento para um determinado sistema depende dos requisitos do sistema, incluindo o IOPS, débito e tamanho de armazenamento. Para o armazenamento de tipo DASD no seu próprio mainframe, aplicações no Azure, normalmente, utilizam o armazenamento de unidade de discos do Azure em vez disso. Para armazenamento de arquivo de mainframe, o armazenamento de BLOBs é utilizado no Azure.

Os SSDs fornecem o melhor desempenho de armazenamento no Azure. As seguintes opções estão disponíveis (até o momento em deste documento):

| Type         | Tamanho           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB para 64 TB  | IOPS de 1200 para 160,000 |
| SSD Premium  | 32 GB para 32 TB | 12 a 15 000 IOPS     |
| SSD Standard | 32 GB para 32 TB | IOPS de 12 a 2.000      |

Armazenamento de BLOBs oferece o maior volume de armazenamento no Azure. Para além do tamanho de armazenamento, o Azure oferece armazenamento gerido e não geridos. Com o armazenamento gerido, o Azure trata de gerir as contas de armazenamento subjacente. Com o armazenamento não gerido, o utilizador assume a responsabilidade por configuração de contas de armazenamento do Azure de um tamanho adequado cumprir os requisitos de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe realojamento em máquinas de virtuais do Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mover a computação de mainframe para o Azure](mainframe-compute-Azure.md)
- [Decidindo quando usar os Blobs do Azure, ficheiros do Azure ou discos do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [SSD Managed Disks Standard para cargas de trabalho de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralela no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e o recurso de acoplamento: Além do básico](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criação de usuários necessários para uma instalação da funcionalidade de pureScale de Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - criar instância de comando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale solução de banco de dados agrupadas](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud do Microsoft Azure Government para aplicações de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Alliance de modernização de plataforma: IBM Db2 no Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Datacenter Virtual do Azure Lift- and -Shift guia](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
