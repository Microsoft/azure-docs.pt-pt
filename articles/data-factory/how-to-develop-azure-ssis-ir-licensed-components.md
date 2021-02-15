---
title: Instalar componentes licenciados para o tempo de funcionamento da integração Azure-SSIS
description: Saiba como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para o tempo de funcionamento da integração Azure-SSIS
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd62822e111346ee9a81a5d1bcce55191b19da02
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386293"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalar componentes personalizados pagos ou licenciados para o Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para pacotes SQL Server Integration Services (SSIS) que funcionam em Azure no tempo de integração Azure-SSIS.

## <a name="the-problem"></a>O problema

A natureza do tempo de integração Azure-SSIS apresenta vários desafios, que tornam inadequados os métodos típicos de licenciamento utilizados para a instalação de componentes personalizados no local. Como resultado, o Azure-SSIS IR requer uma abordagem diferente.

-   Os nós do Azure-SSIS IR são voláteis e podem ser atribuídos ou libertados a qualquer momento. Por exemplo, pode iniciar ou parar os nós para gerir o custo, ou escalar para cima e para baixo através de vários tamanhos de nó. Como resultado, vincular uma licença de componente de terceiros a um determinado nó utilizando informações específicas da máquina, como endereço MAC ou ID CPU, já não é viável.

-   Também pode escalar o Azure-SSIS IR dentro ou fora, para que o número de nós possa encolher ou expandir a qualquer momento.

## <a name="the-solution"></a>A solução

Como resultado das limitações dos métodos tradicionais de licenciamento descritos na secção anterior, o Azure-SSIS IR fornece uma nova solução. Esta solução utiliza variáveis ambientais do Windows e variáveis do sistema SSIS para a ligação da licença e validação de componentes de terceiros. Os ISVs podem usar estas variáveis para obter informações únicas e persistentes para um Azure-SSIS IR, como o Cluster ID e o Cluster Node Count. Com esta informação, os ISVs podem então ligar a licença do seu componente a um Azure-SSIS IR *como um cluster*. Esta ligação utiliza um ID que não muda quando os clientes iniciam ou param, escalam para cima ou para baixo, escalam dentro ou fora, ou reconfiguram o Azure-SSIS IR de qualquer forma.

O diagrama a seguir mostra a instalação típica, ativação e ligação de licenças, e fluxos de validação para componentes de terceiros que utilizam estas novas variáveis:

![Instalação de componentes licenciados](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruções
1. Os ISVs podem oferecer os seus componentes licenciados em vários SKUs ou níveis (por exemplo, nó único, até 5 nóns, até 10 nóns, etc. O ISV fornece a chave de produto correspondente quando os clientes compram um produto. O ISV também pode fornecer um recipiente de blob de armazenamento Azure que contém um script de configuração ISV e ficheiros associados. Os clientes podem copiar estes ficheiros para o seu próprio recipiente de armazenamento e modificá-los com a sua própria Chave de Produto (por exemplo, `IsvSetup.exe -pid xxxx-xxxx-xxxx` executando). Os clientes podem então provisões ou reconfigurar o Azure-SSIS IR com o SAS URI do seu recipiente como parâmetro. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Quando o Azure-SSIS IR é provisionado ou reconfigurado, a Configuração ISV funciona em cada nó para consultar as variáveis ambientais do `SSIS_CLUSTERID` Windows, e `SSIS_CLUSTERNODECOUNT` . Em seguida, o Azure-SSIS IR submete o seu ID de Cluster e a Chave de Produto para o produto licenciado ao Servidor de Ativação ISV para gerar uma chave de ativação.

3. Depois de receber a Chave de Ativação, a Configuração ISV pode armazenar a chave localmente em cada nó (por exemplo, no Registo).

4. Quando os clientes executam um pacote que utiliza o componente licenciado do ISV num nó do Azure-SSIS IR, o pacote lê a Chave de Ativação armazenada localmente e valida-a contra o ID do cluster do nó. O pacote também pode reportar opcionalmente a contagem de nó de cluster para o servidor de ativação ISV.

    Aqui está um exemplo de código que valida a chave de ativação e reporta a contagem de nó de cluster:

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

Pode encontrar uma lista de parceiros ISV que adaptaram os seus componentes e extensões para o Azure-SSIS IR no final deste blog post - [Enterprise Edition, Custom Setup, e 3ª Extensibilidade de Partes para SSIS em ADF.](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360)

## <a name="next-steps"></a>Passos seguintes

-   [Configuração personalizada para o tempo de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Edição Empresarial do Tempo de Integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
