---
title: Descrição geral do Microsoft Azure Data Box Disk | Microsoft Docs em dados
description: Descreve o Azure Data Box Disk, uma solução cloud que permite transferir grandes quantidades de dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: ea9353ed8c1938fa9b33585a0650b4507c671451
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125035"
---
# <a name="what-is-azure-data-box-disk"></a>O que é o Azure Data Box Disk?

A solução Microsoft Azure Data Box Disk permite enviar terabytes de dados no local para o Azure de forma rápida, económica e fiável. A transferência de dados segura é acelerada pelo envio de 1 a 5 discos de estado sólido (SSDs). Estes discos encriptados 8-TB são enviados para o seu datacenter através de uma transportadora regional.

Pode configurar, ligar e desbloquear rapidamente os discos através do serviço Data Box no portal Azure. Copie os dados para discos e envie os discos novamente para o Azure. No datacenter do Azure, os seus dados são carregados automaticamente de unidades para a cloud através de uma ligação de carregamento de rede privada rápida.

## <a name="use-cases"></a>Casos de utilização

Utilize o Data Box Disk para transferir TBs de dados em cenários sem conectividade de rede limitada. O movimento de dados pode ser único, periódico ou uma transferência de dados em massa inicial seguida de transferências periódicas.

- **Migração única** - Quando é movida uma grande quantidade de dados no local para o Azure. Por exemplo, mover dados de bandas offline para dados de arquivo no armazenamento esporádico do Azure.
- **Transferência incremental** - Quando é efetuada uma transferência em massa inicial com o Data Box Disk (seed) seguida de transferências incrementais através da rede. Por exemplo, são utilizados o Commvault e o Data Box Disk para mover as cópias de segurança para o Azure. Esta migração é seguida pela cópia de dados incrementais utilizando a rede para o Azure Storage.
- **Carregamentos periódicos** - Quando é gerada periodicamente uma grande quantidade de dados e tem de ser movida para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas petrolíferas e parques eólicos.

### <a name="ingestion-of-data-from-data-box"></a>Ingestão de dados da Caixa de Dados

Os fornecedores Azure e os fornecedores não-Azure podem ingerir dados da Azure Data Box. Os serviços Azure que fornecem a ingestão de dados da Azure Data Box incluem:

- **SharePoint Online** - use a Azure Data Box e a SharePoint Migration Tool (SPMT) para migrar o conteúdo da partilha de ficheiros para o SharePoint Online. Utilizando a Caixa de Dados, remove a dependência do seu link WAN para transferir os dados. Para obter mais informações, consulte [use a Caixa de Dados Azure Heavy para migrar o conteúdo da partilha de ficheiros para o SharePoint Online](data-box-heavy-migrate-spo.md).

- **Azure File Sync** - replica ficheiros da sua Caixa de Dados para uma partilha de ficheiros Azure, permitindo-lhe centralizar os seus serviços de ficheiros no Azure, mantendo o acesso local aos seus dados. Para obter mais informações, consulte [implementar o Sync de ficheiros Azure](../storage/files/storage-sync-files-deployment-guide.md).

