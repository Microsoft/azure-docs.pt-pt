---
title: Visualização dos registos de fluxo Azure NSG - Power BI
titleSuffix: Azure Network Watcher
description: Saiba como utilizar o Power BI para visualizar os registos de fluxo do Grupo de Segurança de Rede para permitir visualizar informações sobre o tráfego IP no Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 50f08ffc304962cc66f6eed07adcb5b7e4aa6692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011157"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualizar registos de fluxo do Grupo de Segurança da Rede com Power BI

Os registos de fluxo do Grupo de Segurança de Rede permitem-lhe visualizar informações sobre o tráfego IP de entrada e saída em Grupos de Segurança de Rede. Estes registos de fluxo mostram fluxos de saída e de entrada numa base de regra, o NIC o fluxo aplica-se a, 5-tuple informações sobre o fluxo (Source/Destination IP, Source/Destination Port, Protocol), e se o tráfego foi permitido ou negado.

Pode ser difícil obter informações sobre dados de registo de fluxos, pesquisando manualmente os ficheiros de registo. Neste artigo, fornecemos uma solução para visualizar os seus registos de fluxo mais recentes e aprender sobre o tráfego na sua rede.

> [!Warning]  
> Os passos seguintes funcionam com os registos de fluxo versão 1. Para mais informações, consulte [Introdução à registo de fluxo para grupos de segurança da rede](network-watcher-nsg-flow-logging-overview.md). As seguintes instruções não funcionarão com a versão 2 dos ficheiros de registo, sem modificação.

## <a name="scenario"></a>Scenario

No cenário seguinte, conectamos o desktop Power BI à conta de armazenamento que configuramos como o lavatório para os nossos dados de Registo de Fluxo NSG. Depois de ligarmos à nossa conta de armazenamento, o Power BI descarrega e analisa os registos para fornecer uma representação visual do tráfego que é registado pelos grupos de Segurança da Rede.

Utilizando os visuais fornecidos no modelo, pode examinar:

* Top Talkers
* Datas de fluxo de séries de tempo por direção e decisão de regra
* Fluxos por endereço MAC interface de rede
* Fluxos por NSG e Regra
* Fluxos por Porto de Destino

O modelo fornecido é editável para que possa modificá-lo para adicionar novos dados, visuais ou editar consultas de acordo com as suas necessidades.

## <a name="setup"></a>Configuração

Antes de começar, tem de ter o Fluxo de Fluxo de Rede ativado em um ou em muitos Grupos de Segurança da Rede na sua conta. Para obter instruções sobre a ativação dos registos de fluxo de segurança da rede, consulte o seguinte artigo: [Introdução à registo de fluxo para grupos de segurança da rede](network-watcher-nsg-flow-logging-overview.md).

Também tem de ter o cliente Power BI Desktop instalado na sua máquina e espaço livre suficiente na sua máquina para descarregar e carregar os dados de registo que existem na sua conta de armazenamento.

![Diagrama visio][1]

### <a name="steps"></a>Passos

