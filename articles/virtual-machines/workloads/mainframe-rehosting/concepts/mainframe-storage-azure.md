---
title: Mova o armazenamento do mainframe para o Armazenamento Azure
description: Recursos de armazenamento Azure massivamente escaláveis podem ajudar as organizações baseadas em mainframea a migrar e modernizar as aplicações IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76288919"
---
# <a name="move-mainframe-storage-to-azure"></a>Mova o armazenamento do mainframe para Azure

Para executar cargas de trabalho no Microsoft Azure, precisa de saber como as capacidades do seu mainframe se comparam ao Azure. Os recursos de armazenamento massivamente escaláveis podem ajudar as organizações a começar a modernizar-se sem abandonar as aplicações em que confiam.

O Azure fornece funcionalidades e capacidade de armazenamento semelhantes ao mainframe que é comparável aos sistemas baseados em Z14 (o modelo mais atual a partir desta escrita). Este artigo diz-lhe como obter resultados comparáveis no Azure.

## <a name="mainframe-storage-at-a-glance"></a>Armazenamento mainframe em um relance

O mainframe da IBM caracteriza o armazenamento de duas formas. O primeiro é um dispositivo de armazenamento de acesso direto (DASD). O segundo é armazenamento sequencial. Para gestão do armazenamento, o computador principal fornece o Subsistema de Gestão de Armazenamento de Instalações de Dados (DFSMS). Gere o acesso de dados aos vários dispositivos de armazenamento.

