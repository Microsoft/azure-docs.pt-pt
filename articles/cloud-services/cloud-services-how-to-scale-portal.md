---
title: Escalar automaticamente um serviço de nuvem no portal Microsoft Docs
description: Aprenda a usar o portal para configurar regras de escala automática para um papel web de serviço na nuvem ou papel de trabalhador em Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 06a0209c2bbd0982054d33c199685d016f405b0c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165490"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Como configurar a escala automática para um Serviço de Nuvem no portal

Podem ser definidas condições para um papel de trabalhador de serviço de nuvem que desencadeie uma escala dentro ou fora do funcionamento. As condições para o papel podem basear-se no CPU, disco ou carga de rede da função. Também pode definir uma condição com base numa fila de mensagens ou na métrica de algum outro recurso Azure associado à sua subscrição.

> [!NOTE]
> Este artigo centra-se nas funções web e trabalhadora do Cloud Service. Quando cria uma máquina virtual (clássica) diretamente, é hospedada num serviço de nuvem. Pode escalar uma máquina virtual padrão associando-a a um [conjunto de disponibilidade](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) e ligando-as ou desligando manualmente.

## <a name="considerations"></a>Considerações
Deve considerar as seguintes informações antes de configurar o escalonamento para a sua aplicação:

* A escala é afetada pelo uso do núcleo.

    Casos de papel maior usam mais núcleos. Pode escalar uma aplicação apenas dentro do limite de núcleos para a sua subscrição. Por exemplo, digamos que a sua subscrição tem um limite de 20 núcleos. Se executar uma aplicação com dois serviços de nuvem de tamanho médio (um total de 4 núcleos), só pode aumentar outras implementações de serviços em nuvem na sua subscrição pelos restantes 16 núcleos. Para obter mais informações sobre tamanhos, consulte [os tamanhos do serviço de nuvem.](cloud-services-sizes-specs.md)

* Pode escalar com base num limiar de mensagem de fila. Para obter mais informações sobre como utilizar as filas, consulte [Como utilizar o Serviço de Armazenamento de Filas.](../storage/queues/storage-dotnet-how-to-use-queues.md)

* Também pode escalar outros recursos associados à sua subscrição.

* Para ativar a elevada disponibilidade da sua aplicação, deve certificar-se de que é implementada com duas ou mais instâncias de função. Para mais informações, consulte [os Contratos de Nível de Serviço.](https://azure.microsoft.com/support/legal/sla/)

* A Escala Automática só acontece quando todas as funções estão no estado **de Ready.**  


## <a name="where-scale-is-located"></a>Onde a escala está localizada
Depois de selecionar o seu serviço na nuvem, deverá ter a lâmina de serviço na nuvem visível.

1. Na lâmina de serviço de nuvem, no azulejo **Roles and Instances,** selecione o nome do serviço de nuvem.   
   **IMPORTANTE**: Certifique-se de clicar na função de serviço de nuvem, não na instância de função que está abaixo do papel.

    ![Screenshot dos Papéis e instâncias em azulejo com a função do trabalhador com a opção S B Queue 1 delineada a vermelho.](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selecione o azulejo de **escala.**

    ![Screenshot da página operações com o azulejo de Venda delineado a vermelho.](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Escala automática
Pode configurar as definições de escala para uma função com dois modos **manuais** ou **automáticos**. Manual é como seria de esperar, definir a contagem absoluta de casos. No entanto, o automático permite-lhe definir regras que regem como e por quanto deve escalar.

Desagrema a **Escala por** opção de **programação e de desempenho.**

![Screenshot que mostra a opção de horário e regras de desempenho.](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Um perfil existente.
2. Adicione uma regra para o perfil dos pais.
3. Adicione outro perfil.

Selecione **Adicionar Perfil**. O perfil determina qual o modo que pretende utilizar para a balança: **sempre,** **recorrência,** **data fixa**.

Depois de configurar o perfil e as regras, selecione o ícone **Save** na parte superior.

#### <a name="profile"></a>Perfil
O perfil define instâncias mínimas e máximas para a escala, e também quando esta gama de escala está ativa.

* **Always** (Sempre)

    Mantenha sempre este leque de casos disponíveis.  

    ![Serviço em nuvem que sempre escala](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Recorrência**

    Escolha um conjunto de dias da semana à escala.

    ![Escala de serviço em nuvem com um horário de recorrência](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Data Fixa**

    Um intervalo de data fixa para escalar o papel.

    ![Escala de serviço CLoud com data fixa](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Depois de configurar o perfil, selecione o botão **OK** na parte inferior da lâmina de perfil.

#### <a name="rule"></a>Regra
As regras são adicionadas a um perfil e representam uma condição que desencadeia a escala.

O gatilho da regra baseia-se numa métrica do serviço de nuvem (utilização do CPU, atividade do disco ou atividade de rede) à qual pode adicionar um valor condicional. Além disso, pode ter o gatilho baseado numa fila de mensagens ou na métrica de algum outro recurso Azure associado à sua subscrição.

![Screenshot da caixa de diálogo Regra com a opção nome métrico delineada a vermelho.](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Depois de configurar a regra, selecione o botão **OK** na parte inferior da lâmina de regra.

## <a name="back-to-manual-scale"></a>Voltar à escala manual
Navegue até às [definições](#where-scale-is-located) de escala e defina a **Escala por** opção para **uma contagem de exemplos que inseri manualmente**.

![Configurações de escala de serviços em nuvem com perfil e regra](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Esta definição remove a escala automatizada da função e, em seguida, pode definir a contagem de ocorrências diretamente.

1. A opção de balança (manual ou automatizada).
2. Um slider de exemplo de papel para definir as instâncias para escalar para.
3. Casos do papel à escala para.

Depois de configurar as definições de escala, selecione o ícone **Guardar** na parte superior.