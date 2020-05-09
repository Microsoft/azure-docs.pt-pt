---
title: Descrição geral do modelo vCore
description: O modelo de compra vCore permite-lhe dimensionar de forma independente os recursos de computação e armazenamento, corresponder ao desempenho no local e otimizar o preço.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 78e01c854201e3c5253cd86aebcd85b62bf5568d
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629305"
---
# <a name="vcore-model-overview"></a>Descrição geral do modelo vCore

O modelo de núcleo virtual (vCore) proporciona vários benefícios:

- Computação superior, memória, IO e limites de armazenamento.
- Controle a geração de hardware para combinar melhor os requisitos de computação e memória da carga de trabalho.
- Descontos de preços para [O Benefício Híbrido Azure (AHB)](sql-database-azure-hybrid-benefit.md) e [Instância Reservada (RI)](sql-database-reserved-capacity.md).
- Maior transparência nos detalhes de hardware que alimentam o cálculo; facilita o planeamento das migrações a partir de destacamentos no local.

## <a name="service-tiers"></a>Escalões de serviço

As opções de nível de serviço no modelo vCore incluem Propósito Geral, Business Critical e Hyperscale. O nível de serviço define geralmente os limites de arquitetura de armazenamento, espaço e IO, e opções de continuidade do negócio relacionadas com disponibilidade e recuperação de desastres.

