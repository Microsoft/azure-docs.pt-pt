---
title: Limites de recursos - instância gerida
description: Este artigo fornece uma visão geral dos limites de recursos de base de dados Azure SQL para instâncias geridas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365393"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Visão geral Azure SQL Base de dados gerido limites de recursos de instância

Este artigo fornece uma visão geral das características técnicas e limites de recursos para a instância gerida pela Base de Dados Azure SQL, e fornece informações sobre como solicitar um aumento para estes limites.

> [!NOTE]
> Para diferenças nas funcionalidades suportadas e nas declarações T-SQL ver [Diferenças](sql-database-features.md) de recurso e suporte de [declaração T-SQL](sql-database-managed-instance-transact-sql-information.md). Para as diferenças gerais entre os níveis de serviço numa única base de dados e a instância gerida, consulte a [comparação do nível de serviço](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Características de geração de hardware

O exemplo gerido tem características e limites de recursos que dependem da infraestrutura e arquitetura subjacentes. A instância gerida pela Base de Dados Azure SQL pode ser implementada em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, conforme descrito na tabela seguinte:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Processadores Intel E5-2673 v3 (Haswell) 2.4-GHz, ssd vCore = 1 PP (núcleo físico) | Processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake), processadores NVMe SSD rápidos, vCore=1 LP (hiper-fio) |
| Número de vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória máxima (relação memória/núcleo) | 7 GB por vCore<br/>Adicione mais vCores para obter mais memória. | 5.1 GB por vCore<br/>Adicione mais vCores para obter mais memória. |
| Memória Max OLTP memória | Limite de instância: 1-1,5 GB por vCore| Limite de instância: 0.8 - 1,65 GB por vCore |
| Armazenamento reservado de instância max |  Propósito Geral: 8 TB<br/>Business Critical: 1 TB | Propósito Geral: 8 TB<br/> Business Critical 1 TB, 2 TB ou 4 TB dependendo do número de núcleos |

> [!IMPORTANT]
> - O hardware gen4 está a ser eliminado gradualmente e já não está disponível para as novas implementações. Todas as novas instâncias geridas devem ser implementadas no hardware gen5.
> - Considere [mover as suas instâncias geridas para](sql-database-service-tiers-vcore.md) hardware gen 5 para experimentar uma gama mais ampla de vCore e escalabilidade de armazenamento, networking acelerado, melhor desempenho iO e latência mínima.

### <a name="in-memory-oltp-available-space"></a>Espaço disponível oLTP em memória 

A quantidade de espaço OLTP em memória no nível de serviço [Business Critical](sql-database-service-tier-business-critical.md) depende do número de vCores e geração de hardware. Na tabela a seguir estão listados os limites de memória que podem ser utilizados para objetos OLTP em memória.

| Espaço OLTP em memória  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 vCores  | 3.14 GB | |   
| 8 vCores  | 6.28 GB | 8 GB |
| 16 vCores | 15.77 GB | 20 GB |
| 24 vCores | 25.25 GB | 36 GB |
| 32 vCores | 37.94 GB | |
| 40 vCores | 52.23 GB | |
| 64 vCores | 99.9 GB    | |
| 80 vCores | 131.68 GB| |

## <a name="service-tier-characteristics"></a>Características do nível de serviço

A instância gerida tem dois níveis de serviço: [General Purpose](sql-database-service-tier-general-purpose.md) e [Business Critical](sql-database-service-tier-business-critical.md). Estes níveis fornecem [diferentes capacidades,](sql-database-service-tiers-general-purpose-business-critical.md)como descrito na tabela abaixo.

> [!Important]
> O nível de serviço Business Critical fornece cópia adicional incorporada de instância (réplica secundária) que pode ser usada para apenas carga de trabalho de leitura. Se conseguir separar consultas de leitura e consultas de leitura/analítica/reportagem, está a receber duas vCores e memória pelo mesmo preço. A réplica secundária pode ficar alguns segundos atrás da instância primária, por isso foi concebida para descarregar carga de trabalho de reporte/analítico que não precisa do estado atual exato dos dados. Na tabela abaixo, as **consultas apenas de leitura** são as consultas que são executadas em réplica secundária.

