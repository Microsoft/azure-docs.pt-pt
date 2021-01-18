---
title: Sobre a integração de Forescout
titleSuffix: Azure Defender for IoT
description: O Azure Defender para a integração de IoT com a plataforma Forescout proporciona um novo nível de visibilidade centralizada, monitorização e controlo para a paisagem IoT e OT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: faa53c770d0d6caac471e770c80b4dfd5c5ff603
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558620"
---
# <a name="about-the-forescout-integration"></a>Sobre a integração de Forescout

O Azure Defender for IoT oferece uma plataforma de cibersegurança ICS e IoT construída por especialistas da equipa azul com um historial de defesa de infraestruturas nacionais críticas. O Defender for IoT é a única plataforma com ics patenteado a tentos e machine learning. O Defender for IoT fornece:

- Informações imediatas sobre o ICS a paisagem do dispositivo com uma vasta gama de detalhes sobre atributos.
- Conhecimento profundo e profundo do ICS sobre protocolos de OT, dispositivos, aplicações e seus comportamentos.
- Informações imediatas sobre vulnerabilidades, ameaças conhecidas e de dia zero.
- Uma tecnologia automatizada de modelação de ameaças do ICS para prever os caminhos mais prováveis de ataques ics direcionados através de análises proprietárias.

O Defender para a integração de IoT com a plataforma Forescout proporciona um novo nível de visibilidade centralizada, monitorização e controlo para a paisagem IoT e OT.

Estas plataformas em ponte permitem a visibilidade e gestão automatizadas do dispositivo a dispositivos ICS anteriormente inacessíveis e fluxos de trabalho siloed.

A integração proporciona aos analistas da SOC uma visibilidade multinível nos protocolos de OT implantados em ambientes industriais. Estão disponíveis detalhes para itens como firmware, tipos de dispositivos, sistemas operativos e pontuações de análise de risco com base no Azure Defender proprietário para tecnologias IoT.

> [!Note]
> As referências a CyberX referem-se ao Azure Defender para IoT.
## <a name="devices"></a>Dispositivos

### <a name="device-visibility-and-management"></a>Visibilidade e gestão do dispositivo

O inventário do dispositivo é enriquecido com atributos críticos detetados pelo Defender para a plataforma IoT. Isto irá garantir que:

- Ganhe uma visibilidade abrangente e contínua na paisagem do dispositivo OT a partir de um único painel de vidro.
- Obtenha informações em tempo real sobre os riscos de OT.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Inventário de dispositivos":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Detalhes do dispositivo":::

### <a name="device-control"></a>Controlo do dispositivo

A integração de Forescout ajuda a reduzir o tempo necessário para que as organizações de infraestruturas industriais e críticas detetem, investiguem e atuem sobre ameaças cibernéticas.

- Utilize o Azure Defender para obter inteligência de dispositivo IoT OT para fechar o ciclo de segurança desencadeando ações políticas de Forescout. Por exemplo, pode enviar automaticamente e-mails de alerta aos administradores do SOC quando forem detetados protocolos específicos ou quando os detalhes do firmware mudarem.

- Correlate Defender para informações IoT com outros *módulos Forescout eyeExtended* que supervisionam a monitorização, gestão de incidentes e controlo de dispositivos.

## <a name="system-requirements"></a>Requisitos de sistema

- Azure Defender para versão IoT 2.4 ou superior
- Versão forescout 8.0 ou superior
- Uma licença para o módulo *Forescout eyeExtend* para o Azure Defender para a Plataforma IoT.

### <a name="getting-more-forescout-information"></a>Obtenção de mais informações forescout

