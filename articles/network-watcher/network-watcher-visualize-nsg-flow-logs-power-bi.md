---
title: Visualizar os registos de fluxo azure NSG - Power BI
titleSuffix: Azure Network Watcher
description: Esta página descreve como visualizar os registos de fluxo nsg com Power BI.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 955e13b88037aa42b59707698549b1c980720990
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840609"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualização de registos de fluxo do Grupo de Segurança da Rede com Power BI

Os registos de fluxo do Grupo de Segurança da Rede permitem-lhe visualizar informações sobre o tráfego IP de entrada e fuga em Grupos de Segurança da Rede. Estes registos de fluxo mostram fluxos de saída e de entrada por regra, o NIC que o fluxo se aplica a informações de 5-tuple sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

Pode ser difícil obter informações sobre os dados de registo de fluxo, pesquisando manualmente os ficheiros de registo. Neste artigo, fornecemos uma solução para visualizar os seus registos de fluxo mais recentes e aprender sobre o tráfego na sua rede.

> [!Warning]  
> Os passos seguintes funcionam com os registos de fluxo versão 1. Para mais detalhes, consulte [Introdução à exploração de fluxos de registo para grupos de segurança](network-watcher-nsg-flow-logging-overview.md)da rede . As seguintes instruções não funcionarão com a versão 2 dos ficheiros de registo, sem modificação.

## <a name="scenario"></a>Cenário

No cenário seguinte, ligamos o ambiente de trabalho Power BI à conta de armazenamento que configuramos como o lavatório dos nossos dados de Registo de Fluxo nsg. Depois de ligarmos à nossa conta de armazenamento, o Power BI descarrega e analisa os registos para fornecer uma representação visual do tráfego que é registado por grupos de Segurança de Rede.

Utilizando os visuais fornecidos no modelo que pode examinar:

* Top Talkers
* Dados de fluxo de séries de tempo por direção e decisão de regra
* Fluxos por endereço MAC interface de rede
* Fluxos por NSG e Regra
* Fluxos por Porto de Destino

O modelo fornecido é editável para que possa modificá-lo para adicionar novos dados, visuais ou consultas de edição de acordo com as suas necessidades.

## <a name="setup"></a>Configuração

Antes de começar, deve ter o Fluxo de Registo do Grupo de Segurança da Rede ativado num ou muitos Grupos de Segurança de Rede na sua conta. Para obter instruções sobre a ativação dos registos de fluxo de segurança da rede, consulte o seguinte artigo: [Introdução à exploração de fluxos de registo para grupos](network-watcher-nsg-flow-logging-overview.md)de segurança da rede .

Também deve ter o cliente power BI Desktop instalado na sua máquina, e espaço livre suficiente na sua máquina para descarregar e carregar os dados de registo que existem na sua conta de armazenamento.

![Diagrama de Visio][1]

### <a name="steps"></a>Passos

1. Descarregue e abra o seguinte modelo power BI no modelo de registos de fluxo power bi desktop [powerBI](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Introduza os parâmetros de consulta necessários
   1. **StorageAccountName** – Especifica o nome da conta de armazenamento que contém os registos de fluxo nsg que gostaria de carregar e visualizar.
   1. **NumberOfLogFiles** – Especifica o número de ficheiros de registo que gostaria de descarregar e visualizar no Power BI. Por exemplo, se 50 forem especificados, os 50 ficheiros de registo mais recentes. Se tivermos 2 NSGs ativados e configurados para enviar registos de fluxo nsg para esta conta, então as últimas 25 horas de registos podem ser visualizadas.

      ![power BI principal][2]

1. Introduza a Chave de Acesso para a sua conta de armazenamento. Pode encontrar chaves de acesso válidas navegando para a sua conta de armazenamento no portal Azure e selecionando **As Teclas** de Acesso do menu Definições. Clique em **Ligar** e, em seguida, aplique alterações.

    ![chaves de acesso][3]

    ![chave de acesso 2][4]

4. Os seus registos são descarregados e analisados e agora pode utilizar os visuais pré-criados.

## <a name="understanding-the-visuals"></a>Compreender os visuais

Fornecidos no modelo são um conjunto de visuais que ajudam a dar sentido aos dados do NSG Flow Log. As imagens que se seguem mostram uma amostra do que o painel de instrumentos parece quando povoado com dados. Abaixo examinamos cada visual com mais detalhes 

![powerbi][5]
 
O visual top Talkers mostra os IPs que iniciaram mais ligações durante o período especificado. O tamanho das caixas corresponde ao número relativo de ligações. 

![toptalkers][6]

Os gráficos da série de tempo seguintemostram o número de fluxos durante o período. O gráfico superior é segmentado pela direção do fluxo, e o inferior é segmentado pela decisão tomada (permitir ou negar). Com este visual, pode examinar as suas tendências de tráfego ao longo do tempo, e detetar quaisquer picos anormais ou declínio na segmentação de tráfego ou tráfego.

![flowsoverperiod][7]

Os gráficos seguintes mostram os fluxos por interface da Rede, com a parte superior segmentada pela direção do fluxo e a parte inferior segmentada por decisão tomada. Com esta informação, pode obter informações sobre quais dos seus VMs comunicaram mais em relação a outros, e se o tráfego para um VM específico está sendo permitido ou negado.

![fluxos pernic][8]

O gráfico da roda de donut seguinte mostra uma avaria de Fluxos por Porta de Destino. Com esta informação, pode ver as portas de destino mais utilizadas no período especificado.

![donut][9]

O gráfico de barras seguinte mostra o Fluxo por NSG e Regra. Com esta informação, pode ver os NSGs responsáveis pela maior parte do tráfego, e a avaria do tráfego num NSG por regra.

![barchart][10]
 
Os gráficos informativos seguintes exibem informações sobre os NSGs presentes nos registos, o número de Fluxos capturados durante o período e a data do primeiro registo capturado. Esta informação dá-lhe uma ideia do que os NSGs estão a ser registados e da gama de fluxos de data.

![infochart1][11]

![infochart2][12]

Este modelo inclui os seguintes cortadores para permitir que você veja apenas os dados que mais lhe interessam. Pode filtrar os seus grupos de recursos, NSGs e regras. Também pode filtrar informações de 5-tuple, decisão e o momento em que o registo foi escrito.

![cortadores][13]

## <a name="conclusion"></a>Conclusão

Mostrámos neste cenário que, utilizando os registos de Fluxo do Grupo de Segurança da Rede fornecidos pelo Network Watcher e power BI, somos capazes de visualizar e compreender o tráfego. Utilizando o modelo fornecido, o Power BI descarrega os registos diretamente do armazenamento e processa-os localmente. O tempo despendido para carregar o modelo varia consoante o número de ficheiros solicitados e o tamanho total dos ficheiros descarregados.

Sinta-se à vontade para personalizar este modelo para as suas necessidades. Existem muitas formas de usar o Power BI com registos de fluxo do grupo de segurança da rede. 

## <a name="notes"></a>Notas

* Os registos por padrão são armazenados em `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Se existirem outros dados noutro diretório, as consultas para puxar e processar os dados devem ser modificadas.

* O modelo fornecido não é recomendado para utilização com mais de 1 GB de registos.

* Se tiver uma grande quantidade de registos, recomendamos que investigue uma solução utilizando outra loja de dados como data Lake ou servidor SQL.

## <a name="next-steps"></a>Próximos Passos

Saiba como visualizar os seus registos de fluxo NSG com a Stack Elástica visitando registos de fluxo NSG do Observador de [Rede Azure utilizando ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