[A DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refere-se a um dispositivo separado para armazenamento secundário (não na memória) que permite utilizar um endereço único para acesso direto de dados. Originalmente, o termo DASD aplicado a discos giratórios, tambores magnéticos ou células de dados. No entanto, agora o termo também pode aplicar-se a dispositivos de armazenamento de estado sólido (SSDs), redes de área de armazenamento (SANs), armazenamento ligado à rede (NAS) e unidades óticas. Para efeitos deste documento, a DASD refere-se a discos giratórios, SANs e SSDs.

Em contraste com o armazenamento DASD, o armazenamento sequencial num computador principal refere-se a dispositivos como unidades de fita em que os dados são acedidos a partir de um ponto de partida, depois lidos ou escritos numa linha.

Os dispositivos de armazenamento são normalmente ligados usando uma ligação de fibra (FICON) ou são acedidos diretamente no ônibus IO do mainframe usando [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), uma tecnologia IBM para comunicações de alta velocidade entre divisórias num servidor com um hipervisor.

A maioria dos sistemas de computador principal separam o armazenamento em dois tipos:

- *O armazenamento online* (também conhecido como armazenamento quente) é necessário para operações diárias. O armazenamento DASD é geralmente utilizado para este fim. No entanto, o armazenamento sequencial, como cópias de segurança de fita diária (lógica ou física), também pode ser usado para o efeito.

- *O armazenamento* de arquivo (também conhecido como armazenamento a frio) não é garantido para ser montado num dado momento. Em vez disso, é montado e acessado conforme necessário. O armazenamento de arquivo é frequentemente implementado usando cópias de fita sequencial (lógica ou física) para armazenamento.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus Latência IO e IOPS

Os mainframes são frequentemente usados para aplicações que requerem io de alto desempenho e baixa latência iO. Podem fazê-lo utilizando as ligações FICON para dispositivos IO e HiperSockets. Quando as HiperSockets são utilizadas para ligar aplicações e dispositivos diretamente ao canal IO de um computador principal, a latência nos microsegundos pode ser alcançada.

## <a name="azure-storage-at-a-glance"></a>Armazenamento azure em um relance

As opções de infraestruturas azure -as-a-service[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)para armazenamento proporcionam uma capacidade de estrutura principal comparável.

A Microsoft oferece petabytes de armazenamento no valor de aplicações hospedadas no Azure, e você tem várias opções de armazenamento. Estes vão desde o armazenamento SSD para armazenamento de bolhas de alto custo até armazenamento de blob de baixo custo para armazenamento em massa e arquivos. Além disso, o Azure fornece uma opção de redundância de dados para armazenamento - algo que requer mais esforço para se instalar num ambiente mainframe.

O armazenamento Azure está disponível como [Discos Azure, Ficheiros](/azure/virtual-machines/windows/managed-disks-overview) [Azure](/azure/storage/files/storage-files-introduction)e [Blobs Azure](/azure/storage/blobs/storage-blobs-overview) como a seguinte tabela resume. Saiba mais sobre [quando usar cada](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Tipo</th><th>Descrição</th><th>Utilize quando quiser:</th></tr>
</thead>
<tbody>
<tr><td>Ficheiros do Azure
</td>
<td>
Fornece uma interface SMB, bibliotecas de clientes e uma interface <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> que permite o acesso de qualquer lugar a ficheiros armazenados.
</td>
<td><ul>
<li>Levante e altere uma aplicação para a nuvem quando a aplicação utiliza o sistema de ficheiros nativo APIs para partilhar dados entre ele e outras aplicações em funcionamento em Azure.</li>
<li>Ferramentas de desenvolvimento e depuração de lojas que precisam de ser acedidas a partir de muitos VMs.</li>
</ul>
</td>
</tr>
<tr><td>Blobs do Azure
</td>
<td>Fornece bibliotecas de clientes e uma interface <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> que permite armazenar e aceder a dados não estruturados em larga escala em blocos de bolhas. Também suporta <a href="/azure/storage/blobs/data-lake-storage-introduction">o Azure Data Lake Storage Gen2</a> para soluções de análise de big data da empresa.
</td>
<td><ul>
<li>Suporte cenários de streaming e acesso aleatório numa aplicação.</li>
<li>Tenha acesso aos dados de aplicação de qualquer lugar.</li>
<li>Construa um lago de dados empresariais em Azure e execute a análise de big data.</li>
</ul></td>
</tr>
<tr><td>Discos do Azure
</td>
<td>Fornece bibliotecas de clientes e uma interface <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> que permite que os dados sejam armazenados e acedidos persistentemente a partir de um disco rígido virtual anexo.
</td>
<td><ul>
<li>Aplicações de elevação e mudança que utilizam APIs do sistema de ficheiros nativopara ler e escrever dados para discos persistentes.</li>
<li>Guarde dados que não sejam necessários para serem acedidos de fora do VM ao qual o disco está ligado.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Armazenamento quente (online) e frio (arquivo)

O tipo de armazenamento de um determinado sistema depende dos requisitos do sistema, incluindo o tamanho do armazenamento, a entrada e o IOPS. Para armazenamento do tipo DASD num computador principal, as aplicações no Azure normalmente utilizam o armazenamento de unidade azure Disks. Para armazenamento de arquivo principal, o armazenamento de blob é usado no Azure.

Os SSDs proporcionam o maior desempenho de armazenamento no Azure. Estão disponíveis as seguintes opções (a partir da escrita deste documento):

| Tipo         | Tamanho           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD Ultra    | 4 GB a 64 TB  | 1.200 a 160.000 IOPS |
| SSD Premium  | 32 GB a 32 TB | 12 a 15.000 IOPS     |
| SSD Standard | 32 GB a 32 TB | 12 a 2.000 IOPS      |

O armazenamento blob fornece o maior volume de armazenamento em Azure. Além do tamanho do armazenamento, o Azure oferece armazenamento gerido e não gerido. Com armazenamento gerido, o Azure cuida da gestão das contas de armazenamento subjacentes. Com armazenamento não gerido, o utilizador assume a responsabilidade pela criação de contas de armazenamento Azure do tamanho apropriado para satisfazer os requisitos de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Realojamento do mainframe em Máquinas Virtuais Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mova a computação do mainframe para Azure](mainframe-compute-Azure.md)
- [Decidir quando usar Blobs Azure, Ficheiros Azure ou Discos Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Discos geridos por SSD padrão para cargas de trabalho Azure VM](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Recursos IBM

- [Sisplex paralelo na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e a Facilidade de Acoplamento: Para além do Básico](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criação de utilizadores necessários para uma instalação de funcionalidade de pureScale Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Criar comando de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Solução de base de dados agrupada de pureScale Db2](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [Estúdio de Dados IBM](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem do Governo do Microsoft Azure para aplicações mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos migratórios

- [Guia de elevador e turno do Centro de Dados Virtuais Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
