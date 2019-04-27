---
title: Visualizar os padrões de tráfego de rede com o observador de rede do Azure e ferramentas de código aberto | Documentos da Microsoft
description: Esta página descreve como utilizar a captura de pacotes do observador de rede com Capanalysis para visualizar os padrões de tráfego de e para as suas VMs.
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
ms.openlocfilehash: 7c239bbf577645ddb8ab12c525d1b3a8832421df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680565"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualizar os padrões de tráfego de rede de e para as VMs com ferramentas de código aberto

Capturas de pacotes contêm dados de rede que permitem que execute forense de rede e inspeção de pacotes aprofundada. Existem abre muitas ferramentas de código, que pode usar para analisar capturas de pacotes para obter informações sobre a sua rede. Uma dessas ferramentas é CapAnalysis, uma ferramenta de visualização de captura de pacotes de código-fonte aberto. Visualizar dados de captura de pacotes é uma forma valiosa para rapidamente derivar informações sobre padrões e anomalias na sua rede. Visualizações também fornecem um meio de compartilhar essas informações de uma forma facilmente consumível.

Observador de rede do Azure fornece-lhe a capacidade de capturar os dados, permitindo-lhe realizar capturas de pacotes na sua rede. Este artigo fornece uma explicação de como visualizar e obter informações a partir do pacote de captura com CapAnalysis com o observador de rede.

## <a name="scenario"></a>Cenário

Tem uma aplicação web simples implementada numa VM no Azure utilizar ferramentas open source para visualizar o seu tráfego de rede para identificar rapidamente quaisquer anomalias possíveis e padrões de fluxo. Com o observador de rede, pode obter uma captura de pacotes do seu ambiente de rede e armazená-lo diretamente na sua conta de armazenamento. CapAnalysis, em seguida, pode ingerir a captura de pacote diretamente do blob de armazenamento e visualizar o seu conteúdo.

![cenário][1]

## <a name="steps"></a>Passos

### <a name="install-capanalysis"></a>Instalar CapAnalysis

Para instalar CapAnalysis numa máquina virtual, pode consultar as instruções aqui oficiais https://www.capanalysis.net/ca/how-to-install-capanalysis.
Na ordem aceder CapAnalysis remotamente, precisa abrir porta 9877 na sua VM, adicionando uma nova regra de segurança de entrada. Para obter mais informações sobre a criação de regras em grupos de segurança de rede, consulte [criar regras num NSG existente](../virtual-network/manage-network-security-group.md#create-a-security-rule). Assim que a regra foi adicionada com êxito, deverá conseguir aceder CapAnalysis do `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Utilizar o observador de rede do Azure para iniciar uma sessão de captura de pacotes

Observador de rede permite-lhe capturar pacotes para controlar o tráfego que entra e sai de uma máquina virtual. Pode consultar as instruções em [capturas de pacotes de gerir com o observador de rede](network-watcher-packet-capture-manage-portal.md) para iniciar uma sessão de captura de pacotes. Uma captura de pacotes pode ser armazenada num blob de armazenamento para ser acedida por CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Carregar uma captura de pacotes para CapAnalysis
Pode carregar diretamente uma captura de pacotes tomada pelo observador de rede usando a guia "Importação de URL" e fornecer um link para o blob de armazenamento onde está armazenada a captura de pacotes.

Quando fornecer um link para CapAnalysis, certifique-se acrescentar um token SAS para o URL do blob de armazenamento.  Para tal, navegue até à assinatura de acesso partilhado da conta de armazenamento, designar as permissões concedidas e prima o botão de gerar SAS para criar um token. Em seguida, pode acrescentar o token SAS para o URL de blob de armazenamento de captura de pacotes.

O URL resultante será algo parecido com o seguinte URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Capturas de pacotes de análise

CapAnalysis oferece várias opções para visualizar a captura de pacotes, cada fornecer análise de uma perspectiva diferente. Com esses resumos visual, pode compreender as tendências de tráfego de rede e detetar rapidamente quaisquer atividade invulgar. Algumas destas funcionalidades são mostradas na lista seguinte:

1. Tabelas de fluxo

    Esta tabela fornece a lista de fluxos de dados do pacote, o carimbo de hora associados com os fluxos e vários protocolos associados com o flow, bem como o IP de origem e de destino.

    ![página de fluxo de capanalysis][5]

1. Descrição geral do protocolo

    Este painel permite-lhe ver rapidamente a distribuição de tráfego de rede através de vários protocolos e as geografias.

    ![Descrição geral do protocolo capanalysis][6]

1. Estatísticas

    Este painel permite-lhe ver estatísticas de tráfego de rede – bytes enviados e recebidos de origem e destino IPs, fluxos para cada uma de origem e de destino IPs, o protocolo utilizado para diversos fluxos e a duração de fluxos.

    ![estatísticas de capanalysis][7]

1. Mapa geográfico

    Este painel fornece uma vista de mapa de seu tráfego de rede, com cores Dimensionar para o volume de tráfego de cada país. Pode selecionar países realçados para ver estatísticas de fluxo adicionais, tais como a proporção de dados enviados e recebidos de IPs naquele país.

    ![mapa geográfico][8]

1. Filtros

    CapAnalysis fornece um conjunto de filtros para análise rápida de pacotes específicos. Por exemplo, pode optar por filtrar os dados pelo protocolo para obter informações específicas sobre esse subconjunto de tráfego.

    ![filtros][11]

    Visite [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) para saber mais sobre as capacidades de todos os CapAnalysis.

## <a name="conclusion"></a>Conclusão

Funcionalidade de captura de pacotes do observador de rede permite-lhe capturar os dados necessários para efetuar forense de rede e compreender melhor o seu tráfego de rede. Neste cenário, mostramos como capturas de pacotes do observador de rede podem facilmente ser integradas com ferramentas de visualização de código-fonte aberto. Ao utilizar ferramentas de código-fonte aberto como CapAnalysis para visualizar as capturas de pacotes, pode efetuar a inspeção de pacotes aprofundada e identificar rapidamente as tendências em seu tráfego de rede.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os registos de fluxo do NSG, visite [registos de fluxo de NSG](network-watcher-nsg-flow-logging-overview.md)

Saiba como visualizar os seus registos de fluxo NSG com o Power BI, visite a página [registos com o Power BI de fluxos NSG visualizar](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
