---
title: Ver latências relativas a regiões do Azure a partir de localizações específicas | Documentos da Microsoft
description: Saiba como ver latências relativas entre fornecedores de Internet para regiões do Azure a partir de localizações específicas.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4df134c6644a9480460635cbe3565f030d4bf779
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440952"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Vista latência relativa a regiões do Azure a partir de localizações específicas

Neste tutorial, saiba como utilizar o Azure [observador de rede](network-watcher-monitoring-overview.md) para ajudar a decidir em que região do Azure para implementar a sua aplicação ou serviço de serviço, com base no seu usuário demográficos. Além disso, pode utilizá-lo para ajudar a avaliar as ligações de fornecedores de serviços para o Azure.  
        
## <a name="create-a-network-watcher"></a>Criar um observador de rede

Se já tiver um observador de rede no Azure, pelo menos, um [região](https://azure.microsoft.com/regions), pode ignorar as tarefas nesta secção. Crie um grupo de recursos para o observador de rede. Neste exemplo, o grupo de recursos é criado na região E.U.A. leste, mas pode criar o grupo de recursos em qualquer região do Azure.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

Crie um observador de rede. Tem de ter um observador de rede criado em, pelo menos, uma região do Azure. Neste exemplo, um observador de rede é criado na região do Azure do Leste-nos.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Comparar latências de rede relativo numa única região do Azure a partir de uma localização específica

Avaliar os fornecedores de serviços ou resolver problemas de um usuário a relatar um problema, como "o site foi lento," de um local específico na região do azure em que um serviço é implementado. Por exemplo, o comando seguinte devolve as média relativas Internet service provider latências entre o estado de Washington, nos Estados Unidos e a região do Azure de 2 do Oeste-nos entre 15 de 13 de Dezembro de 2017:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> A região que especificar no comando anterior não precisa de ser o mesmo que a região que especificou quando obteve o observador de rede. O comando anterior simplesmente exige a especificação de um observador de rede existente. O observador de rede pode estar em qualquer região. Se especificar valores para `-Country` e `-State`, tem de ser válidos. Os valores diferenciam maiúsculas de minúsculas. Os dados estão disponíveis para um número limitado de países, Estados e cidades. Executar os comandos [ver fornecedores, cidades, Estados e os países disponíveis](#view-available) para ver uma lista de países disponíveis, cidades e Estados para utilizar com o comando anterior. 

> [!WARNING]
> Tem de especificar uma data nos últimos 30 dias para `-StartTime` e `-EndTime`. Especificar uma data anterior irá resultar em nenhum dado a ser devolvido.

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

No resultado retornado, o valor para **pontuação** é a latência relativa entre regiões e fornecedores. Uma pontuação igual a 1 é a pior latência (mais alta), ao passo que 100 é a menor latência. As latências relativas são transformadas em médias para o dia. No exemplo anterior, enquanto está claro que as latências eram os mesmos dois dias e que existe uma pequena diferença entre a latência de dois fornecedores, também está claro que as latências para os dois provedores são baixas na escala de 1 a 100. Enquanto isso é esperado uma vez que o estado de Washington, nos Estados Unidos é fisicamente próximo a região do Azure de 2 do Oeste-nos, às vezes, os resultados não são conforme o esperado. Quanto maior for o intervalo de datas que especificar, quanto mais pode latência média ao longo do tempo.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Comparar as latências de rede relativa entre regiões do Azure de um local específico

Se, em vez de especificar as latências relativas entre uma localização específica ou uma região do Azure específica `-Location`, quiser determinar as latências relativas a todas as regiões do Azure de um local físico específico, poderá fazer isso. Por exemplo, o comando seguinte ajuda-o a avaliar em que região do azure para implementar um serviço no caso os utilizadores primários sejam utilizadores Comcast localizados no Estado norte-americano de Washington:

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
Ao contrário de quando especificar uma localização única, se não especificar uma localização ou especificar várias localizações, como "Oeste 2", o "EUA Oeste", tem de especificar um provedor de serviços de Internet ao executar o comando. 

## <a name="view-available"></a>Ver os fornecedores, cidades, Estados e os países disponíveis

Os dados estão disponíveis para fornecedores de serviços de Internet específicos, países, Estados e cidades. Para ver uma lista de todos os fornecedores de serviços de Internet disponíveis, os países, Estados e cidades, que pode ver os dados para, introduza o seguinte comando:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Dados só estão disponíveis para os países, Estados e cidades devolvidas pelo comando anterior. O comando anterior exige que especifique um observador de rede existente. O exemplo especificado a *NetworkWatcher_eastus* observador de rede num grupo de recursos com o nome *NetworkWatcherRG*, mas pode especificar qualquer observador de rede existente. Se não tiver um observador de rede existente, crie um ao concluir as tarefas no [criar um observador de rede](#create-a-network-watcher). 

Depois de executar o comando anterior, pode filtrar os resultados devolvidos ao especificar valores válidos para **país**, **estado**, e **Cidade**, se desejar.  Por exemplo, para ver a lista de fornecedores de serviços de Internet disponíveis em Seattle, Washington, nos Estados Unidos, introduza o seguinte comando:

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> O valor especificado para **país** tem de estar em maiúsculas e minúsculas. Os valores especificados para **estado** e **Cidade** tem de estar em minúsculas. Os valores devem ser listados no resultado devolvido depois de executar o comando com nenhum valor para **país**, **estado**, e **Cidade**. Se especificar o caso incorreto ou especificar um valor para **país**, **estado**, ou **Cidade** que não se encontra no resultado devolvido depois de executar o comando com sem valores para estas propriedades, o resultado retornado está vazio.
