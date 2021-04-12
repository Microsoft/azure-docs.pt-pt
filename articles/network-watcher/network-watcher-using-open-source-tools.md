---
title: Visualizar padrões de tráfego de rede com ferramentas de código aberto
titleSuffix: Azure Network Watcher
description: Esta página descreve como usar a captura de pacotes do Network Watcher com capanalysis para visualizar padrões de tráfego de e para os seus VMs.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0503f6910186b42bf381c662c7942e37c28bfdf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709517"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualize padrões de tráfego de rede de e para os seus VMs usando ferramentas de código aberto

As capturas de pacotes contêm dados de rede que permitem realizar a análise forense da rede e a inspeção de pacotes profundos. Existem muitas ferramentas de código abertas que pode usar para analisar capturas de pacotes para obter insights sobre a sua rede. Uma dessas ferramentas é a CapAnalysis, uma ferramenta de visualização de captura de pacotes de código aberto. Visualizar dados de captura de pacotes é uma forma valiosa de obter rapidamente insights sobre padrões e anomalias dentro da sua rede. As visualizações também fornecem um meio de partilhar tais insights de uma forma facilmente consumível.

O Observador de Rede do Azure fornece-lhe a capacidade de capturar dados, permitindo-lhe realizar capturas de pacotes na sua rede. Este artigo, proporciona uma caminhada de como visualizar e obter insights de capturas de pacotes usando CapAnalysis com Network Watcher.

## <a name="scenario"></a>Scenario

Você tem uma aplicação web simples implantada em um VM em Azure quer usar ferramentas de código aberto para visualizar o seu tráfego de rede para identificar rapidamente padrões de fluxo e quaisquer anomalias possíveis. Com o Network Watcher, pode obter uma captura de pacotes do ambiente da sua rede e armazená-lo diretamente na sua conta de armazenamento. A CapAnalysis pode então ingerir a captura do pacote diretamente a partir da bolha de armazenamento e visualizar o seu conteúdo.

![cenário][1]

## <a name="steps"></a>Passos

### <a name="install-capanalysis"></a>Instalar CapAnalysis

Para instalar a CapAnalysis numa máquina virtual, pode consultar as instruções oficiais aqui https://www.capanalysis.net/ca/how-to-install-capanalysis .
Para obter acesso à CapAnalysis remotamente, precisa de abrir a porta 9877 no seu VM adicionando uma nova regra de segurança de entrada. Para saber mais sobre a criação de regras em Grupos de Segurança de Rede, consulte [as regras de Criar num NSG existente.](../virtual-network/manage-network-security-group.md#create-a-security-rule) Uma vez que a regra tenha sido adicionada com sucesso, você deve ser capaz de aceder à CapAnalysis a partir de `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Use o Observador de Rede Azure para iniciar uma sessão de captura de pacotes

O Network Watcher permite-lhe capturar pacotes para rastrear o tráfego dentro e fora de uma máquina virtual. Pode consultar as instruções da Manage packet captures com o Network Watcher para iniciar uma sessão de captura de [pacotes.](network-watcher-packet-capture-manage-portal.md) Uma captura de pacote pode ser armazenada numa bolha de armazenamento para ser acedida pela CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Faça o upload de uma captura de pacote para a CapAnalysis
Pode carregar diretamente uma captura de pacotes captada pelo observador de rede utilizando o separador "Importar de URL" e fornecer um link para a bolha de armazenamento onde a captura do pacote está armazenada.

Ao fornecer um link para a CapAnalysis, certifique-se de anexar um token SAS ao URL blob de armazenamento.  Para isso, navegue para assinatura de acesso partilhado a partir da conta de armazenamento, designe as permissões permitidas e pressione o botão Gerar SAS para criar um token. Em seguida, pode anexar o token SAS ao URL de armazenamento de armazenamento de captura de pacote.

O URL resultante será parecido com o seguinte URL: `http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>Analisar capturas de pacotes

A CapAnalysis oferece várias opções para visualizar a sua captura de pacotes, cada uma fornecendo análises de uma perspetiva diferente. Com estes resumos visuais, você pode entender as suas tendências de tráfego de rede e rapidamente detetar qualquer atividade incomum. Algumas destas funcionalidades são mostradas na seguinte lista:

1. Tabelas de fluxo

    Esta tabela dá-lhe a lista de fluxos nos dados do pacote, o carimbo de tempo associado aos fluxos e os vários protocolos associados ao fluxo, bem como a origem e destino IP.

    ![página de fluxo de capanalysis][5]

1. Visão geral do protocolo

    Este painel permite-lhe ver rapidamente a distribuição do tráfego de rede ao longo dos vários protocolos e geografias.

    ![Visão geral do protocolo de capanalysis][6]

1. Estatísticas

    Este painel permite-lhe visualizar estatísticas de tráfego de rede – bytes enviados e recebidos de IPs de origem e destino, fluxos para cada um dos IPs de origem e destino, protocolo utilizado para vários fluxos, e a duração dos fluxos.

    ![estatísticas de capanalysis][7]

1. Geomap

    Este painel proporciona-lhe uma visão do mapa do tráfego da sua rede, com cores que se escalam para o volume de tráfego de cada país/região. Pode selecionar países/regiões destacados para visualizar estatísticas de fluxo adicionais, tais como a proporção de dados enviados e recebidos de IPs nesse país/região.

    ![geomap][8]

1. Filtros

    A CapAnalysis fornece um conjunto de filtros para uma análise rápida de pacotes específicos. Por exemplo, pode optar por filtrar os dados por protocolo para obter informações específicas sobre esse subconjunto de tráfego.

    ![filtros][11]

    Visite [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) para saber mais sobre todas as capacidades da CapAnalysis.

## <a name="conclusion"></a>Conclusão

A funcionalidade de captura de pacotes do Network Watcher permite-lhe capturar os dados necessários para realizar a análise forense da rede e compreender melhor o tráfego da sua rede. Neste cenário, mostrámos como as capturas de pacotes do Network Watcher podem ser facilmente integradas com ferramentas de visualização de código aberto. Ao utilizar ferramentas de código aberto, como a CapAnalysis, para visualizar capturas de pacotes, pode realizar uma inspeção de pacotes profundos e identificar rapidamente tendências dentro do tráfego da sua rede.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os registos de fluxo NSG, visite [os registos do NSG Flow](network-watcher-nsg-flow-logging-overview.md)

Saiba como visualizar os seus registos de fluxo NSG com Power BI visitando [registos de fluxos NSG do Visualize com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
