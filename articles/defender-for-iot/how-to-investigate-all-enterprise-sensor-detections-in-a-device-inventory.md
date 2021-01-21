---
title: Conheça os dispositivos descobertos por todos os sensores da empresa
description: Utilize o inventário do dispositivo na consola de gestão no local para obter uma visão completa das informações do dispositivo a partir de sensores conectados. Utilize ferramentas de importação, exportação e filtragem para gerir esta informação.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9da5c8c89ee124e527584164b21b096ac815e5ca
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625469"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Investigue todas as deteções de sensores empresariais no inventário do dispositivo

Pode ver as informações do dispositivo a partir de sensores conectados utilizando o inventário do *dispositivo* na consola de gestão no local. Esta funcionalidade dá-lhe uma visão completa de todas as informações da rede. Utilize ferramentas de importação, exportação e filtragem para gerir esta informação. As informações sobre o estado das versões do sensor conectado também aparecem.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Screenshot da tabela de dados de inventário do dispositivo.":::

A tabela seguinte descreve as colunas de tabela no inventário do dispositivo.

| Parâmetro | Descrição |
|--|--|
| **Alertas não reconhecidos** | O número de alertas não manipulados associados a este dispositivo. |
| **Unidade de Negócios** | A unidade de negócio que contém este dispositivo. |
| **Região** | A região que contém este dispositivo. |
| **Site** | O site que contém este dispositivo. |
| **Zona** | A zona que contém este dispositivo. |
| **Aparelho** | O Azure Defender para sensor IoT que protege este dispositivo. |
| **Nome** | O nome deste dispositivo como Defender para IoT descobriu-o. |
| **Tipo** | O tipo de dispositivo, como PLC ou HMI. |
| **Fornecedor** | O nome do fornecedor do dispositivo, tal como definido no endereço MAC. |
| **Sistema operativo** | O SO do dispositivo. |
| **Firmware** | O firmware do dispositivo. |
| **Endereço IP** | O endereço IP do dispositivo. |
| **Rio VLAN** | O VLAN do dispositivo. |
| **Endereço MAC** | O endereço MAC do dispositivo. |
| **Protocolos** | Os protocolos que o dispositivo utiliza. |
| **Alertas não reconhecidos** | O número de alertas não manipulados associados a este dispositivo. |
| **Está Autorizado** | O estado de autorização do dispositivo:<br />- **Verdade**: O aparelho foi autorizado.<br />- **Falso**: O aparelho não foi autorizado. |
| **É conhecido como Scanner** | Se este dispositivo realiza atividades semelhantes a digitalização na rede. |
| **É dispositivo de programação** | Se este é um dispositivo de programação:<br />- **Verdade**: O dispositivo realiza atividades de programação para PLCs, RTUs e controladores, que são relevantes para as estações de engenharia.<br />- **Falso**: O aparelho não é um dispositivo de programação. |
| **Grupos** | Grupos em que este dispositivo participa. |
| **Última Atividade** | A última atividade que o dispositivo realizou. |
| **Descoberto** | Quando este dispositivo foi visto pela primeira vez na rede. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrar dados no inventário de dispositivos empresariais

As capacidades de integração de dados permitem-lhe melhorar os dados no inventário do dispositivo com informações de outros recursos empresariais. Estas fontes incluem CMDBs, DNS, firewalls e APIs web.

Pode usar esta informação para aprender. Por exemplo:

- Datas de compra do dispositivo e datas de fim de garantia

- Utilizadores responsáveis por cada dispositivo

- Bilhetes abertos para dispositivos

- A última data em que o firmware foi atualizado

- Dispositivos autorizados a aceder à internet

- Dispositivos que executam aplicações antivírus ativas

- Utilizadores inscritos em dispositivos

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Tabela de dados no ecrã de inventário do dispositivo.":::

Pode integrar dados por qualquer um dos dois:

- Adicionando-o manualmente

- Executando scripts personalizados que o Defender para IoT fornece

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagrama do integrador de dados da empresa.":::

