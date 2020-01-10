---
title: Dimensionar automaticamente um serviço de nuvem no portal | Microsoft Docs
description: Saiba como usar o portal para configurar regras de dimensionamento automático para uma função Web de serviço de nuvem ou função de trabalho no Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 5880544137855a2ea5bcd6d6e4bada46563564ad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360842"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Como configurar o dimensionamento automático para um serviço de nuvem no portal

As condições podem ser definidas para uma função de trabalho do serviço de nuvem que dispare uma operação de redução ou saída. As condições para a função podem ser baseadas na CPU, no disco ou na carga de rede da função. Você também pode definir uma condição com base em uma fila de mensagens ou na métrica de algum outro recurso do Azure associado à sua assinatura.

> [!NOTE]
> Este artigo se concentra nas funções Web e de trabalho do serviço de nuvem. Quando você cria uma máquina virtual (clássica) diretamente, ela é hospedada em um serviço de nuvem. Você pode dimensionar uma máquina virtual padrão associando-a a um [conjunto de disponibilidade](../virtual-machines/windows/classic/configure-availability-classic.md) e ativá-las manualmente.

## <a name="considerations"></a>Considerações
Você deve considerar as seguintes informações antes de configurar o dimensionamento para seu aplicativo:

* O dimensionamento é afetado pelo uso de núcleo.

    As instâncias de função maiores usam mais núcleos. Você pode dimensionar um aplicativo somente dentro do limite de núcleos para sua assinatura. Por exemplo, digamos que sua assinatura tenha um limite de 20 núcleos. Se você executar um aplicativo com dois serviços de nuvem de tamanho médio (um total de 4 núcleos), poderá escalar verticalmente outras implantações de serviço de nuvem em sua assinatura pelos 16 núcleos restantes. Para obter mais informações sobre tamanhos, consulte [tamanhos de serviço de nuvem](cloud-services-sizes-specs.md).

* Você pode dimensionar com base em um limite de mensagem da fila. Para obter mais informações sobre como usar filas, consulte [como usar o serviço de armazenamento de filas](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Você também pode dimensionar outros recursos associados à sua assinatura.

* Para habilitar a alta disponibilidade de seu aplicativo, você deve garantir que ele seja implantado com duas ou mais instâncias de função. Para obter mais informações, consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

* O dimensionamento automático ocorre apenas quando todas as funções estão no estado **pronto** .  


## <a name="where-scale-is-located"></a>Onde a escala está localizada
Depois de selecionar o serviço de nuvem, você deve ter a folha serviço de nuvem visível.

1. Na folha serviço de nuvem, no bloco **funções e instâncias** , selecione o nome do serviço de nuvem.   
   **Importante**: Certifique-se de clicar na função de serviço de nuvem, não na instância de função que está abaixo da função.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Selecione o bloco **escala** .

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Escala automática
Você pode definir configurações de escala para uma função com dois modos **manual** ou **automático**. Manual é como você esperaria, você define a contagem absoluta de instâncias. No entanto, o automático permite que você defina regras que regem como e por quanto você deve dimensionar.

Defina a opção **Dimensionar por** para **agendar e regras de desempenho**.

![Configurações de escala dos serviços de nuvem com perfil e regra](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Um perfil existente.
2. Adicione uma regra para o perfil pai.
3. Adicionar outro perfil.

Selecione **Adicionar perfil**. O perfil determina qual modo você deseja usar para a escala: **sempre**, **recorrência**, **data fixa**.

Depois de configurar o perfil e as regras, selecione o ícone **salvar** na parte superior.

#### <a name="profile"></a>Perfil
O perfil define as instâncias mínima e máxima para a escala e também quando esse intervalo de escala está ativo.

* **Sempre**

    Sempre mantenha esse intervalo de instâncias disponíveis.  

    ![Serviço de nuvem que sempre dimensiona](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Periodicidade**

    Escolha um conjunto de dias da semana para dimensionar.

    ![Escala do serviço de nuvem com uma agenda de recorrência](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Data fixa**

    Um intervalo de datas fixo para dimensionar a função.

    ![Escala do serviço de nuvem com uma data fixa](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Depois de configurar o perfil, selecione o botão **OK** na parte inferior da folha do perfil.

#### <a name="rule"></a>Regra
As regras são adicionadas a um perfil e representam uma condição que dispara a escala.

O gatilho de regra é baseado em uma métrica do serviço de nuvem (uso da CPU, atividade de disco ou atividade de rede) à qual você pode adicionar um valor condicional. Além disso, você pode ter o gatilho baseado em uma fila de mensagens ou a métrica de algum outro recurso do Azure associado à sua assinatura.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Depois de configurar a regra, selecione o botão **OK** na parte inferior da folha da regra.

## <a name="back-to-manual-scale"></a>Voltar ao dimensionamento manual
Navegue até as [configurações de escala](#where-scale-is-located) e defina a opção **Dimensionar por** para **uma contagem de instâncias inserida manualmente**.

![Configurações de escala dos serviços de nuvem com perfil e regra](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Essa configuração remove o dimensionamento automático da função e, em seguida, você pode definir a contagem de instâncias diretamente.

1. A opção escala (manual ou automatizada).
2. Um controle deslizante de instância de função para definir as instâncias a serem dimensionadas.
3. Instâncias da função para a qual dimensionar.

Depois de definir as configurações de escala, selecione o ícone **salvar** na parte superior.



