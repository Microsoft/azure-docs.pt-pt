---
title: Recolher dados de Barracuda sentinela na pré-visualização no Azure | Documentos da Microsoft
description: Saiba como recolher dados de Barracuda no sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2827d7986b8cd9d11ee6a9d678f1c457047f4c6d
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993154"
---
# <a name="connect-your-barracuda-appliance"></a>Ligar a sua aplicação da Barracuda 

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Conector do barracuda Web Application Firewall (WAF) permite-lhe ligar facilmente os seus registos de Barracuda com o Azure sentinela, para ver os dashboards e criar alertas personalizados e a melhorar a investigação. Isso dá-lhe mais informações sobre a rede da sua organização e melhora as capacidades de operação de segurança. Sentinel do Azure tira partido da integração nativa entre **Barracuda** e o Microsoft Azure OMS para fornecer uma integração perfeita. 


>[!NOTE]

> - Os dados serão armazenados na localização geográfica da área de trabalho no qual está a executar sentinela do Azure.

## <a name="configure-and-connect-barracuda-waf"></a>Configurar e ligar o Barracuda WAF
Barracuda Web Application Firewall pode integrar e exportar registos diretamente para [ASI] através do servidor de OMS do Azure.
1. Aceda a [fluxo de configuração do Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)e siga as instruções para configurar a ligação, usando esses parâmetros:
    - **ID da área de trabalho**: Copie o valor do seu ID de área de trabalho a partir da página de conector Barracuda sentinela do Azure.
    - **Chave primária**: Copie o valor da sua chave primária a partir da página de conector Barracuda sentinela do Azure.
2. No portal do Azure sentinela, vá para a área de trabalho no qual implementou sentinela do Azure e selecione as reticências (...) no final da linha e selecione **definições avançadas**. 
1. Selecione **dados** e, em seguida **Syslog**.
1. Certifique-se de que o recurso define no Barracuda existe e definir a gravidade e clique em **guardar**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar aparelhos de Barracuda a sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

