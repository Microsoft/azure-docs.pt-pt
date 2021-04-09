---
title: Provision Enterprise Edition para o tempo de execução da integração Azure-SSIS
description: Este artigo descreve as características da Enterprise Edition para o tempo de execução da integração Azure-SSIS e como procurá-lo
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: f700729c2d059648b1c3a7e451526aefcb436818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387551"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Provision Enterprise Edition para o tempo de execução da integração Azure-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Edição Empresarial do Tempo de Execução de Integração Azure-SSIS permite-lhe utilizar as seguintes funcionalidades avançadas e premium:
-   Alterar componentes de Captura de Dados (CDC)
-   Conectores Oracle, Teradata e SAP BW
-   Serviços de Análise de Servidores SQL (SSAS) e Serviços de Análise de Azure (AAS) conectores e transformações
-   Fuzzy Grouping e fuzzy Lookup transformações
-   Extração de prazos e transformações de procura de prazos

Algumas destas funcionalidades requerem que instale componentes adicionais para personalizar o Azure-SSIS IR. Para obter mais informações sobre como instalar componentes adicionais, consulte [a configuração personalizada para o tempo de execução da integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Características da empresa

| **Características da Empresa** | **Descrições** |
|---|---|
| Componentes CDC | A CDC Source, Control Task e Splitter Transformation estão pré-instaladas na Edição Empresarial Azure-SSIS IR. Para se ligar à Oracle, também precisa de instalar o CDC Designer e o Service noutro computador. |
| Conectores oráculos | O Oracle Connection Manager, Source e Destination estão pré-instalados na Edição Empresarial Azure-SSIS IR. Também é necessário instalar o controlador Oracle Call Interface (OCI) e, se necessário, configurar o Substrato da Rede de Transportes Oracle (TNS), no Azure-SSIS IR. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores teradata | É necessário instalar o Gestor de Ligação Teradata, Fonte e Destino, bem como o condutor da Teradata Parallel Transportr (TPT) API e da Teradata ODBC, na Edição Empresarial Azure-SSIS IR. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores SAP BW | O Sap BW Connection Manager, Source e Destination estão pré-instalados na Edição Empresarial Azure-SSIS IR. Também é necessário instalar o controlador SAP BW no Azure-SSIS IR. Estes conectores suportam as versões SAP BW 7.0 ou anteriores. Para ligar a versões posteriores de SAP BW ou outros produtos SAP, pode adquirir e instalar conectores SAP a partir de ISVs de terceiros no Azure-SSIS IR. Para obter mais informações sobre como instalar componentes adicionais, consulte [a configuração personalizada para o tempo de execução da integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Componentes de Serviços de Análise               | O Destino de Formação do Modelo de Mineração de Dados, o Destino de Processamento de Dimensões e o Destino de Processamento de Partição, bem como a Transformação de Consulta de Data Mining, estão pré-instalados na Edição Empresarial Azure-SSIS IR. Todos estes componentes suportam serviços de análise de servidores SQL (SSAS), mas apenas o Destino de Processamento de Partição suporta os Serviços de Análise Azure (AAS). Para se ligar ao SSAS, também é necessário [configurar credenciais de autenticação do Windows em SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Para além destes componentes, os Serviços de Análise Executam A Tarefa DDL, a Tarefa de Processamento de Serviços de Análise e a Tarefa de Consulta de Data Mining estão também pré-instalados na Norma IR/Edição Empresarial Azure-SSIS. |
| Fuzzy Grouping e fuzzy Lookup transformações  | As transformações fuzzy Grouping e Fuzzy Lookup estão pré-instaladas na Edição Empresarial Azure-SSIS IR. Estes componentes suportam tanto o SQL Server como o Azure SQL Database para armazenar dados de referência. |
| Extração de prazos e transformações de procura de prazos | As transformações de Extração de Prazos e Desacureamento de Prazos estão pré-instaladas na Edição Empresarial Azure-SSIS IR. Estes componentes suportam tanto o SQL Server como o Azure SQL Database para armazenar dados de referência. |

## <a name="instructions"></a>Instruções

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Descarregue e instale [a Azure PowerShell](/powershell/azure/install-az-ps).

2.  Quando forres ou reconfigurares o Azure-SSIS IR com o PowerShell, corra `Set-AzDataFactoryV2IntegrationRuntime` com a **Enterprise** como o valor para o parâmetro **Edição** antes de iniciar o Azure-SSIS IR. Aqui está um guião de amostra:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Passos seguintes

-   [Configuração personalizada para o tempo de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o tempo de execução da integração Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)