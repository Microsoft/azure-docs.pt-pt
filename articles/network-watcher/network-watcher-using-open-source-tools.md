---
title: Visualizar padrões de tráfego de rede com ferramentas de código aberto
titleSuffix: Azure Network Watcher
description: Esta página descreve como usar a captura de pacotes do Network Watcher com a Capanalysis para visualizar padrões de tráfego de e para os seus VMs.
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
ms.openlocfilehash: 32d978ee766f6dbe95bd7158b8060a0302ef8206
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189067"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualizar padrões de tráfego de rede de e para os seus VMs utilizando ferramentas de código aberto

As capturas de pacotes contêm dados de rede que lhe permitem realizar a recolha forense da rede e uma inspeção profunda de pacotes. Existem muitas ferramentas de origem abertas que pode usar para analisar capturas de pacotes para obter informações sobre a sua rede. Uma dessas ferramentas é a CapAnalysis, uma ferramenta de visualização de captura de pacotes de código aberto. Visualizar dados de captura de pacotes é uma forma valiosa de obter rapidamente insights sobre padrões e anomalias dentro da sua rede. As visualizações também fornecem um meio de partilhar tais insights de uma forma facilmente consumível.

O Vigilante da Rede Azure fornece-lhe a capacidade de capturar dados, permitindo-lhe realizar capturas de pacotes na sua rede. Este artigo, fornece uma caminhada sobre como visualizar e obter insights a partir de capturas de pacotes usando CapAnalysis com Network Watcher.

## <a name="scenario"></a>Cenário

Tem uma aplicação web simples implantada num VM em Azure que pretende utilizar ferramentas de código aberto para visualizar o seu tráfego de rede para identificar rapidamente padrões de fluxo e eventuais anomalias. Com o Network Watcher, pode obter uma captura de pacotes do ambiente da sua rede e armazená-lo diretamente na sua conta de armazenamento. O CapAnalysis pode então ingerir a captura do pacote diretamente a partir da bolha de armazenamento e visualizar o seu conteúdo.

![cenário][1]

## <a name="steps"></a>Passos

### <a name="install-capanalysis"></a>Instalar capAnalysis

Para instalar o CapAnalysis numa máquina virtual, pode https://www.capanalysis.net/ca/how-to-install-capanalysisconsultar as instruções oficiais aqui .
Para aceder remotamente à CapAnalysis, é necessário abrir a porta 9877 no seu VM adicionando uma nova regra de segurança de entrada. Para mais informações sobre a criação de regras em Grupos de Segurança de Rede, consulte a [Create rules in a existinte NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). Uma vez adicionada a regra com sucesso, você deve ser capaz de aceder a CapAnalysis a partir de`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Use o Vigilante da Rede Azure para iniciar uma sessão de captura de pacotes

O Network Watcher permite-lhe capturar pacotes para rastrear o tráfego dentro e fora de uma máquina virtual. Pode consultar as instruções da [Manage packet captures com o Network Watcher](network-watcher-packet-capture-manage-portal.md) para iniciar uma sessão de captura de pacotes. Uma captura de pacote pode ser armazenada numa bolha de armazenamento a ser acedida pela CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Faça upload de uma captura de pacote para CapAnalysis
Pode fazer o upload direto de uma captura de pacotes tirada pelo observador de rede utilizando o separador "Importar de URL" e fornecer um link para a bolha de armazenamento onde a captura do pacote é armazenada.

Ao fornecer um link para o CapAnalysis, certifique-se de anexar um token SAS ao URL blob de armazenamento.  Para isso, navegue para a assinatura de acesso partilhado a partir da conta de armazenamento, designe as permissões permitidas e prima o botão Generate SAS para criar um símbolo. Em seguida, pode anexar o token SAS ao URL blob de armazenamento de captura de pacotes.

O URL resultante será parecido com o seguinte URL:`http:\//storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>Analisar capturas de pacotes

O CapAnalysis oferece várias opções para visualizar a captura do seu pacote, cada uma fornecendo análises de uma perspetiva diferente. Com estes resumos visuais, você pode entender as suas tendências de tráfego de rede e rapidamente detetar qualquer atividade incomum. Algumas destas funcionalidades são mostradas na seguinte lista:

1. Tabelas de fluxo

    Esta tabela dá-lhe a lista de fluxos nos dados do pacote, o carimbo de tempo associado aos fluxos e os vários protocolos associados ao fluxo, bem como fonte e destino IP.

    ![página de fluxo de capanálise][5]

1. Visão geral do protocolo

    Este painel permite-lhe ver rapidamente a distribuição do tráfego de rede através dos vários protocolos e geografias.

    ![visão geral do protocolo capanalysis][6]

1. Estatísticas

    Este painel permite-lhe visualizar as estatísticas de tráfego da rede – bytes enviados e recebidos de IPs de origem e destino, fluxos para cada um dos IPs de origem e destino, protocolo utilizado para vários fluxos e duração dos fluxos.

    ![estatísticas da capanálise][7]

1. Geomap

    Este painel proporciona-lhe uma visão de mapa do tráfego da sua rede, com cores a escalar para o volume de tráfego de cada país/região. Pode selecionar países/regiões destacados para visualizar estatísticas de fluxos adicionais, como a proporção de dados enviados e recebidos de IPs nesse país/região.

    ![geomapa][8]

1. Filtros

    O CapAnalysis fornece um conjunto de filtros para uma análise rápida de pacotes específicos. Por exemplo, pode optar por filtrar os dados por protocolo para obter informações específicas sobre esse subconjunto de tráfego.

    ![filtros][11]

    Visite [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) para saber mais sobre todas as capacidades da CapAnalysis.

## <a name="conclusion"></a>Conclusão

A funcionalidade de captura de pacotes do Network Watcher permite-lhe capturar os dados necessários para realizar os forenses da rede e compreender melhor o tráfego da sua rede. Neste cenário, mostrámos como as capturas de pacotes do Network Watcher podem ser facilmente integradas com ferramentas de visualização de código aberto. Ao utilizar ferramentas de código aberto, como o CapAnalysis para visualizar capturas de pacotes, pode realizar uma inspeção profunda de pacotes e identificar rapidamente as tendências dentro do tráfego da sua rede.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os registos de fluxo nsg, visite [registos de fluxo NSG](network-watcher-nsg-flow-logging-overview.md)

Saiba como visualizar os seus registos de fluxo NSG com Power BI visitando visualize os [registos de fluxos NSG com Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
