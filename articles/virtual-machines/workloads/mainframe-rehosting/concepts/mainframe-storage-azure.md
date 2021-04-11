---
title: Mover o armazenamento do computador principal para o armazenamento do Azure
description: Os recursos de armazenamento Azure massivamente escaláveis podem ajudar as organizações baseadas em mainframe a migrar e modernizar aplicações IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 27f625e7fced5ff710a6fa0e8b2045a8c5434258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954365"
---
# <a name="move-mainframe-storage-to-azure"></a>Mover o armazenamento do computador principal para Azure

Para executar cargas de trabalho no Microsoft Azure, precisa de saber como as capacidades do seu computador principal se comparam ao Azure. Os recursos de armazenamento massivamente escaláveis podem ajudar as organizações a começar a modernizar-se sem abandonar as aplicações em que confiam.

O Azure fornece funcionalidades e capacidade de armazenamento semelhantes ao computador principal que é comparável aos sistemas baseados em Z14 da IBM (o modelo mais atual a partir desta escrita). Este artigo diz-lhe como obter resultados comparáveis no Azure.

## <a name="mainframe-storage-at-a-glance"></a>Armazenamento do computador principal num ápice

O computador central da IBM caracteriza o armazenamento de duas formas. O primeiro é um dispositivo de armazenamento de acesso direto (DASD). O segundo é armazenamento sequencial. Para gerir o armazenamento, o computador principal fornece o Subsistema de Gestão de Armazenamento de Facilidades de Dados (DFSMS). Gere o acesso de dados aos vários dispositivos de armazenamento.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refere-se a um dispositivo separado para armazenamento secundário (não na memória) que permite que um endereço único seja usado para o acesso direto de dados. Originalmente, o termo DASD aplicado a discos giratórios, tambores magnéticos ou células de dados. No entanto, agora o termo também pode aplicar-se a dispositivos de armazenamento em estado sólido (SSDs), redes de área de armazenamento (SANs), armazenamento ligado à rede (NAS) e unidades óticas. Para efeitos deste documento, o DASD refere-se a discos giratórios, SANs e SSDs.

Em contraste com o armazenamento das DASD, o armazenamento sequencial num computador central refere-se a dispositivos como unidades de fita onde os dados são acedidos a partir de um ponto de partida, depois lidos ou escritos numa linha.

Os dispositivos de armazenamento são normalmente ligados usando uma ligação de fibra (FICON) ou são acedidos diretamente no ônibus IO do computador principal usando [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), uma tecnologia IBM para comunicações de alta velocidade entre divisórias num servidor com um hipervisor.

A maioria dos sistemas de computador central separam o armazenamento em dois tipos:

- *O armazenamento online* (também conhecido como armazenamento quente) é necessário para operações diárias. O armazenamento DASD é geralmente utilizado para este fim. No entanto, o armazenamento sequencial, como cópias de segurança diárias de fita (lógica ou física), também pode ser usado para este fim.

- *O armazenamento de* arquivo (também conhecido como armazenamento frio) não é garantido para ser montado num dado momento. Em vez disso, é montado e acedido conforme necessário. O armazenamento de arquivo é frequentemente implementado usando cópias de segurança de fita sequencial (lógica ou física) para armazenamento.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus latência IO e IOPS

Os mainframes são frequentemente usados para aplicações que requerem IO de alto desempenho e baixa latência IO. Podem fazê-lo utilizando as ligações FICON a dispositivos IO e HiperSockets. Quando os HiperSockets são utilizados para ligar aplicações e dispositivos diretamente ao canal IO de um computador central, a latência nos microsegundos pode ser alcançada.

## <a name="azure-storage-at-a-glance"></a>Armazenamento azul num relance

As opções de infraestrutura azul como serviço[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)para armazenamento proporcionam uma capacidade de rede comparável.

A Microsoft oferece petabytes de armazenamento para aplicações hospedadas no Azure, e tem várias opções de armazenamento. Estes variam desde o armazenamento SSD para alto desempenho até armazenamento de blob de baixo custo para armazenamento em massa e arquivos. Além disso, o Azure fornece uma opção de redundância de dados para armazenamento - algo que requer mais esforço para se configurar em um ambiente mainframe.

