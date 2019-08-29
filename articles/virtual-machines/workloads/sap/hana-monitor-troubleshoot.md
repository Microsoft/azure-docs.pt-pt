---
title: Monitoramento e solução de problemas do lado do HANA em SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Monitoramento e solução de problemas do lado do HANA em SAP HANA em um Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2c596a876817f0a501025c37e463a7eebb55cf2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099827"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorizar e resolver problemas do lado do HANA

Para analisar efetivamente os problemas relacionados ao SAP HANA no Azure (instâncias grandes), é útil restringir a causa raiz de um problema. A SAP publicou uma grande quantidade de documentação para ajudá-lo.

As perguntas frequentes aplicáveis relacionadas ao desempenho do SAP HANA podem ser encontradas nas seguintes notas SAP:

- [Observação SAP #2222200 – perguntas frequentes: Rede SAP HANA](https://launchpad.support.sap.com/#/notes/2222200)
- [Observação SAP #2100040 – perguntas frequentes: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [Observação SAP #199997 – perguntas frequentes: Memória SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)
- [Observação SAP #200000 – perguntas frequentes: Otimização de desempenho de SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Observação SAP #199930 – perguntas frequentes: SAP HANA análise de e/s](https://launchpad.support.sap.com/#/notes/1999930)
- [Observação SAP #2177064 – perguntas frequentes: Reinicialização e falhas do serviço de SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alertas de SAP HANA

Como uma primeira etapa, verifique os logs de alertas atuais do SAP HANA. No SAP Hana Studio, acesse **console de administração: Alertas Mostrar: todos os**alertas. Esta guia mostrará todos os SAP HANA alertas para valores específicos (memória física livre, utilização de CPU, etc.) que estão fora dos limites mínimo e máximo definido. Por padrão, as verificações são atualizadas automaticamente a cada 15 minutos.

![No SAP HANA Studio, acesse console de administração: Alertas Mostrar: todos os alertas](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Para um alerta disparado devido à configuração de limite inadequado, uma resolução é redefinir para o valor padrão ou um valor de limite mais razoável.

![Redefinir para o valor padrão ou um valor de limite mais razoável](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Os alertas a seguir podem indicar problemas de recursos de CPU:

- Uso de CPU do host (alerta 5)
- Operação de salvamento de pontos mais recente (alerta 28)
- Duração do salvamento de pontos (alerta 54)

Você pode notar um alto consumo de CPU em seu SAP HANA banco de dados de um dos seguintes:

- O alerta 5 (uso de CPU do host) é gerado para uso da CPU atual ou anterior
- O uso da CPU exibido na tela de visão geral

![Exibição do uso da CPU na tela de visão geral](./media/troubleshooting-monitoring/image3-cpu-usage.png)

O grafo de carga pode mostrar alto consumo de CPU ou alto consumo no passado:

![O grafo de carga pode mostrar alto consumo de CPU ou alto consumo no passado](./media/troubleshooting-monitoring/image4-load-graph.png)

Um alerta disparado devido à alta utilização da CPU pode ser causado por vários motivos, incluindo, mas não se limitando a: execução de determinadas transações, carregamento de dados, trabalhos que não estão respondendo, instruções SQL de execução longa e desempenho de consulta inadequado (por exemplo, com BW em cubos HANA).

Consulte a solução [de problemas de SAP Hana: Site de soluções](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) e causas relacionadas à CPU para obter etapas detalhadas de solução de problemas.

## <a name="operating-system"></a>Sistema operativo

Uma das verificações mais importantes para SAP HANA no Linux é verificar se as páginas enormes transparentes estão desabilitadas, consulte [SAP Note #2131662 – páginas enormes transparentes (THP) em servidores SAP Hana](https://launchpad.support.sap.com/#/notes/2131662).

- Você pode verificar se páginas enormes transparentes são habilitadas por meio do seguinte comando do Linux: **Cat/sys/kernel/mm/Transparent\_hugepage/Enabled**
- Se _sempre_ estiver entre colchetes como abaixo, significa que as páginas enormes transparentes estão habilitadas: [Always] madvise Never; Se _nunca_ estiver entre colchetes, como abaixo, significa que as páginas enormes transparentes estão desabilitadas: Always madvise [Never]

O seguinte comando do Linux não deve retornar nada: **rpm-QA | grep ulimit.** Se aparecer _ulimit_ estiver instalado, desinstale-o imediatamente.

## <a name="memory"></a>Memória

Você pode observar que a quantidade de memória alocada pelo banco de dados SAP HANA é maior do que o esperado. Os alertas a seguir indicam problemas com alto uso de memória:

- Uso de memória física do host (alerta 1)
- Uso de memória do servidor de nomes (alerta 12)
- Uso total de memória de tabelas de repositório de coluna (alerta 40)
- Uso de memória de serviços (alerta 43)
- Uso de memória do armazenamento principal de tabelas de repositório de coluna (alerta 45)
- Arquivos de despejo de tempo de execução (alerta 46)

Consulte a solução [de problemas de SAP Hana: Problemas](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) de memória no site para obter as etapas de solução de problemas detalhadas.

## <a name="network"></a>Rede

Consulte o [SAP Note #2081065 – solução de problemas de rede SAP Hana](https://launchpad.support.sap.com/#/notes/2081065) e execute as etapas de solução de problemas de rede nesta observação SAP.

1. Analisando o tempo de ida e volta entre servidor e cliente.
  R. Execute os clientes de [_rede\_\__ ](https://launchpad.support.sap.com/#/notes/1969700)do script SQL do Hana _._
  
2. Analise a comunicação entre nós.
  R. Execute o script [_SQL\_Hana\_serviços de rede_](https://launchpad.support.sap.com/#/notes/1969700) _._

3. Execute o comando do Linux **ifconfig** (a saída mostra se alguma perda de pacote está ocorrendo).
4. Execute o comando do Linux **tcpdump**.

Além disso, use a ferramenta [IPERF](https://iperf.fr/) de código-fonte aberto (ou semelhante) para medir o desempenho real da rede do aplicativo.

Consulte a solução [de problemas de SAP Hana: Site de problemas](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) de desempenho e conectividade de rede para obter etapas de solução de problemas detalhadas.

## <a name="storage"></a>Armazenamento

Do ponto de vista do usuário final, um aplicativo (ou o sistema como um todo) é executado de forma lenta, não responde ou pode até mesmo parecer parar de responder se houver problemas com O desempenho de e/s. Na guia **volumes** no SAP Hana Studio, você pode ver os volumes anexados e quais volumes são usados por cada serviço.

![Na guia volumes no SAP HANA Studio, você pode ver os volumes anexados e quais volumes são usados por cada serviço](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Volumes anexados na parte inferior da tela você pode ver detalhes dos volumes, como arquivos e estatísticas de e/s.

![Volumes anexados na parte inferior da tela você pode ver detalhes dos volumes, como arquivos e estatísticas de e/s](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Consulte a solução [de problemas de SAP Hana: Causas raiz relacionadas a e/s e](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) soluções [e SAP Hana solução de problemas: Site de soluções](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) e causas raiz relacionadas ao disco para obter etapas de solução de problemas detalhadas.

## <a name="diagnostic-tools"></a>Ferramentas de Diagnóstico

Execute uma verificação de integridade SAP Hana por\_meio\_do Hana Configuration Minichecks. Essa ferramenta retorna problemas técnicos potencialmente críticos que já devem ter sido gerados como alertas no SAP HANA Studio.

Consulte o [SAP Note #1969700 – coleção de instruções SQL para SAP Hana](https://launchpad.support.sap.com/#/notes/1969700) e baixe o arquivo. zip de instruções SQL anexado a essa observação. Armazene esse arquivo. zip no disco rígido local.

No SAP HANA Studio, na guia **informações do sistema** , clique com o botão direito do mouse na coluna **nome** e selecione **importar instruções SQL**.

![No SAP HANA Studio, na guia informações do sistema, clique com o botão direito do mouse na coluna nome e selecione Importar instruções SQL](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Selecione o arquivo. zip de instruções SQL armazenado localmente e uma pasta com as instruções SQL correspondentes será importada. Neste ponto, as várias verificações de diagnóstico diferentes podem ser executadas com essas instruções SQL.

Por exemplo, para testar SAP Hana requisitos de largura de banda de replicação do sistema, clique com **o botão direito do mouse na instrução de largura de **banda** em replicação Largura** de banda e selecione **abrir** no console do SQL.

A instrução SQL completa é aberta, permitindo que os parâmetros de entrada (seção de modificação) sejam alterados e executados.

![A instrução SQL completa é aberta permitindo que os parâmetros de entrada (seção de modificação) sejam alterados e executados](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Outro exemplo é clicar com o botão direito do mouse **nas instruções em replicação: Visão**geral. Selecione **executar** no menu de contexto:

![Outro exemplo é clicar com o botão direito do mouse nas instruções em replicação: Sobre. Selecione executar no menu de contexto](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Isso resulta em informações que ajudam na solução de problemas:

![Isso resultará em informações que ajudarão na solução de problemas](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Faça o mesmo para a\_configuração\_do Hana Minichecks e verifique se há marcas _X_ na coluna _C_ (crítico).

Saídas de exemplo:

**Hana\_Configuration\_MiniChecksrev\_102.01 + 1** para verificações gerais de SAP Hana.

![Hana\_Configuration\_MiniChecks\_Rev 102.01 + 1 para verificações gerais de SAP Hana](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Visão\_geral\_dos serviços do Hana** para obter uma visão geral do que SAP Hana serviços estão em execução no momento.

![Visão\_geral\_dos serviços do Hana para obter uma visão geral do que SAP Hana serviços estão em execução no momento](./media/troubleshooting-monitoring/image12-services-overview.png)

**As\_estatísticas\_de serviços do Hana** para SAP Hana informações do serviço (CPU, memória, etc.).

![Estatísticas\_de\_serviços do Hana para informações de SAP Hana serviço](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Hana\_Configuration\_OverviewRev110\_+** para obter informações gerais sobre a instância de SAP Hana.

![Hana\_Configuration\_Overview\_Rev110 + para obter informações gerais sobre a instância de SAP Hana](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**Hana\_Configuration\_Parameters\_Rev70 +** para verificar parâmetros de SAP Hana.

![Os\_parâmetros\_deconfiguraçãodo Hana Rev70 + para verificar parâmetros de SAP Hana\_](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Passos seguintes?**

- Consulte [configuração de alta disponibilidade no SUSE usando o STONITH](ha-setup-with-stonith.md).