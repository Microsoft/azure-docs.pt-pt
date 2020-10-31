---
title: Limites de recursos
titleSuffix: Azure SQL Managed Instance
description: Este artigo fornece uma visão geral dos limites de recursos para Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: 11c3de703a4b37318b7b99f60d74190fe8ec8610
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077375"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Descrição geral dos limites de recursos do Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo fornece uma visão geral das características técnicas e limites de recursos para a Azure SQL Managed Instance, e fornece informações sobre como solicitar um aumento a estes limites.

> [!NOTE]
> Para diferenças nas funcionalidades suportadas e declarações T-SQL ver [diferenças de características](../database/features-comparison.md) e [suporte de declaração T-SQL](transact-sql-tsql-differences-sql-server.md). Para diferenças gerais entre os níveis de serviço para Azure SQL Database e SQL Managed Instance ver [comparação de nível de serviço](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Características de geração de hardware

A SQL Managed Instance tem características e limites de recursos que dependem da infraestrutura e arquitetura subjacentes. SQL Managed Instance pode ser implementado em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, conforme descrito na tabela seguinte:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **Hardware** | Processadores Intel® E5-2673 v3 (Haswell) 2.4 GHz, anexados SSD vCore = 1 PP (núcleo físico) | Intel® E5-2673 v4 (Broadwell) 2.3 GHz, Intel® SP-8160 (Skylake) e Intel® 8272CL (Lago cascata) 2,5 GHz processadores, rápido NVMe SSD, vCore=1 LP (hiper-thread) |
| **Número de vCores** | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| **Memória máxima (relação memória/núcleo)** | 7 GB por vCore<br/>Adicione mais vCores para obter mais memória. | 5.1 GB por vCore<br/>Adicione mais vCores para obter mais memória. |
| **Max In-Memory memória OLTP** | Limite de instância: 1-1.5 GB por vCore| Limite de exemplo: 0.8 - 1.65 GB por vCore |
| **Armazenamento reservado de instância máxima** |  Finalidade Geral: 8 TB<br/>Critical de negócios: 1 TB | Finalidade Geral: 8 TB<br/> Business Critical 1 TB, 2 TB ou 4 TB dependendo do número de núcleos |

> [!IMPORTANT]
> - O hardware da Gen4 está a ser eliminado e já não está disponível para novas implementações. Todas as novas instâncias de SQL Managed Instance devem ser implementadas no hardware da Gen5.
> - Considere [mover o seu exemplo de SQL Managed Instance para o](../database/service-tiers-vcore.md) hardware Da Gen 5 para experimentar uma gama mais ampla de vCore e escalabilidade de armazenamento, networking acelerado, melhor desempenho de IO e latência mínima.

### <a name="in-memory-oltp-available-space"></a>Espaço disponível em memória OLTP 

A quantidade de espaço OLTP na memória no nível de serviço [Business Critical](../database/service-tier-business-critical.md) depende do número de vCores e geração de hardware. A tabela que se segue lista limites de memória que podem ser utilizados para objetos OLTP na memória.

| Espaço OLTP na memória  | **Gen5** | **Gen4** |
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

Sql Managed Instance tem dois níveis de serviço: [Final geral](../database/service-tier-general-purpose.md) e Critical [de Negócios.](../database/service-tier-business-critical.md) Estes níveis fornecem [diferentes capacidades,](../database/service-tiers-general-purpose-business-critical.md)conforme descrito na tabela abaixo.

> [!Important]
> Business Critical o nível de serviço fornece uma cópia adicional incorporada da SQL Managed Instance (réplica secundária) que pode ser usada para carga de trabalho apenas de leitura. Se conseguir separar consultas de leitura-escrita e consultas de leitura/analítica/reporte, está a receber o dobro dos vCores e da memória pelo mesmo preço. A réplica secundária pode ficar alguns segundos atrás da instância primária, por isso é projetada para descarregar cargas de trabalho de reporte/analítico que não precisam do estado exato dos dados atuais. Na tabela abaixo, **as consultas apenas de leitura** são as consultas que são executadas em réplicas secundárias.

| **Destaque** | **Fins Gerais** | **Crítico para a Empresa** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*O mesmo número de vCores é dedicado a consultas apenas de leitura. |
| Memória máxima | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 20.4 GB - 408 GB (5.1GB/vCore)<br/>Adicione mais vCores para obter mais memória. | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 20.4 GB - 408 GB (5.1GB/vCore) para consultas de leitura-escrita<br/>+ mais 20,4 GB - 408 GB (5.1GB/vCore) para consultas só de leitura.<br/>Adicione mais vCores para obter mais memória. |
| Tamanho máximo de armazenamento de instância (reservado) | - 2 TB para 4 vCores (apenas Gen5)<br/>- 8 TB para outros tamanhos | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB para 4, 8, 16 vCores<br/>- 2 TB para 24 vCores<br/>- 4 TB para 32, 40, 64, 80 vCores |
| Tamanho máx. da base de dados | Até ao tamanho da instância atualmente disponível (máx. 2 TB - 8 TB dependendo do número de vCores). | Até ao tamanho da instância atualmente disponível (máx. 1 TB - 4 TB dependendo do número de vCores). |
| Tamanho max tempDB | Limitado a 24 GB/vCore (96 - 1.920 GB) e atualmente disponível tamanho de armazenamento de instância.<br/>Adicione mais vCores para obter mais espaço TempDB.<br/> O tamanho do ficheiro de registo está limitado a 120 GB.| Até o tamanho de armazenamento de instância atualmente disponível. |
| Número máximo de bases de dados por exemplo | 100 bases de dados dos utilizadores, a menos que tenha sido atingido o limite de tamanho de armazenamento de instância. | 100 bases de dados dos utilizadores, a menos que tenha sido atingido o limite de tamanho de armazenamento de instância. |
| Número máximo de ficheiros de base de dados por exemplo | Até 280, a menos que o tamanho do armazenamento de instância ou o limite [de espaço de armazenamento do disco Azure Premium](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) tenha sido atingido. | 32.767 ficheiros por base de dados, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. |
| Tamanho máximo do ficheiro de dados | Limitado ao tamanho de armazenamento de instâncias atualmente disponível (max 2 TB - 8 TB) e [espaço de atribuição de armazenamento de disco Azure Premium](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | Limitado ao tamanho de armazenamento de instâncias atualmente disponível (até 1 TB - 4 TB). |
| Tamanho do ficheiro de registo máximo | Limitado a 2 TB e atualmente disponível tamanho de armazenamento de instância. | Limitado a 2 TB e atualmente disponível tamanho de armazenamento de instância. |
| Dados/IOPS de registo (aproximadamente) | Até 30-40 K IOPS por exemplo*, 500 - 7500 por ficheiro<br/>\*[Aumente o tamanho do ficheiro para obter mais IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (4000 IOPS/vCore)<br/>Adicione mais vCores para obter um melhor desempenho de IO. |
| Limite de produção de registo (por exemplo) | 3 MB/s por vCore<br/>Max 120 MB/s por exemplo<br/>22 - 65 MB/s por DB<br/>\*[Aumente o tamanho do ficheiro para obter um melhor desempenho de IO](#file-io-characteristics-in-general-purpose-tier) | 4 MB/s por vCore<br/>Max 96 MB/s |
| Produção de dados (aproximadamente) | 100 - 250 MB/s por ficheiro<br/>\*[Aumente o tamanho do ficheiro para obter um melhor desempenho de IO](#file-io-characteristics-in-general-purpose-tier) | Não é limitado. |
| Latência IO de armazenamento (aproximada) | 5-10 ms | 1-2 ms |
| OLTP dentro da memória | Não suportado | Disponível, [o tamanho depende do número de vCore](#in-memory-oltp-available-space) |
| Sessões max | 30000 | 30000 |
| Trabalhadores max simultâneos (pedidos) | Gen4: 210 * número de vCores + 800<br>Gen5: 105 * número de vCores + 800 | Gen4: 210 * contagem de vCores + 800<br>Gen5: 105 * contagem de vCores + 800 |
| [Réplicas só de leitura](../database/read-scale-out.md) | 0 | 1 (incluído no preço) |
| Isolamento computacional | Gen5:<br/>-suportado por 80 vCores<br/>-não suportado para outros tamanhos<br/><br/>A Gen4 não é apoiada por depreciação|Gen5:<br/>-suportado por 60, 64, 80 vCores<br/>-não suportado para outros tamanhos<br/><br/>A Gen4 não é apoiada por depreciação|


Algumas considerações adicionais: 

- **Atualmente disponível tamanho de armazenamento de instância** é a diferença entre o tamanho de instância reservado e o espaço de armazenamento usado.
- Tanto o tamanho dos dados como o tamanho do ficheiro de registo nas bases de dados do utilizador e do sistema estão incluídos no tamanho do armazenamento de casos que é comparado com o limite máximo de tamanho de armazenamento. Utilize a vista do sistema [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) para determinar o espaço total utilizado por bases de dados. Os registos de erro não são persistidos e não estão incluídos no tamanho. As cópias de segurança não estão incluídas no tamanho do armazenamento.
- A produção e o IOPS no nível de Finalidade Geral também dependem do tamanho do [ficheiro](#file-io-characteristics-in-general-purpose-tier) que não é explicitamente limitado pela SQL Managed Instance.
  Pode criar outra réplica legível numa região de Azure diferente utilizando [grupos de auto-failover](../database/auto-failover-group-configure.md)
- O IOPS de instância máxima depende da disposição do ficheiro e da distribuição da carga de trabalho. Como exemplo, se criar ficheiros 7 x 1TB com max 5K IOPS cada e 7 pequenos ficheiros (menores de 128 GB) com 500 IOPS cada, pode obter 38500 IOPS por exemplo (7x5000+7x500) se a sua carga de trabalho puder utilizar todos os ficheiros. Note que alguns IOPS também são usados para auto-backups.

Encontre mais informações sobre os [limites de recursos nas piscinas SQL Managed Instance neste artigo.](instance-pools-overview.md#resource-limitations)

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Características de IO de ficheiro no nível de finalidade geral

No nível de serviço De Finalidade Geral todos os ficheiros de base de dados recebem IOPS dedicado e produção que dependem do tamanho do ficheiro. Ficheiros maiores obtêm mais IOPS e produção. As características io dos ficheiros de base de dados são mostradas na tabela seguinte:

| Tamanho dos ficheiros | >=0 e <=128 GiB | >128 e <= 512 GiB | >0,5 e <=1 TiB    | >1 e <=2 TiB    | >2 e <=4 TiB | >4 e <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS por ficheiro       | 500   | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Produção por ficheiro | 100 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se notar uma elevada latência de IO em algum ficheiro de base de dados ou se vir que o IOPS/produção está a atingir o limite, poderá melhorar o desempenho [aumentando o tamanho do ficheiro](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Existe também um limite de nível de instância na produção de registo máximo (que é de 22 MB/s), pelo que poderá não conseguir alcançar o ficheiro máximo em todo o ficheiro de registo porque está a atingir o limite de produção de caso.

## <a name="supported-regions"></a>Regiões suportadas

A SQL Managed Instance só pode ser criada em [regiões apoiadas.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Para criar uma SQL Managed Instance numa região que não é suportada, pode [enviar um pedido de apoio através do portal Azure.](../database/quota-increase-request.md)

## <a name="supported-subscription-types"></a>Tipos de subscrições suportadas

A SQL Managed Instance suporta atualmente a implementação apenas nos seguintes tipos de subscrições:

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Fornecedor de serviços na nuvem (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Subscrições com crédito mensal da Azure para assinantes do Estúdio Visual](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

> [!Note]
> Para obter as informações mais recentes sobre a disponibilidade da região para assinaturas, consulte primeiro [uma região](https://aka.ms/sqlcapacity).

Os tipos de subscrição suportados podem conter um número limitado de recursos por região. A SQL Managed Instance tem dois limites por defeito por região de Azure (que podem ser aumentados a pedido através da criação de um pedido especial de [apoio no portal Azure,](../database/quota-increase-request.md) dependendo de um tipo de subscrição:

- **Limite da sub-rede** : O número máximo de sub-redes em que os casos de SQL Managed Instance são implantados numa única região.
- **vCore limite de unidade** : O número máximo de unidades vCore que podem ser implantadas em todas as instâncias numa única região. Um GP vCore usa uma unidade vCore e uma BC vCore leva 4 unidades vCore. O número total de casos não é limitado enquanto estiver dentro do limite da unidade vCore.

> [!Note]
> Estes limites são definições padrão e não limitações técnicas. Os limites podem ser aumentados a pedido através da criação de um pedido especial de [apoio no portal Azure,](../database/quota-increase-request.md) caso necessite de mais casos na região atual. Como alternativa, pode criar novos casos de SQL Managed Instance em outra região do Azure sem enviar pedidos de apoio.

O quadro que se segue mostra os **limites regionais predefinidos** para os tipos de subscrição suportados (os limites por defeito podem ser alargados usando o pedido de apoio descrito abaixo):

|Tipo de subscrição| Número máximo de sub-redes sql de instância gerida | Número máximo de unidades vCore* |
| :---| :--- | :--- |
|Pay as you go|3|320|
|CSP |8 (15 em algumas regiões**)|960 (1440 em algumas regiões**)|
|Pay-as-you-go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 em algumas regiões**)|960 (1440 em algumas regiões**)|
|Visual Studio Enterprise|2 |64|
|Plataformas Visual Studio Professional e MSDN|2|32|

\* Nas implementações de planeamento, tenha em consideração que o nível de serviço Business Critical (BC) requer quatro (4) vezes mais capacidade vCore do que o nível de serviço De Finalidade Geral (GP). Por exemplo: 1 GP vCore = 1 vCore unit e 1 BC vCore = 4 unidades vCore. Para simplificar a sua análise de consumo em função dos limites predefinidos, resumir as unidades vCore em todas as sub-redes da região onde o SQL Managed Instance é implantado e comparar os resultados com os limites de unidade de instância para o seu tipo de subscrição. **O número máximo de unidades vCore** é aplicável a cada subscrição de uma região. Não existe limite por sub-redes individuais, exceto que a soma de todos os vCores implantados em várias sub-redes deve ser inferior ou igual ao **número máximo de unidades vCore** .

\*\* Os limites de sub-rede e vCore de maiores dimensões estão disponíveis nas seguintes regiões: Austrália Oriental, Leste dos EUA, Leste dos EUA 2, Norte da Europa, South Central US, Sudeste Asiático, Reino Unido Sul, Europa Ocidental, Eua Ocidental 2.

> [!IMPORTANT]
> Caso o seu vCore e o limite de sub-rede seja 0, significa que o limite regional padrão para o seu tipo de subscrição não está definido. Também pode utilizar o pedido de aumento de quota para obter acesso por subscrição em região específica seguindo o mesmo procedimento - fornecendo valores de vCore e sub-redes necessários.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de quota

Se precisar de mais casos nas suas regiões atuais, envie um pedido de apoio para alargar a quota através do portal Azure. Para obter mais informações, consulte [os aumentos de quota de pedido para a Base de Dados Azure SQL](../database/quota-increase-request.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o SQL Managed Instance, veja [o que é uma sql Managed Instance?](sql-managed-instance-paas-overview.md)
- Para obter informações sobre preços, consulte [os preços da SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para aprender a criar o seu primeiro SQL Managed Instance, consulte [o guia quickstart](instance-create-quickstart.md).