- **HdFS armazena** - migrar dados de uma loja de ficheiros distribuídos Hadoop (HDFS) do seu cluster Hadoop para o Armazenamento Azure usando a Data Box. Para obter mais informações, consulte [Migrar da loja ON-Prem HDFS para o Azure Storage com Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Azure Backup** - permite mover grandes backups de dados críticos da empresa através de mecanismos offline para um Cofre de Serviços de Recuperação Azure. Para mais informações, consulte [a visão geral da Cópia de Segurança do Azure](../backup/backup-overview.md).

Pode utilizar os seus dados da Caixa de Dados com muitos prestadores de serviços não-Azure. Por exemplo:

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** - permite-lhe migrar grandes volumes de dados para o Microsoft Azure usando a Caixa de Dados Azure.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** - permite-lhe fazer backup e replicar grandes quantidades de dados da sua máquina Hiper-V para a sua Caixa de Dados.

Para obter uma lista de outros prestadores de serviços não-Azure que se integram com a Data Box, consulte [a Azure Data Box Partners](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>Fluxo de trabalho

Um fluxo típico inclui os seguintes passos:

1. **Encomenda** - Crie uma encomenda no portal Azure, forneça informações de envio e o destino Azure Storage conta para os seus dados. Se os discos estiverem disponíveis, o Azure encripta-os, prepara-os e envia-os com um ID de controlo de envio.

2. **Receber** - Assim que os discos forem entregues, desembale-os e ligue-os ao computador que tem os dados a copiar. Desbloqueie os discos.

3. **Copiar dados** - Arraste e largue para copiar os dados para os discos.

4. **Devolver** - Prepare e envie os discos de volta para o datacenter do Azure.

5. **Carregar** - Os dados são copiados automaticamente dos discos para o Azure. Os discos são apagados em segurança de acordo com as diretrizes do National Institute of Standards and Technology (NIST).

Ao longo deste processo, é notificado através de e-mail todas as alterações de estado. Para obter mais informações sobre o fluxo detalhado, aceda a [Implementar Data Box Disks no portal do Azure](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Benefícios

O Data Box Disk foi concebido para mover grandes quantidades de dados para o Azure sem afetar a rede. A solução possui os benefícios seguintes:

- **Velocidade** - O Data Box Disk utiliza uma ligação USB 3.0 para mover até 35 TB de dados para o Azure em menos de uma semana.

- **Fácil de utilizar** - O Data Box é uma solução de fácil utilização.

  - Os discos utilizam a conectividade USB quase sem tempo de configuração.
  - Os discos têm um fator de forma pequeno que facilita a utilização.
  - Os discos não têm requisitos de alimentação externa.
  - Os discos podem ser utilizados com servidores de datacenter, computadores de secretária e portáteis.
  - A solução fornece um rastreio de ponta a ponta utilizando o portal Azure.

- **Proteger** - O Data Box Disk tem proteções de segurança incorporadas para os discos, os dados e o serviço.
  - Os discos são resistentes a adulterações e suportam atualizações seguras.
  - Os dados nos discos são sempre protegidos com uma encriptação AES de 128 bits.
  - Os discos só podem ser desbloqueados com uma chave fornecida no portal do Azure.
  - O serviço está protegido pelos recursos de segurança do Azure.
  - Assim que os dados forem carregados para o Azure, são apagados de acordo com as normas 800-88r1 do NIST.  

Para obter mais informações, aceda a [Proteção de dados e segurança do Azure Data Box Disk](data-box-disk-security.md).

## <a name="features-and-specifications"></a>Funcionalidades e especificações

| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | < 1 kg por caixa. Até 5 discos na caixa                |
| Dimensões                                              | Disco - SSD de 2,5" |
| Cabos                                                  | 1 cabo USB 3.1 por disco|
| Capacidade de armazenamento por encomenda                              | 40 TB (~ 35 TB utilizáveis)|
| Capacidade de armazenamento do disco                                   | 8 TB (~ 7 TB utilizáveis)|
| Interface de dados                                          | USB   |
| Segurança                                                | Pré-encriptada com BitLocker e atualizações seguras <br> Discos protegidos por chave de acesso <br> Dados sempre encriptados  |
| Taxa de transferência de dados                                      | até 430 MBps, consoante o tamanho do ficheiro      |
|Gestão                                               | Portal do Azure |

## <a name="region-availability"></a>Disponibilidade de região

Para obter informações sobre a disponibilidade da região, aceda aos [produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) O Disco de Caixa de Dados também pode ser implantado na Nuvem do Governo de Azure. Para mais informações, veja [o que é o Governo Azure?](../azure-government/documentation-government-welcome.md)

## <a name="pricing"></a>Preços

Para obter informações sobre preços, [aceda](https://azure.microsoft.com/pricing/details/databox/disk/)à página de Preços .

## <a name="next-steps"></a>Passos seguintes

- Reveja os [Requisitos do Data Box Disk](data-box-disk-system-requirements.md).
- Compreenda os [Limites do Data Box Disk](data-box-disk-limits.md).
- Implemente rapidamente o [Azure Data Box Disk](data-box-disk-quickstart-portal.md) no portal do Azure.