---
title: Gerir modelos de largura de banda para série StorSimple 8000 | Microsoft Docs
description: Descreve como gerir modelos de largura de banda StorSimple, que permitem controlar o consumo de largura de banda.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 56170ffbbfe14248bcfd1f94549a3565873f8646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005899"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Utilize o serviço StorSimple Device Manager para gerir modelos de largura de banda StorSimple

## <a name="overview"></a>Descrição Geral

Os modelos de largura de banda permitem configurar o uso da largura de banda da rede em vários horários do dia para nivelar os dados do dispositivo StorSimple para a nuvem.

Com horários de aceleração de largura de banda, pode:

* Especifique os horários de largura de banda personalizados dependendo do uso da rede de carga de trabalho.
* Centralize a gestão e reutilização dos horários em vários dispositivos de forma fácil e sem emenda.

> [!NOTE]
> Esta funcionalidade está disponível apenas para dispositivos físicos StorSimple (modelos 8100 e 8600) e não para Aparelhos Cloud StorSimple (modelos 8010 e 8020).


## <a name="the-bandwidth-templates-blade"></a>A lâmina de modelos de largura de banda

A lâmina **de modelos de** largura de banda tem todos os modelos de largura de banda para o seu serviço em formato tabular, e contém as seguintes informações:

* **Nome** – Um nome único atribuído ao modelo de largura de banda quando foi criado.
* **Horário** – O número de horários contidos num determinado modelo de largura de banda.
* **Utilizado por** – O número de volumes utilizando os modelos de largura de banda.

Também pode encontrar informações adicionais para ajudar a configurar os modelos de largura de banda em:

