---
title: Monitorando e solucionando problemas do HANA lado no SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Monitorando e solucionando problemas do lado do HANA no SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 392dd39e2b3b0e26e7b0ba645bf5a70231167ad5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014395"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorando e solucionando problemas do lado do HANA

Para analisar efetivamente os problemas relacionados com o SAP HANA no Azure (instâncias grandes), é útil restringir as causas de raiz de um problema. SAP publicou uma grande quantidade de documentação para ajudá-lo.

FAQs aplicável, relacionados com desempenho de SAP HANA podem ser encontradas nas notas para o SAP seguintes:

- [Nota SAP #2222200 – perguntas frequentes: Rede do SAP HANA](https://launchpad.support.sap.com/#/notes/2222200)
- [Nota SAP #2100040 – perguntas frequentes: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [Nota SAP #199997 – perguntas frequentes: Memória do SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)
- [Nota SAP #200000 – perguntas frequentes: Otimização de desempenho do SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Nota SAP #199930 – perguntas frequentes: Análise de e/s do SAP HANA](https://launchpad.support.sap.com/#/notes/1999930)
- [Nota SAP #2177064 – perguntas frequentes: SAP HANA serviço reiniciar e falhas](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alertas do SAP HANA

Como primeiro passo, verifique os registos de alerta do SAP HANA atuais. No SAP HANA Studio, aceda a **consola de administração: Alertas: Show: todos os alertas**. Este separador mostra todos os alertas de SAP HANA por valores específicos (memória física livre, utilização da CPU, etc.) que estão fora dos limiares de mínimo e máximo do conjunto. Por predefinição, verificações são atualizados em automática a cada 15 minutos.

![No SAP HANA Studio, aceda à consola de administração: Alertas: Show: todos os alertas](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Um alerta acionado devido à definição de limiar inadequada, uma resolução é reposto para o valor predefinido ou um valor de limiar mais razoável.

![Repor o valor predefinido ou um valor de limiar mais razoável](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Os seguintes alertas podem indicar problemas de recursos de CPU:

- Utilização da CPU do anfitrião (de alerta 5)
- Operação de ponto de reposição mais recente (28 alerta)
- Duração de ponto de reposição (54 alerta)

Pode observar o consumo de CPU elevado na sua base de dados do SAP HANA a partir de um dos seguintes procedimentos:

- Alerta 5 (utilização da CPU do anfitrião) é gerado para a utilização da CPU atual ou nos últimos
- A utilização da CPU apresentada no ecrã de descrição geral

![Apresentados a utilização da CPU no ecrã de descrição geral](./media/troubleshooting-monitoring/image3-cpu-usage.png)

O gráfico de carga poderá mostrar elevado consumo de CPU ou o alto consumo no passado:

![O gráfico de carga poderá mostrar elevado consumo de CPU ou o alto consumo no passado](./media/troubleshooting-monitoring/image4-load-graph.png)

Um alerta acionado devido a alta utilização da CPU pode ser causado por vários motivos, incluindo, mas não limitado a: execução de determinadas transações, o carregamento de dados, o que de tarefas, de longa execução instruções SQL e o desempenho de consulta incorreta (por exemplo, com a BW em HANA cubos).

Consulte o [SAP HANA de resolução de problemas: Soluções e CPU relacionados faz com que](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) para passos de resolução de problemas detalhada do site.

## <a name="operating-system"></a>Sistema Operativo

Uma das verificações mais importantes para o SAP HANA no Linux é certificar-se de que as páginas enorme transparente estão desativadas, consulte [2131662 no SAP Note # – transparente enorme páginas (THP) nos servidores do SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Pode verificar se as páginas enorme transparente habilitadas por meio do seguinte comando do Linux: **cat /sys/kernel/mm/transparent\_hugepage/ativada**
- Se _sempre_ está entre parênteses Retos, conforme mostrado a seguir, significa que as páginas de enorme transparente estão ativadas: [sempre] madvise nunca; se _nunca_ está entre parênteses Retos, conforme mostrado a seguir, significa que o enorme transparente Páginas estão desativadas: sempre madvise [nunca]

O seguinte comando do Linux deverá devolver nada: **rpm - controle de qualidade | grep ulimit.** Se for apresentada _ulimit_ é instalado, desinstale-a imediatamente.

## <a name="memory"></a>Memória

Pode observar que a quantidade de memória atribuída pela base de dados SAP HANA é superior ao esperado. Os seguintes alertas indicam problemas com a utilização elevada da memória:

- Utilização de memória física do anfitrião (1 alerta)
- Utilização da memória de servidor de nomes (12 alerta)
- Utilização da memória total das tabelas de coluna Store (40 alerta)
- Utilização de memória de serviços (43 alerta)
- Utilização da memória de armazenamento principal das tabelas de coluna Store (45 alerta)
- Ficheiros de informação de tempo de execução (46 alerta)

Consulte o [SAP HANA de resolução de problemas: Problemas de memória](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) para passos de resolução de problemas detalhada do site.

## <a name="network"></a>Rede

Consulte a [2081065 no SAP Note # – resolução de problemas de rede do SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) e executar da rede passos esta nota SAP de resolução de problemas.

1. Análise de tempo de ida e volta entre o servidor e cliente.
  R. Execute o script SQL [ _HANA\_rede\_clientes_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analise a comunicação internós.
  R. Executar script de SQL [ _HANA\_rede\_serviços_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Execute o comando do Linux **ifconfig** (o resultado mostra se ocorrem quaisquer perdas de pacote).
4. Execute o comando do Linux **tcpdump**.

Além disso, utilize o código-fonte aberto [IPERF](https://iperf.fr/) ferramenta (ou semelhante) para medir o desempenho de rede de aplicativo real.

Consulte o [SAP HANA de resolução de problemas: Desempenho de rede e problemas de conectividade](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) para passos de resolução de problemas detalhada do site.

## <a name="storage"></a>Armazenamento

Da perspectiva do usuário final, um aplicativo (ou o sistema como um todo) é executado lentamente, não está a responder ou pode até mesmo parecer suspender se existirem problemas com o desempenho de e/s. Na **Volumes** separador no SAP HANA Studio, pode ver os volumes anexados e os volumes são utilizados por cada serviço.

![No separador de Volumes no SAP HANA Studio, pode ver os volumes anexados e os volumes são utilizados por cada serviço](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Anexado volumes na parte inferior do ecrã pode ver detalhes dos volumes, como arquivos e as estatísticas de e/s.

![Anexado volumes na parte inferior do ecrã pode ver detalhes dos volumes, como arquivos e as estatísticas de e/s](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Consulte o [SAP HANA de resolução de problemas: E/s relacionados com as principais causas e soluções](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) e [SAP HANA de resolução de problemas: Relacionados causas e soluções de disco](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) para passos de resolução de problemas detalhada do site.

## <a name="diagnostic-tools"></a>Ferramentas de diagnóstico

Executar uma verificação de estado de funcionamento do SAP HANA através do HANA\_configuração\_Minichecks. Essa ferramenta devolve potencialmente críticos problemas técnicos que devem já ter sido gerados como alertas no SAP HANA Studio.

Consulte a [1969700 de n. º de nota SAP – recolha de instrução de SQL para o SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) e transfira o ficheiro SQL Statements.zip anexado a essa nota. Store este ficheiro. zip no disco rígido local.

No SAP HANA Studio, sobre o **informações do sistema** separador, clique com o botão direito no **nome** coluna e selecione **instruções SQL de importação**.

![No SAP HANA Studio, no separador de informações do sistema, clique com o botão direito na coluna nome e selecione instruções SQL de importação](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Selecione o ficheiro SQL Statements.zip armazenado localmente e uma pasta com as instruções de SQL correspondentes será importada. Neste momento, as verificações de diagnóstico muitos diferentes podem ser executadas com estas instruções SQL.

Por exemplo, para testar os requisitos de largura de banda de replicação do sistema do SAP HANA, com o botão direito a **largura de banda** instrução em **replicação: Largura de banda** e selecione **aberto** na consola de SQL.

A instrução SQL completa abre-se de que permite que os parâmetros de entrada (secção de modificação) para ser alterada e, em seguida, executados.

![A instrução SQL completa é aberta que permite que os parâmetros de entrada (secção de modificação) para ser alterada e, em seguida, executados](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Outro exemplo é right-clicking nas instruções em **replicação: Descrição geral**. Selecione **Execute** no menu de contexto:

![Outro exemplo é right-clicking nas instruções em replicação: Descrição geral. Selecione a Execute no menu de contexto](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Isso resulta em informações que ajuda na resolução de problemas:

![Isto resultará num informações que ajudarão na resolução de problemas](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Faça o mesmo para HANA\_Configuration\_Minichecks e verificação de qualquer _X_ marca no _C_ coluna (crítico).

Saídas de exemplo:

**HANA\_Configuration\_MiniChecks\_Rev102.01 + 1** para verifica geral SAP HANA.

![HANA\_Configuration\_MiniChecks\_Rev102.01 + 1 para verificações gerais do SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_serviços\_descrição geral** para uma descrição geral de que os serviços de SAP HANA estão atualmente em execução.

![HANA\_serviços\_descrição geral para uma descrição geral de que os serviços de SAP HANA estão atualmente em execução](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_serviços\_estatísticas** para o SAP HANA (CPU, memória, etc.) de informações do serviço.

![HANA\_serviços\_informações do serviço de estatísticas para o SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_Configuration\_descrição geral\_Rev110 +** para obter informações gerais sobre a instância do SAP HANA.

![HANA\_Configuration\_descrição geral\_Rev110 + para obter informações gerais sobre a instância do SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_Configuration\_parâmetros\_Rev70 +** para verificar os parâmetros de SAP HANA.

![HANA\_Configuration\_parâmetros\_Rev70 + para verificar os parâmetros de SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Passos seguintes?**

- Consultar [elevada disponibilidade no SUSE usando o STONITH](ha-setup-with-stonith.md).