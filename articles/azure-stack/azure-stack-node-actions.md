---
title: Ações de nó de unidade de dimensionamento no Azure Stack | Documentos da Microsoft
description: Saiba como ver o estado do nó e tire partido do poder no poder de fora, desativar e retomar a ações de nó num sistema integrado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 24c500251c8e91b7542c5c9d3e77676205c88c1f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090972"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Ações de nó de unidade de dimensionamento no Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve como visualizar o estado de uma unidade de escala. Pode ver nós da unidade. Pode executar ações de nó, como o power no power, encerrar, drenar, retomar e reparar. Normalmente, utiliza estas ações de nó durante a substituição de campo de partes ou para ajudar a recuperar um nó.

> [!Important]  
> Todas as ações de nó descritas neste artigo devem visar um nó por vez.

## <a name="view-the-node-status"></a>Ver o estado do nó

No portal do administrador, pode ver o estado de uma unidade de escala e de seus nós associados.

Para ver o estado de uma unidade de escala:

1. Sobre o **gestão da região** mosaico, selecione a região.
2. No lado esquerdo, sob **recursos de infraestrutura**, selecione **unidades de escala**.
3. Nos resultados, selecione a unidade de escala.
4. No lado esquerdo, sob **gerais**, selecione **nós**.

   Ver as seguintes informações:

   - A lista de nós individuais
   - Estado operacional (veja a lista abaixo)
   - Estado de energia (em execução ou parado)
   - modelo de servidor
   - Endereço IP do controlador de gestão da placa base (BMC)
   - Número total de núcleos
   - quantidade total de memória

![Estado de uma unidade de escala](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Estados operacionais do nó

| Estado | Descrição |
|----------------------|-------------------------------------------------------------------|
| A executar | O nó está a participar ativamente na unidade de escala. |
| Parada | O nó não está disponível. |
| A adicionar | O nó está ativamente a ser adicionado para a unidade de escala. |
| A reparar | O nó está ativamente a ser reparado. |
| Manutenção | O nó está em pausa e nenhuma carga de trabalho de utilizador do Active Directory está em execução. |
| Requer remediação | Foi detetado um erro que exige que o nó ser reparado. |

## <a name="scale-unit-node-actions"></a>Ações de nó de unidade de escala

Quando Exibe informações sobre um nó de unidade de escala, também pode realizar ações de nó, tais como:
 - Iniciar e parar (consoante o estado de energia atual)
 - Desativar e retomar (consoante o estado das operações)
 - Reparar
 - Encerrar

Estado operacional do nó determina quais as opções estão disponíveis.

Tem de instalar os módulos do Azure Stack do PowerShell. Estes cmdlets estão no **Azs.Fabric.Admin** módulo. Para instalar ou verificar sua instalação do PowerShell para o Azure Stack, veja [instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Parar

O **parar** ação se desligar o nó. É o mesmo como se pressionar o botão de energia. Ele não envia um sinal de encerramento para o sistema operativo. Para operações de paragem planeada, sempre tente a operação de encerramento pela primeira vez. 

Esta ação é normalmente utilizada quando um nó está em estado suspenso e já não responde a pedidos.

Para executar a ação de parar, abra uma linha de comandos elevada do PowerShell e execute o seguinte cmdlet:

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

No caso improvável de que a ação de stop não funcionar, repita a operação e se ele falhar uma segunda vez utilizam a interface da web BMC em vez disso.

Para obter mais informações, consulte [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Iniciar

O **iniciar** ação ativa o nó. É o mesmo como se pressionar o botão de energia. 
 
Para executar a ação de início, abra uma linha de comandos elevada do PowerShell e execute o seguinte cmdlet:

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

No caso improvável de que a ação de início não funcionar, repita a operação e se ele falhar uma segunda vez utilizam a interface da web BMC em vez disso.

Para obter mais informações, consulte [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Drenar

O **drenar** ação move todas as cargas de trabalho ativas para os nós restantes nessa unidade de escala específico.

Esta ação é normalmente utilizada durante a substituição de campo de partes, como a substituição de um nó completo.

> [!Important]
> Certifique-se de que utilize uma operação num nó de drenagem durante uma janela de manutenção planeada, onde os utilizadores foram notificadas sobre. Em algumas condições, cargas de trabalho ativas podem assistir a interrupções.

Para executar a ação de drenagem, abra uma linha de comandos elevada do PowerShell e execute o seguinte cmdlet:

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Para obter mais informações, consulte [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Retomar

O **retomar** ação de retoma de um nó desativado e marca-o Active Directory para colocação de carga de trabalho. Cargas de trabalho anteriores que estavam em execução no nó não a reativação pós-falha. (Se usar uma operação de drenagem num nó não se esqueça de desligar. Quando liga o nó de volta no, não está marcado como ativo para colocação de carga de trabalho. Quando estiver pronto, tem de utilizar a ação de retoma para marcar o nó como o Active Directory.)

Para executar a ação de retoma, abra uma linha de comandos elevada do PowerShell e execute o seguinte cmdlet:

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Para obter mais informações, consulte [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Reparar

O **reparar** ação repara um nó. Utilize apenas para qualquer um dos seguintes cenários:
 - Substituição de nó completo (com ou sem novos discos de dados)
 - Após a falha de componente de hardware e de substituição (se aconselhado na documentação (FRU) de unidade substituível em campo).

> [!Important]  
> Consulte a documentação de FRU do seu fornecedor de hardware de OEM para obter os passos exatos quando é necessário substituir um nó ou de componentes de hardware individuais. A documentação de FRU irá especificar se tiver de executar a ação de reparação depois de substituir um componente de hardware. 

Quando executa a ação de reparação, tem de especificar o endereço IP do BMC. 

Para executar a ação de reparação, abra uma linha de comandos elevada do PowerShell e execute o seguinte cmdlet:

  ```PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Encerrar

O **encerramento** fist ação move todas as cargas de trabalho ativas para os nós restantes na mesma unidade de escala. Em seguida, a ação normalmente encerra o nó de unidade de escala.

Depois de iniciar um nó que estava encerrado, tem de executar o [retomar](#resume) ação. Cargas de trabalho anteriores que estavam em execução no nó não a reativação pós-falha.

Se a operação de encerramento falhar, tente o [drenar](#drain) operação seguido da operação de encerramento.

Para executar a ação de encerramento, abra uma linha de comandos elevada do PowerShell e execute o seguinte cmdlet:

  ```PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o módulo de administrador de recursos de infraestrutura do Azure Stack, veja [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
