---
title: Informações de alerta a prazo
description: Pode enviar informações de alerta para sistemas parceiros, trabalhando com regras de encaminhamento.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0b71f7ca3f812de1514612f8b0dd5915f3f81bc4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843565"
---
# <a name="forward-alert-information"></a>Informações de alerta a prazo

Pode enviar informações de alerta a parceiros que estão a integrar-se com o Azure Defender para IoT, para syslog servidores, para endereços de e-mail, e muito mais. Trabalhar com regras de encaminhamento permite-lhe entregar rapidamente informações de alerta às partes interessadas em segurança.  

Syslog e outras ações de encaminhamento padrão são entregues com o seu sistema. Poderão tornar-se mais ações de encaminhamento quando se integra com fornecedores parceiros, como o Microsoft Azure Sentinel, ServiceNow ou Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Informação de alerta.":::

Os administradores do Defender para IoT têm permissão para usar regras de encaminhamento.

## <a name="about-forwarded-alert-information"></a>Sobre informações de alerta reencaminhadas

Os alertas fornecem informações sobre um vasto leque de eventos de segurança e operacionais. Por exemplo:

  - Data e hora do alerta

  - Motor que detetou o evento

  - Título de alerta e mensagem descritiva

  - Gravidade do alerta

  - Nome de origem e destino e endereço IP

  - Tráfego suspeito detetado

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Tomografia de endereço detetada.":::

As informações relevantes são enviadas para os sistemas parceiros quando são criadas regras de encaminhamento.

## <a name="create-forwarding-rules"></a>Criar regras de encaminhamento

Para criar uma nova regra de encaminhamento:

1. Selecione **Encaminhamento** no menu lateral.

   ::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule-screen.png" alt-text="Create a Forwarding Rule icon."::

2. Selecione **Criar Regra de Encaminhamento**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Criar uma nova regra de encaminhamento.":::

3. Insira o nome da regra de encaminhamento.

### <a name="forwarding-rule-criteria"></a>Encaminhamento de critérios de regra 

Defina critérios para desencadear uma regra de encaminhamento. Trabalhar com os critérios de regra de encaminhamento ajuda a identificar e gerir o volume de informação enviada do sensor para sistemas externos. Estão disponíveis as seguintes opções:

**Protocolos**: Só desencadeie a regra de encaminhamento se o tráfego detetado estiver a passar por protocolos específicos. Selecione os protocolos necessários da lista de suspensos ou escolha-os todos.

**Motores**: Selecione os motores necessários ou escolha-os todos. Serão enviados alertas de motores selecionados.

**Níveis de gravidade**: Este é o incidente mínimo a encaminhar, em termos de nível de gravidade. Por exemplo, se selecionar **Minor,** serão reencaminhados alertas menores e qualquer alerta acima deste nível de gravidade. Os níveis são predefinidos.

### <a name="forwarding-rule-actions"></a>Encaminhamento de ações de regras

As ações de reencaminhamento instruem o sensor a encaminhar informações de alerta para fornecedores ou servidores parceiros. Pode criar múltiplas ações para cada regra de encaminhamento.

Além das ações de encaminhamento entregues com o seu sistema, outras ações poderão ficar disponíveis quando se integrar com os fornecedores parceiros. 

#### <a name="email-address-action"></a>Ação de endereço de e-mail

Envie um e-mail que inclua a informação de alerta. Pode introduzir um endereço de e-mail por regra.

Para definir e-mail para a regra de encaminhamento:

1. Insira um único endereço de e-mail. Se tiver de ser enviado mais do que um correio, crie outra ação.

2. Introduza o fuso horário para a hora de deteção do alerta no SIEM.

3. Selecione **Submeter**.

#### <a name="syslog-server-actions"></a>Ações de servidor syslog

Os seguintes formatos são suportados:

- Mensagens de texto

- Mensagens CEF

- Mensagens LEEF

- Mensagens de objeto

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Criar ações de regra de encaminhamento.":::

Introduza os seguintes parâmetros:

