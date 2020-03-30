---
title: Soluções de monitorização direcionadas no Monitor Azure Microsoft Docs
description: As soluções de monitorização direcionadas permitem limitar as soluções de monitorização a um conjunto específico de agentes.  Este artigo descreve como criar uma configuração de âmbito e aplicá-la a uma solução.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: dd3279db67fb45aee43cf1b0ef1bebf49433eef4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663237"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Soluções de monitorização direcionadas no Monitor Azure (Pré-visualização)
Quando adiciona uma solução de monitorização à sua subscrição, é automaticamente implementada por padrão a todos os agentes Windows e Linux ligados ao seu espaço de trabalho Log Analytics.  Pode querer gerir os seus custos e limitar a quantidade de dados recolhidos para uma solução, limitando-os a um determinado conjunto de agentes.  Este artigo descreve como utilizar o **Solution Targeting,** que é uma funcionalidade que lhe permite aplicar um âmbito às suas soluções.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Como direcionar uma solução
Existem três passos para direcionar uma solução, tal como descrito nas seguintes secções. 


### <a name="1-create-a-computer-group"></a>1. Criar um grupo informático
Especifica os computadores que pretende incluir num âmbito através da criação de um [grupo informático](../platform/computer-groups.md) no Monitor Azure.  O grupo informático pode basear-se numa consulta de registo ou importado de outras fontes, tais como o Ative Directory ou os grupos WSUS. Como [descrito abaixo,](#solutions-and-agents-that-cant-be-targeted)apenas os computadores que estão diretamente ligados ao Monitor Azure serão incluídos no âmbito.

Assim que tiver o grupo de computador criado no seu espaço de trabalho, irá incluí-lo numa configuração de âmbito que pode ser aplicada a uma ou mais soluções.
 
 
### <a name="2-create-a-scope-configuration"></a>2. Criar uma configuração de âmbito
 Uma **Configuração** de Âmbito inclui um ou mais grupos informáticos e pode ser aplicada a uma ou mais soluções. 
 
 Criar uma configuração de âmbito utilizando o seguinte processo.  

 1. No portal Azure, navegue para espaços de **trabalho de Log Analytics** e selecione o seu espaço de trabalho.
 2. Nas propriedades do espaço de trabalho sob fontes de **dados do espaço de trabalho,** selecione **Configurações**de âmbito .
 3. Clique em **Adicionar** para criar uma nova configuração de âmbito.
 4. Digite um **nome** para a configuração de âmbito.
 5. Clique em **Selecionar grupos de computador**.
 6. Selecione o grupo de computador que criou e opcionalmente quaisquer outros grupos para adicionar à configuração.  Clique em **Selecionar**.  
 6. Clique **em OK** para criar a configuração de âmbito. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Aplique a configuração de âmbito numa solução.
Uma vez configurada uma configuração de âmbito, pode aplicá-la a uma ou mais soluções.  Note que, embora uma configuração de âmbito único possa ser usada com múltiplas soluções, cada solução só pode usar uma configuração de âmbito.

Aplique uma configuração de âmbito utilizando o seguinte processo.  

 1. No portal Azure, navegue para espaços de **trabalho de Log Analytics** e selecione o seu espaço de trabalho.
 2. Nas propriedades para o espaço de trabalho selecione **Soluções**.
 3. Clique na solução que pretende ser ampla.
 4. Nas propriedades para a solução em Fontes de **Dados workspace** selecione **Solution Targeting**.  Se a opção não estiver disponível, [esta solução não pode ser direcionada](#solutions-and-agents-that-cant-be-targeted).
 5. Clique na **configuração de alcance Adicionar**.  Se já tiver uma configuração aplicada a esta solução, esta opção não estará disponível.  Tem de remover a configuração existente antes de adicionar outra.
 6. Clique na configuração de âmbito que criou.
 7. Observe o **Estado** da configuração para se certificar de que mostra **sucesso**.  Se o estado indicar um erro, clique na elipse à direita da configuração e selecione a **configuração** de âmbito editar para fazer alterações.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluções e agentes que não podem ser visados
Seguem-se os critérios para agentes e soluções que não podem ser utilizados com a orientação da solução.

- A solução visaa apenas se aplica a soluções que se desdobrem aos agentes.
- A solução visaa apenas se aplica às soluções fornecidas pela Microsoft.  Não se aplica a soluções [criadas por si ou por parceiros.](solutions-creating.md)
- Só é possível filtrar agentes que se ligam diretamente ao Monitor Azure.  As soluções serão automaticamente implementadas para quaisquer agentes que façam parte de um grupo de gestão de Gestão de Operações conectado, quer estejam ou não incluídos numa configuração de âmbito.

### <a name="exceptions"></a>Exceções
A orientação da solução não pode ser utilizada com as seguintes soluções, mesmo que se encaixem nos critérios indicados.

- Avaliação de Saúde do Agente

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre soluções de monitorização, incluindo as soluções disponíveis para instalar no seu ambiente em [Add Azure Log Analytics, soluções de monitorização para o seu espaço de trabalho](solutions.md).
- Saiba mais sobre a criação de grupos informáticos [em grupos de computador em consultas](../platform/computer-groups.md)de registo do Monitor Azure .
