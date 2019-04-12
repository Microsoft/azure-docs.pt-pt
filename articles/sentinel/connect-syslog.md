---
title: Ligar a dados do Syslog para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados do Syslog para sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9144f20eae5a925ad88054e972c99ae024bcefe3
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496527"
---
# <a name="connect-your-external-solution-using-syslog"></a>Ligue a sua solução externa com o Syslog

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode ligar-se a qualquer aplicação no local com suporte para Syslog para sentinela do Azure. Isso é feito através de um agente com base numa máquina Linux entre a aplicação e, em seguida, sentinela do Azure. Se a sua máquina Linux encontra-se no Azure, pode transmitir os registos da sua aplicação ou para uma área de trabalho dedicada que cria no Azure e ligá-la. Se não for sua máquina Linux no Azure, pode transmitir os registos da sua aplicação para um dedicado no local, VM ou numa máquina em que instalar o agente para Linux. 

> [!NOTE]
> Se a aplicação da suporte CEF de Syslog, a ligação é mais completa e deve escolher esta opção e siga as instruções em [ligar dados a partir de CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Como funciona

Ligação de syslog é realizada através de um agente para Linux. Por predefinição, o agente para Linux recebe o eventos partir do Syslog daemon através do UDP, mas em casos em que uma máquina Linux é esperada para recolher um grande volume de eventos do Syslog, como quando um agente do Linux é receber eventos de outros dispositivos, a configuração seja alterada para Utilize o transporte TCP entre o Syslog daemon e o agente.

## <a name="connect-your-syslog-appliance"></a>Ligar a sua aplicação do Syslog

1. No portal do Azure sentinela, selecione **conectores de dados** e escolha o **Syslog** mosaico.
2. Se sua máquina Linux não estiver dentro do Azure, transfira e instale o Azure sentinela **agente para Linux** na sua aplicação. 
1. Se estiver a trabalhar no Azure, selecione ou crie uma VM que em área de trabalho do Azure sentinela dedicado ao recebimento de mensagens do Syslog. Selecione a VM em áreas de trabalho de sentinela do Azure e clique em **Connect** na parte superior do painel esquerdo.
3. Clique em **configurar os registos sejam conectadas** na configuração do conector do Syslog. 
4. Clique em **prima aqui para abrir o painel de configuração**.
1. Selecione **dados** e, em seguida **Syslog**.
   - Certifique-se de que cada recurso que está a enviar por Syslog é na tabela. Para cada recurso vai monitorizar, defina uma gravidade. Clique em **Aplicar**.
1. No seu computador de Syslog, certifique-se de que está enviando nessas instalações. 

3. Para utilizar o esquema relevante no Log Analytics para os registos de Syslog, procure **Syslog**.




## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar aparelhos do Syslog no local ao Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
