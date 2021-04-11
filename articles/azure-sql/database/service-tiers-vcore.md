---
title: vCore modelo de compra visão geral
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: O modelo de compra vCore permite-lhe escalar independentemente os recursos de computação e armazenamento, combinar o desempenho no local e otimizar o preço para a Base de Dados Azure SQL e Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 499e0aa1ee451969923dbdf5f84be1c844a9aab4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659346"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>vCore model overview - Azure SQL Database e Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O modelo de compra de núcleo virtual (vCore) utilizado pela Azure SQL Database e pela Azure SQL Managed Instance proporciona vários benefícios:

- Computação mais alta, memória, E.I., e limites de armazenamento.
- Controle a geração de hardware para melhor combinar os requisitos de computação e memória da carga de trabalho.
- Descontos de preços para [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md) e [Instância Reservada (RI)](reserved-capacity-overview.md).
- Maior transparência nos detalhes do hardware que alimentam o computação, o que facilita o planeamento das migrações a partir de implementações no local.

## <a name="service-tiers"></a>Escalões de serviço

As opções de nível de serviço no modelo vCore incluem Final Geral, Critical de Negócios e Hiperescala. O nível de serviço define geralmente os limites de arquitetura de armazenamento, espaço e E/S, e opções de continuidade de negócios relacionadas com disponibilidade e recuperação de desastres.

