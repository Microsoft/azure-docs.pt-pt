---
title: Escala automática de um serviço de nuvem no portal Microsoft Docs
description: Aprenda a usar o portal para configurar regras de escala automática para uma função web de serviço na nuvem ou papel de trabalhador no Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 5880544137855a2ea5bcd6d6e4bada46563564ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360842"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Como configurar a escala automática para um Serviço de Nuvem no portal

Podem ser definidas condições para uma função de trabalhador de serviço na nuvem que desencadeie uma escala dentro ou fora de funcionamento. As condições para o papel podem basear-se no CPU, no disco ou na carga de rede do papel. Também pode definir uma condição com base numa fila de mensagens ou na métrica de algum outro recurso Azure associado à sua subscrição.

> [!NOTE]
> Este artigo centra-se nas funções web e dos trabalhadores do Cloud Service. Quando se cria uma máquina virtual (clássica) diretamente, está alojada num serviço de nuvem. Pode escalar uma máquina virtual padrão associando-a a um conjunto de [disponibilidade](../virtual-machines/windows/classic/configure-availability-classic.md) e ligá-la manualmente ou desligá-las manualmente.

## <a name="considerations"></a>Considerações
Deve considerar as seguintes informações antes de configurar a escala para a sua aplicação:

* A escalação é afetada pelo uso do núcleo.

    Casos de papel maiores usam mais núcleos. Só pode escalar uma aplicação dentro do limite de núcleos para a sua subscrição. Por exemplo, digamos que a sua subscrição tem um limite de 20 núcleos. Se executar uma aplicação com dois serviços de nuvem de tamanho médio (um total de 4 núcleos), só pode aumentar outras implementações de serviços na nuvem na sua subscrição pelos restantes 16 núcleos. Para mais informações sobre tamanhos, consulte [Cloud Service Sizes](cloud-services-sizes-specs.md).

* Pode escalar com base num limiar de mensagem de fila. Para obter mais informações sobre como utilizar as filas, consulte [Como utilizar o Serviço](../storage/queues/storage-dotnet-how-to-use-queues.md)de Armazenamento de Filas .

* Também pode escalar outros recursos associados à sua subscrição.

* Para permitir uma elevada disponibilidade da sua aplicação, deve certificar-se de que é implementada com duas ou mais instâncias de função. Para mais informações, consulte acordos de [nível de serviço](https://azure.microsoft.com/support/legal/sla/).

* A Escala Automática só acontece quando todos os papéis estão em estado **de Ready.**  


## <a name="where-scale-is-located"></a>Onde a escala está localizada
Depois de selecionar o seu serviço de cloud, deverá ter a lâmina de serviço em nuvem visível.

1. Na lâmina de serviço da nuvem, no azulejo **Roles and Instances,** selecione o nome do serviço de nuvem.   
   **IMPORTANTE**: Certifique-se de clicar na função de serviço na nuvem, não na instância de função que está abaixo da função.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selecione o azulejo de **escala.**

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Escala automática
Pode configurar as definições de escala para uma função com dois modos **manual** ou **automático**. Manual é como seria de esperar, define a contagem absoluta de casos. No entanto, o automático permite-lhe definir regras que regem como e por quanto deve escalar.

Desconte a **Escala por** opção para **agendar e regras**de desempenho.

![Configurações de escala de serviços em nuvem com perfil e regra](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Um perfil existente.
2. Adicione uma regra para o perfil dos pais.
3. Adicione outro perfil.

Selecione **Adicionar Perfil**. O perfil determina qual o modo que pretende utilizar para a escala: **sempre,** **recorrência,** **data fixa**.

Depois de configurar o perfil e as regras, selecione o ícone **Guardar** na parte superior.

#### <a name="profile"></a>Perfil
O perfil define instâncias mínimas e máximas para a escala, e também quando esta escala está ativa.

* **Sempre**

    Mantenha sempre esta gama de instâncias disponíveis.  

    ![Serviço de nuvem que sempre escala](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Recorrência**

    Escolha um conjunto de dias da semana para escalar.

    ![Escala de serviço em nuvem com um horário de recorrência](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Data Fixa**

    Uma faixa de data fixa para escalar o papel.

    ![Escala de serviço CLoud com data fixa](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Depois de configurar o perfil, selecione o botão **OK** na parte inferior da lâmina de perfil.

#### <a name="rule"></a>Regra
As regras são adicionadas a um perfil e representam uma condição que desencadeia a escala.

O gatilho da regra baseia-se numa métrica do serviço de nuvem (utilização do CPU, atividade do disco ou atividade da rede) à qual pode adicionar um valor condicional. Além disso, pode ter o gatilho com base numa fila de mensagens ou na métrica de algum outro recurso Azure associado à sua subscrição.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Depois de configurar a regra, selecione o botão **OK** na parte inferior da lâmina de regra.

## <a name="back-to-manual-scale"></a>De volta à escala manual
Navegue para as [definições](#where-scale-is-located) de escala e detete a **Balança por** opção para uma contagem de **exemplos que introduzo manualmente**.

![Configurações de escala de serviços em nuvem com perfil e regra](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Esta definição remove a escala automatizada da função e, em seguida, pode definir a contagem de ocorrência sem rodeios.

1. A opção de escala (manual ou automatizada).
2. Um slider de exemplo de papel para definir as instâncias à escala para.
3. Casos do papel a escalar para.

Depois de configurar as definições de escala, selecione o ícone **Guardar** na parte superior.



