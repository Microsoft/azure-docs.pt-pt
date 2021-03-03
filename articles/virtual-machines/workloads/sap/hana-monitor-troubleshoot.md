---
title: Monitorização e resolução de problemas do lado hana na SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Monitorização e resolução de problemas do lado hana em SAP HANA em um Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83743a6985bef8ce6c03e01ed8d10aa740852106
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668803"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorizar e resolver problemas do lado do HANA

Para analisar eficazmente os problemas relacionados com o SAP HANA em Azure (Grandes Instâncias), é útil reduzir a causa principal de um problema. A SAP publicou uma grande quantidade de documentação para o ajudar.

As FAQs aplicáveis relacionadas com o desempenho do SAP HANA podem ser encontradas nas seguintes notas SAP:

- [SAP Note #2222200 – FAQ: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Nota #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Nota #199997 – FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – FAQ: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Nota #199930 – FAQ: SAP HANA I/O Análise](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – FAQ: SAP HANA Service Restart and Crashes](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alertas SAP HANA

Como primeiro passo, verifique os atuais registos de alerta SAP HANA. No Estúdio SAP HANA, vá à **Consola de Administração: Alertas: Mostrar: todos os alertas**. Este separador mostrará todos os alertas SAP HANA para valores específicos (memória física gratuita, utilização de CPU, etc.) que ficam fora dos limiares mínimos e máximos estabelecidos. Por predefinição, os controlos são atualizados automaticamente a cada 15 minutos.

![No Estúdio SAP HANA, vá à Consola de Administração: Alertas: Mostrar: todos os alertas](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Para um alerta desencadeado devido a uma definição de limiar inadequada, uma resolução consiste em redefinir o valor padrão ou um valor limiar mais razoável.

![Reset ao valor padrão ou a um valor limiar mais razoável](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Os seguintes alertas podem indicar problemas de recursos da CPU:

- Utilização do CPU do anfitrião (Alerta 5)
- Operação de ponto de poupança mais recente (Alerta 28)
- Duração do ponto de salvamento (Alerta 54)

Pode notar um elevado consumo de CPU na sua base de dados SAP HANA a partir de um dos seguintes:

- O alerta 5 (utilização do CPU anfitrião) é levantado para uso atual ou anterior do CPU
- A utilização do CPU exibido no ecrã geral

![Visualizado uso do CPU no ecrã geral](./media/troubleshooting-monitoring/image3-cpu-usage.png)

O gráfico de carga pode mostrar um elevado consumo de CPU, ou alto consumo no passado:

![O gráfico de carga pode mostrar um alto consumo de CPU, ou alto consumo no passado](./media/troubleshooting-monitoring/image4-load-graph.png)

Um alerta desencadeado devido à elevada utilização do CPU pode ser causado por várias razões, incluindo, mas não se limitando a: execução de determinadas transações, carregamento de dados, empregos que não estão a responder, declarações de SQL de longa duração e mau desempenho de consulta (por exemplo, com BW em cubos HANA).

Consulte o [resolução de problemas SAP HANA: Site de causas e soluções relacionadas com](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) o CPU para obter etapas detalhadas de resolução de problemas.

## <a name="operating-system"></a>Sistema Operativo

Um dos controlos mais importantes para o SAP HANA no Linux é garantir que as Páginas Enormes Transparentes são desativadas, ver [#2131662 SAP Note – Páginas Enormes Transparentes (THP) nos Servidores SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Pode verificar se as páginas enormes transparentes estão ativadas através do seguinte comando Linux: **cat /sys/kernel/mm/transparente \_ hugepage/enabled**
- Se _estiver sempre_ incluído em parênteses como abaixo, significa que as Páginas Enormes Transparentes estão ativadas: [sempre] madvise nunca; se _nunca_ for fechado em parênteses como abaixo, significa que as Páginas Enormes Transparentes são desativadas: sempre loucas [nunca]

O seguinte comando Linux não deve devolver nada: **rpm -qa | grep ulimit.** Se parecer _que o ulimit_ está instalado, desinstale-o imediatamente.

## <a name="memory"></a>Memória

Pode observar que a quantidade de memória atribuída pela base de dados SAP HANA é superior ao esperado. Os seguintes alertas indicam problemas com uma elevada utilização da memória:

- Hospedar o uso da memória física (Alerta 1)
- Utilização da memória do servidor de nomes (Alerta 12)
- Utilização total da memória das tabelas da Column Store (Alerta 40)
- Utilização da memória dos serviços (Alerta 43)
- Utilização da memória do armazenamento principal das tabelas da Column Store (Alerta 45)
- Ficheiros de despejo de tempo de execução (Alerta 46)

Consulte a [resolução de problemas DA SAP HANA:](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) Site de problemas de memória para obter etapas detalhadas de resolução de problemas.

## <a name="network"></a>Rede

Consulte a [SAP Note #2081065 – Resolução de problemas da Rede SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) e execute os passos de resolução de problemas da rede nesta Nota SAP.

1. Analisando o tempo de ida e volta entre o servidor e o cliente.
  A. Executar o script SQL [_HANA \_ Network \_ Clientes_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analise a comunicação internade.
  A. Executar serviços de rede [_HANA \_ script \_ SQL_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Executar o comando Linux **seconfig** (a saída mostra se estão a ocorrer perdas de pacotes).
4. Executar **tcpdump** comando Linux .

Além disso, utilize a ferramenta [IPERF](https://iperf.fr/) de código aberto (ou similar) para medir o desempenho real da rede de aplicações.

Consulte o [site SAP HANA Troubleshooting: Networking Performance and Connectivity Problems](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) para obter etapas detalhadas de resolução de problemas.

## <a name="storage"></a>Armazenamento

Do ponto de vista do utilizador final, uma aplicação (ou o sistema como um todo) funciona lentamente, não responde, ou pode mesmo parar de responder se houver problemas com o desempenho de E/S. No separador **Volumes** no Estúdio SAP HANA, pode ver os volumes anexos e quais os volumes utilizados por cada serviço.

![No separador Volumes no Estúdio SAP HANA, pode ver os volumes anexos e quais os volumes utilizados por cada serviço](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Volumes anexados na parte inferior do ecrã podem ver detalhes dos volumes, tais como ficheiros e estatísticas de E/S.

![Volumes anexos na parte inferior do ecrã podem ver detalhes dos volumes, tais como ficheiros e estatísticas de E/S](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Consulte a [resolução de problemas DA SAP HANA: Causas e soluções de raiz relacionadas com a I/O](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) e [resolução de problemas SAP HANA: Causas e soluções relacionadas com o disco](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) para obter etapas detalhadas de resolução de problemas.

## <a name="diagnostic-tools"></a>Ferramentas de Diagnóstico

Efetue um SAP HANA Health Check através de \_ Minichecks de configuração \_ HANA. Esta ferramenta devolve problemas técnicos potencialmente críticos que já deveriam ter sido levantados como alertas no SAP HANA Studio.

Consulte a [SAP Note #1969700 – coleção de declaração SQL para SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) e descarregue o ficheiro de Statements.zip SQL anexado a essa nota. Guarde este ficheiro .zip no disco rígido local.

No Estúdio SAP HANA, no separador Informações do **Sistema,** clique à direita na coluna **Nome** e selecione **Import SQL Statements**.

![No Estúdio SAP HANA, no separador Informações do Sistema, clique à direita na coluna Nome e selecione Import SQL Statements](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Selecione o ficheiro DE Statements.zip SQL armazenado localmente e será importada uma pasta com as declarações SQL correspondentes. Neste momento, os muitos controlos de diagnóstico diferentes podem ser executados com estas declarações SQL.

Por exemplo, para testar os requisitos de largura de banda do sistema SAP HANA, clique com o botão direito na declaração de largura de **banda** em **replicação: Largura de banda** e selecione **Open** in SQL Console.

A declaração COMPLETA SQL abre permitindo que os parâmetros de entrada (secção de modificação) sejam alterados e executados.

![A declaração completa do SQL abre permitindo que os parâmetros de entrada (secção de modificação) sejam alterados e executados](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Outro exemplo é o de clicar à direita nas declarações em **replicação: Visão geral**. Selecione **Executar** a partir do menu de contexto:

![Outro exemplo é o de clicar à direita nas declarações em Replication: Overview. Selecione Executar a partir do menu de contexto](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Isto resulta em informações que ajudam na resolução de problemas:

![Isto resultará em informações que ajudarão na resolução de problemas](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Faça o mesmo para \_ minichecks de configuração HANA e verifique se há marcas \_ _X_ na coluna _C_ (Critical).

Saídas de amostras:

**HANA \_ Configuração \_ MiniChecks \_ Rev102.01+1** para verificações gerais do SAP HANA.

![\_HANA Configuração \_ MiniChecks \_ Rev102.01+1 para verificações gerais SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA \_ Visão \_ geral** dos serviços para uma visão geral do que os serviços SAP HANA estão atualmente a executar.

![Visão \_ geral dos serviços \_ HANA para uma visão geral do que os serviços SAP HANA estão atualmente a executar](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA \_ \_Estatísticas de Serviços** para informação de serviço SAP HANA (CPU, memória, etc.).

![Estatísticas dos Serviços HANA \_ \_ para informações de serviço SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA \_ Visão geral de configuração \_ \_ Rev110+** para informações gerais sobre a instância SAP HANA.

![Visão geral da configuração HANA \_ \_ \_ Rev110+ para informações gerais sobre a instância SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA \_ Parâmetros de configuração \_ \_ Rev70+** para verificar os parâmetros SAP HANA.

![Parâmetros de configuração HANA \_ \_ \_ Rev70+ para verificar os parâmetros SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Próximos passos**

- Consulte [a alta disponibilidade configurada em SUSE utilizando o STONITH](ha-setup-with-stonith.md).