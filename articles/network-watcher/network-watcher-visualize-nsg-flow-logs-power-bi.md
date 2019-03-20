---
title: Os registos de fluxo do grupo de segurança de rede do visualizing do Azure com o Power BI | Documentos da Microsoft
description: Esta página descreve como visualizar registos de fluxo NSG com o Power BI.
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
ms.openlocfilehash: 6df49f9cd308f4bb9b1fef6e5860872526ce8bb7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079031"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Registos de fluxo do grupo de segurança de rede visualizing com o Power BI

Os registos de fluxo do grupo de segurança de rede permitem-lhe ver informações sobre o tráfego IP de entrada e de saída nos grupos de segurança de rede. Estes fluir Mostrar registos fluxos de saída e entrados numa base por regra, a NIC, o fluxo de mensagens em fila aplica-se a, informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo) e se o tráfego foi permitido ou negado.

Pode ser difícil obter informações sobre os dados de registo do fluxo ao pesquisar manualmente os ficheiros de registo. Neste artigo, fornecemos uma solução para visualizar os registos de fluxo mais recentes e saiba mais sobre o tráfego na sua rede.

> [!Warning]  
> Os seguintes passos funcionam com a versão de registos de fluxo 1. Para obter detalhes, consulte [introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md). As instruções seguintes não irão funcionar com a versão 2 dos ficheiros de registo, sem modificações.

## <a name="scenario"></a>Cenário

O cenário a seguir, vamos ligar ambiente de trabalho do Power BI para a conta de armazenamento que configurou como o sink para os nossos dados do registo de fluxo de NSG. Depois de nos conectarmos a nossa conta de armazenamento, o Power BI transfere e analisa os registos para fornecer uma representação visual do tráfego que tem sessão iniciada por grupos de segurança de rede.

Os elementos visuais fornecidos no modelo que pode examinar a utilizar:

* Principais tópicos de conversação
* Dados de fluxo de séries temporais por decisão de direção e regra
* Fluxos por endereço MAC da Interface de rede
* Fluxos por NSG e regra
* Fluxos por porta de destino

O modelo fornecido é editável para que possa modificá-lo para adicionar novos dados, os elementos visuais, ou editar as consultas de acordo com as suas necessidades.

## <a name="setup"></a>Configurar

Antes de começar, tem de ter a rede segurança grupo fluxo o registo ativado num ou vários grupos de segurança de rede na sua conta. Para obter instruções sobre como ativar os registos de fluxo de segurança de rede, consulte o artigo seguinte: [Introdução ao registo do fluxo para grupos de segurança de rede](network-watcher-nsg-flow-logging-overview.md).

Também tem de ter o cliente do Power BI Desktop instalado no seu computador e o espaço livre suficiente no seu computador para transferir e carregar os dados de registo que exista na sua conta de armazenamento.

![Diagrama do Visio][1]

### <a name="steps"></a>Passos

