---
title: Configure desempenho para o tempo de execução da integração Azure-SSIS
description: Saiba como configurar as propriedades do Tempo de Execução de Integração Azure-SSIS para um alto desempenho
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: f0fcd61230d68d7b26017237e2b7e0465fcb1f07
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635325"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configure o tempo de execução da integração Azure-SSIS para um alto desempenho

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Este artigo descreve como configurar um Tempo de Execução de Integração Azure-SSIS (IR) para um desempenho elevado. O Azure-SSIS IR permite-lhe implementar e executar pacotes SQL Server Integration Services (SSIS) em Azure. Para obter mais informações sobre o Azure-SSIS IR, consulte o artigo [de execução da Integração.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Para obter informações sobre a implementação e execução de pacotes SSIS no Azure, consulte [as cargas de trabalho dos Serviços de Integração de Servidores SQL para a nuvem](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Este artigo contém resultados de desempenho e observações de testes internos realizados por membros da equipa de desenvolvimento do SSIS. Os seus resultados podem variar. Faça o seu próprio teste antes de finalizar as definições de configuração, que afetam tanto o custo como o desempenho.

## <a name="properties-to-configure"></a>Propriedades para configurar

A parte seguinte de um script de configuração mostra as propriedades que pode configurar quando cria um Tempo de Execução de Integração Azure-SSIS. Para obter o roteiro e descrição completos do PowerShell, consulte [os pacotes de Serviços de Integração de ServidorES SQL para Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzuressisLocation
**A azuresssisLocation** é o local para o nó do trabalhador em execução de integração. O nó do trabalhador mantém uma ligação constante à base de dados do Catálogo SSIS (SSISDB) na Base de Dados Azure SQL. Desloja o **AzureSSSISLocation** para o mesmo local que o [servidor lógico SQL](../azure-sql/database/logical-servers.md) que acolhe o SSISDB, o que permite que o tempo de integração funcione da forma mais eficiente possível.

## <a name="azuressisnodesize"></a>AzuressisNodesize
A Data Factory, incluindo o Azure-SSIS IR, suporta as seguintes opções:
-   Norma \_ A4 \_ v2
-   Standard \_ A8 \_ v2
-   D1 \_ v2 padrão \_
-   \_D2 \_ padrão v2
-   \_D3 \_ padrão v2
-   D4 \_ v2 padrão \_
-   \_D2 \_ padrão v3
-   Padrão \_ D4 \_ v3
-   D8 \_ v3 padrão \_
-   Padrão \_ D16 \_ v3
-   \_D32 \_ v3 padrão
-   \_D64 \_ v3 padrão
-   Standard \_ E2 \_ v3
-   Standard \_ E4 \_ v3
-   Standard \_ E8 \_ v3
-   Standard \_ E16 \_ v3
-   Standard \_ E32 \_ v3
-   Standard \_ E64 \_ v3

Nos testes internos não oficiais da equipa de engenharia SSIS, a série D parece ser mais adequada para a execução do pacote SSIS do que a série A.

-   A relação desempenho/preço da série D é superior à série A e a relação desempenho/preço da série V3 é superior à série V2.
-   A produção para a série D é superior à série A ao mesmo preço e a produção para a série V3 é superior à série V2 ao mesmo preço.
-   Os nós da série V2 do Azure-SSIS IR não são adequados para configuração personalizada, por isso, utilize os nós da série V3. Se já utilizar os nós da série V2, troque para utilizar os nós da série V3 o mais rapidamente possível.
-   A série E é tamanhos VM otimizados de memória que proporcionam uma relação memória-CPU mais alta do que outras máquinas. Se o seu pacote necessitar de muita memória, pode considerar escolher a série E VM.

### <a name="configure-for-execution-speed"></a>Configuração para a velocidade de execução
Se não tiver muitos pacotes para executar e quiser que os pacotes corram rapidamente, use as informações no gráfico seguinte para escolher um tipo de máquina virtual adequado para o seu cenário.

Estes dados representam uma única execução de pacote num único nó de trabalhador. O pacote carrega 3 milhões de registos com colunas de nome e apelido do Azure Blob Storage, gera uma coluna de nome completo e escreve os registos que têm o nome completo mais de 20 caracteres para Azure Blob Storage.

O eixo y é o número de pacotes que completaram a execução em uma hora. Por favor, note que este é apenas um resultado de teste de um pacote que consome memória. Se quiser saber a produção do seu pacote, recomenda-se que realize o teste sozinho.

![Velocidade de execução do pacote de runtime de integração SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Configuração para a produção global

Se tiver muitos pacotes para executar e se importar mais com o rendimento geral, use as informações no gráfico seguinte para escolher um tipo de máquina virtual adequado para o seu cenário.

O eixo y é o número de pacotes que completaram a execução em uma hora. Por favor, note que este é apenas um resultado de teste de um pacote que consome memória. Se quiser saber a produção do seu pacote, recomenda-se que realize o teste sozinho.

![Funcionação de integração SSIS produção máxima global](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzuressISNodeNumber

**O AzureSSISNodeNumber** ajusta a escalabilidade do tempo de integração. O funcionamento da integração é proporcional ao **AzuresSISNodeNumber** . Desafie o **AzureSSSISNodeNumber** a um pequeno valor no início, monitorize a produção do tempo de execução da integração e, em seguida, ajuste o valor para o seu cenário. Para reconfigurar a contagem de nóiros, consulte [Gerir um tempo de integração Azure-SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Quando já está a usar um poderoso nó de trabalhador para executar pacotes, o aumento **do AzureSSISMaxParallelExecutionsPerNode** pode aumentar o rendimento geral do tempo de execução da integração. Se pretender aumentar o valor máximo, precisa de utilizar o Azure PowerShell para atualizar **o AzureSSISMaxParallelExecutionsPerNode** . Pode estimar o valor adequado com base no custo do seu pacote e nas seguintes configurações para os nós do trabalhador. Para obter mais informações, consulte [os tamanhos da máquina virtual para fins gerais.](../virtual-machines/sizes-general.md)

| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| D1 \_ v2 padrão \_ | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| \_D2 \_ padrão v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| \_D3 \_ padrão v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| D4 \_ v2 padrão \_ | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Norma \_ A4 \_ v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard \_ A8 \_ v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| \_D2 \_ padrão v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Padrão \_ D4 \_ v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| D8 \_ v3 padrão \_ | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Padrão \_ D16 \_ v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| \_D32 \_ v3 padrão| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| \_D64 \_ v3 padrão| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Standard \_ E2 \_ v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standard \_ E4 \_ v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard \_ E8 \_ v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard \_ E16 \_ v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standard \_ E32 \_ v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standard \_ E64 \_ v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Aqui estão as diretrizes para a definição do valor certo para a propriedade **AzureSSISMaxParallelExecutionsPerNode:** 

1. Deu-lhe um pequeno valor no início.
2. Aumente-o em um pequeno montante para verificar se a produção global é melhorada.
3. Pare de aumentar o valor quando a produção global atingir o valor máximo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** é o nível de preços da base de dados do Catálogo SSIS (SSISDB) na Base de Dados Azure SQL. Esta definição afeta o número máximo de trabalhadores na instância de INFRA, a velocidade para fazer fila de uma execução do pacote e a velocidade para carregar o diário de execução.

-   Se não se importar com a velocidade para a execução do pacote de fila e para carregar o registo de execução, pode escolher o nível de preços mais baixo da base de dados. A Azure SQL Database com preços básicos suporta 8 trabalhadores em um caso de integração.

-   Escolha uma base de dados mais poderosa do que a Basic se a contagem de trabalhadores for superior a 8, ou se a contagem de núcleos for superior a 50. Caso contrário, a base de dados torna-se o estrangulamento do caso de execução da integração e o desempenho global é impactado negativamente.

-   Escolha uma base de dados mais poderosa, como o S3, se o nível de registo estiver definido para verbose. De acordo com os nossos testes não oficiais internos, o nível de preços s3 pode suportar a execução do pacote SSIS com 2 nós, 128 contagens paralelas e nível de registo verboso.

Também pode ajustar o nível de preços da base de dados com base na informação de utilização da [unidade de transação de bases de dados](../azure-sql/database/service-tiers-dtu.md) (DTU) disponível no portal Azure.

## <a name="design-for-high-performance"></a>Conceber o elevado desempenho
Conceber um pacote SSIS para funcionar em Azure é diferente de desenhar um pacote para execução no local. Em vez de combinar várias tarefas independentes no mesmo pacote, separe-as em vários pacotes para uma execução mais eficiente no Azure-SSIS IR. Crie uma execução de pacote para cada pacote, para que não tenham que esperar um pelo outro para terminar. Esta abordagem beneficia da escalabilidade do tempo de integração Azure-SSIS e melhora a produção global.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o tempo de execução da integração Azure-SSIS. Ver [tempo de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).