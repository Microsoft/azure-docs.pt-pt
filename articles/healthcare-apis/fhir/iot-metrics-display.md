---
title: Apresentar e configurar métricas do Conector IoT do Azure para FHIR (pré-visualização)
description: Este artigo explica como exibir e configurar o Conector Azure IoT para métricas de FHIR (pré-visualização).
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 831c6df3f50bfff9b411660d9efc4cd78ee5e8d9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020633"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Apresentar e configurar métricas do Conector IoT do Azure para FHIR (pré-visualização) 

Neste artigo, você vai aprender a exibir e configurar O Conector Azure IoT para Recursos de Interoperabilidade de Cuidados rápidos de saúde (FHIR&#174;)* métricas.

> [!TIP]
> Para aprender a configurar a exportação de dados de métricas, siga as orientações no [Conector IoT de Exportação para métricas FHIR (pré-visualização) através de definições de diagnóstico](iot-metrics-diagnostics-export.md).

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>Métricas de exibição para Conector Azure IoT para FHIR (pré-visualização)

1. Inscreva-se no portal Azure e, em seguida, selecione a sua AZure API para o serviço FHIR. 

2. No painel esquerdo, selecione **Métricas**. 

3. Selecione o **separador IoT Connector.**

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="Screenshot do painel 'IoT Connector', com gráficos de linha que exibem o número de mensagens recebidas e normalizadas." lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Selecione um Conector IoT para ver as suas métricas. Por exemplo, existem quatro Conectores IoT *(conector 1*, *conector 2*, e assim por diante) associados a este API Azure para o serviço FHIR.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="Screenshot do painel 'IoT Connector', exibindo os separadores IoT Connector 1, 2, 3 e 4." lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. Selecione o período de tempo (por exemplo, **1 hora**, **24 horas**, **7 dias**, ou **personalizado**) das métricas do Conector IoT que pretende visualizar. Ao selecionar o separador **Personalizado,** pode criar combinações específicas de tempo/data para visualizar métricas do Conector IoT.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="Screenshot do painel 'IoT Connector', exibindo um gráfico de linha de período de 1 hora para 'conector 1'." lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>Tipos métricos para Conector Azure IoT para FHIR (pré-visualização) 

> [!TIP]
> Para saber mais sobre o fluxo de dados no Conector Azure IoT para FHIR, consulte [o Conector Azure IoT para o fluxo de dados FHIR (pré-visualização)](iot-data-flow.md) e [o Conector Azure IoT para o guia de resolução de problemas FHIR (pré-visualização)](iot-troubleshoot-guide.md) para saber mais sobre mensagens de erro e correções.

As métricas do Conector IoT que pode apresentar estão listadas na tabela seguinte:

|Tipo métrico|Finalidade métrica| 
|-----------|--------------|
|Número de Mensagens recebidas|Exibe o número de mensagens recebidas em bruto (por exemplo, os eventos do dispositivo).|
|Número de mensagens normalizadas|Apresenta o número de mensagens normalizadas.|
|Número de grupos de mensagens|Exibe o número de grupos que têm mensagens agregadas na janela de tempo designada.|
|Latência média do estágio normalizado|Apresenta a latência média do estágio normalizado. O estágio normalizado realiza a normalização em mensagens de entrada brutas.|
|Latência média da fase de grupo|Mostra a latência média da fase de grupos. A fase de grupos realiza tampão, agregação e agrupamento em mensagens normalizadas.| 
|Contagem total de erros|Apresenta o número total de erros.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Concentre-se e configuure Azure IoT Connector para métricas FHIR (pré-visualização)

Neste exemplo, concentremo-nos na métrica **do Número de Mensagens recebidas.**

1. Selecione um ponto no tempo em que queira concentrar-se.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="Screenshot do painel métrico 'Número de Mensagens recebidas', realçando um único ponto no tempo no gráfico de linha." lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. No **painel de mensagens recebidas,** pode personalizar ainda mais a métrica selecionando Add **metric**, **Add filter**, ou Apply **split .** 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="Screenshot do painel métrico 'Número de Mensagens recebidas', destacando os botões 'Adicionar métrica', 'Adicionar filtro' e 'Aplicar a divisão'." lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Conclusão 
Ter acesso às métricas do plano de dados é essencial para a monitorização e resolução de problemas. O Conector Azure IoT para FHIR ajuda-o com estas ações através de métricas. 

## <a name="next-steps"></a>Passos seguintes

Obtenha respostas para perguntas frequentes sobre O Conector Azure IoT para FHIR.

>[!div class="nextstepaction"]
>[Conector Azure IoT para FAQ FHIR](fhir-faq.md)

*No portal Azure, o Conector Azure IoT para FHIR é referido como Conector IoT (pré-visualização). FHIR é uma marca registada do HL7 e é usada com a permissão do HL7. 
