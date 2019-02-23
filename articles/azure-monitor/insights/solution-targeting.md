---
title: Direcionamento de monitorização de soluções no Azure Monitor | Documentos da Microsoft
description: Direcionamento de soluções de monitorização permite limitar as soluções de monitorização a um conjunto específico de agentes.  Este artigo descreve como criar uma configuração de âmbito e aplicá-la para uma solução.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 4beb9811a57ecd0a1eada5b36bd65b67851d2564
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731978"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Filtragem de soluções de monitorização no Azure Monitor (pré-visualização)
Quando adiciona uma solução de monitorização para a sua subscrição, será automaticamente implantado por predefinição para todos os agentes Windows e Linux ligados à sua área de trabalho do Log Analytics.  Poderá querer gerir os custos e limitar a quantidade de dados recolhidos para uma solução ao limitá-la para um determinado conjunto de agentes.  Este artigo descreve como utilizar **filtragem da solução** que é uma funcionalidade que lhe permite aplicar um âmbito às suas soluções.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Como uma solução de destino
Existem três passos necessários para direcionar uma solução, conforme descrito nas seções a seguir. 


### <a name="1-create-a-computer-group"></a>1. Criar um grupo de computadores
Especificar os computadores que pretende incluir num âmbito através da criação de um [grupo de computadores](../platform/computer-groups.md) no Azure Monitor.  O grupo de computadores pode ser com base numa consulta de registo ou importado a partir de outras origens, como grupos do Active Directory ou o WSUS. Como [descrito abaixo](#solutions-and-agents-that-cant-be-targeted), apenas os computadores que estão ligados diretamente para o Azure Monitor serão incluídos no escopo.

Assim que tiver criado na sua área de trabalho, o grupo de computadores, em seguida, irá incluir numa configuração de âmbito que pode ser aplicada a uma ou mais soluções.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Criar uma configuração de âmbito
 R **a configuração de âmbito** inclui um ou mais grupos de computadores e podem ser aplicadas a uma ou mais soluções. 
 
 Crie uma configuração de âmbito utilizando o seguinte processo.  

 1. No portal do Azure, navegue até **áreas de trabalho do Log Analytics** e selecione a sua área de trabalho.
 2. Nas propriedades para a área de trabalho sob **origens de dados de área de trabalho** selecionar **configurações de âmbito**.
 3. Clique em **adicionar** para criar uma nova configuração de âmbito.
 4. Introduza um **nome** para a configuração de âmbito.
 5. Clique em **selecionar grupos de computadores**.
 6. Selecione o grupo de computadores que criou e, opcionalmente, quaisquer outros grupos para adicionar à configuração.  Clique em **Selecionar**.  
 6. Clique em **OK** para criar a configuração de âmbito. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Aplica a configuração de âmbito a uma solução.
Depois de ter uma configuração de âmbito, pode aplicá-la para uma ou mais soluções.  Tenha em atenção que, enquanto uma configuração de âmbito único pode ser utilizada com várias soluções, cada solução só pode utilizar uma configuração de âmbito.

Aplica uma configuração de âmbito, com o processo seguinte.  

 1. No portal do Azure, navegue até **áreas de trabalho do Log Analytics** e selecione a sua área de trabalho.
 2. Nas propriedades para a área de trabalho, selecione **soluções**.
 3. Clique na solução que pretende âmbito.
 4. Nas propriedades para a solução sob **origens de dados de área de trabalho** selecionar **filtragem da solução**.  Se a opção não estiver disponível, em seguida, [esta solução não pode ser segmentada](#solutions-and-agents-that-cant-be-targeted).
 5. Clique em **Adicionar configuração de âmbito**.  Se já tiver uma configuração aplicada a esta solução, em seguida, esta opção estará indisponível.  Tem de remover a configuração existente antes de adicionar outro.
 6. Clique na configuração de âmbito que criou.
 7. Assista a **Status** da configuração para se certificar de que mostra **Succeeded**.  Se o estado indica um erro, em seguida, clique nas reticências à direita da configuração e selecione **Editar configuração de âmbito** para fazer alterações.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluções e os agentes que não podem ser segmentados
Seguem-se os critérios para agentes e soluções que não podem ser utilizadas com a filtragem da solução.

- Filtragem da solução só se aplica a soluções que implementar agentes.
- Filtragem da solução só se aplica a soluções fornecidas pela Microsoft.  Não é aplicável às soluções [criado por si próprio ou parceiros](solutions-creating.md).
- Só pode filtrar os agentes que se ligam diretamente para o Azure Monitor.  Soluções irão implementar automaticamente a quaisquer agentes que fazem parte de um grupo de gestão do Operations Manager ligado, independentemente de qual estão incluídos numa configuração de âmbito.

### <a name="exceptions"></a>Exceções
Não é possível utilizar a filtragem da solução com as seguintes soluções, mesmo que eles se ajustam os critérios declarados.

- Avaliação de estado de funcionamento do agente

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre a monitorização de soluções, incluindo as soluções que estão disponíveis para instalação no seu ambiente no [adicionar Azure Log Analytics soluções à sua área de trabalho de monitorização](solutions.md).
- Saiba mais sobre a criação de grupos de computadores em [consultas de registo de grupos de computadores no Azure Monitor](../platform/computer-groups.md).
