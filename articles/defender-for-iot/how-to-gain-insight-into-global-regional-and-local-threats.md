---
title: Obter informações sobre ameaças globais, regionais e locais
description: Obtenha informações sobre ameaças globais, regionais e locais usando o mapa do site na consola de gestão no local.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784123"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Obter informações sobre ameaças globais, regionais e locais

O mapa do site na consola de gestão no local ajuda-o a obter uma cobertura de segurança completa dividindo a sua rede em segmentos geográficos e lógicos que refletem a sua topologia de negócios:

- **Nível de instalação geográfica**: Um local reflete uma série de dispositivos agrupados de acordo com uma localização geográfica apresentada no mapa. Por defeito, o Azure Defender for IoT fornece-lhe um mapa mundial. Atualize o mapa para refletir a sua estrutura organizacional ou empresarial. Por exemplo, use um mapa que reflita sites em um país específico, cidade ou campus industrial. Quando a cor do site muda no mapa, fornece à equipa SOC uma indicação do estado crítico do sistema na instalação.

  O mapa é interativo e permite abrir cada site e aprofundar a informação deste site.

- **Camada lógica global**: Uma unidade de negócio é uma forma de dividir a sua empresa em segmentos lógicos de acordo com indústrias específicas. Quando fazes isto, a topologia do teu negócio reflete-se no mapa.

  Por exemplo, uma empresa global que contenha fábricas de vidro, fábricas de plástico e fábricas de automóveis pode ser gerida como três unidades de negócio diferentes. Um local físico localizado em Toronto inclui três linhas de produção de vidro diferentes, uma linha de produção de plástico e uma linha de produção de motores de caminhão. Então, este site tem representantes das três unidades de negócio.

- **Nível da região geográfica**: Criar regiões para dividir uma empresa global em regiões geográficas. Por exemplo, a empresa que descrevemos poderá utilizar as regiões da América do Norte, da Europa Ocidental e da Europa Oriental. A América do Norte tem fábricas das três unidades de negócio. A Europa Ocidental tem fábricas de automóveis e fábricas de vidro, e a Europa Oriental tem apenas fábricas de plástico.

- **Nível de segmento lógico local**: Uma zona é um segmento lógico dentro de um site que define, por exemplo, uma área funcional ou linha de produção. Trabalhar com zonas permite a aplicação de políticas de segurança relevantes para a definição de zona. Por exemplo, um site que contenha cinco linhas de produção pode ser segmentado em cinco zonas.

- **Nível de visão local**: Uma visão local de uma única instalação de sensor fornece uma visão do estado operacional e de segurança dos dispositivos conectados.

## <a name="work-with-site-map-views"></a>Trabalhar com vistas do mapa do site

A consola de gestão no local proporciona uma visão geral da sua rede industrial num mapa relacionado com o contexto. A vista geral do mapa apresenta o mapa global da sua organização com a localização geográfica de cada site.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Screenshot da vista do Mapa da Empresa.":::

### <a name="color-coded-map-views"></a>Vistas de mapa codificadas por cores

**Verde**: O número de eventos de segurança está abaixo do limiar que o Defender para IoT definiu para o seu sistema. Não é necessária nenhuma ação.

**Amarelo**: O número de eventos de segurança é igual ao limiar que o Defender para IoT definiu para o seu sistema. Considere investigar os acontecimentos.  

**Vermelho**: O número de eventos de segurança está fora do limiar que o Defender para IoT definiu para o seu sistema. Tome medidas imediatas.

### <a name="risk-level-map-views"></a>Vistas de mapa de nível de risco

**Avaliação de Riscos**: A visualização da Avaliação de Riscos apresenta informações sobre os riscos do local. A informação de risco ajuda-o a priorizar a mitigação e a construir um roteiro para planear melhorias de segurança.

**Resposta incidente**: Obtenha uma visão centralizada de todos os alertas não reconhecidos em cada site em toda a empresa. Pode perfurar e gerir os alertas detetados num local específico.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Screenshot da vista do Mapa da Empresa com Resposta a Incidentes.":::

**Atividade maliciosa**: Se o malware foi detetado, o site aparece a vermelho. Isto indica que deve tomar medidas imediatas.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Screenshot da vista do Mapa da Empresa com Atividade Maliciosa.":::

**Alertas Operacionais**: Esta visão do mapa para sistemas OT proporciona uma melhor compreensão de qual o sistema OT pode experimentar incidentes operacionais, tais como paragens de PLC, upload de firmware e upload de programas.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Screenshot da vista do Mapa da Empresa com Alertas Operacionais.":::

Para escolher uma vista de mapa:

1. Selecione **'Vista Predefinitiva'** a partir do mapa.
2. Selecione uma vista.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Screenshot da vista padrão do mapa do site.":::

## <a name="update-the-site-map-image"></a>Atualizar a imagem do mapa do site

O Defender for IoT fornece um mapa mundial padrão. Pode alterá-lo para refletir a sua organização: um mapa do país ou um mapa da cidade, por exemplo. 

Para substituir o mapa:

1. No painel esquerdo, selecione **Definições do Sistema**.

2. Selecione o **Mapa do Site de Alteração** e carreque o ficheiro gráfico para substituir o mapa predefinido.

## <a name="next-step"></a>Passo seguinte

[Ver alertas](how-to-view-alerts.md)
