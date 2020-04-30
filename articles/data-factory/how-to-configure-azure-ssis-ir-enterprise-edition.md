---
title: Provision Enterprise Edition para o Tempo de Integração Azure-SSIS
description: Este artigo descreve as características da Enterprise Edition para o Runtime de Integração Azure-SSIS e como fortá-lo
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: a0e35a309fd30a36263b498b6a8e74a312004a99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606164"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Provision Enterprise Edition para o Tempo de Integração Azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Edição Enterprise do Runtime de Integração Azure-SSIS permite-lhe utilizar as seguintes funcionalidades avançadas e premium:
-   Alterar componentes de captura de dados (CDC)
-   Conectores Oracle, Teradata e SAP BW
-   Os Serviços de Análise de Servidores SQL (SSAS) e os Serviços de Análise De Azure (AAS) e os conectores e transformações
-   Transformações de Agrupamento fuzzy e lookup fuzzy
-   Transformações de extração a termo e de procura de termo

Algumas destas funcionalidades exigem que instale componentes adicionais para personalizar o IR Azure-SSIS. Para obter mais informações sobre como instalar componentes adicionais, consulte a configuração personalizada para o tempo de [funcionamento da integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funcionalidades do Enterprise

| **Características da empresa** | **Descrições** |
|---|---|
| Componentes CDC | A CDC Source, Control Task e Splitter Transformation estão pré-instaladas na Edição Empresarial Azure-SSIS IR. Para se ligar à Oracle, também precisa de instalar o CDC Designer e O Serviço noutro computador. |
| Conectores oráculo | O Oracle Connection Manager, Source e Destination estão pré-instalados na Edição Empresarial Azure-SSIS IR. Também é necessário instalar o controlador Oracle Call Interface (OCI) e, se necessário, configurar o Substrato da Rede de Transporte oracle (TNS), no IR Azure-SSIS. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores Teradata | É necessário instalar o Gestor de Ligação teradata, Fonte e Destino, bem como o controlador API e Teradata ODBC, na Edição Empresarial Do II da Azure-SSIS. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores SAP BW | O SAP BW Connection Manager, Source e Destination estão pré-instalados na Azure-SSIS IR Enterprise Edition. Também precisa de instalar o controlador SAP BW no IR Azure-SSIS. Estes conectores suportam versões SAP BW 7.0 ou anteriores. Para se ligar a versões posteriores de SAP BW ou outros produtos SAP, pode adquirir e instalar conectores SAP a partir de ISVs de terceiros no IR Azure-SSIS. Para obter mais informações sobre como instalar componentes adicionais, consulte a configuração personalizada para o tempo de [funcionamento da integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Componentes dos Serviços de Análise               | O Destino de Formação de Modelos de Mineração de Dados, o Destino de Processamento de Dimensões e o Destino de Processamento de Partição, bem como a Transformação da Consulta de Mineração de Dados, estão pré-instalados na Edição Empresarial Azure-SSIS IR. Todos estes componentes suportam serviços de análise de servidores SQL (SSAS), mas apenas o Destino de Processamento de Partição suporta serviços de análise azure (AAS). Para se ligar ao SSAS, também é necessário [configurar as credenciais de autenticação do Windows no SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Além destes componentes, os Serviços de Análise executam a Tarefa DDL, a Tarefa de Processamento de Serviços de Análise e a Tarefa de Consulta de Mineração de Dados também estão pré-instaladas na Edição Padrão/Empresa Do Azure-SSIS IR. |
| Transformações de Agrupamento fuzzy e lookup fuzzy  | As transformações fuzzy Grouping e Fuzzy Lookup estão pré-instaladas na Azure-SSIS IR Enterprise Edition. Estes componentes suportam tanto o SQL Server como o Azure SQL Database para armazenar dados de referência. |
| Transformações de extração a termo e de procura de termo | As transformações de Extração a Prazo e Descuido a Prazo estão pré-instaladas na Edição Empresarial Azure-SSIS IR. Estes componentes suportam tanto o SQL Server como o Azure SQL Database para armazenar dados de referência. |

## <a name="instructions"></a>Instruções

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Descarregue e [instale o Azure PowerShell.](/powershell/azure/install-az-ps)

2.  Quando fornecer ou reconfigurar o IR Azure-SSIS `Set-AzDataFactoryV2IntegrationRuntime` com powerShell, corra com a **Enterprise** como o valor para o parâmetro **Edition** antes de iniciar o IR Azure-SSIS. Aqui está um roteiro de amostra:

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

-   [Configuração personalizada para o tempo de execução de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o tempo de funcionamento da integração Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
