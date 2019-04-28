---
title: Dimensionar automaticamente um serviço em nuvem no portal do | Documentos da Microsoft
description: Saiba como utilizar o portal para configurar regras de dimensionamento automático para uma função de web do serviço cloud ou a função de trabalho no Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: f5597773b3127852481d5e14844bed889c4d6f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435321"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Como configurar o dimensionamento automático para um serviço em nuvem no portal

Condições podem ser definidas para uma função de trabalho do serviço de cloud que acionam um dimensionamento e reduzir a operação. As condições para a função podem basear-se a CPU, disco ou de carga na rede da função. Também pode definir uma condição com base numa fila de mensagens ou a métrica de alguns outros recursos do Azure associados à subscrição.

> [!NOTE]
> Este artigo se concentra em funções web e de trabalho do serviço Cloud. Quando cria uma máquina virtual (clássico) diretamente, está alojada num serviço cloud. Pode aumentar uma máquina virtual padrão por meio de associação com um [conjunto de disponibilidade](../virtual-machines/windows/classic/configure-availability-classic.md) e transformá-los manualmente ou desativar.

## <a name="considerations"></a>Considerações
Deve considerar as seguintes informações antes de configurar o dimensionamento para a sua aplicação:

* Dimensionamento é afetado pela utilização de núcleos.

    Maior instâncias de função utilizem mais núcleos. Pode dimensionar uma aplicação apenas dentro do limite de núcleos para a sua subscrição. Por exemplo, digamos que a sua subscrição tem um limite de 20 núcleos. Se executar um aplicativo com os dois serviços de cloud de médio porte (um total de 4 núcleos), pode apenas aumentar verticalmente outras implementações de serviços cloud na sua subscrição aos restantes 16 núcleos. Para obter mais informações sobre os tamanhos, veja [tamanhos do serviço Cloud](cloud-services-sizes-specs.md).

* Pode dimensionar com base num limiar de mensagem de fila. Para obter mais informações sobre como utilizar as filas, veja [como utilizar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Também pode escalar a outros recursos associados à subscrição.

* Para ativar a elevada disponibilidade do seu aplicativo, deve garantir que é implementado com duas ou mais instâncias de função. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

* Dimensionamento automático só acontece quando todas as funções na **pronto** estado.  


## <a name="where-scale-is-located"></a>Onde está localizado o dimensionamento
Depois de selecionar o seu serviço cloud, deve ter o painel do serviço cloud visível.

1. No painel do serviço cloud, sobre o **funções e instâncias** mosaico, selecione o nome do serviço cloud.   
   **IMPORTANTE**: Lembre-se de que clique na função de serviço cloud, não a instância de função que é inferior a função.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selecione o **dimensionamento** mosaico.

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Dimensionamento automático
Pode configurar definições de dimensionamento para uma função com qualquer um dos dois modos **manual** ou **automática**. Manual é como poderia esperar, definir o número absoluto de instâncias. No entanto, automático permite-lhe o conjunto de regras que controlam como e por como muito que deve aumentar.

Definir o **Dimensionar** a opção de **regras de agendamento e desempenho**.

![Definições de dimensionamento de serviços na cloud com o perfil e regra](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Um perfil existente.
2. Adicione uma regra para o perfil de principal.
3. Adicione outro perfil.

Selecione **adicionar perfil**. O perfil determina o modo de que pretende utilizar para a escala: **sempre**, **periodicidade**, **fixo data**.

Depois de ter configurado o perfil e as regras, selecione o **guardar** na parte superior.

#### <a name="profile"></a>Perfil
O perfil define instâncias mínimas e máxima para a escala, e também quando está ativo neste intervalo da escala.

* **Sempre**

    Tenha sempre este intervalo de instâncias disponíveis.  

    ![Serviço em nuvem que sempre se ajustam](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Periodicidade**

    Escolha um conjunto de dias da semana para dimensionar.

    ![Nível de serviço da cloud com uma agenda de periodicidade](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Data fixa**

    Um intervalo de datas fixo para aumentar a função.

    ![Nível de serviço da cLoud com uma data fixa](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Depois de ter configurado o perfil, selecione o **OK** na parte inferior do painel do perfil.

#### <a name="rule"></a>Regra
As regras são adicionadas a um perfil e representam uma condição que aciona a escala.

O acionador de regra se baseia numa métrica do serviço cloud (utilização da CPU, a atividade do disco ou atividade de rede) ao qual pode adicionar um valor condicional. Além disso, pode ter o acionador com base numa fila de mensagens ou a métrica de alguns outros recursos do Azure associados à subscrição.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Depois de configurar a regra, selecione o **OK** na parte inferior do painel de regra.

## <a name="back-to-manual-scale"></a>Voltar ao dimensionamento manual
Navegue para o [definições de dimensionamento](#where-scale-is-located) e defina a **Dimensionar por** a opção de **uma contagem de instâncias que introduzo manualmente**.

![Definições de dimensionamento de serviços na cloud com o perfil e regra](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Esta definição remove o dimensionamento automático da função e, em seguida, pode definir a contagem de instâncias diretamente.

1. A opção de (manuais ou automáticos) de dimensionamento.
2. Um slider de instância de função para definir as instâncias para escalar até.
3. Instâncias de função para escalar até.

Depois de ter configurado as definições de dimensionamento, selecione o **guardar** na parte superior.