- Syslog nome de anfitrião e porto.

- Protocolo TCP e UDP.

- Fuso horário para a marca de tempo para a deteção de alerta no SIEM.

- Ficheiro de certificado de encriptação TLS e ficheiro chave para servidores CEF (opcional).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Configure a sua encriptação para a sua regra de encaminhamento.":::

| Syslog texto mensagem campos de saída | Description |
|--|--|
| Data e hora | Data e hora que a máquina do servidor syslog recebeu a informação. |
| Prioridade | Utilizador.Alerta |
| Hostname (Nome do anfitrião) | Endereço IP do sensor |
| Protocolo | TCP ou UDP |
| Mensagem | Sensor: O nome do sensor.<br /> Alerta: O título do alerta.<br /> Tipo: O tipo de alerta. Pode ser **Violação de Protocolo,** **Violação de Políticas,** **Malware,** **Anomalia** ou **Operacional**.<br /> Gravidade: A gravidade do alerta. Pode ser **Aviso,** **Menor,** **Major** ou **Crítico**.<br /> Fonte: O nome do dispositivo de origem.<br /> Origem IP: O endereço IP do dispositivo de origem.<br /> Destino: O nome do dispositivo de destino.<br /> Destino IP: O endereço IP do dispositivo de destino.<br /> Mensagem: A mensagem do alerta.<br /> Grupo de alerta: O grupo de alerta associado ao alerta. |


| Saída de objeto syslog | Description |
|--|--|
| Data e Hora |   Data e hora que a máquina do servidor syslog recebeu a informação. |  
| Prioridade |    Utilizador.Alerta | 
| Hostname (Nome do anfitrião) |    Sensor IP | 
| Mensagem | Nome do sensor: O nome do aparelho. <br /> Tempo de alerta: A hora em que o alerta foi detetado: Pode variar a partir do tempo da máquina do servidor syslog, e depende da configuração do fuso horário da regra de encaminhamento. <br /> Título de alerta: O título do alerta. <br /> Mensagem de alerta: A mensagem do alerta. <br /> Gravidade do alerta: A gravidade do alerta: **Aviso,** **Menor,** **Major** ou **Crítico**. <br /> Tipo de alerta: **Violação de protocolos,** **violação de políticas,** **malware,** **anomalia,** ou **operacional**. <br /> Protocolo: O protocolo do alerta.  <br /> **Source_MAC:** endereço IP, nome, fornecedor ou SISTEMA do dispositivo de origem. <br /> Destination_MAC: endereço IP, nome, fornecedor ou SISTEMA do destino. Se os dados faltarem, o valor será **N/A**. <br /> alert_group: O grupo de alerta associado ao alerta. |


| Formato de saída cef syslog | Description |
|--|--|
| Data e hora | Data e hora que a máquina do servidor syslog recebeu a informação. |
| Prioridade | Utilizador.Alerta | 
| Hostname (Nome do anfitrião) | Endereço IP do sensor |
| Mensagem | CEF:0 <br />Azure Defender para IoT <br />Nome do sensor: O nome do aparelho sensor. <br />Versão sensor <br />Título de alerta: O título do alerta. <br />MSG: A mensagem do alerta. <br />protocolo: O protocolo do alerta. <br />gravidade: **Aviso,** **Menor,** **Major** ou **Crítico**. <br />tipo: **Violação de Protocolos,** **Violação de Políticas,** **Malware,** **Anomalia** ou **Operacional**. <br /> início: A hora em que o alerta foi detetado. <br />Pode variar a partir do tempo da máquina do servidor syslog, e depende da configuração do fuso horário da regra de encaminhamento. <br />src_ip: endereço IP do dispositivo de origem.  <br />dst_ip: endereço IP do dispositivo de destino.<br />gato: O grupo de alerta associado ao alerta.  |

