---
title: Limites de recursos do banco de dados SQL do Azure – instância gerenciada | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do banco de dados SQL do Azure para instâncias gerenciadas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 09/16/2019
ms.openlocfilehash: 7f7faf11ed18fa2a85587c193376a3e4ce905fd2
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010195"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Visão geral dos limites de recursos de instância gerenciada do banco de dados SQL

Este artigo fornece uma visão geral das características técnicas e dos limites de recursos para a instância gerenciada do banco de dados SQL do Azure e fornece informações sobre como solicitar um aumento desses limites.

> [!NOTE]
> Para obter diferenças em recursos com suporte e instruções T-SQL, consulte [diferenças de recursos](sql-database-features.md) e [suporte a instruções t-SQL](sql-database-managed-instance-transact-sql-information.md). Para obter a diferença geral entre as camadas de serviço em um único banco de dados e instância gerenciada, consulte [comparação da camada de serviço](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="instance-level-resource-limits"></a>Limites de recursos em nível de instância

A instância gerenciada tem características e limites de recursos que dependem da infraestrutura e da arquitetura subjacentes. Os limites dependem da geração de hardware e da camada de serviço.

### <a name="hardware-generation-characteristics"></a>Características de geração de hardware

Instância gerenciada do banco de dados SQL do Azure pode ser implantada em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, conforme descrito na tabela a seguir:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Processadores Intel E5-2673 v3 (Haswell) 2,4-GHz, vCore de SSD anexado = 1 PP (núcleo físico) | Processadores Intel E5-2673 V4 (Broadwell) 2,3-GHz, SSD Fast NVMe, vCore = 1 LP (Hyper-thread) |
| Número de vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória máxima (taxa de memória/núcleo) | 7 GB por vCore<br/>Adicione mais vCores para obter mais memória. | 5,1 GB por vCore<br/>Adicione mais vCores para obter mais memória. |
| Memória máxima OLTP na memória | Limite de instância: 3 GB por vCore<br/>Limites de banco de dados:<br/> -8-núcleo: 8 GB por banco de dados<br/> -16-núcleo: 20 GB por banco de dados<br/> -24-núcleo: 36 GB por banco de dados | Limite de instância: 2,5 GB por vCore<br/>Limites de banco de dados:<br/> -8-núcleo: 13 GB por banco de dados<br/> -16-núcleo: 32 GB por banco de dados |
| Armazenamento reservado de instância máx. |  Uso Geral: 8 TB<br/>Comercialmente Crítico: 1TB | Uso Geral: 8 TB<br/> Comercialmente Crítico 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

> [!IMPORTANT]
> - O hardware Gen4 está sendo desativado. É recomendável implantar novas instâncias gerenciadas em hardware Gen5.
> - O hardware do Gen4 no momento ainda está disponível apenas nas seguintes regiões: Europa Setentrional, Europa Ocidental, leste dos EUA, Sul EUA Central, norte EUA Central, oeste dos EUA 2, EUA Central, Canadá central, sul da Índia, Sudeste Asiático e Coreia central.

### <a name="service-tier-characteristics"></a>Características da camada de serviço

A instância gerenciada tem duas camadas de serviço: [Uso geral](sql-database-service-tier-general-purpose.md) e [comercialmente crítico](sql-database-service-tier-business-critical.md). Essas camadas fornecem [recursos diferentes](sql-database-service-tiers-general-purpose-business-critical.md), conforme descrito na tabela a seguir:

| **Funcionalidade** | **Uso Geral** | **Comercialmente Crítico** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5 4, 8, 16, 24, 32, 40, 64, 80 |
| Memória máxima | Gen4: 56 GB-168 GB (7 GB/vCore)<br/>Gen5 40,8 GB-408 GB (5.1 GB/vCore)<br/>Adicione mais vCores para obter mais memória. | Gen4: 56 GB-168 GB (7 GB/vCore)<br/>Gen5 40,8 GB-408 GB (5.1 GB/vCore)<br/>Adicione mais vCores para obter mais memória. |
| Tamanho máximo de armazenamento reservado de instância | -2 TB para 4 vCores (somente Gen5)<br/>-8 TB para outros tamanhos | Gen4: 1 TB <br/> Gen5 <br/>-1 TB para 4, 8, 16 vCores<br/>-2 TB para 24 vCores<br/>-4 TB para 32, 40, 64, 80 vCores |
| Tamanho máx. da base de dados | 8 TB | 4 TB |
| Número máximo de bancos de dados por instância | 100 | 100 |
| Número máximo de arquivos de banco de dados por instância | Até 280 | 32.767 arquivos por banco de dados |
| Tamanho máximo do arquivo | 8 TB | 4 TB |
| Tamanho máximo do arquivo de log | 2 TB | 2 TB |
| IOPS de dados/log (aproximado) | 500-7.500 por arquivo<br/>\*[Aumentar o tamanho do arquivo para obter mais IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5,5 k-110 K (1375/vCore)<br/>Adicione mais vCores para obter melhor desempenho de e/s. |
| Limite de produtividade de gravação de log | 3 MB/s por vCore<br/>Máximo de 22 MB/s por instância | 4 MB/s por vCore<br/>Máximo de 48 MB/s por instância|
| Taxa de transferência de dados (aproximada) | 100-250 MB/s por arquivo<br/>\*[Aumente o tamanho do arquivo para obter melhor desempenho de e/s](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/A |
| Latência de e/s de armazenamento (aproximada) | 5-10 ms | 1-2 MS |
| Tamanho máximo de tempDB | 192-1.920 GB (24 GB por vCore)<br/>Adicione mais vCores para obter mais espaço de TempDB. | Limitado pelo tamanho máximo de armazenamento da instância. O tamanho do arquivo de log de TempDB está limitado atualmente a 24 GB/vCore. |
| OLTP dentro da memória | Não suportado | Disponível |
| Máx. de sessões | 30000 | 30000 |
| [Réplicas somente leitura](sql-database-read-scale-out.md) | 0 | 1 (incluído no preço) |

> [!NOTE]
> - Os dados e o tamanho do arquivo de log nos bancos de dado do usuário e do sistema são incluídos no tamanho do armazenamento da instância que é comparado com o limite de tamanho máximo de armazenamento. Use a exibição do sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">Sys. master_files</a> para determinar o total de espaço usado por bancos de dados. Os logs de erros não são persistidos e não são incluídos no tamanho. Os backups não são incluídos no tamanho do armazenamento.
> - A taxa de transferência e o IOPS também dependem do tamanho da página que não é explicitamente limitado pela instância gerenciada.
> Você pode criar outra réplica legível em uma região do Azure diferente usando grupos de failover automático.

> [!NOTE]
> Encontre mais informações sobre os [limites de recursos em pools de instâncias gerenciadas neste artigo](sql-database-instance-pools.md#instance-pools-resource-limitations).

## <a name="supported-regions"></a>Regiões suportadas

As instâncias gerenciadas podem ser criadas somente em [regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerenciada em uma região que não tem suporte no momento, você pode [Enviar uma solicitação de suporte por meio do portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

Atualmente, a instância gerenciada dá suporte à implantação somente nos seguintes tipos de assinaturas:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provedor de serviços de nuvem (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Assinaturas com crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

Os tipos de assinatura com suporte podem conter um número limitado de recursos por região. A instância gerenciada tem dois limites padrão por região do Azure, dependendo de um tipo de assinatura:

- **Limite de sub-rede**: O número máximo de sub-redes em que as instâncias gerenciadas são implantadas em uma única região.
- **limite de vCore**: O número máximo de vCores que podem ser implantadas em todas as instâncias em uma única região. O número total de instâncias não é limitado, desde que esteja dentro do limite vCore.

> [!Note]
> Esses limites são configurações padrão e não limitações técnicas. Os limites podem ser aumentados sob demanda criando uma solicitação especial [de suporte no portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se você precisar de mais instâncias gerenciadas na região atual. Como alternativa, você pode criar novas instâncias gerenciadas em outra região do Azure sem enviar solicitações de suporte.

A tabela a seguir mostra os **limites regionais padrão** para os tipos de assinatura com suporte (os limites padrão podem ser estendidos usando a solicitação de suporte descrita abaixo):

|Tipo de subscrição| Número máximo de sub-redes de instância gerenciada | Número máximo de unidades vCore * |
| :---| :--- | :--- |
|"Pay-as-you-go"|3|320|
|CSP |8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Desenvolvimento/teste pago conforme o uso|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e Plataformas MSDN|2|32|

\*No planejamento de implantações, leve em consideração que a camada de serviço de Comercialmente Crítico (BC) requer quatro (4) vezes mais capacidade de vCore do que a camada de serviço de Uso Geral (GP). Por exemplo: 1 GP vCore = 1 unidade vCore e 1 BC vCore = 4 unidades vCore. Para simplificar a análise de consumo em relação aos limites padrão, resuma as unidades vCore em todas as sub-redes na região em que as instâncias gerenciadas são implantadas e compare os resultados com os limites de unidade de instância para o tipo de assinatura. O limite **máximo de unidades vCore** aplica-se a cada assinatura em uma região. Não há limite por sub-redes individuais, exceto que a soma de todos os vCores implantados em várias sub-redes deve ser menor ou igual ao **número máximo de unidades vCore**.

\*\*Os limites de maior sub-rede e vCore estão disponíveis nas seguintes regiões: Leste da Austrália, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental, oeste dos EUA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtendo uma cota maior para a instância gerenciada do SQL

Se você precisar de mais instâncias gerenciadas em suas regiões atuais, envie uma solicitação de suporte para estender a cota usando o portal do Azure.
Para iniciar o processo de obtenção de uma cota maior:

1. Abra **ajuda + suporte**e clique em **nova solicitação de suporte**.

   ![Ajuda e Suporte](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na guia noções básicas da nova solicitação de suporte:
   - Para **tipo de problema**, selecione **limites de serviço e de assinatura (cotas)** .
   - Em **Subscrição**, selecione a sua subscrição.
   - Para **tipo de cota**, selecione **instância gerenciada do banco de dados SQL**.
   - Para o **plano de suporte**, selecione seu plano de suporte.

     ![Cota do tipo de problema](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Clique em **Seguinte**.
4. Na **guia problema** da nova solicitação de suporte:
   - Para **severidade**, selecione o nível de severidade do problema.
   - Para obter **detalhes**, forneça informações adicionais sobre o problema, incluindo mensagens de erro.
   - Para **upload de arquivo**, anexe um arquivo com mais informações (até 4 MB).

     ![Detalhes do problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Uma solicitação válida deve incluir:
     > - Região na qual o limite de assinatura precisa ser aumentado.
     > - Número necessário de vCores, por camada de serviço em sub-redes existentes após o aumento da cota (se qualquer uma das sub-redes existentes precisar ser expandida.
     > - Número necessário de novas sub-redes e número total de vCores por camada de serviço nas novas sub-redes (se você precisar implantar instâncias gerenciadas em novas sub-redes).

5. Clique em **Seguinte**.
6. Na guia informações de contato da nova solicitação de suporte, insira o método de contato preferencial (email ou telefone) e os detalhes de contato.
7. Clique em **Criar**.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a instância gerenciada, consulte [o que é uma instância gerenciada?](sql-database-managed-instance.md).
- Para obter informações sobre preços, consulte [preços da instância gerenciada do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar sua primeira instância gerenciada, consulte [o guia de início rápido](sql-database-managed-instance-get-started.md).
