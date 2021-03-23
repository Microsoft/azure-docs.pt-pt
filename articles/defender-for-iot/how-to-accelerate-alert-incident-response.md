---
title: Acelerar fluxos de trabalho de alerta
description: Melhorar os fluxos de trabalho de alerta e incidentes.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: c12d1135b7a7bc87a38a609aeeb6ada8caa9a25e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779125"
---
# <a name="accelerate-alert-workflows"></a>Acelerar fluxos de trabalho de alerta

Este artigo descreve como acelerar os fluxos de trabalho de alerta usando comentários de alerta, grupos de alerta e regras de alerta personalizados no Azure Defender para IoT.  Estas ferramentas ajudam-no:

- Analise e gere o grande volume de eventos de alerta detetados na sua rede.

- Localizar e lidar com a atividade específica da rede.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Acelere os fluxos de trabalho de incidentes usando comentários de alerta

Trabalhe com comentários de alerta para melhorar a comunicação entre indivíduos e equipas durante a investigação de um evento de alerta.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Screenshot que mostra atividade maliciosa.":::

Utilize comentários de alerta para melhorar:

- **Etapas de fluxo de trabalho**: Forneça medidas de atenuação de alerta.

- **Acompanhamento do fluxo de trabalho**: Notifique que foram tomadas medidas.

- **Orientação do fluxo de** trabalho : Fornecer recomendações, insights ou avisos sobre o evento.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Screenshot que mostra comentários de alerta.":::

A lista de opções disponíveis aparece em cada alerta. Os utilizadores podem selecionar uma ou várias mensagens.

Para adicionar comentários de alerta:

1. No menu lateral, selecione **Definições do Sistema**.

2. Na janela **Definição do Sistema,** selecione **Comentários de Alerta**.

3. Na caixa **de comentários Add, insira** o texto de comentário. Use até 50 caracteres. Vírgulas não são permitidas.

4. Selecione **Adicionar**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Acelere os fluxos de trabalho de incidentes utilizando grupos de alerta

Os grupos de alerta permitem que as equipas soc vejam e filtrem alertas nas suas soluções SIEM e depois gerem estes alertas com base nas políticas de segurança das empresas e nas prioridades empresariais. Por exemplo, alertas sobre novas deteções são organizados num grupo de descobertas. Este grupo inclui alertas que lidam com a deteção de novos dispositivos, novos VLANs, novas contas de utilizador, novos endereços MAC e muito mais.

Os grupos de alerta são aplicados quando cria regras de encaminhamento para as seguintes soluções parceiras:

  - Servidores Syslog

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Screenshot de criar uma regra de encaminhamento.":::

O grupo de alerta relevante aparece em soluções de saída de parceiros. 

### <a name="requirements"></a>Requisitos

O grupo de alerta aparecerá em soluções parceiras apoiadas com os seguintes prefixos:

- **gato** para QRadar, ArcSight, Syslog CEF, Syslog LEEF

- **Grupo de alerta** para mensagens de texto do Syslog

- **alert_group** para objetos Syslog

Estes campos devem ser configurados na solução parceira para mostrar o nome do grupo de alerta. Se não houver nenhum alerta associado a um grupo de alerta, o campo na solução parceira apresentará **NA**.

### <a name="default-alert-groups"></a>Grupos de alerta predefinidos

Os seguintes grupos de alerta são definidos automaticamente:

- Comportamento de comunicação anormal
- Alertas personalizados
- Acesso remoto
- Comportamento anormal da comunicação HTTP
- Deteção
- Reiniciar e parar comandos
- Autenticação
- Mudança de firmware
- Analisar
- Comportamento de comunicação não autorizado
- Comandos ilegais
- Tráfego de sensores
- Anomalias de largura de banda
- Acesso à Internet
- Suspeita de malware
- Transbordo de tampão 
- Falhas de operação
- Suspeita de atividade maliciosa
- Falhas de comando
- Problemas operacionais
- Alterações de configuração
- Programação

Os grupos de alerta são predefinidos. Para mais detalhes sobre alertas associados a grupos de alerta e sobre a criação de grupos de alerta personalizados, contacte o [Microsoft Support](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Personalizar regras de alerta

Utilize regras de alerta personalizados para identificar mais especificamente a atividade de interesse para si. 

Pode adicionar regras de alerta personalizadas com base em:

- Uma categoria, por exemplo, um protocolo, porta ou arquivo.
- Endereços de origem e destino
- Uma condição baseada na categoria escolhida, por exemplo, uma função associada a um protocolo, nome de ficheiro, porto ou número de transporte.
- Uma condição baseada na data e na hora de referência, por exemplo, se uma deteção foi feita num dia específico ou numa determinada parte do dia.

Se o sensor detetar a atividade descrita na regra, o alerta é enviado.
informação que os sensores individuais detetam. Por exemplo, defina uma regra que instrua um sensor a acionar um alerta com base numa fonte IP, destino IP ou comando (dentro de um protocolo). Quando o sensor deteta o tráfego definido na regra, é gerado um alerta ou evento.

Também pode usar ações de regra de alerta para instruir o Defender para ioT para:

- Permitir que os utilizadores acedam ao ficheiro PCAP a partir do alerta.
- Atribua uma gravidade de alerta.
- Gere um evento em vez de alerta. A informação detetada aparecerá na linha temporal do evento.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Screenshot que mostra uma regra definida pelo utilizador.":::

A mensagem de alerta indica que uma regra definida pelo utilizador desencadeou o alerta.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Screenshot que mostra alertas personalizados.":::

Para criar uma regra de alerta personalizado:

1. Selecione **Alertas Personalizados** a partir do menu lateral de um sensor.
1. Selecione o sinal de mais **+** () para criar uma regra.
1. Defina um nome de regra.
1. Selecione uma categoria ou protocolo a partir do painel **de categorias.**
1. Defina um endereço IP ou MAC de origem específica ou MAC ou escolha qualquer endereço.
1. Defina uma ou várias condições de regra. Podem ser criadas duas categorias de condições:
    - Condições baseadas em valores únicos associados à categoria selecionada. Selecione Adicionar e definir os valores.
    - Condições baseadas no momento em que a atividade foi detetada. Na secção Deteções, selecione um período de tempo e um dia em que a deteção deve ocorrer para enviar o alerta. Pode optar por enviar o alerta se a atividade for detetada a qualquer momento, durante ou após o horário de trabalho. Utilize a opção Definir horas de trabalho para instruir o Defender para horas de trabalho IoT para a sua organização.
1. Definir ações de regra: 
    - Indicar se a regra aciona um **Alarme** ou **Evento**.
    - Atribua um nível de gravidade ao alerta.
    - Indique se o alerta incluirá um ficheiro PCAP.
1. Selecione **Guardar**.

A regra é adicionada à lista **de Regras de Alerta Personalizado,** onde pode rever os parâmetros básicos da regra, a última vez que a regra foi desencadeada, e muito mais. Também pode ativar e desativar a regra da lista.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Screenshot de uma regra personalizada adicionada ao utilizador.":::

### <a name="see-also"></a>Ver também

[Ver as informações fornecidas pelos alertas](how-to-view-information-provided-in-alerts.md)

[Gerir o evento de alerta](how-to-manage-the-alert-event.md)