| Formato de saída Syslog LEEF | Description |
|--|--|
| Data e hora |   Data e hora que a máquina do servidor syslog recebeu a informação. |  
| Prioridade |    Utilizador.Alerta | 
| Hostname (Nome do anfitrião) |    Sensor IP |
| Mensagem | Nome do sensor: O nome do Azure Defender para aparelho IoT. <br />LEEF:1.0 <br />Azure Defender para IoT <br />Sensor  <br />Versão sensor <br />Azure Defender para alerta IoT <br />título: O título do alerta. <br />MSG: A mensagem do alerta. <br />protocolo: O protocolo do alerta.<br />gravidade: **Aviso,** **Menor,** **Major** ou **Crítico**. <br />tipo: O tipo de alerta: Violação de **Protocolo,** **Violação de Políticas,** **Malware,** **Anomalia** ou **Operacional**. <br />início: A hora do alerta.Note que pode ser diferente do tempo da máquina do servidor syslog. (Isto depende da configuração do fuso horário.) <br />src_ip: endereço IP do dispositivo de origem.<br />dst_ip: endereço IP do dispositivo de destino. <br />gato: O grupo de alerta associado ao alerta. |

Depois de introduzir todas as informações, **selecione Enviar por isso.**

#### <a name="netwitness-action"></a>Ação NetWitness

Envie informações de alerta para um servidor NetWitness.

Para definir os parâmetros de encaminhamento da NetWitness:

1. Insira informações **sobre o nome de anfitrião netwitness** e **portuário.**

2. Introduza o fuso horário para a hora de deteção do alerta no SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Adicione um fuso horário à sua regra de encaminhamento.":::

3. Selecione **Submeter**.

#### <a name="integrated-vendor-actions"></a>Ações integradas de fornecedor

Pode ter integrado o seu sistema com uma segurança, gestão de dispositivos ou outro fornecedor da indústria. Estas integrações permitem-lhe:

  - Envie informações de alerta.

  - Envie informações sobre inventário de dispositivos.

  - Comunicar com firewalls do lado do fornecedor.

As integrações ajudam a colmatar soluções de segurança previamente siloed, aumentar a visibilidade do dispositivo e acelerar a resposta a todo o sistema para mitigar mais rapidamente os riscos.

Utilize a secção de ações para introduzir as credenciais e outras informações necessárias para comunicar com os fornecedores integrados.

Para obter detalhes sobre a criação de regras de encaminhamento para as integrações, consulte os artigos de integração de parceiros relevantes.

### <a name="test-forwarding-rules"></a>Testar regras de encaminhamento

Teste a ligação entre o sensor e o servidor parceiro definido nas suas regras de encaminhamento:

1. Selecione a regra na caixa de diálogo **de regras de encaminhamento.**

2. Selecione a caixa **Mais.**

3. Selecione **Enviar mensagem de teste**.

4. Vá ao seu sistema de parceiros para verificar se as informações enviadas pelo sensor foram recebidas.

### <a name="edit-and-delete-forwarding-rules"></a>Editar e eliminar regras de encaminhamento 

Para editar uma regra de encaminhamento:

- No ecrã **'Reencaminhar regra',** **selecione Editar** no menu **Mais** suspenso. Faça as alterações desejadas e **selecione Enviar por isso.**

Para remover uma regra de encaminhamento:

- No ecrã **'Reencaminhar' Regra,** selecione **Remover** no menu **Mais** suspenso. Na caixa de diálogo **aviso,** selecione **OK**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Reencaminamento de regras e regras de exclusão de alerta

O administrador pode ter definido regras de exclusão de alerta. Estas regras ajudam os administradores a conseguir mais controlo granular sobre o desencadeamento de alerta, instruindo o sensor a ignorar eventos de alerta com base em vários parâmetros. Estes parâmetros podem incluir endereços de dispositivos, nomes de alerta ou sensores específicos.

Isto significa que as regras de encaminhamento que define podem ser ignoradas com base nas regras de exclusão que o seu administrador criou. As regras de exclusão são definidas na consola de gestão no local.

## <a name="see-also"></a>Veja também

[Acelerar fluxos de trabalho de alerta](how-to-accelerate-alert-incident-response.md)
