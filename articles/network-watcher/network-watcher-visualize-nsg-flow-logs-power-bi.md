---
title: Visualizando logs de fluxo NSG do Azure-Power BI
titleSuffix: Azure Network Watcher
description: Esta página descreve como Visualizar logs de fluxo NSG com Power BI.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 05378799dd77a17b69adbcf492af2e1cb1030375
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277720"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualizando logs de fluxo do grupo de segurança de rede com Power BI

Os logs de fluxo do grupo de segurança de rede permitem que você exiba informações sobre o tráfego IP de entrada e saída em grupos de segurança de rede. Esses logs de fluxo mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, informações de 5 tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

Pode ser difícil obter informações sobre os dados de log de fluxo pesquisando manualmente os arquivos de log. Neste artigo, fornecemos uma solução para visualizar seus logs de fluxo mais recentes e saber mais sobre o tráfego em sua rede.

> [!Warning]  
> As etapas a seguir funcionam com os logs de fluxo versão 1. Para obter detalhes, consulte [introdução ao log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções a seguir não funcionarão com a versão 2 dos arquivos de log, sem modificação.

## <a name="scenario"></a>Cenário

No cenário a seguir, conectamos Power BI área de trabalho à conta de armazenamento que configuramos como o coletor para nossos dados de log de fluxo NSG. Depois de nos conectarmos à nossa conta de armazenamento, Power BI baixar e analisar os logs para fornecer uma representação visual do tráfego que é registrado por grupos de segurança de rede.

Usando os visuais fornecidos no modelo, você pode examinar:

* Principais palestrantes
* Dados de fluxo de série temporal por decisão de direção e regra
* Fluxos por endereço MAC de interface de rede
* Fluxos por NSG e regra
* Fluxos por porta de destino

O modelo fornecido é editável para que você possa modificá-lo para adicionar novos dados, visuais ou editar consultas para atender às suas necessidades.

## <a name="setup"></a>Configurar

Antes de começar, você deve ter o log de fluxo do grupo de segurança de rede habilitado em um ou vários grupos de segurança de rede em sua conta. Para obter instruções sobre como habilitar os logs de fluxo de segurança de rede, consulte o seguinte artigo: [introdução ao log de fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

Você também deve ter o cliente do Power BI Desktop instalado em seu computador e espaço livre suficiente em seu computador para baixar e carregar os dados de log que existem na sua conta de armazenamento.

![Diagrama do Visio][1]

### <a name="steps"></a>Passos

1. Baixe e abra o modelo de Power BI a seguir no [modelo de logs de fluxo do PowerBI do Inspetor de rede](https://aka.ms/networkwatcherpowerbiflowlogstemplate) do aplicativo Power bi desktop
1. Insira os parâmetros de consulta necessários
   1. **StorageAccountName** – especifica o nome da conta de armazenamento que contém os logs de fluxo do NSG que você deseja carregar e Visualizar.
   1. **NumberOfLogFiles** – especifica o número de arquivos de log que você deseja baixar e visualizar no Power bi. Por exemplo, se 50 for especificado, os arquivos de log do 50 mais recentes. Se tivermos 2 NSGs habilitados e configurados para enviar logs de fluxo de NSG para essa conta, as últimas 25 horas de logs poderão ser exibidas.

      ![principal do Power BI][2]

1. Insira a chave de acesso para sua conta de armazenamento. Você pode encontrar chaves de acesso válidas navegando até sua conta de armazenamento no portal do Azure e selecionando **chaves de acesso** no menu configurações. Clique em **conectar** e aplique as alterações.

    ![chaves de acesso][3]

    ![chave de acesso 2][4]

4. Os logs são baixados e analisados, e agora você pode utilizar os visuais criados previamente.

## <a name="understanding-the-visuals"></a>Noções básicas sobre os visuais

Fornecidas no modelo estão um conjunto de visuais que ajudam a entender os dados de log do fluxo de NSG. As imagens a seguir mostram um exemplo de como o painel é exibido quando preenchido com dados. Abaixo, examinamos cada visual com mais detalhes 

![powerbi][5]
 
O Visual dos principais palestrantes mostra os IPs que iniciaram a maioria das conexões durante o período especificado. O tamanho das caixas corresponde ao número relativo de conexões. 

![toptalkers][6]

Os gráficos de série temporal a seguir mostram o número de fluxos ao longo do período. O grafo superior é segmentado pela direção do fluxo e o menor é segmentado pela decisão tomada (permitir ou negar). Com esse visual, você pode examinar suas tendências de tráfego ao longo do tempo e identificar picos anormais ou recusar o tráfego ou segmentação de tráfego.

![flowsoverperiod][7]

Os gráficos a seguir mostram os fluxos por interface de rede, com o segmento superior por direção de fluxo e o menor segmentado por decisão tomada. Com essas informações, você pode obter ideias sobre quais das suas VMs se comunicaram mais em relação a outras pessoas e se o tráfego para uma VM específica está sendo permitido ou negado.

![flowspernic][8]

O gráfico de roda de rosca a seguir mostra uma divisão dos fluxos por porta de destino. Com essas informações, você pode exibir as portas de destino usadas com mais frequência no período especificado.

![rosca][9]

O gráfico de barras a seguir mostra o fluxo por NSG e regra. Com essas informações, você pode ver o NSGs responsável pela maior parte do tráfego e a divisão de tráfego em um NSG por regra.

![barchart][10]
 
Os gráficos informativos a seguir exibem informações sobre o NSGs presente nos logs, o número de fluxos capturados no período e a data do log mais antigo capturado. Essas informações dão uma ideia de quais NSGs estão sendo registrados em log e o intervalo de datas dos fluxos.

![infochart1][11]

![infochart2][12]

Este modelo inclui as segmentações de dados a seguir para permitir que você exiba somente os dados dos quais está mais interessado. Você pode filtrar seus grupos de recursos, NSGs e regras. Você também pode filtrar as informações de 5 tuplas, a decisão e a hora em que o log foi gravado.

![Segmentações][13]

## <a name="conclusion"></a>Conclusão

Mostramos neste cenário que, usando os logs de fluxo do grupo de segurança de rede fornecidos pelo observador de rede e Power BI, podemos Visualizar e entender o tráfego. Usando o modelo fornecido, Power BI baixa os logs diretamente do armazenamento e os processa localmente. O tempo necessário para carregar o modelo varia dependendo do número de arquivos solicitados e do tamanho total dos arquivos baixados.

Sinta-se à vontade para personalizar esse modelo para suas necessidades. Há várias maneiras de usar Power BI com os logs de fluxo do grupo de segurança de rede. 

## <a name="notes"></a>Notas

* Os logs por padrão são armazenados em `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Se outros dados existirem em outro diretório, as consultas para efetuar pull e processar os dados deverão ser modificadas.

* O modelo fornecido não é recomendado para uso com mais de 1 GB de logs.

* Se você tiver uma grande quantidade de logs, recomendamos que investigue uma solução usando outro armazenamento de dados como o Data Lake ou o SQL Server.

## <a name="next-steps"></a>Passos Seguintes

Saiba como visualizar seus logs de fluxo do NSG com a pilha elástica visitando [Visualizar logs de fluxo do NSG do observador de rede do Azure usando ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
