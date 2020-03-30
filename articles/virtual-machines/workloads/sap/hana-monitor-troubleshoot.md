---
title: Monitorização e resolução de problemas do lado hana na SAP HANA em Azure (Grandes Instâncias) [ Microsoft Docs
description: Monitorização e resolução de problemas do lado hana no SAP HANA num Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 047ea4d07f2b497ac8c7deb90c056d63976094f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617082"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorizar e resolver problemas do lado do HANA

A fim de analisar eficazmente os problemas relacionados com o SAP HANA em Azure (Grandes Instâncias), é útil reduzir a causa principal de um problema. A SAP publicou uma grande quantidade de documentação para o ajudar.

As FAQ aplicáveis relacionadas com o desempenho do SAP HANA podem ser encontradas nas seguintes notas SAP:

- [Nota SAP #2222200 – FAQ: Rede SAP HANA](https://launchpad.support.sap.com/#/notes/2222200)
- [Nota SAP #2100040 – FAQ: CpU SAP HANA](https://launchpad.support.sap.com/#/notes/0002100040)
- [Nota SAP #199997 – FAQ: Memória SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)
- [Nota SAP #200000 – FAQ: Otimização de Desempenho SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Nota SAP #199930 – FAQ: SAP HANA I/O Análise](https://launchpad.support.sap.com/#/notes/1999930)
- [Nota SAP #2177064 – FAQ: Reinício e Acidentes de Serviço SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alertas SAP HANA

Como primeiro passo, verifique os registos de alerta SAP HANA atuais. No Estúdio SAP HANA, vá à Consola de **Administração: Alertas: Mostrar: todos os alertas**. Este separador mostrará todos os alertas SAP HANA para valores específicos (memória física gratuita, utilização de CPU, etc.) que se situam fora dos limiares mínimos e máximos definidos. Por padrão, as verificações são renovadas automaticamente a cada 15 minutos.

![No Estúdio SAP HANA, vá à Consola de Administração: Alertas: Mostrar: todos os alertas](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Para um alerta desencadeado devido à definição de limiar inadequada, uma resolução é repor o valor predefinido ou um valor limiar mais razoável.

![Redefinir o valor predefinido ou um valor limiar mais razoável](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Os seguintes alertas podem indicar problemas de recursos da CPU:

- Utilização do CPU anfitrião (Alerta 5)
- Operação de ponto de salvamento mais recente (Alerta 28)
- Duração do ponto de salvação (Alerta 54)

Pode notar um elevado consumo de CPU na sua base de dados SAP HANA a partir de uma das seguintes:

- O alerta 5 (utilização do CPU hospedeiro) é aumentado para uso de CPU atual ou passado
- O uso do CPU exibido no ecrã de visão geral

![Utilização do CPU exibida no ecrã de visão geral](./media/troubleshooting-monitoring/image3-cpu-usage.png)

O gráfico de carga pode mostrar um elevado consumo de CPU, ou um consumo elevado no passado:

![O gráfico de carga pode mostrar um alto consumo de CPU, ou alto consumo no passado](./media/troubleshooting-monitoring/image4-load-graph.png)

Um alerta desencadeado devido à elevada utilização do CPU pode ser causado por várias razões, incluindo, mas não se limitando a: execução de determinadas transações, carregamento de dados, empregos que não estão a responder, declarações de SQL de longa duração e mau desempenho da consulta (por exemplo, com BW em cubos HANA).

Consulte o site [SAP HANA Troubleshooting: CPU Related Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) para obter passos detalhados de resolução de problemas.

## <a name="operating-system"></a>Sistema Operativo

Um dos controlos mais importantes para o SAP HANA no Linux é certificar-se de que as Páginas Enormes Transparentes são desativadas, consulte [sap note #2131662 – Páginas Enormes Transparentes (THP) nos Servidores SAP HANA.](https://launchpad.support.sap.com/#/notes/2131662)

- Pode verificar se as Páginas Enormes Transparentes estão ativadas através do seguinte comando Linux: **cat /sys/kernel/mm/transparente\_hugepage/enabled**
- Se _estiver sempre_ fechado em parênteses como abaixo, significa que as Páginas Enormes Transparentes estão ativadas: [sempre] aconselham nunca; se _nunca_ for fechado em parênteses como abaixo, significa que as Páginas Enormes Transparentes são desativadas: sempre aconselhar [nunca]

O seguinte comando Linux não deve devolver nada: **rpm -qa [ grep ulimit.** Se parecer que _o ulimit_ está instalado, desinstale-o imediatamente.

## <a name="memory"></a>Memória

Pode observar que a quantidade de memória atribuída pela base de dados SAP HANA é superior ao esperado. Os seguintes alertas indicam problemas com elevado uso da memória:

- Utilização da memória física do hospedeiro (Alerta 1)
- Utilização da memória do servidor de nome (Alerta 12)
- Utilização total da memória das tabelas da Loja de Colunas (Alerta 40)
- Utilização da memória dos serviços (Alerta 43)
- Utilização da memória do armazenamento principal das tabelas da Loja coluna (Alerta 45)
- Ficheiros de despejo de tempo de execução (Alerta 46)

Consulte o site [SAP HANA Troubleshooting: Memory Problems](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) para obter passos detalhados de resolução de problemas.

## <a name="network"></a>Rede

Consulte a [Nota SAP #2081065 – Resolução de Problemas Da Rede SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) e execute os passos de resolução de problemas da rede nesta Nota SAP.

1. Analisando o tempo de ida e volta entre o servidor e o cliente.
  R. Executar o script SQL [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analise a comunicação interna.
  R. Executar script SQL [_HANA\_\_Network Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Executar o comando Linux **ifconfig** (a saída mostra se houver perdas de pacote).
4. Executar o **tcpdump**de comando Linux.

Além disso, utilize a ferramenta [IPERF](https://iperf.fr/) de código aberto (ou similar) para medir o desempenho real da rede de aplicações.

Consulte o site [SAP HANA Troubleshooting: Networking Performance and Connectivity Problems](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) para obter passos detalhados de resolução de problemas.

## <a name="storage"></a>Storage

Do ponto de vista do utilizador final, uma aplicação (ou o sistema como um todo) funciona lentamente, não responde, ou pode mesmo parecer parar de responder se há problemas com o desempenho de I/S. No separador **Volumes** no Estúdio SAP HANA, pode ver os volumes anexados e quais os volumes utilizados por cada serviço.

![No separador Volumes no Estúdio SAP HANA, pode ver os volumes anexados e quais os volumes utilizados por cada serviço](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Volumes anexados na parte inferior do ecrã pode ver detalhes dos volumes, tais como ficheiros e estatísticas de I/S.

![Volumes anexados na parte inferior do ecrã pode ver detalhes dos volumes, tais como ficheiros e estatísticas de I/S](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Consulte o site [SAP HANA Troubleshooting: I/O Relacionado causas e soluções](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) e resolução de [problemas Relacionados Com o Disco HANA para passos detalhados](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) de resolução de problemas.

## <a name="diagnostic-tools"></a>Ferramentas de Diagnóstico

Execute uma verificação de saúde\_\_SAP HANA através de Minichecks de Configuração HANA. Esta ferramenta devolve problemas técnicos potencialmente críticos que já deveriam ter sido levantados como alertas no Estúdio SAP HANA.

Consulte a recolha de [declarações SAP Note #1969700 – SQL para SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) e descarregue o ficheiro SQL Statements.zip anexado a essa nota. Guarde este ficheiro .zip no disco rígido local.

No Estúdio SAP HANA, no separador **Informação** do Sistema, clique à direita na coluna **Nome** e selecione **Import SQL Statements**.

![No Estúdio SAP HANA, no separador Informação do Sistema, clique à direita na coluna Nome e selecione Import SQL Statements](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Selecione o ficheiro SQL Statements.zip armazenado localmente e será importada uma pasta com as correspondentes declarações SQL. Neste ponto, os muitos controlos de diagnóstico diferentes podem ser executados com estas declarações SQL.

Por exemplo, para testar os requisitos de largura de largura de banda do sistema SAP HANA, clique na declaração de largura de **banda** em **replicação: largura** de banda e selecione **Open** na Consola SQL.

A declaração completa do SQL abre permitindo que os parâmetros de entrada (secção de modificação) sejam alterados e executados.

![A declaração completa do SQL abre permitindo que os parâmetros de entrada (secção de modificação) sejam alterados e executados](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Outro exemplo é clicar à direita nas declarações em **Replication: Overview**. Selecione **Executar** a partir do menu de contexto:

![Outro exemplo é clicar à direita nas declarações em Replication: Overview. Selecione Executar a partir do menu de contexto](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Isto resulta em informações que ajudam na resolução de problemas:

![Isto resultará em informações que ajudarão na resolução de problemas](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Faça o mesmo\_para\_mini-verificações de configuração HANA e verifique se há marcas _X_ na coluna _C_ (Crítica).

Saídas de amostra:

**Hana\_\_Configuração\_MiniChecks Rev102.01+1** para verificações gerais SAP HANA.

![Hana\_\_Configuração\_MiniChecks Rev102.01+1 para verificações gerais SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Visão\_geral\_dos serviços da HANA** para uma visão geral do que os serviços SAP HANA estão atualmente a funcionar.

![Visão\_geral\_dos serviços da HANA para uma visão geral do que os serviços SAP HANA estão atualmente a funcionar](./media/troubleshooting-monitoring/image12-services-overview.png)

**Estatísticas\_de\_serviços HANA** para informações de serviço SAP HANA (CPU, memória, etc.).

![Estatísticas\_de\_serviços da HANA para informações sobre serviços SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Visão\_geral\_\_de configuração HANA Rev110+** para informações gerais sobre a instância SAP HANA.

![Visão\_geral\_\_de configuração HANA Rev110+ para informações gerais sobre a instância SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**PARÂMETROS\_\_de\_configuração HANA Rev70+** para verificar os parâmetros SAP HANA.

![PARÂMETROS\_\_de\_configuração HANA Rev70+ para verificar parâmetros SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Passos seguintes**

- Consulte [a elevada disponibilidade configurada no SUSE utilizando o STONITH](ha-setup-with-stonith.md).