1. Descarregue e abra o modelo de POWER BI seguinte no modelo de [registos de fluxo powerbi do powerbi](https://aka.ms/networkwatcherpowerbiflowlogstemplate) de aplicação de ambiente de trabalho powerbi
1. Introduza os parâmetros de consulta necessários
   1. **ArmazenamentoCocountName** – Especifica o nome da conta de armazenamento que contém os registos de fluxo NSG que gostaria de carregar e visualizar.
   1. **NúmeroOfLogFiles** – Especifica o número de ficheiros de registo que gostaria de descarregar e visualizar no Power BI. Por exemplo, se 50 forem especificados, os 50 ficheiros de registo mais recentes. Se tivermos 2 NSGs habilitados e configurados para enviar registos de fluxo NSG para esta conta, então as últimas 25 horas de registos podem ser visualizadas.

      ![poder BI principal][2]

1. Introduza a Chave de Acesso para a sua conta de armazenamento. Pode encontrar teclas de acesso válidas navegando na sua conta de armazenamento no portal Azure e selecionando **As Chaves** de Acesso a partir do menu Definições. Clique **em Ligar** e aplicar alterações.

    ![chaves de acesso][3]

    ![chave de acesso 2][4]

4. Os seus registos são descarregados e analisados e agora pode utilizar os visuais pré-criados.

## <a name="understanding-the-visuals"></a>Compreender os visuais

Fornecidos no modelo são um conjunto de visuais que ajudam a dar sentido aos dados do NSG Flow Log. As imagens que se seguem mostram uma amostra de como é o painel quando preenchido com dados. Abaixo examinamos cada visual com maior detalhe 

![powerbi][5]
 
O visual top talkers mostra os IPs que iniciaram mais conexões durante o período especificado. O tamanho das caixas corresponde ao número relativo de ligações. 

![toptalkers][6]

Os gráficos das séries de tempo seguintes mostram o número de fluxos durante o período. O gráfico superior é segmentado pela direção do fluxo, e o inferior é segmentado pela decisão tomada (permitir ou negar). Com este visual, você pode examinar as suas tendências de tráfego ao longo do tempo, e detetar quaisquer picos anormais ou declínio na segmentação de tráfego ou tráfego.

![fluxos sobreoperídio][7]

Os gráficos seguintes mostram os fluxos por interface de Rede, com a parte superior segmentada pela direção de fluxo e a menor segmentada por decisão tomada. Com esta informação, pode obter informações sobre qual dos seus VMs comunicou mais relativamente a outros, e se o tráfego para um VM específico está a ser permitido ou negado.

![flowspernic][8]

O gráfico das rodas de donut que se segue mostra uma desagregação de Flows by Destination Port. Com estas informações, pode ver as portas de destino mais utilizadas no período especificado.

![rosca][9]

O gráfico de barras que se segue mostra o Fluxo por NSG e Regra. Com esta informação, você pode ver os NSGs responsáveis pela maior parte do tráfego, e a quebra de tráfego em um NSG por regra.

![barchart][10]
 
Os gráficos informativos a seguir exibem informações sobre os NSGs presentes nos registos, o número de Fluxos capturados durante o período e a data do registo mais antigo capturado. Esta informação dá-lhe uma ideia do que os NSGs estão a ser registados e a gama de datas de fluxos.

![infochart1][11]

![infochart2][12]

Este modelo inclui os seguintes cortadores para permitir que você veja apenas os dados que mais lhe interessam. Pode filtrar os seus grupos de recursos, NSGs e regras. Também pode filtrar informações de 5 tuple, decisão e a hora da escrita do registo.

![cortadores][13]

## <a name="conclusion"></a>Conclusão

Neste cenário mostrámos que ao utilizar os registos de Fluxo do Grupo de Segurança de Rede fornecidos pelo Network Watcher e pelo Power BI, somos capazes de visualizar e compreender o tráfego. Utilizando o modelo fornecido, o Power BI descarrega os registos diretamente do armazenamento e processa-os localmente. O tempo de carregamento do modelo varia consoante o número de ficheiros solicitados e o tamanho total dos ficheiros descarregados.

Sinta-se livre para personalizar este modelo para as suas necessidades. Existem muitas formas de utilizar o Power BI com registos de fluxo do grupo de segurança da rede. 

## <a name="notes"></a>Notas

* Os registos por predefinição são armazenados em `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Se existirem outros dados noutro diretório, as consultas para retirar e processar os dados devem ser modificadas.

* O modelo fornecido não é recomendado para utilização com mais de 1 GB de troncos.

* Se tiver uma grande quantidade de registos, recomendamos que investigue uma solução utilizando outra loja de dados como o Data Lake ou o servidor SQL.

## <a name="next-steps"></a>Passos Seguintes

Saiba como visualizar os seus registos de fluxo NSG com a Pilha Elástica visitando [registos de fluxo NSG do Observador de Rede Azure usando ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
