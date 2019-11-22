---
title: Visualizar padrões de tráfego de rede com ferramentas de código aberto
titleSuffix: Azure Network Watcher
description: Esta página descreve como usar a captura de pacotes do observador de rede com o Capanalysis para visualizar os padrões de tráfego de e para suas VMs.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 3baf73eed09e6880820952b8457526b6c21dd83b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277769"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualizar padrões de tráfego de rede de e para suas VMs usando ferramentas de código-fonte aberto

As capturas de pacote contêm dados de rede que permitem a realização de análise forense de rede e inspeção profunda de pacotes. Há muitas ferramentas de código aberto que você pode usar para analisar capturas de pacote para obter informações sobre sua rede. Uma dessas ferramentas é a CapAnalysis, uma ferramenta de visualização de captura de pacotes de código aberto. Visualizar dados de captura de pacote é uma maneira valiosa de rapidamente obter informações sobre padrões e anomalias em sua rede. As visualizações também fornecem um meio de compartilhar essas informações de uma maneira facilmente consumível.

O observador de rede do Azure fornece a capacidade de capturar dados, permitindo que você execute capturas de pacote em sua rede. Este artigo fornece uma orientação sobre como Visualizar e obter informações de capturas de pacote usando o CapAnalysis com o observador de rede.

## <a name="scenario"></a>Cenário

Você tem um aplicativo Web simples implantado em uma VM no Azure deseja usar ferramentas de código-fonte aberto para visualizar seu tráfego de rede para identificar rapidamente os padrões de fluxo e quaisquer anomalias possíveis. Com o observador de rede, você pode obter uma captura de pacote do seu ambiente de rede e armazená-lo diretamente em sua conta de armazenamento. CapAnalysis pode então ingerir a captura de pacotes diretamente do blob de armazenamento e visualizar seu conteúdo.

![cenário][1]

## <a name="steps"></a>Passos

### <a name="install-capanalysis"></a>Instalar o CapAnalysis

Para instalar o CapAnalysis em uma máquina virtual, você pode consultar as instruções oficiais aqui https://www.capanalysis.net/ca/how-to-install-capanalysis.
Para acessar o CapAnalysis remotamente, você precisa abrir a porta 9877 em sua VM adicionando uma nova regra de segurança de entrada. Para obter mais informações sobre a criação de regras em grupos de segurança de rede, consulte [criar regras em um NSG existente](../virtual-network/manage-network-security-group.md#create-a-security-rule). Depois que a regra tiver sido adicionada com êxito, você poderá acessar o CapAnalysis de `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Usar o observador de rede do Azure para iniciar uma sessão de captura de pacote

O observador de rede permite que você capture pacotes para acompanhar o tráfego de entrada e saída de uma máquina virtual. Você pode consultar as instruções em [gerenciar capturas de pacote com o observador de rede](network-watcher-packet-capture-manage-portal.md) para iniciar uma sessão de captura de pacote. Uma captura de pacote pode ser armazenada em um blob de armazenamento para ser acessada pelo CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Carregar uma captura de pacote para o CapAnalysis
Você pode carregar diretamente uma captura de pacote realizada pelo observador de rede usando a guia "importar da URL" e fornecendo um link para o blob de armazenamento em que a captura de pacote está armazenada.

Ao fornecer um link para CapAnalysis, certifique-se de acrescentar um token SAS à URL do blob de armazenamento.  Para fazer isso, navegue até assinatura de acesso compartilhado da conta de armazenamento, designe as permissões permitidas e pressione o botão gerar SAS para criar um token. Em seguida, você pode acrescentar o token SAS à URL do blob de armazenamento de captura de pacote.

A URL resultante será semelhante à seguinte URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analisando capturas de pacote

O CapAnalysis oferece várias opções para visualizar a captura de pacotes, cada uma fornecendo análise de uma perspectiva diferente. Com esses resumos visuais, você pode entender suas tendências de tráfego de rede e identificar rapidamente qualquer atividade incomum. Alguns desses recursos são mostrados na lista a seguir:

1. Tabelas de fluxo

    Esta tabela fornece a lista de fluxos nos dados do pacote, o carimbo de data/hora associado aos fluxos e os vários protocolos associados ao fluxo, bem como o IP de origem e de destino.

    ![página de fluxo do capanalysis][5]

1. Visão geral do protocolo

    Esse painel permite que você veja rapidamente a distribuição do tráfego de rede nos vários protocolos e regiões geográficas.

    ![Visão geral do protocolo capanalysis][6]

1. Estatísticas

    Esse painel permite exibir estatísticas de tráfego de rede – bytes enviados e recebidos dos IPs de origem e de destino, fluxos para cada um dos IPs de origem e de destino, o protocolo usado para vários fluxos e a duração dos fluxos.

    ![Estatísticas de capanalysis][7]

1. um mapa geográfico

    Esse painel fornece uma exibição de mapa do tráfego de rede, com cores dimensionando para o volume de tráfego de cada país/região. Você pode selecionar países/regiões realçados para exibir estatísticas de fluxo adicionais, como a proporção de dados enviados e recebidos de IPs nesse país/região.

    ![um mapa geográfico][8]

1. Filtros

    O CapAnalysis fornece um conjunto de filtros para análise rápida de pacotes específicos. Por exemplo, você pode optar por filtrar os dados por protocolo para obter informações específicas sobre esse subconjunto de tráfego.

    ![filtros][11]

    Visite [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) para saber mais sobre todos os recursos do CapAnalysis.

## <a name="conclusion"></a>Conclusão

O recurso de captura de pacotes do observador de rede permite que você capture os dados necessários para executar a perícia de rede e entenda melhor o tráfego de rede. Nesse cenário, mostramos como as capturas de pacote do observador de rede podem ser facilmente integradas com ferramentas de visualização de código aberto. Usando ferramentas de código-fonte aberto, como o CapAnalysis, para visualizar capturas de pacotes, você pode executar uma inspeção profunda de pacotes e identificar rapidamente as tendências dentro do tráfego de rede.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os logs de fluxo do NSG, visite [logs de fluxo do NSG](network-watcher-nsg-flow-logging-overview.md)

Saiba como visualizar seus logs de fluxo do NSG com Power BI visitando [Visualizar logs do NSG fluxos com Power bi](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