|-|**Fins Gerais**|**Crítico para a Empresa**|**Hyperscale**|
|---|---|---|---|
|Melhor para|A maioria das cargas de trabalho. Oferece opções de computação e armazenamento equilibradas, dimensionáveis e orientadas para orçamentos. |Oferece aplicações de negócio a maior resiliência às falhas usando várias réplicas isoladas, e fornece o maior desempenho de E/S por réplica de base de dados.|A maioria das cargas de trabalho de negócios com requisitos de armazenamento altamente escaláveis e escala de leitura.  Oferece maior resiliência às falhas, permitindo a configuração de mais de uma réplica isolada da base de dados. |
|Armazenamento|Usa armazenamento remoto.<br/>**Cálculo da base de dados SQL:**<br/>5 GB - 4 TB<br/>**Computação sem servidor:**<br/>5 GB - 3 TB<br/>**Sql Gestão de Instância**: 32 GB - 8 TB |Utiliza o armazenamento SSD local.<br/>**Cálculo da base de dados SQL:**<br/>5 GB - 4 TB<br/>**Sql Caso gerido**:<br/>32 GB - 4 TB |Flexível autogrow de armazenamento, se necessário. Suporta até 100 TB de armazenamento. Utiliza o armazenamento SSD local para cache local de piscina tampão e armazenamento de dados local. Utiliza o armazenamento remoto Azure como loja final de dados a longo prazo. |
|IOPS e produção (aproximada)|**Base de Dados SQL**: Consulte os limites de recursos para [bases de dados individuais](resource-limits-vcore-single-databases.md) e [piscinas elásticas.](resource-limits-vcore-elastic-pools.md)<br/>**SQL Managed Instance**: Ver [visão geral Azure SQL Managed Instance limites de recursos](../managed-instance/resource-limits.md#service-tier-characteristics).|Consulte os limites de recursos para [bases de dados individuais](resource-limits-vcore-single-databases.md) e [piscinas elásticas.](resource-limits-vcore-elastic-pools.md)|Hyperscale é uma arquitetura multi-camadas com caching em vários níveis. O IOPS eficaz e a produção dependerão da carga de trabalho.|
|Disponibilidade|1 réplica, sem réplicas em escala de leitura|3 réplicas, 1 [réplica em escala de leitura,](read-scale-out.md)<br/>zona redundante alta disponibilidade (HA)|1 réplica de leitura-escrita, mais 0-4 [réplicas em escala de leitura](read-scale-out.md)|
|Cópias de segurança|[Armazenamento geo-redundante de acesso à leitura (RA-GRS)](../../storage/common/geo-redundant-design.md), 1-35 dias (7 dias por defeito)|[RA-GRS,](../..//storage/common/geo-redundant-design.md)1-35 dias (7 dias por defeito)|Cópias de segurança baseadas em instantâneos no armazenamento remoto Azure. Os restauradores usam estas fotos para uma rápida recuperação. As cópias de segurança são instantâneas e não têm impacto no desempenho de I/O do cálculo. As restaurações são rápidas e não são uma operação de tamanho de dados (demorando minutos em vez de horas ou dias).|
|Dentro da memória|Não suportado|Suportado|Não suportado|
|||


### <a name="choosing-a-service-tier"></a>Escolher um escalão de serviço

Para obter informações sobre a seleção de um nível de serviço para a sua carga de trabalho específica, consulte os seguintes artigos:

- [Quando escolher o nível de serviço de Finalidade Geral](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando escolher o nível de serviço Business Critical](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando escolher o nível de serviço hyperscale](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Níveis de cálculo

As opções de nível de cálculo no modelo vCore incluem os níveis de computação a provisionados e sem servidor.


### <a name="provisioned-compute"></a>Cálculo provisionado

O nível de computação a provisionado fornece uma quantidade específica de recursos computacional que são continuamente a provisionados independentemente da atividade de carga de trabalho, e faturas para o montante do cálculo a provisionado a um preço fixo por hora.


### <a name="serverless-compute"></a>Computação sem servidor

O [nível de cálculo sem servidor](serverless-tier-overview.md) calcula os recursos de cálculo com base na atividade da carga de trabalho, e as contas da quantidade de cálculo utilizado por segundo.



## <a name="hardware-generations"></a>Gerações de hardware

As opções de geração de hardware no modelo vCore incluem Gen 4/5, série M, série Fsv2 e séries DC. A geração de hardware define geralmente os limites de cálculo e memória e outras características que impactam o desempenho da carga de trabalho.

### <a name="gen4gen5"></a>Gen4/Gen5

- O hardware Gen4/Gen5 fornece recursos de computação e memória equilibrados, e é adequado para a maioria das cargas de trabalho de base de dados que não têm memória mais alta, vCore mais alto, ou requisitos vCore individuais mais rápidos, conforme fornecido pela série Fsv2 ou série M.

Para as regiões onde a Gen4/Gen5 está disponível, consulte [a disponibilidade da Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-series"></a>Série Fsv2

- Fsv2-series é uma opção de hardware otimizada computacional que oferece baixa latência de CPU e alta velocidade do relógio para as cargas de trabalho mais exigentes do CPU.
- Dependendo da carga de trabalho, a série Fsv2 pode oferecer mais desempenho cpU por vCore do que a Gen5, e o tamanho de 72 vCore pode fornecer mais desempenho cpU por menos custos do que 80 vCores na Gen5. 
- O Fsv2 fornece menos memória e temperatura por vCore do que outros hardware, pelo que cargas de trabalho sensíveis a esses limites podem querer considerar a série Gen5 ou M.  

Série Fsv2 apenas suportada no nível de Finalidade Geral. Para as regiões onde a série Fsv2 está disponível, consulte [a disponibilidade da série Fsv2](#fsv2-series-1).

### <a name="m-series"></a>Série M

- A série M é uma opção de hardware otimizada pela memória para cargas de trabalho que exigem mais memória e limites de computação mais elevados do que os fornecidos pela Gen5.
- A série M fornece 29 GB por vCore e até 128 vCores, o que aumenta o limite de memória em relação à Gen5 em 8x para quase 4 TB.

A série M só é suportada no nível Business Critical e não suporta o despedimento de zona.  Para as regiões onde a série M está disponível, consulte [a disponibilidade da série M](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Tipos de oferta Azure suportados pela série M

Para aceder à série M, a subscrição deve ser um tipo de oferta paga, incluindo Pay-As-You-Go ou Enterprise Agreement (EA).  Para obter uma lista completa dos tipos de oferta Azure suportados pela série M, consulte [as ofertas atuais sem limites de gastos](https://azure.microsoft.com/support/legal/offer-details).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>Série DC

> [!NOTE]
> A série DC está atualmente em **pré-visualização pública.**

- O hardware da série DC utiliza processadores Intel com tecnologia De Extensões de Guarda de Software (Intel SGX).
- A série DC é necessária para [Sempre Encriptada com enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves), que não é suportado com outras configurações de hardware.
- A série DC é projetada para cargas de trabalho que processam dados sensíveis e exigem capacidades confidenciais de processamento de consultas, fornecidas pela Always Encrypted com enclaves seguros.
- O hardware da série DC fornece recursos de computação e memória equilibrados.

A série DC só é suportada para o cálculo Provisionado (o Serverless não é suportado) e não suporta a redundância da zona. Para as regiões onde a série DC está disponível, consulte [a disponibilidade da série DC](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>Tipos de oferta Azure suportados por séries DC

Para aceder às séries DC, a subscrição deve ser um tipo de oferta paga, incluindo Pay-As-You-Go ou Enterprise Agreement (EA).  Para obter uma lista completa de tipos de oferta Azure suportados por séries DC, consulte [as ofertas atuais sem limites de gastos](https://azure.microsoft.com/support/legal/offer-details).

### <a name="compute-and-memory-specifications"></a>Especificações de cálculo e memória


|Geração de hardware  |Computação  |Memória  |
|:---------|:---------|:---------|
|Gen4     |- Processadores Intel® E5-2673 v3 (Haswell) 2.4 GHz<br>- Provisão até 24 vCores (1 vCore = 1 núcleo físico)  |- 7 GB por vCore<br>- Provisão até 168 GB|
|Gen5     |**Cálculo provisionado**<br>- Intel® E5-2673 v4 (Broadwell) 2.3-GHz, Intel® SP-8160 (Skylake) \* e Intel® 8272CL (Lago cascata) 2,5 \* GHz processadores<br>- Provisão até 80 vCores (1 vCore = 1 hiper-thread)<br><br>**Computação sem servidor**<br>- Intel® E5-2673 v4 (Broadwell) 2.3-GHz e Intel® processadores SP-8160 (Skylake)*<br>- Escala automática até 40 vCores (1 vCore = 1 hiper-thread)|**Cálculo provisionado**<br>- 5,1 GB por vCore<br>- Provisão até 408 GB<br><br>**Computação sem servidor**<br>- Escala automática até 24 GB por vCore<br>- Escala automática até 120 GB max|
|Série Fsv2     |- Processadores Intel® 8168 (Skylake)<br>- Com uma velocidade do relógio turbo de 3,4 GHz e uma velocidade máxima máxima de 3,7 GHz.<br>- Provisão até 72 vCores (1 vCore = 1 hiper-thread)|- 1,9 GB por vCore<br>- Provisão até 136 GB|
|Série M     |- Intel® E7-8890 v3 2,5 GHz e Intel® processadores 8280M 2.7 GHz (Lago cascata)<br>- Provisão até 128 vCores (1 vCore = 1 hiper-thread)|- 29 GB por vCore<br>- Provisão até 3,7 TB|
|Série DC     | - Processadores Intel XEON E-2288G<br>- Com extensão da Intel Software Guard (Intel SGX))<br>- Provisão até 8 vCores (1 vCore = 1 núcleo físico) | 4,5 GB por vCore |

\* Na [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) visão dinâmica de gestão, a geração de hardware para bases de dados que utilizam processadores Intel® SP-8160 (Skylake) aparece como Gen6, enquanto a geração de hardware para bases de dados utilizando a Intel® 8272CL (Lago de Cascade) aparece como Gen7. Os limites de recursos para todas as bases de dados da Gen5 são os mesmos, independentemente do tipo de processador (Broadwell, Skylake ou Cascade Lake).

Para obter mais informações sobre os limites de recursos, consulte [os limites de recursos para bases de dados individuais (vCore)](resource-limits-vcore-single-databases.md)ou [limites de recursos para piscinas elásticas (vCore)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Selecionando uma geração de hardware

No portal Azure, pode selecionar a geração de hardware para uma base de dados ou piscina na Base de Dados SQL no momento da criação, ou pode alterar a geração de hardware de uma base de dados ou piscina existente.

**Para selecionar uma geração de hardware ao criar uma Base de Dados SQL ou piscina**

Para obter informações detalhadas, consulte [criar uma Base de Dados SQL.](single-database-create-quickstart.md)

No **separador Básicos,** selecione o link **de base de dados Configure** na secção **de armazenamento Compute +** e, em seguida, selecione o link de **configuração De alteração:**

  ![configurar a base de dados](./media/service-tiers-vcore/configure-sql-database.png)

Selecione a geração de hardware desejada:

  ![selecionar hardware](./media/service-tiers-vcore/select-hardware.png)


**Para alterar a geração de hardware de uma base de dados ou piscina SQL existente**

Para uma base de dados, na página 'Vista Geral', selecione o link **de nível de preços:**

  ![alterar hardware](./media/service-tiers-vcore/change-hardware.png)

Para uma piscina, na página 'Vista Geral', selecione **Configure**.

Siga os passos para alterar a configuração e selecione a geração de hardware como descrito nos passos anteriores.

**Para selecionar uma geração de hardware ao criar uma SQL Managed Instance**

Para obter informações detalhadas, consulte [Criar uma SqL Managed Instance](../managed-instance/instance-create-quickstart.md).

No **separador Básicos,** selecione o link **de base de dados Configure** na secção **de armazenamento Compute +** e, em seguida, selecione a geração de hardware desejada:

  ![configurar a sql gestudida instância](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Para alterar a geração de hardware de uma sql existente Exemplo gerido**

# <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

A partir da página SQL Managed Instance, selecione o link **de nível de preços** colocado na secção Definições

![alterar hardware de instância gerida SQL](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Na página de nível de preços, poderá alterar a geração de hardware como descrito nos passos anteriores.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize o seguinte script do PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Para mais detalhes, consulte o comando [Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance)

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Para mais detalhes, consulte o comando [de atualização az sql mi.](/cli/azure/sql/mi#az-sql-mi-update)

---

### <a name="hardware-availability"></a>Disponibilidade de hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

O hardware da Gen4 está [a ser eliminado](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) e já não está disponível para novas implementações. Todas as novas bases de dados devem ser implantadas no hardware da Gen5.

A Gen5 está disponível em todas as regiões públicas do mundo.

#### <a name="fsv2-series"></a>Série Fsv2

A série Fsv2 está disponível nas seguintes regiões: Austrália Central, Austrália Central 2, Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Leste Asiático, Leste dos Eua, França Central, Índia Central, Coreia Do Sul, Coreia do Sul, Europa do Norte, África do Sul Norte, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Eua Ocidental 2.


#### <a name="m-series"></a>Série M

A série M está disponível nas seguintes regiões: Leste dos EUA, Norte da Europa, Europa Ocidental, EUA Ocidental 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>Série DC

> [!NOTE]
> A série DC está atualmente em **pré-visualização pública.**

A série DC está disponível nas seguintes regiões: Canadá Central, Canadá Leste, Leste dos EUA, Norte da Europa, Reino Unido Sul, Europa Ocidental, Eua Ocidentais.

Se necessitar de séries DC numa região atualmente não apoiada, [envie um bilhete de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) seguindo as instruções de Pedido de aumento de quota para [Azure SQL Database e SQL Managed Instance](quota-increase-request.md).

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte: 
- [Criação de uma Base de Dados SQL utilizando o portal Azure](single-database-create-quickstart.md)
- [Criação de uma SQL Managed Instance usando o portal Azure](../managed-instance/instance-create-quickstart.md)

Para obter detalhes sobre os preços, consulte a [página de preços da Base de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/).

Para obter mais informações sobre os tamanhos específicos de computação e armazenamento disponíveis nos níveis gerais de serviço e de serviço crítico de negócios, consulte:

- [vCore limites de recursos para Azure SQL Database](resource-limits-vcore-single-databases.md).
- [vCore-based limites de recursos para base de dados Azure SQL em conjunto](resource-limits-vcore-elastic-pools.md).
- [vCore-based limites de recursos para Azure SQL Managed Instance](../managed-instance/resource-limits.md).
