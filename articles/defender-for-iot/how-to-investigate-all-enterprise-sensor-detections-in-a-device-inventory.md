---
title: Conheça os dispositivos descobertos por todos os sensores da empresa
description: Utilize o inventário do dispositivo na consola de gestão no local para obter uma visão completa das informações do dispositivo a partir de sensores conectados. Utilize ferramentas de importação, exportação e filtragem para gerir esta informação.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: 0ae59123b59cfb54cba2a2ee9bdeefb411c8793b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782185"
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

## <a name="next-steps"></a>Passos seguintes

[Investigar as deteções do sensor num inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
