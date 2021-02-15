---
title: Trabalhar com alertas na consola de gestão no local
description: Utilize a consola de gestão no local para obter uma visão da empresa das ameaças recentes na sua rede e compreenda melhor como os utilizadores de sensores estão a lidar com as mesmas.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 878b1b2d5ba13b68b5122e4b9cffc3c408e211e2
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526328"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Trabalhar com alertas na consola de gestão no local 

Pode fazer o seguinte a partir da janela **Alertas** na consola de gestão:

- Trabalhar com filtros de alerta

- Trabalhar com balcões de alerta

- Ver informações de alerta

- Gerir eventos de alerta

- Criar regras de exclusão de alerta

- Desencadear regras de exclusão de alerta de sistemas externos

- Acelerar o fluxo de trabalho de incidentes com grupos de alerta

## <a name="view-alerts-in-the-on-premises-management-console"></a>Ver alertas na consola de gestão no local

A consola de gestão no local agrega alertas de todos os sensores conectados. Isto fornece uma visão empresarial das ameaças recentes na sua rede e ajuda-o a entender melhor como os utilizadores de sensores estão a lidar com as mesmas.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Screenshot da janela Alertas.":::

### <a name="work-with-alert-filters"></a>Trabalhar com filtros de alerta

A janela **Alertas** apresenta os alertas gerados por sensores ligados à sua consola de gestão no local. Pode ver todos os alertas para sensores conectados ou apresentar os alertas enviados a partir de um específico:

- Site

- Zona

- Dispositivo

- Sensor

Selecione **Filtros Limpos** para visualizar todos os alertas.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Limpe os filtros selecionando o botão Filtros Limpos.":::

### <a name="work-with-alert-counters"></a>Trabalhar com balcões de alerta

Os balcões de alerta fornecem uma avaria de alertas por gravidade e pelo estado reconhecido.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="O contador de alerta mostra quantos alertas tem.":::

Os seguintes níveis de gravidade aparecem no contador de alerta:

- **Crítico**: Indica um ataque malicioso que deve ser tratado imediatamente.

- **Major:** Indica uma ameaça de segurança que é importante enfrentar.

- **Menor**: Indica algum desvio do comportamento de base que pode conter uma ameaça à segurança.

- **Aviso**: Indica algum desvio do comportamento de base sem ameaças à segurança.

Os níveis de severidade são predefinidos.

Pode ajustar o contador para fornecer números baseados em alertas reconhecidos e não reconhecidos. Os alertas não reconhecidos foram desencadeados no Defender para sensores IoT, mas ainda não foram revistos pelos operadores do sensor.

Quando a opção **'Alertas Reconhecidos'** é selecionada, todos os alertas reconhecidos aparecem na janela **Alertas.**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Veja os seus alertas reconhecidos.":::

### <a name="view-alert-information"></a>Ver informações de alerta

O alerta apresenta as seguintes informações:

- Um resumo do evento de alerta.

- Gravidade do alerta.

- Uma ligação com o alerta no sensor que o detetou.

- Um alerta UUID. O UUID consiste no ID de alerta que está associado ao evento de alerta detetado no sensor, separado por um hífen e seguido por um número único de ID do sistema.

**Consola de gestão no local Alerta UUID**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Um dispositivo está ligado, mas não autorizado.":::

**ID de alerta de sensor**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="Identificação de alerta de sensor.":::

Trabalhar com uUIDs garante que cada alerta exibido na consola de gestão no local é pesmável e identificável por um número único. Isto é necessário porque os alertas gerados a partir de vários sensores podem produzir o mesmo ID de alerta.

> [!NOTE]
> Por padrão, os UUIDs são apresentados nos seguintes sistemas parceiros quando as regras de encaminhamento são definidas: ArcSight, servidores syslog, QRadar, Sentinel e NetWitness. Não é necessária uma configuração.

Para ver informações de alerta:

- Na lista de alerta, selecione um alerta.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Screenshot de informação de alerta.":::

Para ver o alerta no sensor:

- Selecione **O SENSOR ABERTO** a partir do alerta.

Para ver os dispositivos num mapa de zona:

- Para visualizar o mapa do dispositivo com foco no dispositivo alertado e em todos os dispositivos que lhe estão ligados, selecione **DISPOSITIVOS SHOW**.

## <a name="manage-alert-events"></a>Gerir eventos de alerta

Várias opções estão disponíveis para gerir eventos de alerta a partir da consola de gestão no local.

- Aprenda ou reconheça eventos de alerta. **Selecione Saiba & Reconheça** a conhecer todos os eventos de alerta que possam ser autorizados e a reconhecer todos os eventos de alerta que não são reconhecidos atualmente.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Selecione Saiba & Reconheça para aprender tudo.":::

- Eventos de alerta mudos e desajeitados.

Para saber mais sobre eventos de aprendizagem, reconhecimento e alerta de silenciamento, consulte o artigo [de alerta de alerta](how-to-manage-the-alert-event.md) do sensor.

## <a name="export-alert-information"></a>Informações sobre alerta de exportação

Informação de alerta de exportação para um ficheiro .csv. Pode exportar informações de todos os alertas detetados ou exportar informações com base na vista filtrada. São exportadas as seguintes informações:

- Endereço de Origem
- Endereço de destino
- Título do alerta
- Gravidade do alerta
- Mensagem de alerta
- Informações adicionais
- Estatuto reconhecido
- Disponibilidade de PCAP

Para exportar:

1. Selecione Alertas do menu lateral.
1. Selecione Export (Exportar).
1. Selecione Alertas Alargados de Exportação para exportar informações de alerta em linhas separadas para cada alerta que cubra vários dispositivos. Quando se seleciona o Export Extended Alerts, o ficheiro .csv criará uma linha duplicada do alerta com os itens únicos em cada linha. A utilização desta opção facilita a investigação de eventos de alerta exportados.  

## <a name="create-alert-exclusion-rules"></a>Criar regras de exclusão de alerta

Instrua o Defender para que ignore os gatilhos de alerta com base em:

- Fusos horários e períodos de tempo

- Endereço do dispositivo (IP, MAC, sub-rede)

- Nomes de alerta

- Um sensor específico

Crie regras de exclusão de alerta quando quiser que o Defender para ioT ignore a atividade que irá desencadear um alerta.

Por exemplo, se souber que todos os dispositivos OT monitorizados por um sensor específico passarão por procedimentos de manutenção durante dois dias, pode definir uma regra de exclusão que instrui o Defender para o IoT a suprimir os alertas detetados por este sensor durante o período pré-definido.

### <a name="alert-exclusion-logic"></a>Lógica de exclusão de alerta

A lógica da regra de alerta `AND` baseia-se. Isto significa que um alerta só será desencadeado quando todas as condições de regra forem cumpridas.

Se uma condição de regra não for definida, a condição incluirá todas as opções. Por exemplo, se não incluir o nome de um sensor na regra, será aplicado a todos os sensores.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Screenshot da visão 'Criar Regra de Exclusão'.":::

Os resumos de regras aparecem na janela **regra de exclusão.**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Screenshot da visão sumária da regra de exclusão.":::

Além de trabalhar com regras de exclusão, pode suprimir os alertas silenciando-os.

### <a name="create-exclusion-rules"></a>Criar regras de exclusão

Criar regras de exclusão:

1. A partir do painel esquerdo da consola de gestão no local, selecione **Exclusão de Alerta**. Defina uma nova regra de exclusão selecionando o ícone **Adicionar** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: no canto superior direito da janela que se abre. Abre-se a caixa de diálogo **'Regra de Exclusão' "Criar" (criação de regras de exclusão).**

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Crie uma exclusão de alerta preenchendo a informação aqui.":::

2. Introduza um nome de regra no campo **Nome.** O nome não pode conter citações ( `"` ).

3. Na secção **por fuso horário/período,** insira um período de tempo dentro de um fuso horário específico. Utilize esta função quando uma regra de exclusão é criada por um período de tempo específico num fuso horário, mas deve ser implementada ao mesmo tempo em outros fusos horários. Por exemplo, pode ser necessário aplicar uma regra de exclusão entre as 8:00 e as 10:00 em três fusos horários diferentes. Neste caso, crie três regras de exclusão distintas que utilizem o mesmo período de tempo e o fuso horário relevante.

4. Selecione **ADD**. Durante o período de exclusão, não são criados alertas nos sensores ligados.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Screenshot da vista por período de tempo.":::

5. Na secção **Por Endereço do Dispositivo,** defina:

  - Endereço IP do dispositivo, endereço MAC ou endereço de sub-rede que pretende excluir.

  - Direção de tráfego para os dispositivos excluídos, fonte e destino.

6. Selecione **ADD**.

7. Na secção **Título de Alerta,** comece a digitar o título de alerta. Da lista de suspensos, selecione o título de alerta ou títulos a excluir.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Screenshot da vista do título por alerta.":::

8. Selecione **ADD**.

9. Na secção **Por Nome do Sensor,** comece a escrever o nome do sensor. A partir da lista de recuos, selecione o sensor ou sensores que pretende excluir.

10. Selecione **ADD**.

11. Selecione **SAVE**. A nova regra aparece na lista de regras.

Pode suprimir alertas silenciando-os ou criando regras de exclusão de alerta. Esta secção descreve potenciais casos de utilização para ambas as funcionalidades.

- **Regra de exclusão**. Escreva uma regra de exclusão quando:

  - Sabe com antecedência que pretende excluir o evento da base de dados. Por exemplo, sabe que o cenário detetado num determinado sensor irá desencadear alertas irrelevantes. Por exemplo, você estará realizando trabalhos de manutenção em PLCs organizacionais em um site específico e quer suprimir alertas relacionados com PLCs para este site.

  - Pretende que o Defender para ioT ignore os eventos por um intervalo de tempo específico (para tarefas de manutenção do sistema).

  - Queres ignorar os acontecimentos numa sub-rede específica.

  - Pretende controlar os eventos de alerta gerados por vários sensores com uma regra.

  - Não pretende acompanhar a exclusão de alerta como um evento no registo do evento.

- **Mudo.** Mude um alerta quando:

  - Não estão planeados objetos que precisam de ser silenciados. Não sabes com antecedência quais os eventos que serão irrelevantes.

  - Pretende suprimir o alerta da janela **Alertas,** mas ainda assim pretende rastreá-lo no registo do evento.

  - Queres ignorar os acontecimentos num canal específico.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Desencadear regras de exclusão de alerta de sistemas externos

Desencadear regras de exclusão de alerta de sistemas externos. Por exemplo, gerir regras de exclusão de sistemas ou sistemas de bilhética empresarial que gerem os processos de manutenção da rede.

Defina os sensores, os motores, a hora de início e o tempo de fim para aplicar a regra. Para obter mais informações, consulte [Defender para OIT sensor API e consola de gestão APIs](references-work-with-defender-for-iot-apis.md).

As regras que cria utilizando a API aparecem na janela **Regra de Exclusão** como RO.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Screenshot da vista regra de exclusão de edição.":::

## <a name="see-also"></a>Ver também

[Trabalhe com alertas no seu sensor](how-to-work-with-alerts-on-your-sensor.md)