1. Transfira e abra o modelo seguinte do Power BI na aplicação de ambiente de trabalho do Power BI [modelo de registos de fluxo do Power BI do observador de rede](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Introduza os parâmetros de consulta necessários
   1. **StorageAccountName** – Especifica como o nome da conta de armazenamento que contém os registos de fluxo NSG que gostaria de carregar e visualizar.
   1. **NumberOfLogFiles** – Especifica o número de ficheiros de registo que pretende transferir e visualizar no Power BI. Por exemplo, se for especificado 50, 50 ficheiros de registo mais recente. Se tivermos 2 NSGs ativada e configurada para enviar registos de fluxo NSG para esta conta, as últimas 25 horas de registos podem ser visualizadas.

      ![principal do Power BI][2]

1. Introduza a chave de acesso para a sua conta de armazenamento. Pode encontrar as chaves de acesso válido ao navegar para a sua conta de armazenamento no portal do Azure e selecionar **chaves de acesso** no menu definições. Clique em **Connect** , em seguida, aplicar as alterações.

    ![Chaves de acesso][3]

    ![chave de acesso 2][4]

4. Os registos estão a transferir e analisá-la, e agora pode utilizar os elementos visuais previamente criados.

## <a name="understanding-the-visuals"></a>Compreender os elementos visuais

Fornecido no modelo são um conjunto de elementos visuais que ajudam a compreender os dados de registo de fluxo do NSG. As imagens seguintes mostram um exemplo do dashboard do aspeto quando preenchido com dados. Veja a seguir examinamos cada elemento visual mais detalhes 

![Power BI][5]
 
A parte superior Talkers visual mostra os IPs que iniciou a maioria das ligações ao longo do período especificado. O tamanho das caixas de corresponde ao número de ligações relativo. 

![toptalkers][6]

Os gráficos de séries de tempo seguintes mostram o número de fluxos ao longo do período. O gráfico superior é segmentado pela direcção do fluxo e o inferior é segmentado pela decisão tomada (permitir ou negar). Com este elemento visual, pode examinar as tendências de tráfego ao longo do tempo e identificar picos anormais ou recusar no tráfego ou segmentação de tráfego.

![flowsoverperiod][7]

Os gráficos seguintes mostram o fluxos por interface de rede, com o canto superior segmentado por direção do fluxo e menor segmentados por decisão tomada. Com essas informações, pode obter informações sobre quais das suas VMs comunicadas mais relativas a outras pessoas, bem como se o tráfego para uma VM específica está a ser permitido ou negado.

![flowspernic][8]

O gráfico de roda de anel seguinte mostra uma divisão dos fluxos por porta de destino. Com essas informações, pode ver as portas de destino mais comumente usada utilizadas durante o período especificado.

![Anel][9]

O seguinte gráfico de barras mostra o fluxo por NSG e regra. Com essas informações, pode ver os NSGs responsáveis pela maioria do tráfego e a divisão de tráfego num NSG pela regra.

![barchart][10]
 
Os gráficos informativos seguintes apresentam informações sobre os NSGs presentes nos registos, o número de fluxos capturadas ao longo do período e a data do registo mais antigo capturado. Estas informações dá-lhe uma ideia de quais NSGs estão a ser conectado e o intervalo de datas de fluxos.

![infochart1][11]

![infochart2][12]

Esse modelo inclui as seguintes segmentações de dados para que possa ver apenas os dados que está mais interessados. Pode filtrar seus grupos de recursos, os NSGs e regras. Pode também filtrar informações de 5 cadeias de identificação, decisão e o tempo que o registo foi escrito.

![segmentações de dados][13]

## <a name="conclusion"></a>Conclusão

Mostramos neste cenário que ao utilizar os registos de fluxo do grupo de segurança da rede de mensagens em fila fornecidos pelo observador de rede e o Power BI, é possível visualizar e compreender o tráfego. Utilizando o modelo fornecido, o Power BI transfere os registos diretamente a partir do armazenamento e processa-as localmente. Tempo decorrido para carregar o modelo varia consoante o número de ficheiros pedida e o tamanho total dos ficheiros transferidos.

Fique à vontade personalizar este modelo para as suas necessidades. Existem várias formas de vários que pode usar o Power BI com o grupo de fluxo de registos de segurança rede. 

## <a name="notes"></a>Notas

* Os registos por predefinição são armazenados no `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Se existem outros dados de outro diretório que as consultas para a solicitação e processar os dados devem ser modificadas.

* O modelo fornecido não é recomendado para utilização com mais de 1 GB de registos.

* Se tiver uma grande quantidade de registos, é recomendável que investigar uma solução com outro arquivo de dados, como o Data Lake ou SQL server.

## <a name="next-steps"></a>Próximos Passos

Saiba como visualizar os seus registos de fluxo NSG com a pilha elástica, visite a página [os registos de fluxo de NSG de observador de rede do Azure visualizar utilizando ferramentas open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
