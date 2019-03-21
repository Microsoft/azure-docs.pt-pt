---
title: Limites de recursos de base de dados SQL do Azure - instância gerida | Documentos da Microsoft
description: Este artigo fornece uma visão geral dos limites de recursos do SQL Database do Azure para instâncias geridas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 02/27/2019
ms.openlocfilehash: 09ab154494ad3e1276239e36068255c2042358c5
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223823"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Limites de recursos de instância gerida da base de dados SQL do Azure de descrição geral

Este artigo fornece uma visão geral dos limites de recursos de instância gerida da base de dados SQL do Azure e fornece informações como criar o pedido para aumentar os limites de subscrição regional predefinidos.

> [!NOTE]
> Para outras limitações de instância gerida, veja [modelo de compra baseado em vCore](sql-database-managed-instance.md#vcore-based-purchasing-model) e [escalões de serviço da instância gerida](sql-database-managed-instance.md#managed-instance-service-tiers). Para diferenças em funcionalidades suportadas e T-SQL Consulte instruções [diferenças de funcionalidades](sql-database-features.md) e [suporte de instrução de T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limites de recursos de nível de instância

A instância gerida tem características e os limites de recursos que depende da infraestrutura e a arquitetura subjacente. Os limites dependem do escalão de serviço de geração e de hardware.

### <a name="hardware-generation-characteristics"></a>Características de geração de hardware

Instância de gerida de base de dados de SQL do Azure pode ser implementada na geração de hardware de dois (Gen4 e Gen5). Gerações de hardware têm diferentes características que são descritas na tabela a seguir:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | V3 Intel E5-2673 processadores de 2,4 GHz (Haswell), anexado SSD vCore = 1 PP (núcleos físicos) | Intel E5-2673 v4 (Broadwell) 2.3 GHz processadores, rápida de NVMe SSD, vCore = 1 LP (hyper-thread) |
| Computação | 8, 16, 24 vCores | 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória | 7 GB por vCore | 5.1 GB por vCore |
| Memória de OLTP dentro da memória | 3 GB por vCore | 2.6 GB por vCore |
| Armazenamento máximo (fins gerais) |  8 TB | 8 TB |
| Armazenamento máximo (crítico para a empresa) | 1 TB | 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

### <a name="service-tier-characteristics"></a>Características de camada de serviço

A instância gerida tem dois escalões de serviço - fins gerais e crítico para a empresa. Estes escalões oferecem recursos diferentes, conforme descrito na tabela abaixo:

| **Funcionalidade** | **Fins gerais** | **Crítico para a empresa** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| Memória | Gen4: 56 GB - 168 GB<br/>Gen5: 40.8 GB - 408 GB<br/>\*Proporcional ao número de vCores | Gen4: 56 GB - 168 GB <br/> Gen5: 40.8 GB - 408 GB<br/>\*Proporcional ao número de vCores |
| Tamanho máximo de armazenamento | 8 TB | Gen4: 1 TB <br/> Gen5: <br/>-1 TB para 8, 16 vCores<br/>-2 TB para 24 vCores<br/>-4 TB para 32, 40, 64, 80 vCores |
| Armazenamento máximo por base de dados | Determinado pelo tamanho de armazenamento máximo por instância | Determinado pelo tamanho de armazenamento máximo por instância |
| Número máx. de bases de dados por instância | 100 | 100 |
| Ficheiros de base de dados máximo por instância | Até 280 | 32.767 ficheiros por base de dados |
| IOPS de dados/do registo (aproximado) | 500 - 7500 por arquivo<br/>\*[Depende do tamanho de ficheiro](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 mil - 110 mil (1,375 por vCore) |
| Taxa de transferência do registo | 22 MB/s por instância | 3 MB/s por vCore<br/>Máx. de 48 MB/s por instância|
| Débito de dados (aproximado) | 100 - 250 MB/s por arquivo<br/>\*[Depende do tamanho de ficheiro](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 24 - 48 MB/s por vCore |
| Latência de e/s (aproximada) | 5-10 ms | 1-2 ms |
| Tamanho máximo de tempDB | 192 - 1,920 GB (24 GB por vCore) | Sem restrições - limitadas pelo tamanho de armazenamento máximo da instância |

**Notas de**:

- Tamanho do ficheiro de dados e de registo no utilizador e bases de dados do sistema estão incluídas no tamanho de armazenamento de instância é comparado com o limite de tamanho de armazenamento máximo. Uso <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">master_files</a> vista de sistema para determinar o total de espaço utilizado pelas bases de dados. Registos de erros não são persistentes e não incluídos no tamanho. As cópias de segurança não estão incluídas no tamanho de armazenamento.
- Débito e IOPS também dependem do tamanho de página que não está limitado explicitamente por instância gerida.

## <a name="supported-regions"></a>Regiões suportadas

Instanced gerido pode ser criado apenas no [regiões suportadas](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se quiser criar uma instância gerida na região que não é atualmente suportado, pode [enviar o pedido de suporte através do portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipos de subscrição de suporte

Atualmente, a instância gerida suporta implementação apenas nos seguintes tipos de subscrições:

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Fornecedor de serviços cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise programador/teste](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> Esta limitação é temporária. Novos tipos de subscrição vão ser ativados no futuro.

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

Tipos de subscrição de suporte podem conter um número limitado de recursos por região. A instância gerida tem dois limites padrão por região do Azure, dependendo de um tipo de tipo de subscrição:

- **Limite de sub-redes**: O número máximo de sub-redes em que as instâncias geridas são implementadas numa única região.
- **Limite de número de instância**: O número máximo de instâncias que podem ser implementados numa única região.

> [!Note]
> Estes limites são as predefinições e limitações não técnicas. Os limites podem ser demanda aumentada através da criação especial [pedido de suporte no portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se precisar de mais instâncias geridas na região atual. Como alternativa, pode criar novas instâncias geridas noutra região do Azure sem enviar pedidos de suporte.

Na tabela a seguir são apresentados limites regionais predefinidos para subscrições de suporte:

|Tipo de subscrição| Número máx. de sub-redes de instância gerida | Número máximo de instâncias |Número máx. de GP geridos instâncias *|Número máx. de BC geridos instâncias *|
| :---| :--- | :--- |:--- |:--- |
|"Pay-as-you-go"|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|Pay as you go programador/teste|1*|4*|4*|1*|
|Enterprise Dev/Test|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* Pode optar por implementar 1 BC ou 4 instâncias GP numa sub-rede, para que o número total de "unidades de instância" na sub-rede nunca exceda 4.

* * Máximo número de instâncias numa camada de serviço aplica-se se não houver nenhuma instância da outra camada de serviço. No caso de pretender ao combinar as instâncias de GP e BC na mesma sub-rede, utilize a secção seguinte como uma referência para as combinações permitidas. Como regra simple, o número total de sub-redes não pode exceder os 3 e o número total de unidades de instância não pode exceder os 12.



> [!IMPORTANT]
> Quando planear as suas implementações, considere que uma instância de negócio críticos (BC) (devido a redundância adicionada) geralmente consome 4 x de uma instância de finalidade geral (GP) mais capacidade. Assim, para seus cálculos, 1 instância GP = 1 instância de unidade e a instância de 1 BC = 4 unidades de instância. Para simplificar a análise de consumo contra os limites predefinidos, resumir as unidades de instância em todas as sub-redes na região em que estão implementadas instâncias geridas e comparar os resultados com os limites de unidade de instância para o seu tipo de subscrição.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>Estratégias para implementar instâncias de fins gerais e crítico para a empresa mistas

[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) subscrições podem tem combinações de instâncias GP e BC. No entanto, existem algumas restrições sobre a colocação das instâncias nas sub-redes.

> [!Note]
> [Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) e [fornecedor de serviços Cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) tipos de subscrição podem ter qualquer um de um críticos de negócios ou até 4 instâncias para fins gerais.

Os exemplos seguintes abrangem casos de implantação com sub-redes de não vazios e misto GP e BC escalões de serviço.

|Número de sub-redes|Sub-rede 1|Sub-rede 2|Sub-rede 3|
|:---|:---|:---|:---|
|1|1 BC e até 8 GP<br>2 BC e até 4 GP|N/A| N/A|
|2|BC 0, até 4 GP|C. 1, até 4 GP<br>2 BC, 0 GP|N/A|
|2|1 BC, 0 GP|BC 0, até 8 GP<br>C. 1, até 4 GP|N/A|
|2|2 BC, 0 GP|BC 0, até 4 GP|N/A|
|3|1 BC, 0 GP|1 BC, 0 GP|BC 0, até 4 GP|
|3|1 BC, 0 GP|BC 0, até 4 GP|BC 0, até 4 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtendo uma quota maior para a instância gerida SQL

Se precisar de mais instâncias geridas em suas regiões atuais, pode enviar o pedido de suporte para aumentar a quota com o portal do Azure.
Para iniciar o processo de obtenção de uma quota maior:

1. Open **ajuda + suporte**e clique em **novo pedido de suporte**.

   ![Ajuda e Suporte](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na guia Noções básicas para o novo pedido de suporte:
   - Para **tipo de problema**, selecione **limites de serviço e subscrição (cotas)**.
   - Em **Subscrição**, selecione a sua subscrição.
   - Para **tipo de Quota**, selecione **SQL Database Managed Instance**.
   - Para **plano de suporte**, selecione o seu plano de suporte.

     ![Quota de tipo de problema](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Clique em **Seguinte**.
4. No separador de problema para o novo pedido de suporte:
   - Para **gravidade**, selecione o nível de gravidade do problema.
   - Para **detalhes**, fornecem informações adicionais sobre o problema, incluindo mensagens de erro.
   - Para **carregamento de ficheiros**, anexar um ficheiro com mais informações (até 4 MB).

     ![Detalhes do problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Uma solicitação válida deve incluir:
     > - Região na qual assinatura limite tem de ser aumentado
     > - Aumentar o número necessário de instâncias, por camada de serviços em sub-redes existentes após a quota (se nenhuma das sub-redes existentes tem de ser expandido
     > - Necessário número de novas sub-redes e o número total de instâncias por escalão de serviço dentro as novas sub-redes (se precisar de implementar instâncias geridas nas novas sub-redes).

5. Clique em **Seguinte**.
6. No separador de informações de contacto para o novo pedido de suporte, introduza o método preferencial de contacto (e-mail ou telefone) e os detalhes de contactos.
7. Clique em **Criar**.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre a instância gerida, veja [o que é uma instância gerida?](sql-database-managed-instance.md).
- Para obter informações sobre preços, consulte [preços da SQL Database Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar a sua primeira instância gerida, veja [guia de início rápido](sql-database-managed-instance-get-started.md).
