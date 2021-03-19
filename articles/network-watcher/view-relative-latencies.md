---
title: Ver latências relativas às regiões de Azure a partir de locais específicos
description: Saiba como ver latências relativas entre fornecedores de Internet e regiões de Azure a partir de locais específicos.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 938315dac80ae51984851083753a9cb8f77662b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737908"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Ver latência relativa para as regiões do Azure a partir de localizações específicas

> [!WARNING]
> Esta funcionalidade encontra-se atualmente em pré-visualização e ainda está a ser testada para estabilidade.

Neste tutorial, aprenda a usar o serviço Azure [Network Watcher](network-watcher-monitoring-overview.md) para ajudá-lo a decidir em que região de Azure implementar a sua aplicação ou serviço, com base na demografia do seu utilizador. Além disso, pode usá-lo para ajudar a avaliar as ligações dos prestadores de serviços ao Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Criar um observador de rede

Se já tiver um observador de rede em pelo menos uma [região](https://azure.microsoft.com/regions)de Azure, pode saltar as tarefas nesta secção. Criar um grupo de recursos para o observador de rede. Neste exemplo, o grupo de recursos é criado na região leste dos EUA, mas você pode criar o grupo de recursos em qualquer região de Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Criar um observador de rede. Deve ter um observador de rede criado em pelo menos uma região de Azure. Neste exemplo, um observador de rede é criado na região de East US Azure.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Compare latências relativas da rede com uma única região de Azure a partir de um local específico

Avaliar os prestadores de serviços, ou resolver problemas de um utilizador que relata um problema como "o site foi lento", de um local específico para a região azul onde um serviço é implantado. Por exemplo, o seguinte comando devolve as latências médias relativas do fornecedor de serviços de Internet entre o estado de Washington nos Estados Unidos e a região oeste dos EUA 2 Azure entre 13 e 15 de dezembro de 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> A região que especifica no comando anterior não precisa de ser a mesma que a região especificada quando recuperou o observador de rede. O comando anterior simplesmente requer que especifique um observador de rede existente. O observador de rede pode estar em qualquer região. Se especificar valores para `-Country` `-State` e, devem ser válidos. Os valores são sensíveis a casos. Os dados estão disponíveis para um número limitado de países/regiões, estados e cidades. Executar os comandos em [Ver países/regiões, estados, cidades e fornecedores disponíveis](#view-available) para visualizar uma lista de países/regiões, cidades e estados disponíveis para usar com o comando anterior. 

> [!WARNING]
> Deve especificar uma data nos últimos 30 dias para `-StartTime` e `-EndTime` . Especificar uma data anterior não resultará na devolvição de dados.

Segue-se a saída do comando anterior:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

Na produção devolvida, o valor para **a Pontuação** é a relativa latência entre regiões e fornecedores. Uma pontuação de 1 é a pior (maior) latência, enquanto 100 é a latência mais baixa. As latências relativas são médias para o dia. No exemplo anterior, embora seja claro que as latências foram as mesmas nos dois dias e que há uma pequena diferença entre a latência dos dois fornecedores, também é claro que as latências para ambos os fornecedores são baixas na escala 1-100. Embora isso seja esperado, uma vez que o estado de Washington nos Estados Unidos está fisicamente próximo da região ocidental dos EUA 2 Azure, por vezes os resultados não são como esperado. Quanto maior for o intervalo de datas, mais se pode ter uma latência média ao longo do tempo.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Compare latências relativas da rede em regiões de Azure a partir de um local específico

Se, em vez de especificar as latências relativas entre uma localização específica e uma região específica de Azure, `-Location` quiser determinar as latências relativas a todas as regiões de Azure a partir de um local físico específico, também pode fazê-lo. Por exemplo, o seguinte comando ajuda-o a avaliar em que região azul implementar um serviço se os seus utilizadores primários forem utilizadores da Comcast localizados no estado de Washington:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Ao contrário de quando especificar uma única localização, se não especificar uma localização, ou especificar vários locais, tais como "West US2", "West US", deve especificar um fornecedor de serviços de Internet ao executar o comando. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Ver países/regiões, estados, cidades e fornecedores disponíveis

Os dados estão disponíveis para fornecedores específicos de serviços de Internet, países/regiões, estados e cidades. Para ver uma lista de todos os fornecedores de serviços de Internet disponíveis, países/regiões, estados e cidades, para os qual pode ver dados, insira o seguinte comando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Os dados só estão disponíveis para os países/regiões, estados e cidades devolvidos pelo comando anterior. O comando anterior requer que especifique um observador de rede existente. O exemplo especificou o *NetworkWatcher_eastus* observador de rede num grupo de recursos chamado *NetworkWatcherRG*, mas pode especificar qualquer observador de rede existente. Se não tiver um observador de rede existente, crie um, completando as tarefas no [Criar um observador de rede](#create-a-network-watcher). 

Depois de executar o comando anterior, pode filtrar a saída devolvida especificando valores válidos para **País,** **Estado** e **Cidade,** se desejar.  Por exemplo, para ver a lista de fornecedores de serviços de Internet disponíveis em Seattle, Washington, nos Estados Unidos, insira o seguinte comando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> O valor especificado para **o País** deve ser superior e minúsculo. Os valores especificados para **Estado** e **Cidade** devem ser minúsculos. Os valores devem ser listados na saída devolvida após a execução do comando sem valores para **País,** **Estado** e **Cidade.** Se especificar o caso incorreto, ou especificar um valor para **País,** **Estado** ou **Cidade** que não esteja na saída devolvida após executar o comando sem valores para estas propriedades, a saída devolvida está vazia.
