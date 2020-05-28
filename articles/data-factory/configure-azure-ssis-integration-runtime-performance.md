---
title: Configure desempenho para o Tempo de Integração Azure-SSIS
description: Saiba como configurar as propriedades do Tempo de Integração Azure-SSIS para alto desempenho
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: 86fa7da695b185f41d134f67eb7d8155a30e1376
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118934"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configure o tempo de execução de integração Azure-SSIS para um alto desempenho

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Este artigo descreve como configurar um Runtime de Integração Azure-SSIS (IR) para alto desempenho. O Ir Azure-SSIS permite-lhe implementar e executar pacotes sQL Server Integration Services (SSIS) em Azure. Para mais informações sobre o Ir Azure-SSIS, consulte o artigo [Integração.](concepts-integration-runtime.md#azure-ssis-integration-runtime) Para obter informações sobre a implementação e execução de pacotes SSIS no Azure, consulte [lift e transfer SQL Server Integration Services para a nuvem](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Este artigo contém resultados de desempenho e observações de testes internos feitos por membros da equipa de desenvolvimento do SSIS. Os seus resultados podem variar. Faça os seus próprios testes antes de finalizar as definições de configuração, que afetam tanto o custo como o desempenho.

## <a name="properties-to-configure"></a>Propriedades para configurar

A seguinte parte de um script de configuração mostra as propriedades que pode configurar quando cria um Tempo de Execução de Integração Azure-SSIS. Para obter o script e descrição completos do PowerShell, consulte os pacotes de Serviços de Integração de [Servidores SQL para o Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

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
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
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

## <a name="azuressislocation"></a>Localização AzureSSIS
**AzureSSISLocation** é o local para o nó de trabalhador esgotantes de integração. O nó operário mantém uma ligação constante à base de dados do Catálogo SSIS (SSISDB) na Base de Dados Azure SQL. Detete a **Localização AzureSSISPara** o mesmo local que o [servidor lógico SQL](../azure-sql/database/logical-servers.md) que acolhe o SSISDB, o que permite que o tempo de funcionamento da integração funcione da forma mais eficiente possível.

## <a name="azuressisnodesize"></a>Tamanho do nódodesize azuressis
A Fábrica de Dados, incluindo o IR Azure-SSIS, suporta as seguintes opções:
-   Padrão \_ A4 \_ v2
-   Padrão \_ A8 \_ v2
-   Padrão \_ D1 \_ v2
-   Padrão \_ D2 \_ v2
-   Padrão \_ D3 \_ v2
-   Padrão \_ D4 \_ v2
-   Padrão \_ D2 \_ v3
-   Padrão \_ D4 \_ v3
-   Padrão \_ D8 \_ v3
-   Padrão \_ D16 \_ v3
-   Padrão \_ D32 \_ v3
-   Padrão \_ D64 \_ v3
-   Padrão \_ E2 \_ v3
-   Padrão \_ E4 \_ v3
-   Padrão \_ E8 \_ v3
-   Padrão \_ E16 \_ v3
-   Padrão \_ E32 \_ v3
-   Padrão \_ E64 \_ v3

Nos testes não oficiais internos da equipa de engenharia ssis, a série D parece ser mais adequada para a execução de pacotes SSIS do que a série A.

-   O rácio desempenho/preço da série D é superior à série A e o rácio desempenho/preço da série v3 é superior à série v2.
-   O resultado da série D é superior à série A ao mesmo preço e o resultado para a série v3 é superior à série v2 ao mesmo preço.
-   Os nós da série V2 do Azure-SSIS IR não são adequados para configuração personalizada, por isso, por favor, use os nós da série V3. Se já utilizar os nós da série V2, troque para utilizar os nós da série V3 o mais rapidamente possível.
-   A série E é tamanhos VM otimizados de memória que proporcionam uma relação memória-CPU mais elevada do que outras máquinas. Se o seu pacote necessitar de muita memória, pode considerar escolher a série E VM.

### <a name="configure-for-execution-speed"></a>Configurar para a velocidade de execução
Se não tiver muitos pacotes para executar e quiser que os pacotes sejam executados rapidamente, use a informação no gráfico seguinte para escolher um tipo de máquina virtual adequado para o seu cenário.

Estes dados representam uma execução de um único pacote num único nó de trabalhador. O pacote carrega 3 milhões de registos com o primeiro nome e colunas de apelido do Armazenamento De Blob Azure, gera uma coluna de nome completo, e escreve os registos que têm o nome completo com mais de 20 caracteres para o Armazenamento De Blob Azure.

![Velocidade de execução do pacote de execução do pacote de integração SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Configurar para a maior parte

Se tiver muitos pacotes para executar, e mais se importar com a entrada geral, use a informação no gráfico seguinte para escolher um tipo de máquina virtual adequado para o seu cenário.

![SSIS Integração Prazo máximo de execução global](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>Número de nódonúmero azureSsIS

**AzureSSISNodeNumber** ajusta a escalabilidade do tempo de execução da integração. A entrada do tempo de execução da integração é proporcional ao **Número de Nódonúmero AzureSSIS**. Ajuste o **AzureSSISNodeNumber** para um pequeno valor no início, monitorize a entrada do tempo de execução da integração e, em seguida, ajuste o valor para o seu cenário. Para reconfigurar a contagem do nó do trabalhador, consulte Gerir um tempo de execução de [integração Azure-SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxExecuções PerNode

Quando já está a usar um poderoso nó de trabalhador para executar pacotes, o aumento do **AzureSSISMaxParallelExecutionsPerNode** pode aumentar a potência global do tempo de execução da integração. Para Standard_D1_v2 nós, são apoiadas 1-4 execuções paralelas por nó. Para todos os outros tipos de nós, são suportadas execuções paralelas de 1-max (2 x, 8) por nó. Se quiser **AzureSSISMaxParallelExecutionsPerNode** para além do valor máximo que suportamos, pode abrir um bilhete de suporte e podemos aumentar o valor máximo para si e depois disso precisa de usar a Azure Powershell para atualizar **o AzureSISMaxParallelExecutionsPerNode**.
Pode estimar o valor adequado com base no custo do seu pacote e nas seguintes configurações para os nódosos dos trabalhadores. Para mais informações, consulte os tamanhos das [máquinas virtuais de uso geral](../virtual-machines/windows/sizes-general.md).

| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Padrão \_ D1 \_ v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Padrão \_ D2 \_ v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Padrão \_ D3 \_ v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Padrão \_ D4 \_ v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Padrão \_ A4 \_ v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Padrão \_ A8 \_ v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| Padrão \_ D2 \_ v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Padrão \_ D4 \_ v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Padrão \_ D8 \_ v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Padrão \_ D16 \_ v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Padrão \_ D32 \_ v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Padrão \_ D64 \_ v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Padrão \_ E2 \_ v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Padrão \_ E4 \_ v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Padrão \_ E8 \_ v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Padrão \_ E16 \_ v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Padrão \_ E32 \_ v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Padrão \_ E64 \_ v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Aqui estão as diretrizes para a definição do valor certo para a propriedade **AzureSSISMaxParallelExecutionsPerNode:** 

1. Coloque-o num pequeno valor no início.
2. Aumente-o em uma pequena quantidade para verificar se a entrada global é melhorada.
3. Pare de aumentar o valor quando a entrada global atingir o valor máximo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** é o nível de preços da base de dados do Catálogo SSIS (SSISDB) numa base de dados Azure SQL. Esta definição afeta o número máximo de trabalhadores na instância de IR, a velocidade para fazer fila uma execução de pacote, e a velocidade para carregar o registo de execução.

-   Se não se importar com a velocidade de execução do pacote de filas e carregar o registo de execução, pode escolher o nível de preços de base de dados mais baixo. A Base de Dados Azure SQL com preços Básicos suporta 8 trabalhadores numa instância de tempo de integração.

-   Escolha uma base de dados mais poderosa do que o Básico se a contagem de trabalhadores for superior a 8, ou se a contagem de núcleos for superior a 50. Caso contrário, a base de dados torna-se o estrangulamento da instância de tempo de integração e o desempenho global é impactado negativamente.

-   Escolha uma base de dados mais potente, como o s3, se o nível de registo estiver definido para verboso. De acordo com os nossos testes não oficiais internos, o nível de preços S3 pode suportar a execução de pacotes SSIS com 2 nós, 128 contagens paralelas e nível de registo verboso.

Também pode ajustar o nível de preços da base de dados com base nas informações de utilização da unidade de [transação](../sql-database/sql-database-what-is-a-dtu.md) de base de dados (DTU) disponíveis no portal Azure.

## <a name="design-for-high-performance"></a>Conceber o elevado desempenho
Desenhar um pacote SSIS para funcionar no Azure é diferente de conceber um pacote para a execução no local. Em vez de combinar várias tarefas independentes no mesmo pacote, separe-as em vários pacotes para uma execução mais eficiente no IR Azure-SSIS. Crie uma execução organizada para cada pacote, para que não tenham que esperar um para o outro terminar. Esta abordagem beneficia da escalabilidade do tempo de execução de integração Azure-SSIS e melhora o rendimento global.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre o Tempo de Execução de Integração Azure-SSIS. Ver Tempo de [execução de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
