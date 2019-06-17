---
title: Instalar componentes licenciados para o runtime de integração de SSIS do Azure | Documentos da Microsoft
description: Saiba como pode desenvolver um ISV e instalação pago ou licenciado componentes personalizados para o runtime de integração Azure-SSIS
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
ms.openlocfilehash: 80d4fff03422beacccd3aff3cdd8cb1047d5f5af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61344659"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalar componentes personalizados pagos ou licenciados para o runtime de integração Azure-SSIS

Este artigo descreve como um ISV pode desenvolver e instalar os componentes personalizados pagos ou licenciados para os pacotes do SQL Server Integration Services (SSIS) que são executadas no Azure no runtime de integração Azure-SSIS.

## <a name="the-problem"></a>O problema

A natureza do runtime de integração Azure-SSIS apresenta vários desafios, que tornam os métodos de licenciamento típicos utilizados para a instalação de locais de componentes personalizados inadequados. Como resultado, o IR Azure-SSIS requer uma abordagem diferente.

-   Os nós do Runtime de integração do Azure-SSIS são voláteis e podem ser alocados ou lançadas em qualquer altura. Por exemplo, pode iniciar ou parar nós para gerir o custo ou aumentar vertical ou horizontalmente por meio de vários tamanhos de nó. Como resultado, uma licença de componente de terceiros de ligação para um nó particular ao utilizar as informações de específicas da máquina, como o endereço MAC ou o ID de CPU já não é viável.

-   Também pode aumentar o IR Azure-SSIS dentro ou para fora, para que o número de nós pode reduzir ou expandir a qualquer momento.

## <a name="the-solution"></a>A solução

Como resultado das limitações dos métodos de licenciamento tradicionais, descritos na secção anterior, o IR Azure-SSIS fornece uma nova solução. Esta solução utiliza as variáveis de ambiente do Windows e de variáveis de sistema do SSIS para a ligação de licença e a validação dos componentes de terceiros. Os ISVs podem usar essas variáveis para obter informações sobre a exclusivo e persistente para um runtime de integração Azure-SSIS, como ID de Cluster e a contagem de nós de Cluster. Com estas informações, os ISVs podem fazer a ligação a licença para o seu componente um IR Azure-SSIS *como um cluster*. Esta ligação utiliza um ID que não é alterada quando os clientes iniciar ou pararem, aumente ou diminua, dimensionamento e reduzir verticalmente ou reconfigurar o IR Azure-SSIS de forma alguma.

O diagrama seguinte mostra o tipo de instalação típico, a ativação e a vinculação de licença e fluxos de validação para os componentes de terceiros que usam essas novas variáveis:

![Instalação de componentes licenciados](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruções
1. Os ISVs podem oferecer a seus componentes licenciados em várias SKUs ou camadas (por exemplo, nó único, até 5 nós, até 10 nós e assim por diante). O ISV fornece a chave de produto correspondentes quando os clientes comprar um produto. O ISV pode também fornecer um contentor de BLOBs de armazenamento do Azure que contém um script de configuração de ISV e os ficheiros associados. Os clientes podem copiar esses arquivos em seu próprio contentor de armazenamento e modificá-los com a sua própria chave de produto (por exemplo, ao executar `IsvSetup.exe -pid xxxx-xxxx-xxxx`). Os clientes podem aprovisionar ou reconfigurar o IR Azure-SSIS com o URI de SAS do respetivo contentor como parâmetro. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Quando o IR Azure-SSIS é aprovisionado ou reconfigurado, configuração de ISV é executada em cada nó para consultar as variáveis de ambiente do Windows `SSIS_CLUSTERID` e `SSIS_CLUSTERNODECOUNT`. Em seguida, o IR Azure-SSIS submete o seu ID de Cluster e a chave de produto para o produto licenciado para o servidor de ativação de ISV para gerar uma chave de ativação.

3. Depois de receber a chave de ativação, configuração de ISV pode armazenar a chave localmente em cada nó (por exemplo, no Registro).

4. Quando os clientes executar um pacote que usa o componente de licenciada do ISV num nó do Runtime de integração do Azure-SSIS, o pacote lê a chave de ativação armazenado localmente e validá-lo contra o ID de Cluster. do nó O pacote opcionalmente, também pode informar a contagem de nós de Cluster para o servidor de ativação de ISV.

    Eis um exemplo de código que valida a chave de ativação e reporta a contagem de nós de cluster:

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

## <a name="isv-partners"></a>Parceiros de ISV

Pode encontrar uma lista de parceiros de ISV que tem adaptada seus componentes e extensões para o runtime de integração Azure-SSIS no final desta mensagem de blogue - [Enterprise Edition, a configuração personalizada e o 3º extensibilidade de terceiros para SSIS no ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/).

## <a name="next-steps"></a>Passos Seguintes

-   [Configuração personalizada para o runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Edição Enterprise do Runtime de integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
