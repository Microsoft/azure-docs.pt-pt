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
ms.openlocfilehash: 271da0a6ff443fcee28bc870821f4222b3018c91
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576876"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configurar o Runtime de integração Azure-SSIS para elevado desempenho

Este artigo descreve como configurar um IR Azure-SSIS Integration Runtime () para elevado desempenho. O IR Azure-SSIS permite-lhe implementar e executar pacotes do SQL Server Integration Services (SSIS) no Azure. Para obter mais informações sobre o IR Azure-SSIS, veja [runtime de integração](concepts-integration-runtime.md#azure-ssis-integration-runtime) artigo. Para obter informações sobre como implementar e executar pacotes do SSIS no Azure, consulte [Lift- and -shift do SQL Server Integration Services cargas de trabalho para a cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Este artigo contém os resultados de desempenho e observações de teste internos, executado por membros da equipe de desenvolvimento do SSIS. Os resultados podem variar. Faça seu próprio teste antes de finalizar as definições de configuração que afetam o desempenho e custo.

## <a name="properties-to-configure"></a>Propriedades de configuração

A seguinte parte de um script de configuração mostra as propriedades que pode configurar quando criar um Runtime de integração Azure-SSIS. Para o script de PowerShell completo e a descrição, consulte [pacotes de implementar o SQL Server Integration Services para o Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
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
-   Standard\_D4\_v2.

No não oficial internas teste de pela equipe de engenharia do SSIS, a série D parece ser mais adequado para a execução de pacotes do SSIS que a série A.

-   A proporção de preço/desempenho da série D é maior do que a série A.
-   A taxa de transferência para a série de D é maior do que a série A, pelo mesmo preço.

### <a name="configure-for-execution-speed"></a>Configurar para a velocidade de execução
Se não tiver muitos pacotes para executar e pretender que os pacotes para executar rapidamente, utilize as informações na tabela a seguir para escolher um tipo de máquina virtual adequado para o seu cenário.

Estes dados representam uma execução de único pacote num nó de trabalho única. O pacote carrega 10 milhões de registros com o nome próprio e o último colunas de nome de armazenamento de Blobs do Azure, gera uma coluna de nome completo e grava os registros que têm o nome completo mais de 20 carateres para o armazenamento de Blobs do Azure.

![Velocidade de execução do pacote de integração do SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Configurar para o débito global

Se tiver muitos pacotes para executar e, mais importantes sobre a produtividade geral, utilize as informações na tabela a seguir para escolher um tipo de máquina virtual adequado para o seu cenário.

![Débito total máximo de Runtime de integração de SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** ajusta a escalabilidade do runtime de integração. O débito do integration runtime é proporcional para o **AzureSSISNodeNumber**. Definir o **AzureSSISNodeNumber** para um valor pequeno em primeiro lugar, monitorizar o débito do integration runtime, em seguida, ajustar o valor para o seu cenário. Para reconfigurar a contagem de nós de trabalho, consulte [gerir um runtime de integração Azure-SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Quando um nó de trabalho eficiente já estiver a utilizar para executar pacotes, aumentando **AzureSSISMaxParallelExecutionsPerNode** pode aumentar o débito global do integration runtime. Para nós Standard_D1_v2, 1 a 4 de execuções paralelas por nó são suportadas. Para todos os outros tipos de nós, 1 a 8 de execuções paralelas por nó são suportadas.
Pode estimar o valor apropriado com base no custo do seu pacote e as seguintes configurações para os nós de trabalho. Para obter mais informações, consulte [tamanhos de máquina de virtual para fins gerais](../virtual-machines/windows/sizes-general.md).

| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos / débito: IOPS | NICs. Máx. / Desempenho de rede esperado (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |

Seguem-se as diretrizes para definir o valor correto para o **AzureSSISMaxParallelExecutionsPerNode** propriedade: 

1. Defina-o como um valor pequeno em primeiro lugar.
2. Aumentá-la por uma pequena quantidade para verificar se o débito global foi melhorado.
3. Pare a aumentar o valor quando o débito global atinge o valor máximo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** é o escalão de preço para a base de catálogo de SSIS (SSISDB) numa base de dados SQL do Azure. Esta definição afeta o número máximo de trabalhadores na instância do Runtime de integração, a velocidade para uma execução de pacotes de fila e a velocidade, para carregar o registo de execução.

-   Se não se preocupa a velocidade de execução de pacotes de fila e para carregar o registo de execução, pode escolher o escalão de preço mais baixo da base de dados. Base de dados de SQL do Azure com preços básico suporta os operadores de 8 numa instância do runtime de integração.

-   Se a contagem de trabalho é mais do que 8 ou o número de núcleos é mais do que 50, escolha uma base de dados mais poderosa do básico. Caso contrário, a base de dados torna-se o afunilamento da instância do runtime de integração e o desempenho geral é afetado negativamente.

Também pode ajustar a base de dados com base do escalão de preço [unidade de transação de base de dados](../sql-database/sql-database-what-is-a-dtu.md) (DTU) informações de utilização disponíveis no portal do Azure.

## <a name="design-for-high-performance"></a>Conceber o elevado desempenho
A criação de um pacote do SSIS para execução no Azure é diferente da criação de um pacote para execução no local. Em vez de combinar várias tarefas independentes no mesmo pacote, separá-las em vários pacotes para execução mais eficiente no ir Azure-SSIS. Crie uma execução de pacotes para cada pacote, para que não tenham de aguardar por si para concluir. Esta abordagem se beneficia da escalabilidade do runtime de integração Azure-SSIS e melhora a produtividade geral.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o Runtime de integração Azure-SSIS. Ver [Runtime de integração Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