||**Fins gerais**|**Crítico de negócios**|**Hyperscale**|
|---|---|---|---|
|Melhor para|A maioria das cargas de trabalho de negócios. Oferece opções de cálculo e armazenamento orientados para o orçamento, equilibrados e escaláveis. |Oferece às aplicações empresariais a maior resiliência a falhas utilizando várias réplicas isoladas, e fornece o desempenho de I/S mais elevado por réplica de base de dados.|A maioria das cargas de trabalho empresariais com armazenamento altamente escalável e requisitos à escala de leitura.  Oferece maior resiliência às falhas, permitindo a configuração de mais de uma réplica de base de dados isolada. |
|Armazenamento|Usa armazenamento remoto.<br/>**Bases de dados únicas e piscinas elásticas aprovisionadas computação:**<br/>5 GB - 4 TB<br/>**Computação sem servidor:**<br/>5 GB - 3 TB<br/>**Instância Gerida**: 32 GB - 8 TB |Usa armazenamento local de SSD.<br/>**Bases de dados únicas e piscinas elásticas aprovisionadas computação:**<br/>5 GB - 4 TB<br/>**Instância gerida:**<br/>32 GB - 4 TB |Auto-cultivo flexível de armazenamento, se necessário. Suporta até 100 TB de armazenamento. Utiliza o armazenamento local de SSD para cache de piscina tampão local e armazenamento de dados locais. Utiliza o armazenamento remoto Azure como loja de dados final de longo prazo. |
|IOPS e entrada (aproximada)|**Bases de dados únicas e piscinas elásticas**: Consulte os limites dos recursos para [bases de dados únicas](../sql-database/sql-database-vcore-resource-limits-single-databases.md) e [piscinas elásticas](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Instância gerida**: Ver [visão geral Azure SQL Base](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)de dados gerido limites de recursos de instância .|Consulte os limites de recursos para [bases de dados individuais](../sql-database/sql-database-vcore-resource-limits-single-databases.md) e [piscinas elásticas](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Hyperscale é uma arquitetura multi-nível com cache a vários níveis. IOPS eficaz estórias e a sua entrada dependerá da carga de trabalho.|
|Disponibilidade|1 réplica, sem réplicas à escala de leitura|3 réplicas, 1 [réplica em escala de leitura,](sql-database-read-scale-out.md)<br/>alta disponibilidade redundante (HA)|1 réplica de leitura- escrita, mais [0-4 réplicas em escala de leitura](sql-database-read-scale-out.md)|
|Cópias de segurança|[Armazenamento geo-redundante de acesso de leitura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|[RA-GRS,](../storage/common/storage-designing-ha-apps-with-ragrs.md)7-35 dias (7 dias por defeito)|Backups baseados em instantâneos no armazenamento remoto azure. Os restauros usam estes instantâneos para uma rápida recuperação. As cópias de segurança são instantâneas e não impactam o desempenho do Computação Em/O. As restaurações são rápidas e não são uma operação de tamanho de dados (demorando minutos em vez de horas ou dias).|
|Dentro da memória|Não suportado|Suportado|Não suportado|
|||


### <a name="choosing-a-service-tier"></a>Escolher um escalão de serviço

Para obter informações sobre a seleção de um nível de serviço para a sua carga de trabalho específica, consulte os seguintes artigos:

- [Quando escolher o nível de serviço de propósito geral](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando escolher o nível de serviço Business Critical](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando escolher o nível de serviço de hiperescala](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Níveis de computação

As opções de nível computacional no modelo vCore incluem os níveis de computação provisionados e sem servidor.


### <a name="provisioned-compute"></a>Cálculo provisionado

O nível de cálculo provisionado fornece uma quantidade específica de recursos computacionais que são continuamente provisionados independentemente da atividade de carga de trabalho, e contas para o montante do cálculo provisionado a um preço fixo por hora.


### <a name="serverless-compute"></a>Computação sem servidor

O [nível de computação de nível de computação de nível de](sql-database-serverless.md) servidor escamas computarecursos baseados na atividade da carga de trabalho, e contas para a quantidade de computação usada por segundo.



## <a name="hardware-generations"></a>Gerações de hardware

As opções de geração de hardware no modelo vCore incluem Gen 4/5, série M (pré-visualização) e série Fsv2 (pré-visualização). A geração de hardware geralmente define os limites de computação e memória e outras características que impactam o desempenho da carga de trabalho.

### <a name="gen4gen5"></a>Gen4/Gen5

- O hardware Gen4/Gen5 fornece recursos de computação e memória equilibrados, e é adequado para a maioria das cargas de trabalho de base de dados que não têm memória mais alta, vCore mais alto ou requisitos vCore únicos mais rápidos, conforme fornecido pela série Fsv2 ou série M.

Para as regiões onde a Gen4/Gen5 está disponível, consulte [a disponibilidade da Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Série Fsv2 (pré-visualização)

- A série Fsv2 é uma opção de hardware otimizada para computação, proporcionando baixa latência cpu e alta velocidade do relógio para as cargas de trabalho mais exigentes da CPU.
- Dependendo da carga de trabalho, a série Fsv2 pode fornecer mais desempenho cpu por vCore do que gen5, e o tamanho de 72 vCore pode fornecer mais desempenho cpu por menos custo do que 80 vCores em Gen5. 
- O Fsv2 fornece menos memória e tempdb por vCore do que outro hardware, pelo que cargas de trabalho sensíveis a esses limites podem querer considerar a Gen5 ou a série M.  

Série Fsv2 apenas suportada no nível de Propósito Geral.  Para as regiões onde a série Fsv2 está disponível, consulte a [disponibilidade da série Fsv2](#fsv2-series).


### <a name="m-seriespreview"></a>Série M (pré-visualização)

- A série M é uma opção de hardware otimizada pela memória para cargas de trabalho que exigem mais memória e limites de computação mais elevados do que o fornecido pela Gen5.
- A série M fornece 29 GB por vCore e 128 vCores, o que aumenta o limite de memória relativo à Gen5 em 8x para quase 4 TB.

A série M só é suportada no nível Business Critical e não suporta a redundância da zona.

Para permitir o hardware da série M para uma subscrição e região, deve ser aberto um pedido de apoio. A subscrição deve ser um tipo de oferta paga, incluindo Pay-As-You-Go ou Enterprise Agreement (EA).  Se o pedido de suporte for aprovado, então a experiência de seleção e provisionamento da série M segue o mesmo padrão que para outras gerações de hardware. Para as regiões onde a série M está disponível, consulte [a disponibilidade da série M](#m-series).


### <a name="compute-and-memory-specifications"></a>Especificações computadas e de memória


|Geração de hardware  |Computação  |Memória  |
|:---------|:---------|:---------|
|Gen4     |- Processadores Intel E5-2673 v3 (Haswell) 2.4 GHz<br>- Provisão até 24 vCores (1 vCore = 1 núcleo físico)  |- 7 GB por vCore<br>- Provisão até 168 GB|
|Gen5     |**Cálculo provisionado**<br>- Processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake)*<br>- Provisão até 80 vCores (1 vCore = 1 hiper-fio)<br><br>**Computação sem servidor**<br>- Processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake)*<br>- Auto-escala até 16 vCores (1 vCore = 1 hiper-fio)|**Cálculo provisionado**<br>- 5,1 GB por vCore<br>- Provisão até 408 GB<br><br>**Computação sem servidor**<br>- Auto-escala até 24 GB por vCore<br>- Auto-escala até 48 GB max|
|Série Fsv2     |- Processadores Intel Xeon Platinum 8168 (SkyLake)<br>- Com uma velocidade de relógio turbo de 3,4 GHz e uma velocidade máxima de 3,7 GHz no relógio turbo do núcleo único.<br>- Provisão 72 vCores (1 vCore = 1 hiper-fio)|- 1,9 GB por vCore<br>- Provisão 136 GB|
|Série M     |- Processadores Intel Xeon E7-8890 v3 2.5 GHz e Intel Xeon Platinum 8280M 2,7 GHz (Lago cascade)<br>- Provisão 128 vCores (1 vCore = 1 hiper-fio)|- 29 GB por vCore<br>- Provisão 3.7 TB|

\*Na visão dinâmica de gestão [sys.dm_user_db_resource_governance,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) a geração de hardware para bases de dados gen5 usando processadores Intel SP-8160 (Skylake) aparece como Gen6. Os limites de recursos para todas as bases de dados gen5 são os mesmos, independentemente do tipo de processador (Broadwell ou Skylake).

Para obter mais informações sobre os limites dos recursos, consulte [os limites de recursos para bases de dados únicas (vCore)](sql-database-vcore-resource-limits-single-databases.md)ou limites de [recursos para piscinas elásticas (vCore)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Selecionando uma geração de hardware

No portal Azure, pode selecionar a geração de hardware para uma base de dados SQL ou piscina no momento da criação, ou pode alterar a geração de hardware de uma base de dados ou piscina SQL existente.

**Para selecionar uma geração de hardware ao criar uma base de dados SQL ou piscina**

Para obter informações detalhadas, consulte [Criar uma base de dados SQL](sql-database-single-database-get-started.md).

No separador **Basics,** selecione a ligação de base de **dados Configure** na secção **Compute + armazenamento** e, em seguida, selecione o link de **configuração Alterar:**

  ![configurar a base de dados](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Selecione a geração de hardware desejada:

  ![selecionar hardware](media/sql-database-service-tiers-vcore/select-hardware.png)


**Para alterar a geração de hardware de uma base de dados ou piscina SQL existente**

Para uma base de dados, na página 'Overview', selecione o link de nível de **preços:**

  ![alterar hardware](media/sql-database-service-tiers-vcore/change-hardware.png)

Para uma piscina, na página 'Overview', **selecione Configurar**.

Siga os passos para alterar a configuração e selecione a geração de hardware conforme descrito nos passos anteriores.

**Para selecionar uma geração de hardware ao criar uma instância gerida**

Para obter informações detalhadas, consulte [Criar uma instância gerida](sql-database-managed-instance-get-started.md).

No separador **Basics,** selecione a ligação de base de **dados Configure** na secção **Compute + armazenamento** e, em seguida, selecione a geração de hardware desejada:

  ![configurar instância gerida](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Para mudar a geração de hardware de um caso gerido existente**

# <a name="portal"></a>[Portal](#tab/azure-portal)

A partir da página de instância gerida, selecione o link **de nível de preços** colocado na secção Definições

![alterar hardware de instância gerido](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Na página do **nível** de preços poderá alterar a geração de hardware conforme descrito nos passos anteriores.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize o seguinte script do PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Para mais detalhes, consulte o comando [Set-AzSqlInstance.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Para mais detalhes, consulte o comando de [atualização az sql mi.](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

---

### <a name="hardware-availability"></a>Disponibilidade de hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

O hardware gen4 está [a ser eliminado gradualmente](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) e já não está disponível para as novas implementações. Todas as novas bases de dados devem ser implantadas no hardware gen5.

A Gen5 está disponível na maioria das regiões do mundo.

#### <a name="fsv2-series"></a>Série Fsv2

As séries FSV2 estão disponíveis nas seguintes regiões: Austrália Central, Austrália Central 2, Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Ásia Oriental, Leste dos Estados-Americanos, França Central, Índia Central, Índia Ocidental, Coreia Central, Coreia do Sul, Norte da Europa, África do Sul Norte, Sudeste Asiático, Reino Unido Sul, Reino Unido Ocidental, Europa Ocidental, Oeste dos Eua 2.


#### <a name="m-series"></a>Série M

A série M está disponível nas seguintes regiões: Leste dos EUA, Norte da Europa, Europa Ocidental, US Ocidental 2.
As séries M também podem ter uma disponibilidade limitada em regiões adicionais. Pode solicitar uma região diferente da lista da lista, mas o cumprimento numa região diferente pode não ser possível.

Para permitir a disponibilidade da série M numa subscrição, o acesso deve ser solicitado através do apresentação de [um novo pedido](#create-a-support-request-to-enable-m-series)de apoio .


##### <a name="create-a-support-request-to-enable-m-series"></a>Crie um pedido de suporte para ativar a série M: 

1. Selecione **Ajuda + suporte** no portal.
2. Selecione **Novo pedido de suporte**.

Na página **Basics,** forneça o seguinte:

1. Para **o tipo de emissão,** selecione limites de serviço e **subscrição (quotas)**.
2. Para **subscrição** = selecione a subscrição para ativar a série M.
3. Para **o tipo quota,** selecione base de **dados SQL**.
4. Selecione **Next** para ir à página **Detalhes.**

Na página **Detalhes,** forneça o seguinte:

1. Na secção **DETALHES DO PROBLEMA,** selecione o link **Fornecer detalhes.** 
2. Para o tipo de quota de base de **dados SQL,** selecione **série M**.
3. Para **a Região,** selecione a região para ativar a série M.
    Para as regiões onde a série M está disponível, consulte [a disponibilidade da série M](#m-series).

Os pedidos de apoio aprovados são normalmente preenchidos no prazo de 5 dias úteis.


## <a name="next-steps"></a>Passos seguintes

- Para criar uma base de dados SQL, consulte [a Criação de uma base de dados SQL utilizando o portal Azure](sql-database-single-database-get-started.md).
- Para obter os tamanhos específicos da computação e as escolhas de tamanho de armazenamento disponíveis para bases de dados únicas, consulte [os limites de recursos baseados na Base de Dados SQL vCore para bases de dados únicas](sql-database-vcore-resource-limits-single-databases.md).
- Para os tamanhos específicos da computação e escolhas de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados em Bases de Dados SQL vCore para piscinas elásticas](sql-database-vcore-resource-limits-elastic-pools.md).
- Para obter detalhes sobre preços, consulte a página de preços da Base de [Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/single/).
