---
title: 'Provisione Enterprise Edition para o Azure-SSIS Integration Runtime '
description: Este artigo descreve os recursos do Enterprise Edition para o Azure-SSIS Integration Runtime e como provisioná-los
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8c9db18d416d508dd5a98cbacc14b7db6c075a55
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673962"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Provisione Enterprise Edition para o Azure-SSIS Integration Runtime

A edição Enterprise do Azure-SSIS Integration Runtime permite que você use os seguintes recursos avançados e Premium:
-   Componentes de captura de dados de alterações (CDC)
-   Conectores do Oracle, Teradata e SAP BW
-   Conectores e transformações do SQL Server Analysis Services (SSAS) e Azure Analysis Services (AAS)
-   Transformações de agrupamento difuso e pesquisa difusa
-   Transformações de extração de termo e pesquisa de termos

Alguns desses recursos exigem que você instale componentes adicionais para personalizar o Azure-SSIS IR. Para obter mais informações sobre como instalar componentes adicionais, consulte [configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Recursos empresariais

| **Recursos empresariais** | **Descrições** |
|---|---|
| Componentes CDC | A fonte CDC, a tarefa de controle e a transformação do Splitter são pré-instalados no Azure-SSIS IR Enterprise Edition. Para se conectar ao Oracle, você também precisa instalar o CDC Designer e o serviço em outro computador. |
| Conectores Oracle | O Gerenciador de conexões Oracle, a origem e o destino são pré-instalados no Azure-SSIS IR Enterprise Edition. Você também precisa instalar o driver do Oracle Call interface (OCI) e, se necessário, configurar o substrato da rede de transporte Oracle (TNS) na Azure-SSIS IR. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores do Teradata | Você precisa instalar o Gerenciador de conexões Teradata, a origem e o destino, bem como a API do Teradata Parallel transporte (TPT) e o driver de ODBC do Teradata, no Azure-SSIS IR Enterprise Edition. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Conectores de SAP BW | O Gerenciador de conexões SAP BW, a origem e o destino são pré-instalados no Azure-SSIS IR Enterprise Edition. Você também precisa instalar o driver SAP BW no Azure-SSIS IR. Esses conectores dão suporte a SAP BW 7,0 ou versões anteriores. Para se conectar a versões posteriores do SAP BW ou de outros produtos SAP, você pode comprar e instalar conectores do SAP de ISVs de terceiros no Azure-SSIS IR. Para obter mais informações sobre como instalar componentes adicionais, consulte [configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Componentes do Analysis Services               | O destino de treinamento do modelo de mineração de dados, o destino de processamento de dimensões e o destino de processamento de partições, bem como a transformação consulta de mineração de dados, são pré-instalados no Azure-SSIS IR Enterprise Edition. Todos esses componentes dão suporte a SQL Server Analysis Services (SSAS), mas somente o destino de processamento de partições dá suporte a Azure Analysis Services (AAS). Para se conectar ao SSAS, você também precisa [configurar as credenciais de autenticação do Windows no SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Além desses componentes, a Analysis Services executar a tarefa DDL, a tarefa de processamento de Analysis Services e a tarefa de consulta de mineração de dados também são pré-instalados no Azure-SSIS IR Standard/Enterprise Edition. |
| Transformações de agrupamento difuso e pesquisa difusa  | As transformações de agrupamento difuso e pesquisa difusa são pré-instalados no Azure-SSIS IR Enterprise Edition. Esses componentes dão suporte tanto ao SQL Server quanto ao banco de dados SQL do Azure para armazenamento de dado de referência. |
| Transformações de extração de termo e pesquisa de termos | As transformações de extração de termos e pesquisa de termos são pré-instalados no Azure-SSIS IR Enterprise Edition. Esses componentes dão suporte tanto ao SQL Server quanto ao banco de dados SQL do Azure para armazenamento de dado de referência. |

## <a name="instructions"></a>Instruções

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Baixe e instale o [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Ao provisionar ou reconfigurar o Azure-SSIS IR com o PowerShell, execute `Set-AzDataFactoryV2IntegrationRuntime` com **Enterprise** como o valor do parâmetro de **edição** antes de iniciar o Azure-SSIS ir. Aqui está um script de exemplo:

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

-   [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o tempo de execução de integração do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
