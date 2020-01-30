---
title: Exibir latências relativas a regiões do Azure de locais específicos | Microsoft Docs
description: Saiba como exibir latências relativas entre provedores de Internet para regiões do Azure de locais específicos.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: c3a85de3a201a89d6d9500e4f4b2df9e667e3537
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840541"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Exibir a latência relativa para regiões do Azure de locais específicos

> [!WARNING]
> Este recurso está atualmente em visualização e ainda está sendo testado quanto à estabilidade.

Neste tutorial, saiba como usar o serviço [observador de rede](network-watcher-monitoring-overview.md) do Azure para ajudá-lo a decidir em qual região do Azure implantar seu aplicativo ou serviço, com base no perfil demográfico do usuário. Além disso, você pode usá-lo para ajudar a avaliar as conexões dos provedores de serviços com o Azure.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Criar um observador de rede

Se você já tiver um observador de rede em pelo menos uma [região](https://azure.microsoft.com/regions)do Azure, poderá ignorar as tarefas nesta seção. Crie um grupo de recursos para o observador de rede. Neste exemplo, o grupo de recursos é criado na região leste dos EUA, mas você pode criar o grupo de recursos em qualquer região do Azure.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Criar um observador de rede. Você deve ter um observador de rede criado em pelo menos uma região do Azure. Neste exemplo, um observador de rede é criado na região do Azure leste dos EUA.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Comparar as latências de rede relativas a uma única região do Azure de um local específico

Avalie os provedores de serviços ou solucione um problema de um usuário, como "o site foi lento", de um local específico para a região do Azure em que um serviço está implantado. Por exemplo, o comando a seguir retorna as latências de provedor de serviços de Internet relativas à média entre o estado de Washington na Estados Unidos e a região oeste dos EUA 2 do Azure entre 13-15 de dezembro de 2017:

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
> A região que você especificar no comando anterior não precisará ser a mesma que a região especificada quando você recuperou o observador de rede. O comando anterior simplesmente requer que você especifique um observador de rede existente. O observador de rede pode estar em qualquer região. Se você especificar valores para `-Country` e `-State`, eles deverão ser válidos. Os valores diferenciam maiúsculas de minúsculas. Os dados estão disponíveis para um número limitado de países/regiões, Estados e cidades. Execute os comandos em [Exibir países/regiões, Estados, cidades e provedores disponíveis](#view-available) para exibir uma lista de países/regiões, cidades e Estados disponíveis para uso com o comando anterior. 

> [!WARNING]
> Você deve especificar uma data nos últimos 30 dias para `-StartTime` e `-EndTime`. A especificação de uma data anterior resultará em nenhum dado retornado.

A saída do comando anterior segue:

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

Na saída retornada, o valor de **Score** é a latência relativa entre regiões e provedores. Uma pontuação de 1 é a pior (maior) latência, enquanto 100 é a menor latência. As latências relativas são médias para o dia. No exemplo anterior, embora esteja claro que as latências foram as mesmas em ambos os dias e que há uma pequena diferença entre a latência dos dois provedores, também é claro que as latências para ambos os provedores estão baixas na escala 1-100. Embora isso seja esperado, como o estado de Washington na Estados Unidos está fisicamente perto da região oeste dos EUA 2 do Azure, às vezes os resultados não são os esperados. Quanto maior o intervalo de datas especificado, mais você poderá obter a latência média ao longo do tempo.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Comparar latências de rede relativas em regiões do Azure de um local específico

Se, em vez de especificar as latências relativas entre um local específico e uma região específica do Azure usando `-Location`, você quisesse determinar as latências relativas a todas as regiões do Azure de um local físico específico, também poderá fazer isso. Por exemplo, o comando a seguir o ajudará a avaliar em qual região do Azure implantar um serviço se os usuários primários estiverem Comcast usuários localizados no estado de Washington:

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
> Ao contrário de quando você especifica um único local, se não especificar um local ou especificar vários locais, como "West dos EUA 2", "oeste dos EUA", você deve especificar um provedor de serviços de Internet ao executar o comando. 

## <a name="view-available"></a>Exibir países/regiões, Estados, cidades e provedores disponíveis

Os dados estão disponíveis para provedores de serviços de Internet específicos, países/regiões, Estados e cidades. Para exibir uma lista de todos os provedores de serviços de Internet, países/regiões, Estados e cidades disponíveis, para os quais você pode exibir dados, insira o seguinte comando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Os dados só estão disponíveis para os países/regiões, Estados e cidades retornados pelo comando anterior. O comando anterior exige que você especifique um observador de rede existente. O exemplo especificao o observador de rede *NetworkWatcher_eastus* num grupo de recursos chamado *NetworkWatcherRG,* mas pode especificar qualquer observador de rede existente. Se você não tiver um observador de rede existente, crie um, concluindo as tarefas em [criar um observador de rede](#create-a-network-watcher). 

Depois de executar o comando anterior, você pode filtrar a saída retornada especificando valores válidos para **país**, **estado**e **cidade**, se desejado.  Por exemplo, para exibir a lista de provedores de serviços de Internet disponíveis em Seattle, Washington, na Estados Unidos, digite o seguinte comando:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> O valor especificado para **Country** deve estar em letras maiúsculas e minúsculas. Os valores especificados para **estado** e **cidade** devem estar em minúsculas. Os valores devem ser listados na saída retornada após a execução do comando sem valores para **país**, **estado**e **cidade**. Se você especificar o caso incorreto ou especificar um valor para **país**, **estado**ou **cidade** que não esteja na saída retornada após a execução do comando sem valores para essas propriedades, a saída retornada estará vazia.