| **Funcionalidade** | **Propósito Geral** | **Business Critical** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*O mesmo número de vCores é dedicado a consultas apenas de leitura. |
| Memória máxima | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 20.4 GB - 408 GB (5.1GB/vCore)<br/>Adicione mais vCores para obter mais memória. | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 20.4 GB - 408 GB (5.1GB/vCore) para consultas de leitura<br/>+ 20,4 GB adicionais - 408 GB (5.1GB/vCore) para consultas apenas de leitura.<br/>Adicione mais vCores para obter mais memória. |
| Tamanho máximo de armazenamento de instância (reservado) | - 2 TB para 4 vCores (apenas Gen5)<br/>- 8 TB para outros tamanhos | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB para 4, 8, 16 vCores<br/>- 2 TB para 24 vCores<br/>- 4 TB para 32, 40, 64, 80 vCores |
| Tamanho máx. da base de dados | Até ao tamanho da instância atualmente disponível (máx 2 TB - 8 TB dependendo do número de vCores). | Até ao tamanho da instância atualmente disponível (máx 1 TB - 4 TB dependendo do número de vCores). |
| Tamanho máximo tempDB | Limitado a 24 GB/vCore (96 - 1.920 GB) e atualmente disponível tamanho de armazenamento de instâncias.<br/>Adicione mais vCores para obter mais espaço TempDB.<br/> O tamanho do ficheiro de registo está limitado a 120 GB.| Até o tamanho de armazenamento de instâncias atualmente disponível. |
| Número máximo de bases de dados por instância | 100, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. | 100, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. |
| Número máximo de ficheiros de base de dados por instância | Até 280, a menos que tenha sido atingido o tamanho do armazenamento da instância ou o limite de espaço de alocação de [armazenamento do Disco Premium Azure.](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) | 32.767 ficheiros por base de dados, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. |
| Tamanho máximo do ficheiro de dados | Limitado ao tamanho de armazenamento de instâncias atualmente disponível (máx 2 TB - 8 TB) e espaço de alocação de [armazenamento de Disco Premium Azure.](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) | Limitado ao tamanho de armazenamento de instâncias atualmente disponível (até 1 TB - 4 TB). |
| Tamanho máximo do ficheiro de registo | Limitado a 2 TB e atualmente disponível tamanho de armazenamento de instância. | Limitado a 2 TB e atualmente disponível tamanho de armazenamento de instância. |
| IOPS de dados/registo (aproximação) | Até 30-40 K IOPS por instância*, 500 - 7500 por ficheiro<br/>\*[Aumentar o tamanho dos ficheiros para obter mais IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Adicione mais vCores para obter um melhor desempenho iO. |
| Limite de entrada de escrita de registo (por exemplo) | 3 MB/s por vCore<br/>Max 22 MB/s | 4 MB/s por vCore<br/>Max 48 MB/s |
| Entrada de dados (aproximada) | 100 - 250 MB/s por ficheiro<br/>\*[Aumente o tamanho do ficheiro para obter um melhor desempenho de IO](#file-io-characteristics-in-general-purpose-tier) | Não limitado. |
| Latência IO de armazenamento (aproximada) | 5-10 ms | 1-2 ms |
| OLTP em memória | Não suportado | Disponível, [o tamanho depende do número de vCore](#in-memory-oltp-available-space) |
| Sessões max | 30000 | 30000 |
| [Réplicas só de leitura](sql-database-read-scale-out.md) | 0 | 1 (incluído no preço) |

> [!NOTE]
> - **Atualmente disponível,** o tamanho do armazenamento por exemplo é a diferença entre o tamanho reservado da instância e o espaço de armazenamento usado.
> - Tanto o tamanho dos dados como o tamanho do ficheiro de registo nas bases de dados do utilizador e do sistema estão incluídos no tamanho do armazenamento por exemplo que é comparado com o limite de tamanho de armazenamento Max. Utilize a visão do sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> para determinar o espaço total utilizado por bases de dados. Os registos de erro não são persistidos e não estão incluídos no tamanho. As cópias de segurança não estão incluídas no tamanho do armazenamento.
> - A entrada e o IOPS no nível de Propósito Geral também dependem do tamanho do [ficheiro](#file-io-characteristics-in-general-purpose-tier) que não é explicitamente limitado por instância gerida.
> - Pode criar outra réplica legível em diferentes regiões de Azure utilizando grupos de falha automática.
> - Os IOPS de instância máxima dependem do layout de ficheiros e distribuição da carga de trabalho. Como exemplo, se criar ficheiros 7 x 1TB com iOPS max 5K cada e 7 pequenos ficheiros (menores que 128 GB) com 500 IOPS cada, pode obter 38500 IOPS por instância (7x5000+7x500) se a sua carga de trabalho puder utilizar todos os ficheiros. Note que uma quantidade de IOPS também é usada para cópias de segurança automática.

> [!NOTE]
> Encontre mais informações sobre os limites de [recursos em piscinas de instânciageridas neste artigo.](sql-database-instance-pools.md#instance-pools-resource-limitations)

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Características iO do ficheiro no nível de Propósito Geral

No nível de serviço General Purpose, todos os ficheiros de base de dados estão a receber IOPS dedicados e a uma entrada que depende do tamanho do ficheiro. Ficheiros maiores estão a receber mais IOPS e entrada. As características iO dos ficheiros da base de dados são mostradas no quadro seguinte:

| Tamanho dos ficheiros | >=0 e <=128 GiB | >128 e <=256 GiB | >256 e <= 512 GiB | >0,5 e <=1 TiB    | >1 e <=2 TiB    | >2 e <=4 TiB | >4 e <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS por ficheiro       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Entrada por ficheiro | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se notar alta latência de IO em algum ficheiro de base de dados ou se vir que o IOPS/a sua entrada está a atingir o limite, poderá melhorar o desempenho [aumentando o tamanho do ficheiro](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Existem também limites de nível de instância, como o limite máximo de escrita de registo de 22 MB/s, pelo que poderá não conseguir chegar ao ficheiro através do ficheiro de registo porque está a atingir o limite de entrada de instâncias.

## <a name="supported-regions"></a>Regiões suportadas

Os casos geridos só podem ser criados em [regiões apoiadas.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Para criar uma instância gerida numa região que não é atualmente suportada, pode [enviar um pedido de apoio através do portal Azure.](quota-increase-request.md)

## <a name="supported-subscription-types"></a>Tipos de subscrições suportadas

A instância gerida suporta atualmente a implantação apenas nos seguintes tipos de subscrições:

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provedor de Serviço sinuoso (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Assinaturas com crédito mensal Azure para assinantes do Estúdio Visual](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações regionais de recursos

Os tipos de subscrição suportados podem conter um número limitado de recursos por região. A instância gerida tem dois limites de incumprimento por região de Azure (que podem ser aumentados a pedido através da criação de um pedido especial de [apoio no portal Azure,](quota-increase-request.md) dependendo de um tipo de tipo de subscrição:

- **Limite de subrede**: O número máximo de subredes onde os casos geridos são implantados numa única região.
- **vCore limite de unidade**: O número máximo de unidades vCore que podem ser implantadas em todas as instâncias de uma única região. Um GP vCore usa uma unidade vCore e um BC vCore leva 4 unidades vCore. O número total de casos não é limitado desde que esteja dentro do limite de unidade vCore.

> [!Note]
> Estes limites são definições predefinidas e não limitações técnicas. Os limites podem ser aumentados a pedido através da criação de um pedido especial de [apoio no portal Azure](quota-increase-request.md) se precisar de instâncias mais geridas na região atual. Como alternativa, pode criar novos casos geridos noutra região do Azure sem enviar pedidos de apoio.

O quadro seguinte mostra os **limites regionais predefinidos** para os tipos de subscrição suportados (os limites de predefinição podem ser alargados através do pedido de apoio descrito abaixo):

|Tipo de subscrição| Número máximo de subredes de instância gerida | Número máximo de unidades vCore* |
| :---| :--- | :--- |
|Pay-as-you-go|3|320|
|CSP |8 (15 em algumas regiões**)|960 (1440 em algumas regiões**)|
|Pay-as-you-go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 em algumas regiões**)|960 (1440 em algumas regiões**)|
|Visual Studio Enterprise|2 |64|
|Plataformas Visual Studio Professional e MSDN|2|32|

\*No planeamento de implementações, tenha em consideração que o nível de serviço Business Critical (BC) requer quatro (4) vezes mais capacidade vCore do que o nível de serviço de Propósito Geral (GP). Por exemplo: 1 GP vCore = 1 unidade vCore e 1 BC vCore = 4 unidades vCore. Para simplificar a sua análise de consumo com os limites de predefinição, resumir as unidades vCore em todas as subredes da região onde são implementadas instâncias geridas e comparar os resultados com os limites unitários da instância para o seu tipo de subscrição. **O número máximo de unidades vCore** aplica-se a cada subscrição numa região. Não existe um limite por sub-redes individual, exceto que a soma de todos os vCores implantados em várias subredes deve ser inferior ou igual ao **número máximo de unidades vCore**.

\*\*Os maiores limites de subnet e vCore estão disponíveis nas seguintes regiões: Austrália Leste, Leste dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Europa Ocidental, Oeste DOS EUA 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Solicitar um aumento de quota para a instância gerida pela SQL

Se precisar de instâncias mais geridas nas suas regiões atuais, envie um pedido de apoio para alargar a quota através do portal Azure. Para mais informações, consulte o pedido de aumento da quota para a Base de [Dados SQL azure](quota-increase-request.md).

## <a name="next-steps"></a>Passos seguintes

- Para mais informações sobre o exemplo gerido, veja [o que é um caso gerido?](sql-database-managed-instance.md)
- Para obter informações sobre preços, consulte a Base de [Dados SQL gerida por preços](https://azure.microsoft.com/pricing/details/sql-database/managed/)de instância .
- Para aprender a criar a sua primeira instância gerida, consulte [o guia quickstart](sql-database-managed-instance-get-started.md).