Para obter mais informações sobre a plataforma Forescout, consulte o [Centro de Recursos forescout.](https://www.forescout.com/company/resources/#resource_filter_group)

## <a name="system-setup"></a>Configuração do sistema

Este artigo descreve como configurar a comunicação entre o Defender para a plataforma IoT e a plataforma Forescout.

### <a name="set-up-the-defender-for-iot-platform"></a>Configurar o Defender para a plataforma IoT

Para garantir a comunicação do Defender para IoT a Forescout, gere um token de acesso no Defender para IoT.

Os tokens de acesso permitem que os sistemas externos acedam aos dados descobertos pelo Defender para IoT e realizem ações com esses dados utilizando a API externa REST, através de ligações SSL. Pode gerar fichas de acesso para aceder ao AZure Defender para IoT REST API.

Para gerar um símbolo:

1. Inscreva-se no Defender para o Sensor IoT que será consultado por Forescout.

1. Selecione **Definições do Sistema** e, em seguida, selecione **Tokens** de acesso na secção **Geral.** A caixa de diálogo **Access Tokens** abre.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Tokens de acesso":::
1. **Selecione Gereque um novo símbolo** a partir da caixa de diálogo Access **Tokens.**
1. Introduza uma descrição simbólica na caixa de diálogo de novo sinal de **acesso.**
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Novo token de acesso":::
1. Selecione **Seguinte**. O token é apresentado na caixa de diálogo. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Ver token":::
   > [!NOTE]
   > *Grave o símbolo num lugar seguro. Vai precisar dele ao configurar a Plataforma forescout*.
1. Selecione **Concluir**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Termine de adicionar ficha":::

### <a name="set-up-the-forescout-platform"></a>Configurar a plataforma Forescout

Pode configurar a plataforma Forescout para comunicar com um sensor Defender para IoT.

Para configurar:

1. Instale *o módulo Forescout eyeExtend para CyberX* na plataforma Forescout.

1. Inscreva-se na consola CounterACT e selecione **Opções** no menu **Ferramentas.** Abre-se a caixa de diálogo **Options.**

1. Navegue e selecione a pasta **Módulos.**

1. No painel **de módulos,** selecione **CyberX Platform**. Abre o painel de plataformas Defender for IoT.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Azure Defender para configurações de módulo IoT":::

   Introduza as seguintes informações:

   - **Endereço do servidor** - Introduza o endereço IP do sensor Defender para IoT que será consultado pelo aparelho Forescout.
   - **Token de acesso** - Introduza o token de acesso gerado para o sensor Defender para IoT que irá ligar-se ao aparelho Forescout. Para gerar um token, consulte [Configurar o Defender para a plataforma IoT](#set-up-the-defender-for-iot-platform).

1. Selecione **Aplicar**.

Se quiser que a plataforma Forescout comunique com outro sensor:

1. Crie um novo token de acesso no sensor IoT relevante do Defender.

1. Na caixa de diálogo da Plataforma CyberX **dos Módulos Forescout:**  >  

   - Elimine as informações apresentadas.
   
   - Insira o novo endereço IP do sensor e as novas informações de acesso.

### <a name="verify-communication"></a>Verificar comunicação

Depois de configurar o Defender para IoT e Forescout, abra a caixa de diálogo Access Tokens do sensor no Defender para IoT.

Se **a N/A** for apresentada no campo **Usado** para este token, a ligação entre o sensor e o aparelho forescout não está a funcionar.

**Usado** indica a última vez que uma chamada externa com este símbolo foi recebida.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Verifica que o símbolo foi recebido":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Ver Defender para deteções de IoT em Forescout

Para ver os atributos de um dispositivo:

1. Inscreva-se na plataforma Forescout e, em seguida, navegue para o **Inventário de Ativos.**

1. Navegue para a **Plataforma CyberX.** Os seguintes atributos do dispositivo são apresentados para dispositivos OT detetados pelo Defender para IoT.

   | Item | Descrição |
   |--|--|
   | Autorizado por Azure Defender para IoT | Um dispositivo detetado na sua rede pelo Defender para IoT durante o período de aprendizagem da rede. |
   | Firmware | Os detalhes do firmware do dispositivo. Por exemplo, detalhes do modelo e da versão. |
   | Name | O nome do dispositivo. |
   | Sistema Operativo | O sistema operativo do dispositivo. |
   | Tipo | O tipo de dispositivo. Por exemplo, uma ESTAÇÃO PLC, Historiadora ou De Engenharia. |
   | Fornecedor | O fornecedor do dispositivo. Por exemplo, a Rockwell Automation. |
   | Nível de risco | O nível de risco calculado pelo Defender para ioT. |
   | Protocolos | Os protocolos detetados no tráfego gerado pelo dispositivo. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Ver os atributos do firmware.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Ver os atributos dos fornecedores.":::

### <a name="viewing-more-details"></a>Ver mais detalhes

Consulte informações adicionais do dispositivo para dispositivos dirigidos pelo Defender para IoT. Por exemplo, a conformidade do Forescout e a informação política.

Para o conseguir, clique com o botão direito num dispositivo da secção **de anfitriões de inventário de dispositivos.** A caixa de diálogo de detalhes do anfitrião abre com informações adicionais.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Detalhes do anfitrião":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Criar Azure Defender para políticas IoT em Forescout

As políticas de forescout podem ser usadas para automatizar o controlo e gestão de dispositivos detetados pelo Defender para IoT. Por exemplo,

- Envie automaticamente um e-mail aos administradores do SOC quando forem detetadas versões específicas do firmware.

- Adicione dispositivos específicos do Defender para IoT detetados a um grupo Forescout para posterior manipulação em fluxos de trabalho de incidentes e segurança, por exemplo com outras integrações siem.

Crie uma política personalizada forescout, com o Defender para IoT utilizando propriedades de condição.

Para aceder ao Defender para propriedades IoT:

1. Navegue para a **Árvore das Propriedades** a partir da caixa de diálogo Condições de **Política.**

1. Expandir a pasta **da Plataforma CyberX** na **Árvore das Propriedades.** O Defender para IoT segue propriedades estão disponíveis.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Propriedades":::

## <a name="next-steps"></a>Passos seguintes

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
