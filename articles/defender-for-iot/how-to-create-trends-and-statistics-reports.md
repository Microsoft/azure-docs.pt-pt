---
title: Gerar tendências e relatórios estatísticos
description: Obtenha informações sobre a atividade da rede, estatísticas e tendências utilizando widgets defender para tendências e estatísticas de IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 2/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 7cb3848f3711b6bf10c316ba4f5321286e2260fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706484"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendências dos sensores e relatórios estatísticos

Você pode criar gráficos de widget e gráficos de tortas para obter informações sobre as tendências e estatísticas da rede. Os widgets podem ser agrupados em dashboards definidos pelo utilizador.

> [!NOTE]
> Os analistas de administrador e de segurança podem criar relatórios de Tendências e Estatísticas.

O painel é composto por widgets que descrevem graficamente os seguintes tipos de informação:

- Tráfego por porto
- Tráfego superior por porto
- Largura de banda do canal
- Largura de banda total
- Conexão TCP ativa
- Largura de banda de topo por VLAN
- Dispositivos:
  - Novos dispositivos
  - Dispositivos ocupados
  - Dispositivos por fornecedor
  - Dispositivos por SISTEMA
  - Número de dispositivos por VLAN
  - Dispositivos desligados
- Conectividade cai por horas
- Alertas para incidentes por tipo
- Acesso à tabela de bases de dados
- Widgets de dissecação de protocolo
- DELTAV
  - Distribuição de operações deltaV roc
  - Eventos deltav roc pelo nome
  - Eventos DeltaV por tempo
- AMS
  - Tráfego AMS por porta de servidor
  - Tráfego AMS por comando
- Endereço Ethernet e IP:
  - Tráfego de endereços Ethernet e IP pelo serviço CIP
  - Tráfego de endereços Ethernet e IP por classe CIP
  - Tráfego de endereços Ethernet e IP por comando
- OPC:
  - Operações de gestão de topo da OPC
  - Operações de E/S de topo da OPC
- Siemens S7:
  - Tráfego S7 por função de controlo
  - Tráfego S7 por subfunção
- Rio VLAN
  - Número de dispositivos por VLAN
  - Largura de banda superior por VLAN
- 60870-5-104
  - Tráfego IEC-60870 pela ASDU
- BACNET
  - Serviços BACnet
- DNP3
  - Tráfego DNP3 por função
- SRTP:
  - Tráfego SRTP por código de serviço
  - Erros SRTP de dia
- SuiteLink:
  - Etiquetas de topo suiteLink
  - Comportamento da etiqueta numérica SuiteLink
- Tráfego IEC-60870 pela ASDU
- Tráfego DNP3 por função
- Tráfego MMS por serviço
- Modbus tráfego por função
- Tráfego OPC-UA por serviço

> [!NOTE]
>  O tempo nos widgets é definido de acordo com o tempo do sensor.

## <a name="create-reports"></a>Criar relatórios

Para ver painéis e widgets:

Selecione **Tendências & Estatísticas** no menu lateral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Screenshot de uma investigação.":::

Por predefinição, os resultados são apresentados para deteções ao longo dos últimos sete dias. Pode utilizar ferramentas de filtro para alterar esta gama. Por exemplo, uma pesquisa gratuita.

## <a name="create-a-dashboard"></a>Criar um dashboard

Crie um novo painel de instrumentos selecionando o menu drop-down **do Dashboard.** Pode criar e adicionar o máximo de widgets a um dashboard.

Pode criar dashboards personalizados utilizando as seguintes opções:

- Adicione um widget ao painel de instrumentos

- Elimine um widget do painel de instrumentos

- Modificar o filtro de um widget

- Redimensione um widget

- Alterar a localização de um widget

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Mude a localização de um widget.":::

Para criar um dashboard personalizado:

1. Selecione **Tendências e Estatísticas** do painel esquerdo.

1. Selecione o menu de drop down **do Painel select e** selecione o botão Criar painel de **instrumentos.**

