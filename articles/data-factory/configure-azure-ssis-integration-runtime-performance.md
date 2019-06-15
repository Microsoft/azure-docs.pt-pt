---
title: Configurar o desempenho para o Runtime de integração de SSIS do Azure | Documentos da Microsoft
description: Saiba como configurar as propriedades do Runtime de integração Azure-SSIS para elevado desempenho
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 42c69653a002446552da998320a43730dfdaadf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232521"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configurar o Runtime de integração Azure-SSIS para elevado desempenho

Este artigo descreve como configurar um IR Azure-SSIS Integration Runtime () para elevado desempenho. O IR Azure-SSIS permite-lhe implementar e executar pacotes do SQL Server Integration Services (SSIS) no Azure. Para obter mais informações sobre o IR Azure-SSIS, veja [runtime de integração](concepts-integration-runtime.md#azure-ssis-integration-runtime) artigo. Para obter informações sobre como implementar e executar pacotes do SSIS no Azure, consulte [Lift- and -shift do SQL Server Integration Services cargas de trabalho para a cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Este artigo contém os resultados de desempenho e observações de teste internos, executado por membros da equipe de desenvolvimento do SSIS. Os resultados podem variar. Faça seu próprio teste antes de finalizar as definições de configuração que afetam o desempenho e custo.

## <a name="properties-to-configure"></a>Propriedades de configuração

A seguinte parte de um script de configuração mostra as propriedades que pode configurar quando criar um Runtime de integração Azure-SSIS. Para o script de PowerShell completo e a descrição, consulte [pacotes de implementar o SQL Server Integration Services para o Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** é a localização para o nó de trabalho do runtime de integração. Nó de trabalho mantém uma conexão constante com o catálogo de SSIS da base de dados (SSISDB) numa base de dados SQL do Azure. Definir o **AzureSSISLocation** para a mesma localização que o servidor de base de dados SQL que aloja o SSISDB, que permite que o runtime de integração para trabalhar mais eficientemente possível.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
O Data Factory, incluindo o IR Azure-SSIS, suporta as seguintes opções:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

No não oficial internas teste de pela equipe de engenharia do SSIS, a série D parece ser mais adequado para a execução de pacotes do SSIS que a série A.

-   A proporção de preço/desempenho da série D é maior do que a série A e a proporção de preço/desempenho da série v3 é maior do que a série dv2.
-   A taxa de transferência para a série de D é maior do que a série A, pelo mesmo preço e o débito para a série de v3 é maior do que a série dv2, ao mesmo preço.
-   Nós da série a v2 de IR Azure-SSIS não são adequados para a configuração personalizada, para que utilize em vez disso, os nós de série v3. Se já utilizar nós de série a v2, mude para utilizar os nós de série v3 logo que possível.
-   A série E é a tamanhos de VM que fornece um rácio de memória de CPU superior a outras máquinas com otimização de memória. Se o seu pacote requer muita memória, pode considerar a escolha de série E VM.

### <a name="configure-for-execution-speed"></a>Configurar para a velocidade de execução
Se não tiver muitos pacotes para executar e pretender que os pacotes para executar rapidamente, utilize as informações na tabela a seguir para escolher um tipo de máquina virtual adequado para o seu cenário.

Estes dados representam uma execução de único pacote num nó de trabalho única. O pacote carrega 3 milhões de registros com o nome próprio e o último colunas de nome de armazenamento de Blobs do Azure, gera uma coluna de nome completo e grava os registros que têm o nome completo mais de 20 carateres para o armazenamento de Blobs do Azure.

![Velocidade de execução do pacote de integração do SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Configurar para o débito global

Se tiver muitos pacotes para executar e, mais importantes sobre a produtividade geral, utilize as informações na tabela a seguir para escolher um tipo de máquina virtual adequado para o seu cenário.

![Débito total máximo de Runtime de integração de SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** ajusta a escalabilidade do runtime de integração. O débito do integration runtime é proporcional para o **AzureSSISNodeNumber**. Definir o **AzureSSISNodeNumber** para um valor pequeno em primeiro lugar, monitorizar o débito do integration runtime, em seguida, ajustar o valor para o seu cenário. Para reconfigurar a contagem de nós de trabalho, consulte [gerir um runtime de integração Azure-SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Quando um nó de trabalho eficiente já estiver a utilizar para executar pacotes, aumentando **AzureSSISMaxParallelExecutionsPerNode** pode aumentar o débito global do integration runtime. Para nós Standard_D1_v2, 1 a 4 de execuções paralelas por nó são suportadas. Para todos os outros tipos de nós, 1-max(2 x number of cores, 8) execuções paralelas por nó são suportadas. Se desejar **AzureSSISMaxParallelExecutionsPerNode** além do valor máximo suportámos, pode abrir um pedido de suporte e pode aumentar o valor máximo para e depois que precisa utilizar o Azure Powershell para atualizar  **AzureSSISMaxParallelExecutionsPerNode**.
Pode estimar o valor apropriado com base no custo do seu pacote e as seguintes configurações para os nós de trabalho. Para obter mais informações, consulte [tamanhos de máquina de virtual para fins gerais](../virtual-machines/windows/sizes-general.md).

| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos / débito: IOPS | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Seguem-se as diretrizes para definir o valor correto para o **AzureSSISMaxParallelExecutionsPerNode** propriedade: 

1. Defina-o como um valor pequeno em primeiro lugar.
2. Aumentá-la por uma pequena quantidade para verificar se o débito global foi melhorado.
3. Pare a aumentar o valor quando o débito global atinge o valor máximo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** é o escalão de preço para a base de catálogo de SSIS (SSISDB) numa base de dados SQL do Azure. Esta definição afeta o número máximo de trabalhadores na instância do Runtime de integração, a velocidade para uma execução de pacotes de fila e a velocidade, para carregar o registo de execução.

-   Se não se preocupa a velocidade de execução de pacotes de fila e para carregar o registo de execução, pode escolher o escalão de preço mais baixo da base de dados. Base de dados de SQL do Azure com preços básico suporta os operadores de 8 numa instância do runtime de integração.

-   Se a contagem de trabalho é mais do que 8 ou o número de núcleos é mais do que 50, escolha uma base de dados mais poderosa do básico. Caso contrário, a base de dados torna-se o afunilamento da instância do runtime de integração e o desempenho geral é afetado negativamente.

-   Escolha uma base de dados mais poderosa, como o s3, se o nível de registo está definido para verboso. De acordo com nossos testes internos não oficial, escalão de preço s3 pode suportar a execução de pacotes do SSIS com 2 nós, 128 contagens paralelas e nível de registo verboso.

Também pode ajustar a base de dados com base do escalão de preço [unidade de transação de base de dados](../sql-database/sql-database-what-is-a-dtu.md) (DTU) informações de utilização disponíveis no portal do Azure.

## <a name="design-for-high-performance"></a>Conceber o elevado desempenho
A criação de um pacote do SSIS para execução no Azure é diferente da criação de um pacote para execução no local. Em vez de combinar várias tarefas independentes no mesmo pacote, separá-las em vários pacotes para execução mais eficiente no ir Azure-SSIS. Crie uma execução de pacotes para cada pacote, para que não tenham de aguardar por si para concluir. Esta abordagem se beneficia da escalabilidade do runtime de integração Azure-SSIS e melhora a produtividade geral.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o Runtime de integração Azure-SSIS. Ver [Runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
