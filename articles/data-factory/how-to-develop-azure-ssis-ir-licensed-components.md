---
title: Instale componentes licenciados para o tempo de funcionamento da integração Azure-SSIS
description: Saiba como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para o tempo de funcionamento da integração Azure-SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: b7079262dc7db4f4a00a9dc79193da1574c7153a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605862"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalar componentes personalizados pagos ou licenciados para o Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como um ISV pode desenvolver e instalar componentes personalizados pagos ou licenciados para pacotes SQL Server Integration Services (SSIS) que funcionam em Azure no tempo de funcionamento da integração Azure-SSIS.

## <a name="the-problem"></a>O problema

A natureza do tempo de funcionamento da integração Azure-SSIS apresenta vários desafios, que tornam inadequados os métodos típicos de licenciamento utilizados para a instalação no local de componentes personalizados. Como resultado, o IR Azure-SSIS requer uma abordagem diferente.

-   Os nódosos do IR Azure-SSIS são voláteis e podem ser atribuídos ou libertados a qualquer momento. Por exemplo, você pode iniciar ou parar nós para gerir o custo, ou escalar para cima e para baixo através de vários tamanhos de nó. Como resultado, a vinculação de uma licença de componente de terceiros a um determinado nó utilizando informações específicas da máquina, como o endereço MAC ou o ID cpu, já não é viável.

-   Também pode escalar o IR Azure-SSIS dentro ou fora, para que o número de nós possa encolher ou expandir a qualquer momento.

## <a name="the-solution"></a>A solução

Como resultado das limitações dos métodos de licenciamento tradicionais descritos na secção anterior, o IR Azure-SSIS fornece uma nova solução. Esta solução utiliza variáveis do ambiente Windows e variáveis do sistema SSIS para a ligação da licença e validação de componentes de terceiros. Os ISVs podem usar estas variáveis para obter informações únicas e persistentes para um IR Azure-SSIS, como o Cluster ID e a Contagem de Nódeclusters. Com esta informação, os ISVs podem então ligar a licença para o seu componente a um IR Azure-SSIS *como cluster*. Esta ligação utiliza um ID que não muda quando os clientes iniciam ou param, escalam para cima ou para baixo, escalam para dentro ou para fora, ou reconfiguram o IR Azure-SSIS de qualquer forma.

O diagrama seguinte mostra a instalação típica, a ativação e a ligação da licença, e fluxos de validação para componentes de terceiros que utilizam estas novas variáveis:

![Instalação de componentes licenciados](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instruções
1. Os ISVs podem oferecer os seus componentes licenciados em várias SKUs ou níveis (por exemplo, nó simples, até 5 nós, até 10 nós, e assim por diante). O ISV fornece a chave de produto correspondente quando os clientes compram um produto. O ISV também pode fornecer um recipiente de blob de armazenamento Azure que contém um script de configuração ISV e ficheiros associados. Os clientes podem copiar estes ficheiros no seu próprio recipiente de armazenamento `IsvSetup.exe -pid xxxx-xxxx-xxxx`e modificá-los com a sua própria Chave de Produto (por exemplo, executando). Os clientes podem então fornecer ou reconfigurar o IR Azure-SSIS com o SAS URI do seu recipiente como parâmetro. Para mais informações, veja [Configuração personalizada do runtime de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Quando o IR Azure-SSIS é aprovisionado ou reconfigurado, a configuração do `SSIS_CLUSTERID` ISV funciona em cada nó para consultar as variáveis ambientais do Windows, e `SSIS_CLUSTERNODECOUNT`. Em seguida, o Azure-SSIS IR submete o seu ID de Cluster e a Chave do Produto para o produto licenciado ao Servidor de Ativação ISV para gerar uma Chave de Ativação.

3. Depois de receber a Chave de Ativação, a configuração isv pode armazenar a chave localmente em cada nó (por exemplo, no Registo).

4. Quando os clientes executam um pacote que utiliza o componente licenciado do ISV num nó do IR Azure-SSIS, o pacote lê a Chave de Ativação armazenada localmente e valida-a contra o ID cluster do nó. O pacote também pode reportar opcionalmente a Contagem do Nó cluster ao servidor de ativação ISV.

    Aqui está um exemplo de código que valida a chave de ativação e reporta a contagem do nó de cluster:

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

Pode encontrar uma lista de parceiros isv que adaptaram os seus componentes e extensões para o IR Azure-SSIS no final deste post de blog - [Enterprise Edition, Custom Setup e 3ª Extensibility de Partidos para O SSIS em ADF.](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360)

## <a name="next-steps"></a>Passos seguintes

-   [Configuração personalizada para o tempo de execução de integração Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Edição Empresarial do Tempo de Integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