Pode trabalhar com o Defender para suporte técnico IoT para configurar o seu sistema para receber consultas de API web.

Para adicionar dados manualmente:

1. No menu lateral, selecione **o Inventário do Dispositivo** e, em seguida, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Edite as definições de inventário do seu dispositivo.":::

2. Na caixa de diálogo **de definições** de inventário do dispositivo, selecione **ADD CUSTOM COLUMN**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Adicione uma coluna personalizada ao seu inventário.":::

3. Na caixa de diálogo **'Adicionar Coluna Personalizada',** adicione o nome da nova coluna (até 250 caracteres UTF), selecione **Manual** e selecione **SAVE**. O novo item aparece na caixa de diálogo **de configurações** de inventário do dispositivo.

4. No canto superior direito da janela de inventário do **dispositivo,** selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: e selecione **Export All Device Inventory**. O ficheiro CSV é gerado.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="O ficheiro CSV exportado.":::

5. Adicione manualmente as informações à nova coluna e guarde o ficheiro.

6. No canto superior direito da janela de inventário do **dispositivo,** selecione, :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: selecione **Colunas de Entrada Manual de Importação** e navegue no ficheiro CSV. Os novos dados aparecem na tabela **de Inventário de Dispositivos.**

Para integrar dados de outras entidades empresariais:

1. No canto superior direito da janela de inventário do **dispositivo,** selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: e selecione **Export All Device Inventory**.

2. Na caixa de diálogo **de definições** de inventário do dispositivo, selecione **ADD CUSTOM COLUMN**.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Adicione uma coluna personalizada ao seu inventário.":::

3. Na caixa de diálogo **'Adicionar Coluna Personalizada',** adicione o nome da nova coluna (até 250 caracteres UTF) e, em seguida, selecione **Automatic**. Aparecem as opções **de SCRIPT** DE UPLOAD e TEST **SCRIPT.**

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Adicione automaticamente colunas personalizadas.":::