1. Introduza um nome significativo para o novo painel de instrumentos e selecione **Criar**.

1. **Selecione Adicionar Widget** no topo esquerdo da página.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selecione o widget da loja widget.":::

1. **Segurança** e widgets **operacionais** estão disponíveis no topo direito da janela. Escolha entre várias categorias e protocolos. Uma breve descrição com um gráfico em miniatura aparece com cada widget. Utilize a barra de deslocação para ver todos os widgets disponíveis.

1. Selecione um widget utilizando o botão **Click to Add.** O widget é imediatamente exibido no painel de instrumentos.

Para eliminar um dashboard:

1. Selecione o nome do painel de instrumentos no menu suspenso.

1. Selecione o ícone **Eliminar** e, em seguida, selecione **OK**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Selecione o ícone de eliminação para eliminar o painel de instrumentos.":::

Para editar um nome de painel de instrumentos:

1. Selecione o nome do painel de instrumentos no menu suspenso.

1. Selecione o ícone **Editar.**
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Selecione o ícone de edição para editar o nome do seu painel de instrumentos.":::

1. Introduza um novo nome para o painel de instrumentos e **selecione Save**.
 
Para definir o painel padrão:

1. Selecione o nome do painel de instrumentos no menu suspenso.

1. Selecione o ícone **Star** para selecionar o painel de instrumentos a definir como o painel padrão.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Selecione o ícone da estrela para escolher o seu painel de instrumentos padrão."::: 

Para modificar os dados de filtragem num widget:

1. Selecione o ícone **Filtro.**

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Selecione o ícone do filtro para definir parâmetros para o seu widget.":::

1. Editar os parâmetros necessários.

1. Selecione **OK**.

Para eliminar um widget:

- Selecione o :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: ícone.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Selecione o X para eliminar o widget.":::

## <a name="creating-widgets"></a>Criação de widgets 

A Loja Widget permite-lhe selecionar widgets por categoria e protocolo. Pode exibir os widgets **de Segurança** ou **Operacionais** disponíveis selecionando-os.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Selecione o seu widget na Loja Widget.":::

Cada widget contém informações específicas sobre o tráfego do sistema, estatísticas de rede, estatísticas de protocolos, dispositivos e informações de alerta. Uma mensagem é exibida quando não há dados para um widget.

Você pode remover uma secção da tarte, em um gráfico de torta, para ver o significado relativo das fatias restantes mais claramente. Selecione o nome da fatia na legenda na parte inferior do ecrã para fazer isto.

As secções seguintes apresentam exemplos de casos de utilização para alguns dos widgets.

### <a name="busy-devices-widget"></a>Widget de dispositivos ocupados

Este widget lista os cinco dispositivos mais movimentados. No modo **Editar,** pode filtrar por protocolos conhecidos.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="Uma vista do widget do dispositivo ocupado.":::

### <a name="total-bandwidth-widget"></a>Widget total de largura de banda

Este widget rastreia a largura de banda em Mbps (megabits por segundo). A largura de banda é indicada no eixo y, com a data aparecendo no eixo x. O modo **de edição** permite filtrar resultados de acordo com o Cliente, Servidor, Porta do Servidor ou Sub-rede. Uma ponta de ferramenta aparece quando se paira sobre o cursor sobre o gráfico.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="Uma vista do widget total de largura de banda.":::

### <a name="channels-bandwidth-widget"></a>Widget de largura de banda dos canais

Este widget exibe os cinco principais canais de tráfego. Pode filtrar por Endereço e definir o número de Resultados Apresentados. Selecione a seta para baixo para mostrar mais canais.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="Uma vista dos canais widget de largura de banda.":::

### <a name="traffic-by-port-widget"></a>Tráfego por widget portuário

Este widget exibe o tráfego por porta, que é indicado por um gráfico de tartes com cada porta designada por uma cor diferente. A quantidade de tráfego em cada porta é proporcional ao tamanho da sua parte da tarte.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="Uma vista do tráfego por widget port.":::

