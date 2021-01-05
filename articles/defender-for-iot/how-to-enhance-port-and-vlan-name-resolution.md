---
title: Melhorar a resolução do nome do porto e do VLAN
description: Personalize os nomes de porta e VLAN nos seus sensores para enriquecer a resolução do dispositivo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 1747af77468ebe41f59892dea4bb49ce24757f37
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842428"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>Melhorar a resolução do nome do porto e do VLAN

Pode personalizar nomes de porta e VLAN nos seus sensores para enriquecer a resolução do dispositivo.

## <a name="customize-port-names"></a>Personalizar nomes de portas

O Azure Defender for IoT atribui automaticamente nomes às portas mais reservadas universalmente, tais como DHCP ou HTTP. Pode personalizar nomes portuários para outras portas que o Defender for IoT deteta. Por exemplo, atribua um nome a uma porta não reservada porque esse porto mostra uma atividade invulgarmente elevada.

Estes nomes aparecem quando:

  - Selecione grupos de **dispositivos** a partir do mapa do dispositivo.

  - Cria widgets que fornecem informações portuárias.

### <a name="view-custom-port-names-in-the-device-map"></a>Ver nomes de portas personalizados no mapa do dispositivo

As portas que incluem um nome definido pelos utilizadores aparecem no mapa do dispositivo, no grupo **Aplicações Conhecidas.**

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Screenshot do mapa do dispositivo, mostrando o grupo aplicações conhecidas.":::

### <a name="view-custom-port-names-in-widgets"></a>Ver nomes de portas personalizados em widgets

Os nomes portuários que definiu aparecem nos widgets que cobrem o tráfego por porto.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Cobrir o tráfego.":::

Para definir nomes de portas personalizados:

1. Selecione **Definições do Sistema** e, em seguida, selecione **Pseudónimos padrão**.

2. **Selecione Adicionar pseudónimos de portas**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Adicione pseudónimos de porto.":::

3. Introduza o número da porta, selecione **TCP/UDP,** ou selecione **Ambos**, e adicione o nome.

4. Selecione **Guardar**.

## <a name="configure-vlan-names"></a>Configurar nomes VLAN

Pode enriquecer os dados de inventário do dispositivo com números e etiquetas VLAN do dispositivo. Além do enriquecimento de dados, pode ver o número de dispositivos por VLAN e ver largura de banda por widgets VLAN.

O suporte VLANs baseia-se em 802.1q (até VLAN ID 4094).

Estão disponíveis dois métodos para a recuperação de informações VLAN:

- **Descoberto automaticamente:** Por padrão, o sensor descobre automaticamente VLANs. Os VLANs detetados com tráfego são exibidos no ecrã de configuração VLAN, em relatórios de mineração de dados e em outros relatórios que contêm informações VLAN. Não são apresentados VLANs não-reutilizados. Não pode editar ou eliminar estes VLANs. 

  Deve adicionar um nome único a cada VLAN. Se não adicionar um nome, o número VLAN aparece em todos os locais onde o VLAN é relatado.

- **Adicionado manualmente**: Pode adicionar VLANs manualmente. Deve adicionar um nome único para cada VLAN que foi adicionado manualmente, e pode editar ou eliminar estes VLANs.

Os nomes VLAN podem conter até 50 caracteres ASCII.

> [!NOTE]
> Os nomes VLAN não são sincronizados entre o sensor e a consola de gestão. Também tens de definir o nome na consola de gestão.  
Para os interruptores Cisco, adicione a seguinte linha à configuração de envergadura: `monitor session 1 destination interface XX/XX encapsulation dot1q` . Nesse comando, *XX/XX* é o nome e número da porta.

Para configurar VLANs:

1. No menu lateral, selecione **Definições do Sistema**.

2. Na janela Definições do **Sistema,** selecione **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Utilize as definições do sistema para editar os seus VLANs.":::

3. Adicione um nome único ao lado de cada ID VLAN.

## <a name="next-steps"></a>Passos seguintes

Ver informações sobre dispositivos enriquecidos nos relatórios relativos ao inventário do dispositivo e à mineração de dados:

- [Investigar deteções de sensores num inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Gerar relatórios](how-to-generate-reports.md)