4. Faça upload e teste o script que recebeu do [Microsoft Support](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="retrieve-information-from-the-device-inventory"></a>Recuperar informações do inventário do dispositivo

Pode recuperar uma vasta gama de informações de dispositivos detetadas por sensores geridos e integrar essa informação com sistemas parceiros. Por exemplo, pode recuperar sensor, zona, ID do site, endereço IP, endereço MAC, firmware, protocolo e informações do fornecedor. Filtrar informações que obtém com base em:

- Dispositivos autorizados e não autorizados.

- Dispositivos associados a sites específicos.

- Dispositivos associados a zonas específicas.

- Dispositivos associados a sensores específicos.

Trabalhe com o Defender para comandos IoT API para recuperar e integrar esta informação. Para obter mais informações, consulte [Defender para OIT sensor API e consola de gestão APIs](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtrar o inventário do dispositivo

Pode filtrar o inventário do dispositivo para mostrar colunas de interesse. Por exemplo, pode ver as informações do dispositivo PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Screenshot do inventário do dispositivo.":::

O filtro está limpo quando sai da janela.

Para utilizar o mesmo filtro várias vezes, pode guardar um filtro ou uma combinação de filtros de que necessita. Pode abrir um painel esquerdo e ver os filtros que guardou:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Tela de inventários de dispositivos.":::

Para filtrar o inventário do dispositivo:

1. Na coluna que pretende filtrar, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. Na caixa de diálogo **filtro,** selecione o tipo de filtro:

   - **Igual:** O valor exato segundo o qual pretende filtrar a coluna. Por exemplo, se filtrar a coluna de protocolo de acordo com **o Equals** `value=ICMP` e, a coluna apresentará dispositivos que utilizam apenas o protocolo ICMP.

   - **Contém**: O valor que está contido entre outros valores na coluna. Por exemplo, se filtrar a coluna de protocolo de acordo com **o Contém** `value=ICMP` e, a coluna apresentará dispositivos que utilizam o protocolo ICMP como parte da lista de protocolos que o dispositivo utiliza.

3. Para organizar a informação da coluna de acordo com a ordem alfabética, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Organize a encomenda selecionando as :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: setas e :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: as setas.

4. Para guardar um novo filtro, defina o filtro e **selecione Save As**.

5. Para alterar as definições do filtro, altere as definições e selecione **Guardar Alterações**.

## <a name="view-device-information-per-zone"></a>Ver informações do dispositivo por zona

Pode aprender as seguintes informações sobre dispositivos numa zona.

### <a name="view-a-device-map"></a>Ver um mapa de dispositivo

Para visualizar um mapa do dispositivo sensor para uma zona selecionada:

- Na janela **'Gestão** do Site', selecione **'Ver Mapa'** da barra que contém o nome da zona.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Região padrão para unidade de negócio padrão.":::

A janela **do mapa do dispositivo** aparece. Mostra todos os elementos de rede relacionados com a zona selecionada, incluindo os sensores, os dispositivos ligados a eles e outras informações.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Screenshot do mapa da zona.":::

As seguintes ferramentas estão disponíveis para visualizar dispositivos e informações do dispositivo a partir do mapa. Para obter mais informações sobre cada uma destas funcionalidades, consulte o *guia do utilizador da plataforma Defender para IoT*.

- **Vistas de zoom do** mapa : Vista simplificada, vista de conexões e vista detalhada. A vista do mapa apresentado varia consoante o nível de zoom do mapa. Alterna entre as vistas do mapa ajustando os níveis de zoom.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Ferramentas de pesquisa e layout** de mapas : Ferramentas utilizadas para exibir segmentos de rede variados, dispositivos, grupos de dispositivos ou camadas.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Screenshot da vista Ferramentas de Procura e Layout.":::

- **Etiquetas e indicadores nos dispositivos:** Por exemplo, o número de dispositivos agrupados numa sub-rede numa rede de TI. Neste exemplo, são 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Screenshot de etiquetas e indicadores.":::

- **Ver as propriedades** do dispositivo : Por exemplo, o sensor que está a monitorizar o dispositivo e as propriedades básicas do dispositivo. Clique com o botão direito para ver as propriedades do dispositivo.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Screenshot da vista propriedades do dispositivo.":::

- **Alerta associado a um dispositivo:** Clique com o botão direito no dispositivo para visualizar alertas relacionados.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Screenshot da vista 'Show Alerts'.":::

### <a name="view-alerts-associated-with-a-zone"></a>Ver alertas associados a uma zona

Para visualizar alertas associados a uma zona específica:

- Selecione o ícone de alerta formulário a janela **Zona.** 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="A vista padrão da Unidade de Negócio com exemplos.":::

Para mais informações, consulte [a Visão Geral: Trabalhar com alertas.](how-to-work-with-alerts-on-premises-management-console.md)

### <a name="view-the-device-inventory-of-a-zone"></a>Ver o inventário do dispositivo de uma zona

Para visualizar o inventário do dispositivo associado a uma zona específica:

- Selecione O Inventário do Dispositivo de **Visualização** a partir da janela **Zona.**

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Aparecerá o ecrã de inventário do dispositivo.":::

Para obter mais informações, consulte [investigar todas as deteções de sensores empresariais num inventário de dispositivos.](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

### <a name="view-additional-zone-information"></a>Ver informações adicionais sobre zona

Estão disponíveis as seguintes informações adicionais sobre zonas:

- **Detalhes da zona**: Ver o número de dispositivos, alertas e sensores associados à zona.

- **Detalhes do sensor**: Ver o nome, endereço IP e versão de cada sensor atribuído à zona.

- **Estado da conectividade**: Se um sensor estiver desligado, ligue-o do sensor. Consulte [os sensores de ligação à consola de gestão no local.](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console) 

- **Atualização do progresso**: Se o sensor ligado estiver a ser atualizado, aparecerão os estados de atualização. Durante a atualização, a consola de gestão no local não recebe informações do dispositivo do sensor.

## <a name="see-also"></a>Ver também

[Investigar as deteções do sensor num inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
