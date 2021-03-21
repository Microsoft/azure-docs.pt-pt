---
title: Soluções de monitorização direcionais no Azure Monitor | Microsoft Docs
description: Direcionar as soluções de monitorização permite limitar as soluções de monitorização a um conjunto específico de agentes.  Este artigo descreve como criar uma configuração de âmbito e aplicá-la a uma solução.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: cb030456eb3b9e3c81fadd8451b5b0526920ba33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728574"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Soluções de monitorização direcionais no Azure Monitor (Preview)
Quando adiciona uma solução de monitorização à sua subscrição, é automaticamente implementada por padrão a todos os agentes Windows e Linux ligados ao seu espaço de trabalho Log Analytics.  Pode querer gerir os seus custos e limitar a quantidade de dados recolhidos para uma solução, limitando-os a um determinado conjunto de agentes.  Este artigo descreve como utilizar **o Solution Targeting,** que é uma funcionalidade que lhe permite aplicar um âmbito às suas soluções.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Como direcionar uma solução
Existem três passos para direcionar uma solução, tal como descrito nas secções seguintes. 


### <a name="1-create-a-computer-group"></a>1. Criar um grupo de computadores
Especifica os computadores que pretende incluir num âmbito, criando um [grupo de computador](../logs/computer-groups.md) no Azure Monitor.  O grupo informático pode basear-se numa consulta de registo ou importado de outras fontes, tais como Ative Directory ou grupos WSUS. Como [descrito abaixo,](#solutions-and-agents-that-cant-be-targeted)apenas os computadores que estão diretamente ligados ao Azure Monitor serão incluídos no âmbito.

Uma vez criado o grupo de computador no seu espaço de trabalho, então irá incluí-lo numa configuração de âmbito que pode ser aplicada a uma ou mais soluções.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Criar uma configuração de âmbito
 Uma **Configuração de Âmbito** inclui um ou mais grupos de computador e pode ser aplicada a uma ou mais soluções. 
 
 Crie uma configuração de âmbito utilizando o seguinte processo.  

 1. No portal Azure, navegue para **log analytics e** selecione o seu espaço de trabalho.
 2. Nas propriedades para o espaço de trabalho em **Workspace Data Sources** selecione **Configurações de Âmbito**.
 3. Clique **em Adicionar** para criar uma nova configuração de âmbito.
 4. Digite um **Nome** para a configuração do âmbito.
 5. Clique **em Select Computer Groups**.
 6. Selecione o grupo de computador que criou e opcionalmente quaisquer outros grupos para adicionar à configuração.  Clique em **Selecionar**.  
 6. Clique **em OK** para criar a configuração de âmbito. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Aplique a configuração do âmbito numa solução.
Uma vez que tenha uma configuração de âmbito, pode aplicá-la a uma ou mais soluções.  Note que enquanto uma configuração de âmbito único pode ser usada com várias soluções, cada solução só pode usar uma configuração de âmbito.

Aplique uma configuração de âmbito utilizando o seguinte processo.  

 1. No portal Azure, navegue para **log analytics e** selecione o seu espaço de trabalho.
 2. Nas propriedades para o espaço de trabalho **selecione Soluções.**
 3. Clique na solução que pretende encontrar.
 4. Nas propriedades para a solução em **Workspace Data Sources** selecionam **Solução Direcionar.**  Se a opção não estiver disponível, esta [solução não pode ser direcionada](#solutions-and-agents-that-cant-be-targeted).
 5. Clique **na configuração de alcance de adicionar**.  Se já tiver uma configuração aplicada a esta solução, esta opção não estará disponível.  Deve remover a configuração existente antes de adicionar outra.
 6. Clique na configuração de âmbito que criou.
 7. Observe o **estado** da configuração para se certificar de que mostra **"Bem sucedido".**  Se o estado indicar um erro, clique na elipse para a direita da configuração e **selecione editar** a configuração de âmbito para fazer alterações.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluções e agentes que não podem ser direcionados
Seguem-se os critérios para agentes e soluções que não podem ser utilizados com a orientação de solução.

- A solução que visa apenas aplica-se a soluções que se desdobram aos agentes.
- A segmentagem da solução aplica-se apenas às soluções fornecidas pela Microsoft.  Não se aplica a soluções [criadas por si ou parceiros.](./solutions.md)
- Só é possível filtrar agentes que se ligam diretamente ao Azure Monitor.  As soluções serão automaticamente implementadas para quaisquer agentes que façam parte de um grupo de gestão de Gestores de Operações conectados, quer estejam ou não incluídos numa configuração de âmbito.

### <a name="exceptions"></a>Exceções
A orientação de solução não pode ser utilizada com as seguintes soluções, mesmo que se enquadrem nos critérios indicados.

- Avaliação de Saúde do Agente

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre soluções de monitorização, incluindo as soluções disponíveis para instalar no seu ambiente no [Add Azure Log Analytics, soluções de monitorização para o seu espaço de trabalho.](solutions.md)
- Saiba mais sobre a criação de grupos de computador [em grupos de computador em consultas de registo do Monitor Azure](../logs/computer-groups.md).