* [Perguntas e respostas sobre modelos de largura de banda](#questions-and-answers-about-bandwidth-templates)
* [Melhores práticas para modelos de largura de banda](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Adicione um modelo de largura de banda

Execute os seguintes passos para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda

1. Vá ao seu serviço StorSimple Device Manager, clique nos **modelos de largura de banda** e, em seguida, clique em + Adicionar modelo de largura de **banda**.

    ![Clique + Adicionar modelo de largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Na lâmina do modelo de **largura de banda Adicionar,** faça os seguintes passos:
   
    1. Especifique um nome único para o seu modelo de largura de banda.
    2. Defina um horário de largura de banda. Para criar um horário:
   
        1. Da lista de suspensos, escolha os **Dias** da semana para que a programação esteja configurada. Pode selecionar vários dias.        
        
        2. Introduza uma **hora de início** no formato _hh:mm._ É aqui que a agenda vai começar.

        3. Introduza um **Fim de Tempo** em formato _hh:mm._ É aqui que o horário vai parar.
      
           > [!NOTE]
           > Horários sobrepostos não são permitidos. Se os tempos de início e fim resultarem num calendário de sobreposição, verá uma mensagem de erro nesse sentido.

        4. Especificar a **Taxa de Largura de Banda**. Esta é a largura de banda em Megabits por segundo (Mbps) usada pelo seu dispositivo StorSimple em operações que envolvam a nuvem (tanto uploads como downloads). Forneça um número entre 1 e 1000 para este campo.

            ![Definir horário de largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Repita os passos acima para definir vários horários para o seu modelo até estar pronto.

        5. Clique **em Adicionar** para começar a criar um modelo de largura de banda. O modelo criado é adicionado à lista de modelos de largura de banda.
      

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda

Execute os seguintes passos para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda

1. Vá ao seu serviço StorSimple Device Manager e clique nos **modelos de largura de banda**.
2. Na lista de modelos de largura de banda, selecione o modelo que deseja eliminar. Clique à direita e a partir do menu de contexto, **selecione Delete**.
3. Quando solicitado para confirmação, clique **em OK**. Isto deve eliminar o modelo de largura de banda. 
4. A lista de modelos de largura de banda atualiza-se para refletir a eliminação.

> [!NOTE]
> Não é possível guardar as alterações se o calendário editado se sobrepor a um calendário existente no modelo de largura de banda que está a modificar.

## <a name="delete-a-bandwidth-template"></a>Elimine um modelo de largura de banda

Execute os seguintes passos para eliminar um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar um modelo de largura de banda

1. Vá ao seu serviço StorSimple Device Manager e clique nos **modelos de largura de banda**.
2. Na lista de modelos de largura de banda, selecione o modelo que deseja eliminar. Clique à direita e a partir do menu de contexto, selecione Delete.
3. Quando solicitado para confirmação, clique **em OK**. Isto deve eliminar o modelo de largura de banda.
4. A lista de modelos de largura de banda atualiza-se para refletir a eliminação.

Se o modelo for utilizado por qualquer volume, não poderá eliminá-lo. Verá uma mensagem de erro indicando que o modelo está a ser utilizado. Aparecerá uma caixa de diálogo de mensagem de erro aconselhando-o a remover todas as referências ao modelo.

Pode eliminar todas as referências ao modelo acedendo à página **de Recipientes de Volume** e modificando os recipientes de volume que utilizam este modelo para que utilizem outro modelo ou utilizem uma definição de largura de banda personalizada ou ilimitada. Quando todas as referências tiverem sido removidas, pode eliminar o modelo.

## <a name="use-a-default-bandwidth-template"></a>Use um modelo de largura de banda padrão

Um modelo de largura de banda predefinido é fornecido e é usado por recipientes de volume por padrão para impor controlos de largura de banda ao aceder à nuvem. O modelo padrão também serve como uma referência pronta para os utilizadores que criam os seus próprios modelos. Os detalhes deste modelo padrão são:

* **Nome** - Noites e fins de semana ilimitados
* **Horário** – Um único horário de segunda a sexta-feira que aplica uma taxa de largura de banda de 1 Mbps entre as 8:00 e as 17:00 horas. A largura de banda está definida para Ilimitado para o resto da semana.

O modelo predefinido pode ser editado. O uso deste modelo (incluindo versões editadas) é rastreado.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Crie um modelo de largura de banda durante todo o dia que comece num momento especificado

Siga este procedimento para criar um horário que começa a uma hora especificada e funciona durante todo o dia. No exemplo, o horário começa às 9:00 da manhã e decorre até às 9:00 da manhã seguinte. É importante notar que os horários de início e fim de um determinado horário devem ser ambos contidos no mesmo horário de 24 horas e não podem durar vários dias. Se precisar de configurar modelos de largura de banda que se estendem por vários dias, terá de utilizar vários horários (como mostra o exemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para criar um modelo de largura de banda durante todo o dia

1. Crie um horário que começa às 9:00 da manhã e decorre até à meia-noite.
2. Adicione outro horário. Configurar o segundo horário a decorrer a partir da meia-noite até às 9 da manhã.
3. Guarde o modelo de largura de banda.

A programação composta começará então no momento da sua escolha e funcionará durante todo o dia.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Perguntas e respostas sobre modelos de largura de banda

**Q**. O que acontece aos controlos de largura de banda quando se está entre os horários? (Um horário terminou e outro ainda não começou.)

**A**. Nesses casos, não serão utilizados controlos de largura de banda. Isto significa que o dispositivo pode usar largura de banda ilimitada ao nivelar dados para a nuvem.

**Q**. Pode modificar modelos de largura de banda num dispositivo offline?

**A**. Não será possível modificar os modelos de largura de banda nos recipientes de volume se o dispositivo correspondente estiver offline.

**Q**. Pode editar um modelo de largura de banda associado a um recipiente de volume quando os volumes associados estão offline?

**A**. Pode modificar um modelo de largura de banda associado a um recipiente de volume cujos volumes estão offline. Note que quando os volumes estão offline, nenhum dado será hierárquico do dispositivo para a nuvem.

**Q**. Pode eliminar um modelo predefinido?

**A**. Embora possa eliminar um modelo padrão, não é boa ideia fazê-lo. O uso de um modelo padrão, incluindo versões editadas, é rastreado. Os dados de rastreio são analisados e ao longo do tempo, é usado para melhorar o modelo padrão.

**Q**. Como determinar que os seus modelos de largura de banda precisam de ser modificados?

**A**. Um dos sinais de que precisa de modificar os modelos de largura de banda é quando começa a ver a rede abrandar ou sufocar várias vezes num dia. Se isso acontecer, monitorize a rede de armazenamento e utilização olhando para os gráficos de desempenho e de produção de rede.

A partir dos dados de produção da rede, identifique a hora do dia e os recipientes de volume em que ocorre o estrangulamento da rede. Se isso acontecer quando os dados estiverem a ser hierárquicos para a nuvem (obtenha esta informação do desempenho de E/S para todos os recipientes de volume para o dispositivo em nuvem), então terá de modificar os modelos de largura de banda associados aos seus recipientes de volume.

Após a utilização dos modelos modificados, terá de monitorizar novamente a rede para obter atrasos significativos. Se estes ainda existirem, então terá de revisitar os seus modelos de largura de banda.

**Q**. O que acontece se vários recipientes de volume no meu dispositivo tiverem horários que se sobrepõem, mas limites diferentes se aplicam a cada um?

**A**. Vamos supor que tem um dispositivo com 3 recipientes de volume. Os horários associados a estes contentores sobrepõem-se completamente. Para cada um destes recipientes, os limites de largura de banda utilizados são de 5, 10 e 15 Mbps, respectivamente. Quando ocorrem E/O em todos estes recipientes ao mesmo tempo, pode ser aplicado o mínimo dos 3 limites de largura de banda: neste caso, 5 Mbps como estes pedidos de I/O de saída partilham a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Melhores práticas para modelos de largura de banda

Siga estas boas práticas para o seu dispositivo StorSimple:

* Configure os modelos de largura de banda no seu dispositivo para permitir o estrangulamento variável da produção da rede pelo dispositivo em diferentes horas do dia. Estes modelos de largura de banda quando usados com horários de backup podem efetivamente alavancar largura de banda adicional para operações na nuvem durante as horas de ponta.
* Calcular a largura de banda real necessária para uma determinada implantação com base no tamanho da implantação e no objetivo de tempo de recuperação necessário (RTO).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