### <a name="new-devices-widget"></a>Widget de novos dispositivos

Este widget apresenta o novo gráfico de barras de dispositivos, o que indica quantos novos dispositivos foram descobertos numa determinada data.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Uma visão do widget dos novos dispositivos.":::

### <a name="protocol-dissection-widgets"></a>Widgets de dissecação de protocolo

Este widget apresenta um gráfico de tartes que lhe proporciona uma olhada no tráfego de protocolo, dissecado por códigos de função e serviços. O tamanho de cada fatia da tarte é proporcional à quantidade de tráfego em relação às outras fatias.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="Uma visão do widget de dissecação do protocolo.":::

### <a name="active-tcp-connections-widget"></a>Widget de conexões TCP ativo

Este widget apresenta um gráfico que mostra o número de ligações TCP ativas no sistema.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Uma visão do widget de ligações TCP ativo.":::

### <a name="incident-by-type-widget"></a>Incidente por widget tipo

Este widget apresenta um gráfico de tartes que mostra o número de incidentes por tipo. Este é o número de alertas gerados por cada motor durante um período de tempo predefinido.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Uma visão do incidente por widget tipo.":::

## <a name="devices-by-vendor-widget"></a>Dispositivos por widget do fornecedor

Este widget apresenta um gráfico de tartes que mostra o número de dispositivos por fornecedor. O número de dispositivos para um fornecedor específico é proporcional ao tamanho da parte do fornecedor desse dispositivo relativamente a outros fornecedores de dispositivos.

## <a name="number-of-devices-per-vlan-widget"></a>Número de dispositivos por widget VLAN

Este widget apresenta um gráfico de tartes que mostra o número de dispositivos descobertos por VLAN. O tamanho de cada fatia da tarte é proporcional ao número de dispositivos descobertos em relação às outras fatias.

Cada VLAN aparece com a etiqueta VLAN atribuída pelo sensor ou nome que adicionou manualmente.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="Uma visão do número de dispositivos widget.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Largura de banda superior por widget VLAN

Este widget exibe o consumo de largura de banda pela VLAN. Por padrão, o widget mostra cinco VLANs com o maior uso de largura de banda.

Pode filtrar os dados pelo período apresentado no widget. Selecione a seta para baixo para mostrar mais resultados.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="Uma vista da largura de banda superior por widget VLAN.":::

## <a name="system-report"></a>Relatório do sistema

Para descarregar o relatório do sistema: 

1. Selecione **Tendências & Estatísticas** no menu lateral.

1. Selecione **Relatório do Sistema** no canto superior direito. O relatório será descarregado automaticamente.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Selecione o botão relatório do sistema para fazer o download de uma cópia do relatório do sistema.":::

O Relatório do Sistema é um ficheiro PDF que contém todos os dados do sistema:

  - Dispositivos

  - Alertas

  - Informação sobre política de rede

## <a name="devices-in-a-system-report"></a>Dispositivos num relatório do sistema 

O Relatório do Sistema mostra uma lista de todos os dispositivos e as suas informações. Por exemplo, Tipo, Nome e Protocolos utilizados. O Relatório do Sistema também mostra uma lista de dispositivos por fornecedor.

## <a name="alerts-in-system-report"></a>Alertas no relatório do sistema 

O Relatório do Sistema mostra uma lista de todos os alertas com as suas informações, tais como Date e Severidade.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="Uma visão dos alertas nos relatórios do sistema.":::

## <a name="network-information-in-system-report"></a>Informação de rede no relatório do sistema 

O Relatório do Sistema mostra em detalhe, a sua linha de base de rede. Por exemplo, código de função DNP3 e portas abertas por ligação.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="Uma visão da função DNP3 do relatório do sistema.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="Uma vista da porta aberta por relatório de ligação.":::

## <a name="see-also"></a>Ver também

[Relatórios de avaliação de](how-to-create-risk-assessment-reports.md) 
 riscos [Consultas de mineração de dados de sensores](how-to-create-data-mining-queries.md) 
 [Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)