O armazenamento Azure está disponível como [Azure Disks](../../../managed-disks-overview.md), [Azure Files](../../../../storage/files/storage-files-introduction.md)e [Azure Blobs](../../../../storage/blobs/storage-blobs-overview.md) como resume a tabela seguinte. Saiba mais sobre [quando usar cada um.](../../../../storage/common/storage-introduction.md)

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Tipo</th><th>Description</th><th>Use quando quiser:</th></tr>
</thead>
<tbody>
<tr><td>Ficheiros do Azure
</td>
<td>
Fornece uma interface SMB, bibliotecas de clientes e uma interface <a href="/rest/api/storageservices/file-service-rest-api">REST</a> que permite o acesso de qualquer lugar a ficheiros armazenados.
</td>
<td><ul>
<li>Levante e transloja uma aplicação para a nuvem quando a aplicação utiliza as APIs do sistema de ficheiros nativo para partilhar dados entre ele e outras aplicações em execução em Azure.</li>
<li>Armazenar ferramentas de desenvolvimento e depuragem que precisam de ser acedidas a partir de muitos VMs.</li>
</ul>
</td>
</tr>
<tr><td>Blobs do Azure
</td>
<td>Fornece bibliotecas de clientes e uma interface <a href="/rest/api/storageservices/blob-service-rest-api">REST</a> que permite que os dados não estruturados sejam armazenados e acedidos a uma escala massiva em blobs de bloco. Também suporta <a href="/azure/storage/blobs/data-lake-storage-introduction">a Azure Data Lake Storage Gen2</a> para soluções de análise de big data da empresa.
</td>
<td><ul>
<li>Suporte o streaming e cenários de acesso aleatório numa aplicação.</li>
<li>Tenha acesso aos dados da aplicação a partir de qualquer lugar.</li>
<li>Construa um lago de dados empresariais em Azure e realize análise de big data.</li>
</ul></td>
</tr>
<tr><td>Discos do Azure
</td>
<td>Fornece bibliotecas de clientes e uma interface <a href="/rest/api/compute/disks">REST</a> que permite que os dados sejam armazenados e acedidos persistentemente a partir de um disco rígido virtual anexado.
</td>
<td><ul>
<li>Aplicações de elevação e mudança que usam APIs do sistema de ficheiros nativo para ler e escrever dados para discos persistentes.</li>
<li>Armazenar dados que não sejam necessários para serem acedidos de fora do VM ao qual o disco está ligado.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Armazenamento azure quente (on-line) e frio (arquivo)

O tipo de armazenamento para um determinado sistema depende dos requisitos do sistema, incluindo o tamanho do armazenamento, produção e IOPS. Para o armazenamento do tipo DASD num computador central, as aplicações no Azure normalmente utilizam o armazenamento de unidade de discos Azure. Para armazenamento de arquivo de computador principal, o armazenamento de blob é usado em Azure.

Os SSDs proporcionam o maior desempenho de armazenamento em Azure. Estão disponíveis as seguintes opções (a partir da redação deste documento):

| Tipo         | Tamanho           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD Ultra    | 4 GB a 64 TB  | 1.200 a 160.000 IOPS |
| SSD Premium  | 32 GB a 32 TB | 12 a 15.000 IOPS     |
| SSD Standard | 32 GB a 32 TB | 12 a 2.000 IOPS      |

O armazenamento de blob fornece o maior volume de armazenamento em Azure. Além do tamanho de armazenamento, a Azure oferece armazenamento gerido e não gerido. Com o armazenamento gerido, a Azure cuida da gestão das contas de armazenamento subjacentes. Com o armazenamento não gerido, o utilizador assume a responsabilidade de configurar contas de armazenamento Azure de tamanho adequado para satisfazer os requisitos de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehosagem de mainframe em Máquinas Virtuais Azure](../overview.md)
- [Mover o computador principal para Azure](mainframe-compute-Azure.md)
- [Decidir quando usar Azure Blobs, Ficheiros Azure ou Discos Azure](../../../../storage/common/storage-introduction.md)
- [Discos geridos standard SSD para cargas de trabalho Azure VM](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>Recursos IBM

- [Sysplex Paralelo na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e o Mecanismo de Acoplamento: Além do Básico](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criar utilizadores necessários para uma instalação Db2 pureScale Feature](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Criar comando de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Solução de base de dados clustered pureScale Db2](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [Estúdio de Dados IBM](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem do Governo do Microsoft Azure para aplicações mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos migratórios

- [Guia de elevador e turno do centro de dados virtual Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)