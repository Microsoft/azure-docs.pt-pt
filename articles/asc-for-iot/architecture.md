---
title: Entendendo a central de segurança do Azure para a arquitetura da solução de IoT | Microsoft Docs
description: Saiba mais sobre o fluxo de informações na central de segurança do Azure para o serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: a013d4cfcfddc709e60e91adf57bc27c98934a96
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596546"
---
# <a name="azure-security-center-for-iot-architecture"></a>Central de segurança do Azure para arquitetura de IoT

Este artigo explica a arquitetura do sistema funcional da central de segurança do Azure para a solução de IoT. 

## <a name="azure-security-center-for-iot-components"></a>Central de segurança do Azure para componentes de IoT

A central de segurança do Azure para IoT é composta pelos seguintes componentes:
- Integração do Hub IoT
- Agentes de dispositivo (opcional)
- Enviar SDK de mensagem de segurança
- Pipeline de análise
 
### <a name="azure-security-center-for-iot-workflows"></a>Central de segurança do Azure para fluxos de trabalho de IoT

A central de segurança do Azure para IoT funciona em um dos dois fluxos de trabalho de recursos: Interno e avançado  

### <a name="built-in"></a>Incorporado
No modo **interno** , a central de segurança do Azure para IOT é habilitada quando você opta por ativar a opção de **segurança** em seu hub IOT. Oferecendo monitoramento, recomendações e alertas em tempo real, o modo interno oferece visibilidade de dispositivo única e segurança incomparável. O modo de compilação não requer a instalação do agente em nenhum dispositivo e usa análises avançadas em atividades registradas para analisar e proteger o dispositivo de campo. 

### <a name="enhanced"></a>Melhorada 
No modo **avançado** , depois de ativar a opção de **segurança** no Hub IOT e instalar a central de segurança do Azure para agentes de dispositivo IOT em seus dispositivos, os agentes coletam, agregam e analisam eventos de segurança brutos de seus dispositivos. Os eventos de segurança brutos podem incluir conexões IP, criação de processo, logons de usuário e outras informações relevantes de segurança. A central de segurança do Azure para agentes de dispositivo IoT também manipula a agregação de eventos para ajudar a evitar alta taxa de transferência de rede Os agentes são altamente personalizáveis, permitindo que você os use para tarefas específicas, como enviar apenas informações importantes no SLA mais rápido ou para agregar informações de segurança extensivas e contexto em segmentos maiores, evitando custos de serviço mais altos.

![Central de segurança do Azure para arquitetura de IoT](./media/architecture/azure-iot-security-architecture.png)
 
Os agentes de dispositivo e outros aplicativos usam o **SDK de mensagens de segurança do Azure Send** para enviar informações de segurança para o Hub IOT do Azure. O Hub IoT pega essas informações e as encaminha para a central de segurança do Azure para o serviço de IoT.

Depois que a central de segurança do Azure para o serviço de IoT estiver habilitada, além dos dados encaminhados, o Hub IoT também enviará todos os seus dados internos para análise pela central de segurança do Azure para IoT. Esses dados incluem logs de operação do dispositivo-nuvem, identidades do dispositivo e configuração do Hub. Todas essas informações ajudam a criar a central de segurança do Azure para o pipeline de análise de IoT.
 
A central de segurança do Azure para o pipeline do IoT Analytics também recebe fluxos adicionais de inteligência contra ameaças de várias fontes dentro de parceiros da Microsoft e da Microsoft. A central de segurança do Azure para IoT todo o pipeline de análise funciona com cada configuração de cliente feita no serviço (como alertas personalizados e o uso do SDK de envio de mensagem de segurança).
 
Usando o pipeline de análise, a central de segurança do Azure para IoT combina todos os fluxos de informações para gerar alertas e recomendações acionáveis. O pipeline contém as regras personalizadas criadas por pesquisadores e especialistas de segurança, bem como modelos de aprendizado de máquina procurando desvios do comportamento padrão do dispositivo e da análise de riscos.
 
A central de segurança do Azure para alertas e recomendações de IoT (saída de pipeline de análise) é gravada no espaço de trabalho Log Analytics de cada cliente. Incluir os eventos brutos no espaço de trabalho, bem como os alertas e as recomendações, permite investigações e consultas aprofundadas usando os detalhes exatos das atividades suspeitas detectadas.  

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a arquitetura básica e o fluxo de trabalho da central de segurança do Azure para a solução de IoT. Para saber mais sobre os pré-requisitos, como começar e habilitar sua solução de segurança no Hub IoT, consulte os seguintes artigos:

- [Pré-requisitos de serviço](service-prerequisites.md)
- [Introdução](getting-started.md)
- [Configurar sua solução](quickstart-configure-your-solution.md)
- [Habilitar a segurança no Hub IoT](quickstart-onboard-iot-hub.md)
- [FAQ da central de segurança do Azure para IoT](resources-frequently-asked-questions.md)
- [Central de segurança do Azure para alertas de segurança de IoT](concept-security-alerts.md)
