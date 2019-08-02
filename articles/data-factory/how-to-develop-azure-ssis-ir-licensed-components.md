---
title: Instalar componentes licenciados para o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Saiba como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para o tempo de execução de integração do Azure-SSIS
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 1c574578e6ed6ee032be01718eb3e8afd27fdf6f
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708011"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalar componentes personalizados pagos ou licenciados para o tempo de execução de integração do Azure-SSIS

Este artigo descreve como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para pacotes SQL Server Integration Services (SSIS) que são executados no Azure no tempo de execução de integração do Azure-SSIS.

## <a name="the-problem"></a>O problema

A natureza do tempo de execução de integração do Azure-SSIS apresenta vários desafios, que tornam os métodos de licenciamento típicos usados para a instalação local de componentes personalizados inadequados. Como resultado, o IR do Azure-SSIS requer uma abordagem diferente.

-   Os nós do IR do Azure-SSIS são voláteis e podem ser alocados ou liberados a qualquer momento. Por exemplo, você pode iniciar ou parar nós para gerenciar o custo ou escalar verticalmente e horizontalmente por meio de vários tamanhos de nó. Como resultado, a associação de uma licença de componente de terceiros a um nó específico usando informações específicas do computador, como o endereço MAC ou a ID da CPU, não é mais viável.

-   Você também pode dimensionar o IR do Azure-SSIS para dentro ou para fora, para que o número de nós possa ser reduzido ou expandido a qualquer momento.

## <a name="the-solution"></a>A solução

Como resultado das limitações dos métodos tradicionais de licenciamento descritos na seção anterior, o IR do Azure-SSIS fornece uma nova solução. Essa solução usa variáveis de ambiente do Windows e variáveis de sistema do SSIS para a associação de licença e a validação de componentes de terceiros. Os ISVs podem usar essas variáveis para obter informações exclusivas e persistentes para um IR do Azure-SSIS, como ID de cluster e contagem de nós de cluster. Com essas informações, os ISVs podem então associar a licença de seu componente a um IR do Azure-SSIS *como um cluster*. Essa associação usa uma ID que não é alterada quando os clientes iniciam ou param, aumentam ou diminuem, dimensionam ou reconfiguram o IR do Azure-SSIS de qualquer forma.

O diagrama a seguir mostra a instalação típica, a ativação e a associação de licença e os fluxos de validação para componentes de terceiros que usam essas novas variáveis:

![Instalação de componentes licenciados](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruções
1. Os ISVs podem oferecer seus componentes licenciados em várias SKUs ou camadas (por exemplo, nó único, até cinco nós, até 10 nós e assim por diante). O ISV fornece a chave do produto (Product Key) correspondente quando os clientes compram um produto. O ISV também pode fornecer um contêiner de blob de armazenamento do Azure que contém um script de instalação de ISV e arquivos associados. Os clientes podem copiar esses arquivos em seu próprio contêiner de armazenamento e modificá-los com sua própria chave de produto (por `IsvSetup.exe -pid xxxx-xxxx-xxxx`exemplo, executando). Os clientes podem provisionar ou reconfigurar o IR do Azure-SSIS com o URI de SAS de seu contêiner como parâmetro. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Quando o ir do Azure-SSIS é provisionado ou reconfigurado, a instalação do ISV é executada em cada nó para consultar as variáveis `SSIS_CLUSTERID` de ambiente do Windows e. `SSIS_CLUSTERNODECOUNT` Em seguida, o IR do Azure-SSIS envia sua ID de cluster e a chave do produto para o produto licenciado para o servidor de ativação ISV para gerar uma chave de ativação.

3. Depois de receber a chave de ativação, a instalação do ISV pode armazenar a chave localmente em cada nó (por exemplo, no registro).

4. Quando os clientes executam um pacote que usa o componente licenciado do ISV em um nó do IR do Azure-SSIS, o pacote lê a chave de ativação armazenada localmente e a valida em relação à ID de cluster do nó. O pacote também pode relatar a contagem de nós de cluster para o servidor de ativação ISV.

    Aqui está um exemplo de código que valida a chave de ativação e relata a contagem de nós de cluster:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>Parceiros ISV

Você pode encontrar uma lista de parceiros ISV que adaptaram seus componentes e extensões para o IR do Azure-SSIS no final deste blog post- [Enterprise Edition, instalação personalizada e extensibilidade de terceiros para SSIS no ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Passos Seguintes

-   [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enterprise Edition do Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
