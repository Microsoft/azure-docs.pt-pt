---
title: Obtenha informação sobre os dispositivos descobertos por um sensor específico
description: O inventário do dispositivo apresenta uma vasta gama de atributos do dispositivo que um sensor deteta.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 4daec83f44a545d7837a7e73e847f56b1f5770e7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782100"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Investigar as deteções do sensor num inventário de dispositivos

O inventário do dispositivo apresenta uma vasta gama de atributos do dispositivo que um sensor deteta. As opções estão disponíveis para:

 - Filtrar facilmente a informação.

 - Informação de exportação para um ficheiro CSV.

 - Importar detalhes do registo do Windows.

 - Crie grupos para visualização no mapa do dispositivo.

## <a name="view-device-attributes-in-the-device-inventory"></a>Ver atributos do dispositivo no inventário do dispositivo

Os seguintes atributos aparecem na tabela de inventário do dispositivo.

| Parâmetro | Descrição |
|--|--|
| Nome | O nome do dispositivo tal como o sensor o descobriu, ou como introduzido pelo utilizador. |
| Tipo | O tipo de dispositivo determinado pelo sensor, ou conforme introduzido pelo utilizador. |
| Fornecedor | O nome do fornecedor do dispositivo, tal como definido no endereço MAC. |
| Sistema Operativo | O SO do dispositivo, se detetado. |
| Versão do firmware | O firmware do dispositivo, se for detetado. |
| Endereço IP | O endereço IP do dispositivo quando definido. |
| Rio VLAN | O VLAN do dispositivo. Para obter mais informações sobre a instrução do sensor para descobrir VLANs, consulte [os nomes de Definição VLAN](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (como definir-gestão-gestão-consola-definições de rede.md#define-vlan-names). |
| Endereço MAC | O endereço MAC do dispositivo. |
| Protocolos | Os protocolos que o dispositivo utiliza. |
| Alertas não reconhecidos | O número de alertas não reconhecidos associados a este dispositivo. |
| Está Autorizado | O estado de autorização definido pelo utilizador:<br />- **Verdade**: O aparelho foi autorizado.<br />- **Falso**: O aparelho não foi autorizado. |
| É conhecido como Scanner | Definido como um dispositivo de digitalização de rede pelo utilizador. |
| É dispositivo de programação | Definido como um dispositivo de programação autorizado pelo utilizador. <br />- **Verdade**: O dispositivo realiza atividades de programação para PLCs, RTUs e controladores, que são relevantes para as estações de engenharia. <br />- **Falso**: O aparelho não é um dispositivo de programação. |
| Grupos | Os grupos em que este dispositivo participa. |
| Última Atividade | A última atividade que o dispositivo realizou. |
| Discovered | Quando este dispositivo foi visto pela primeira vez na rede. |

Para visualizar o inventário do dispositivo:

1. No painel esquerdo, selecione **Dispositivos**. O **painel de dispositivos** abre-se à direita.

2. No **painel de dispositivos,** selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Para ocultar e exibir colunas, personalize a tabela de inventário do dispositivo:

1. No menu superior direito do inventário do dispositivo, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Tela de configurações de inventário do dispositivo.":::

2. Na janela **Definições de Inventário** do Dispositivo, selecione as colunas que pretende exibir na tabela de inventário do dispositivo.

3. Altere a localização das colunas na tabela utilizando setas.

4. Selecione **Guardar**. A janela **de configuração do inventário** do dispositivo fecha-se e as novas definições aparecem na tabela.

### <a name="create-temporary-device-inventory-filters"></a>Criar filtros de inventário de dispositivos temporários

Pode definir um filtro que defina a informação que a tabela exibe. Por exemplo, pode decidir que pretende ver apenas as informações do dispositivo PLC.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Dispositivos a aprender.":::

O filtro não é guardado quando sai do inventário.

### <a name="save-device-inventory-filters"></a>Guardar filtros de inventário de dispositivos

Pode guardar um filtro ou uma combinação de filtros de que necessita e reaplicá-los no inventário do dispositivo. Crie filtros mais amplos com base num determinado tipo de dispositivo, ou filtros mais estreitos com base num tipo específico e num protocolo específico.

Os filtros que guarda também são guardados como grupos de mapas de dispositivos. Esta funcionalidade fornece um nível adicional de granularidade na visualização de dispositivos de rede no mapa.

Para criar filtros:

1. Na coluna que pretende filtrar, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. Na caixa de diálogo **filtro,** selecione o tipo de filtro:

   - **Igual:** O valor exato segundo o qual pretende filtrar a coluna. Por exemplo, se filtrar a coluna de protocolo de acordo com **o Equals** `value=ICMP` e, a coluna apresentará dispositivos que utilizam apenas o protocolo ICMP.

   - **Contém**: O valor que está contido entre outros valores na coluna. Por exemplo, se filtrar a coluna de protocolo de acordo com **o Contém** `value=ICMP` e, a coluna apresentará dispositivos que utilizam o protocolo ICMP como parte da lista de protocolos que o dispositivo utiliza.

3. Para organizar a informação da coluna de acordo com a ordem alfabética, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Organize a encomenda selecionando as :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: setas e :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: as setas.

4. Para guardar um novo filtro, defina o filtro e **selecione Save As**.

5. Para alterar as definições do filtro, altere as definições e selecione **Guardar Alterações**.

Para ver filtros:

- Abra o painel esquerdo e veja os filtros que guardou:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="Ver os filtros do painel do lado esquerdo.":::

### <a name="view-filtered-information-as-a-map-group"></a>Ver informação filtrada como um grupo de mapas

Quando muda para a vista do mapa, os dispositivos filtrados são realçados e filtrados. O grupo de filtros que guardou aparece no menu lateral sob o grupo **de filtros de inventário** do dispositivo.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Ver filtros quando estiver na vista do mapa.":::

## <a name="learn-windows-registry-details"></a>Saiba detalhes do registo do Windows

Além de aprender dispositivos OT, pode descobrir estações de trabalho e servidores do Microsoft Windows. Estes dispositivos também são apresentados no Inventário do Dispositivo. Depois de aprender dispositivos, pode enriquecer o Inventário do Dispositivo com informações detalhadas do Windows, tais como:

- Versão do Windows instalada

- Aplicações instaladas

- Informações ao nível do patch

- Abrir portas

- Informação mais robusta nas versões de SO

Estão disponíveis duas opções para a recuperação desta informação:

- Sondagem ativa utilizando scans de WMI programados. 

- Levantamento local distribuindo e executando um script no dispositivo. Trabalhar com scripts locais ignora os riscos de correr as sondagens do WMI num ponto final. Também é útil para redes reguladas com cachoeiras e elementos unidirecionais.

Este artigo descreve como fazer um levantamento local do registo de ponto final do Windows com um script. Estas informações serão usadas para gerar alertas, notificações, relatórios de mineração de dados, avaliações de risco e relatórios de vetores de ataque.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Imagem de mineração de dados.":::

Pode pesquisar os seguintes sistemas operativos Windows:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Antes de começar

Para trabalhar com o script, você precisa cumprir os seguintes requisitos:

- As permissões do administrador são necessárias para executar o script no dispositivo.

- O sensor já devia ter aprendido o dispositivo Windows. Isto significa que, se o dispositivo já existir, o script irá recuperar as suas informações.

- Um sensor está a monitorizar a rede a que o Pc do Windows está ligado.

### <a name="acquire-the-script"></a>Adquira o guião

Para receber o script, contacte o [suporte do cliente.](mailto:support.microsoft.com)

### <a name="deploy-the-script"></a>Implementar o script

Pode implementar o script uma vez ou agendar consultas em curso utilizando métodos e ferramentas de implementação automatizados padrão.

### <a name="about-the-script"></a>Sobre o guião

- O script é executado como um utilitário e não como um programa instalado. Executar o script não afeta o ponto final.

- Os ficheiros que o script gera permanecem na unidade local até os eliminar.

- Os ficheiros que o script gera estão localizados ao lado um do outro. Não os separe.

- Se voltar a executar o script no mesmo local, estes ficheiros são substituídos.

Para executar o script:  

1. Copie o guião para uma unidade local e desaperte-o. Aparecem os seguintes ficheiros:

    - start.bat

    - settings.jsem

    - dados.bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="Ver os ficheiros no Explorador de Ficheiros.":::

2. Executar o `run.bat` arquivo.

3. Após a sondação do registo, o ficheiro CX-snapshot aparece com a informação do registo.

4. O nome do ficheiro indica o nome do sistema e a data e hora do instantâneo. Um nome de ficheiro de exemplo é `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Detalhes do dispositivo de importação

As informações aprendidas em cada ponto final devem ser importadas para o sensor.

Os ficheiros gerados a partir das consultas podem ser colocados numa pasta a que pode aceder a partir de sensores. Utilize métodos e ferramentas automatizados padrão para mover os ficheiros de cada ponto final do Windows para o local onde os irá importar para o sensor.

Não atualize os nomes dos ficheiros.

Importar:

1. Selecione **Definições** de importação a partir da caixa de diálogo **de configuração do Windows import.**

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importe as configurações do Windows.":::

2. **Selecione Adicionar** e, em seguida, selecione todos os ficheiros (Ctrl+A).

3. Selecione **Fechar**. A informação do registo do dispositivo é importada. Se houver algum problema em carregar um dos ficheiros, será informado de que ficheiro falhou.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="O upload de ficheiros adicionados foi bem sucedido.":::

## <a name="export-device-inventory-information"></a>Informações sobre inventário de dispositivos de exportação

Pode exportar informações de inventário de dispositivos para um ficheiro Excel.

Para exportar um ficheiro CSV:

- No menu superior direito do inventário do dispositivo, selecione :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . O relatório do CSV é gerado e descarregado.

## <a name="see-also"></a>Ver também

[Investigar todas as deteções do sensor empresarial num inventário de dispositivos](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Trabalhar com vistas do mapa do site](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
