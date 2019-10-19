---
title: Direcionando soluções de monitoramento no Azure Monitor | Microsoft Docs
description: Direcionar soluções de monitoramento permite que você limite soluções de monitoramento para um conjunto específico de agentes.  Este artigo descreve como criar uma configuração de escopo e aplicá-la a uma solução.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 04b47cb6079d9213c1a20425f62286f1b2aa778b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555309"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Direcionando soluções de monitoramento no Azure Monitor (versão prévia)
Quando você adiciona uma solução de monitoramento à sua assinatura, ela é implantada automaticamente por padrão para todos os agentes do Windows e Linux conectados ao seu espaço de trabalho do Log Analytics.  Talvez você queira gerenciar seus custos e limitar a quantidade de dados coletados para uma solução, limitando-o a um determinado conjunto de agentes.  Este artigo descreve como usar o **direcionamento de solução** , que é um recurso que permite aplicar um escopo às suas soluções.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Como direcionar uma solução
Há três etapas para direcionar uma solução, conforme descrito nas seções a seguir. 


### <a name="1-create-a-computer-group"></a>1. criar um grupo de computadores
Você especifica os computadores que deseja incluir em um escopo criando um [grupo de computadores](../platform/computer-groups.md) em Azure monitor.  O grupo de computadores pode ser baseado em uma consulta de log ou importado de outras fontes, como Active Directory ou grupos do WSUS. Conforme [descrito abaixo](#solutions-and-agents-that-cant-be-targeted), somente os computadores conectados diretamente a Azure monitor serão incluídos no escopo.

Depois de criar o grupo de computadores no espaço de trabalho, você o incluirá em uma configuração de escopo que pode ser aplicada a uma ou mais soluções.
 
 
### <a name="2-create-a-scope-configuration"></a>2. criar uma configuração de escopo
 Uma **configuração de escopo** inclui um ou mais grupos de computadores e pode ser aplicada a uma ou mais soluções. 
 
 Crie uma configuração de escopo usando o processo a seguir.  

 1. Na portal do Azure, navegue até **espaços de trabalho do log Analytics** e selecione seu espaço de trabalho.
 2. Nas propriedades do espaço de trabalho em **fontes de dados do espaço de trabalho** , selecione **configurações de escopo**.
 3. Clique em **Adicionar** para criar uma nova configuração de escopo.
 4. Digite um **nome** para a configuração de escopo.
 5. Clique em **Selecionar grupos de computadores**.
 6. Selecione o grupo de computadores que você criou e, opcionalmente, quaisquer outros grupos a serem adicionados à configuração.  Clique em **Selecionar**.  
 6. Clique em **OK** para criar a configuração de escopo. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. aplicar a configuração de escopo a uma solução.
Quando tiver uma configuração de escopo, você poderá aplicá-la a uma ou mais soluções.  Observe que, embora uma única configuração de escopo possa ser usada com várias soluções, cada solução pode usar apenas uma configuração de escopo.

Aplique uma configuração de escopo usando o processo a seguir.  

 1. Na portal do Azure, navegue até **espaços de trabalho do log Analytics** e selecione seu espaço de trabalho.
 2. Nas propriedades do espaço de trabalho, selecione **soluções**.
 3. Clique na solução que você deseja escopo.
 4. Nas propriedades da solução em fontes de **dados do espaço de trabalho** , selecione **direcionamento de solução**.  Se a opção não estiver disponível, [essa solução não poderá ser direcionada](#solutions-and-agents-that-cant-be-targeted).
 5. Clique em **Adicionar configuração de escopo**.  Se você já tiver uma configuração aplicada a essa solução, essa opção não estará disponível.  Você deve remover a configuração existente antes de adicionar outra.
 6. Clique na configuração de escopo que você criou.
 7. Observe o **status** da configuração para garantir que ela seja mostrada com **êxito**.  Se o status indicar um erro, clique na elipse à direita da configuração e selecione **Editar configuração de escopo** para fazer alterações.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluções e agentes que não podem ser direcionados
A seguir estão os critérios para agentes e soluções que não podem ser usados com o direcionamento de solução.

- Direcionamento de solução só se aplica a soluções que são implantadas em agentes.
- Direcionamento de solução só se aplica a soluções fornecidas pela Microsoft.  Ele não se aplica a soluções [criadas por você mesmo ou por parceiros](solutions-creating.md).
- Você só pode filtrar agentes que se conectam diretamente a Azure Monitor.  As soluções serão implantadas automaticamente em todos os agentes que fazem parte de um grupo de gerenciamento Operations Manager conectado, independentemente de estarem ou não incluídas em uma configuração de escopo.

### <a name="exceptions"></a>Exceções
O direcionamento de solução não pode ser usado com as soluções a seguir, embora elas se ajustem aos critérios declarados.

- Avaliação de Integridade do Agente

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as soluções de monitoramento, incluindo as soluções disponíveis para instalação em seu ambiente em [Adicionar soluções de monitoramento de log Analytics do Azure ao seu espaço de trabalho](solutions.md).
- Saiba mais sobre como criar grupos de computadores em [grupos de computadores em Azure monitor consultas de log](../platform/computer-groups.md).
