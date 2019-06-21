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
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 06/13/2019
ms.openlocfilehash: 89df33f453217585e8f0b8ff13d06ee223474fae
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272239"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Descrição geral do Azure SQL Database managed limites de recursos de instância

Este artigo fornece uma visão geral dos limites de recursos para a instância gerida da base de dados do Azure SQL e fornece informações sobre como pedir um aumento para estes limites.

> [!NOTE]
> Para diferenças em funcionalidades suportadas e T-SQL Consulte instruções [diferenças de funcionalidades](sql-database-features.md) e [suporte de instrução de T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limites de recursos de nível de instância

A instância gerida tem características e os limites de recursos que dependem de arquitetura e infra-estrutura subjacente. Os limites dependem do escalão de serviço de geração e de hardware.

### <a name="hardware-generation-characteristics"></a>Características de geração de hardware

A instância gerida de base de dados SQL do Azure pode ser implementada em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, conforme descrito na tabela a seguir:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | V3 Intel E5-2673 processadores de 2,4 GHz (Haswell), anexado SSD vCore = 1 PP (núcleos físicos) | Intel E5-2673 v4 (Broadwell) 2.3 GHz processadores, rápida de NVMe SSD, vCore = 1 LP (hyper-thread) |
| vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória (memória/núcleo) | 7 GB por vCore | 5.1 GB por vCore |
| Memória de OLTP máximo em memória | Limite da instância: 3 GB por vCore<br/>Limites de base de dados:<br/> -8 núcleos: 8 GB por base de dados<br/> -16 núcleos: 20 GB por base de dados<br/> -24-core: 36 GB por base de dados | Limite da instância: 2,5 GB por vCore<br/>Limites de base de dados:<br/> -8 núcleos: 13 GB por base de dados<br/> -16 núcleos: 32 GB por base de dados |
| Armazenamento de instância de máximo (fins gerais) |  8 TB | 8 TB |
| Armazenamento de instância de máximo (crítico para a empresa) | 1 TB | 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

### <a name="service-tier-characteristics"></a>Características de camada de serviço

A instância gerida tem dois escalões de serviço: Fins gerais e crítico para a empresa. Estes escalões oferecem recursos diferentes, conforme descrito na tabela abaixo:

| **Funcionalidade** | **Fins gerais** | **Crítico para a empresa** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 |
| Memória | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB - 408 GB (5.1 GB/vCore) | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB - 408 GB (5.1 GB/vCore) |
| Tamanho máximo de armazenamento de instância | -2 TB para 4 vCores (Gen5 apenas)<br/>-8 TB para outros tamanhos | Gen4: 1 TB <br/> Gen5: <br/>-1 TB para 4, 8, 16 vCores<br/>-2 TB para 24 vCores<br/>-4 TB para 32, 40, 64, 80 vCores |
| Armazenamento máximo por base de dados | Determinado pelo tamanho de armazenamento máximo por instância | Determinado pelo tamanho de armazenamento máximo por instância |
| Número máx. de bases de dados por instância | 100 | 100 |
| Ficheiros de base de dados máximo por instância | Até 280 | 32.767 ficheiros por base de dados |
| IOPS de dados/do registo (aproximado) | 500 - 7500 por arquivo<br/>\*[Depende do tamanho de ficheiro](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 mil - 110 K (1375/vCore) |
| Taxa de transferência do registo | 3 MB/s por vCore<br/>Máx. de 22 MB/s por instância | 4 MB/s por vCore<br/>Máx. de 48 MB/s por instância|
| Débito de dados (aproximado) | 100 - 250 MB/s por arquivo<br/>\*[Depende do tamanho de ficheiro](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/A |
| Latência de e/s (aproximada) | 5-10 ms | 1-2 ms |
| Tamanho máximo de tempDB | 192 - 1,920 GB (24 GB por vCore) | Sem restrições - limitadas pelo tamanho de armazenamento máximo da instância |
| Número máximo de sessões | 30000 | 30000 |

> [!NOTE]
> - Tamanho do ficheiro de dados e de registo no utilizador e bases de dados do sistema estão incluídas no tamanho de armazenamento de instância é comparado com o limite de tamanho de armazenamento máximo. Uso <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">master_files</a> vista de sistema para determinar o total de espaço utilizado pelas bases de dados. Registos de erros não são persistentes e não incluídos no tamanho. As cópias de segurança não estão incluídas no tamanho de armazenamento.
> - Débito e IOPS também dependem do tamanho de página que não está limitado explicitamente por instância gerida.

## <a name="supported-regions"></a>Regiões suportadas

Instâncias geridas podem ser criadas apenas no [regiões suportadas](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerida numa região que não é atualmente suportado, pode [enviar um pedido de suporte através do portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipos de subscrição de suporte

Atualmente, a instância gerida suporta implementação apenas nos seguintes tipos de subscrições:

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Fornecedor de serviços cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise programador/teste](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Subscrições com o crédito do Azure mensal para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

Tipos de subscrição de suporte podem conter um número limitado de recursos por região. A instância gerida tem dois limites padrão por região do Azure, dependendo de um tipo de tipo de subscrição:

- **Limite de sub-redes**: O número máximo de sub-redes em que as instâncias geridas são implementadas numa única região.
- **limite de vCore**: O número máximo de vCores que pode ser implantado em todas as instâncias numa única região.

> [!Note]
> Estes limites são as predefinições e limitações não técnicas. Os limites podem ser demanda aumentada através da criação de um especial [pedido de suporte no portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se precisar de mais instâncias geridas na região atual. Como alternativa, pode criar novas instâncias geridas noutra região do Azure sem enviar pedidos de suporte.

A tabela seguinte mostra os limites de regionais padrão para subscrições de suporte:

|Tipo de subscrição| Número máx. de sub-redes de instância gerida | Número máximo de unidades de vCore * |
| :---| :--- | :--- |
|"Pay-as-you-go"|3|320|
|CSP |8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Pay as you go programador/teste|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e MSDN Platform|2|32|

\* Ao planear as suas implementações, considere que um vCore comercial crítico (BC) (devido a redundância adicionado) consome 4 x de um vCore de fins gerais (GP) mais capacidade. Assim, para seus cálculos, 1 vCore GP = 1 vCore de unidade e 1 BC vCore = 4 unidades de vCore. Para simplificar a análise de consumo contra os limites predefinidos, resuma as unidades de vCore em todas as sub-redes na região em que as instâncias geridas são implementadas e comparam os resultados com os limites de unidade de instância para o seu tipo de subscrição. **Número máximo de unidades de vCore** limite aplica-se a cada subscrição numa região. Não existe nenhum limite por sub-redes individuais, exceto que a soma de todos os vCores implementado em várias sub-redes têm de ser inferior ou igual a **máximo de número de unidades de vCore**.

\*\* Maiores limites de sub-rede e vCore estão disponíveis nas seguintes regiões: Leste da Austrália, E.U.A. leste, E.U.A. Leste 2, Europa do Norte, EUA Centro-Sul, Sudeste asiático, do Reino Unido Sul, Europa Ocidental, E.U.A. oeste 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtendo uma quota maior para SQL instância gerida

Se precisar de mais instâncias geridas no seu regiões atuais, envie um pedido de suporte para aumentar a quota com o portal do Azure.
Para iniciar o processo de obtenção de uma quota maior:

1. Open **ajuda + suporte**e clique em **novo pedido de suporte**.

   ![Ajuda e suporte](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na guia Noções básicas para o novo pedido de suporte:
   - Para **tipo de problema**, selecione **limites de serviço e subscrição (cotas)** .
   - Em **Subscrição**, selecione a sua subscrição.
   - Para **tipo de Quota**, selecione **SQL Database Managed Instance**.
   - Para **plano de suporte**, selecione o seu plano de suporte.

     ![Quota de tipo de problema](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Clique em **Seguinte**.
4. Sobre o **separador problema** para o novo pedido de suporte:
   - Para **gravidade**, selecione o nível de gravidade do problema.
   - Para **detalhes**, fornecem informações adicionais sobre o problema, incluindo mensagens de erro.
   - Para **carregamento de ficheiros**, anexar um ficheiro com mais informações (até 4 MB).

     ![Detalhes do problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Uma solicitação válida deve incluir:
     > - Região na qual assinatura limite tem de ser aumentados.
     > - Aumentar o número necessário de vCores, por escalão de serviço em sub-redes existentes após a quota (se nenhuma das sub-redes existentes tem de ser expandido.
     > - Necessário número de novas sub-redes e o número total de vCores por escalão de serviço dentro as novas sub-redes (se precisar de implementar instâncias geridas nas novas sub-redes).

5. Clique em **Seguinte**.
6. No separador de informações de contacto para o novo pedido de suporte, introduza o método preferencial de contacto (e-mail ou telefone) e os detalhes de contactos.
7. Clique em **Criar**.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre a instância gerida, veja [o que é uma instância gerida?](sql-database-managed-instance.md).
- Para obter informações sobre preços, consulte [gerida de base de dados SQL a preços de instância](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar a sua primeira instância gerida, veja [guia de início rápido](sql-database-managed-instance-get-started.md).
