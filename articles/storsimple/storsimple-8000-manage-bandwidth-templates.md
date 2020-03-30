---
title: Gerir modelos de largura de banda para série StorSimple 8000 [ Microsoft Docs
description: Descreve como gerir os modelos de largura de banda StorSimple, que lhe permitem controlar o consumo de largura de banda.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 13a3e57bb27c075fc045e87790dbe13369ed9f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254901"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Utilize o serviço StorSimple Device Manager para gerir modelos de largura de banda StorSimple

## <a name="overview"></a>Descrição geral

Os modelos de largura de banda permitem configurar o uso da largura de banda da rede através de vários horários do dia para nificar os dados do dispositivo StorSimple para a nuvem.

Com horários de estrangulamento da largura de banda pode:

* Especifique os horários personalizados de largura de banda dependendo das utilizações da rede de carga de trabalho.
* Centralize a gestão e reutilize os horários em vários dispositivos de forma fácil e perfeita.

> [!NOTE]
> Esta funcionalidade está disponível apenas para dispositivos físicos StorSimple (modelos 8100 e 8600) e não para os Eletrodomésticos StorSimple Cloud (modelos 8010 e 8020).


## <a name="the-bandwidth-templates-blade"></a>A lâmina de modelos de largura de banda

A lâmina de **modelos de largura de banda** tem todos os modelos de largura de banda para o seu serviço num formato tabular, e contém as seguintes informações:

* **Nome** – Um nome único atribuído ao modelo de largura de banda quando foi criado.
* **Horário** – O número de horários contidos num determinado modelo de largura de banda.
* **Utilizado por** – O número de volumes utilizando os modelos de largura de banda.

Também pode encontrar informações adicionais para ajudar a configurar modelos de largura de banda em:

* [Perguntas e respostas sobre modelos de largura de banda](#questions-and-answers-about-bandwidth-templates)
* [Boas práticas para modelos de largura de banda](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Adicione um modelo de largura de banda

Execute os seguintes passos para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda

1. Vá ao seu serviço StorSimple Device Manager, clique em **modelos** de largura de banda e, em seguida, clique **em + Adicionar modelo de largura de banda**.

    ![Clique + Adicionar modelo de largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Na lâmina de modelo adicionar largura de **banda,** faça os seguintes passos:
   
    1. Especifique um nome único para o seu modelo de largura de banda.
    2. Defina um horário de largura de banda. Para criar um horário:
   
        1. A partir da lista de lançamentos, escolha os **Dias** da semana para o qual o horário está configurado. Pode selecionar vários dias.        
        
        2. Introduza um Tempo de **Início** em formato _hh:mm._ É aqui que a programação vai começar.

        3. Introduza um **Tempo Final** em formato _hh:mm._ É aqui que o horário vai parar.
      
           > [!NOTE]
           > Não são permitidos horários sobrepostos. Se os tempos de início e de fim resultarem num calendário sobreposto, verá uma mensagem de erro nesse sentido.

        4. Especifique a **taxa de largura de banda**. Esta é a largura de banda em Megabits por segundo (Mbps) utilizada pelo seu dispositivo StorSimple em operações que envolvem a nuvem (tanto uploads como downloads). Forneça um número entre 1 e 1000 para este campo.

            ![Definir o horário da largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Repita os passos acima para definir vários horários para o seu modelo até que esteja feito.

        5. Clique em **Adicionar** para começar a criar um modelo de largura de banda. O modelo criado é adicionado à lista de modelos de largura de banda.
      

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda

Execute os seguintes passos para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda

1. Vá ao seu serviço StorSimple Device Manager e clique em **modelos**de largura de banda .
2. Na lista de modelos de largura de banda, selecione o modelo que pretende eliminar. Clique à direita e a partir do menu de contexto, **selecione Eliminar**.
3. Quando solicitado para confirmação, clique em **OK**. Isto deve eliminar o modelo de largura de banda. 
4. A lista de modelos de largura de banda atualiza para refletir a eliminação.

> [!NOTE]
> Não é possível guardar as suas alterações se o calendário editado se sobrepor a um horário existente no modelo de largura de banda que está a modificar.

## <a name="delete-a-bandwidth-template"></a>Eliminar um modelo de largura de banda

Execute os seguintes passos para eliminar um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar um modelo de largura de banda

1. Vá ao seu serviço StorSimple Device Manager e clique em **modelos**de largura de banda .
2. Na lista de modelos de largura de banda, selecione o modelo que pretende eliminar. Clique à direita e a partir do menu de contexto, selecione Eliminar.
3. Quando solicitado para confirmação, clique em **OK**. Isto deve eliminar o modelo de largura de banda.
4. A lista de modelos de largura de banda atualiza para refletir a eliminação.

Se o modelo estiver a ser utilizado por qualquer volume, não poderá eliminá-lo. Verá uma mensagem de erro indicando que o modelo está a ser utilizado. Aparecerá uma caixa de diálogo de mensagem de erro a conselhá-lo para que todas as referências ao modelo sejam removidas.

Pode eliminar todas as referências ao modelo acedendo à página dos **Contentores** de Volume e modificando os recipientes de volume que utilizam este modelo para que utilizem outro modelo ou utilizem uma definição de largura de banda personalizada ou ilimitada. Quando todas as referências tiverem sido removidas, pode eliminar o modelo.

## <a name="use-a-default-bandwidth-template"></a>Use um modelo de largura de banda padrão

Um modelo de largura de banda predefinido é fornecido e é utilizado por recipientes de volume por padrão para impor controlos de largura de banda ao aceder à nuvem. O modelo padrão também serve como uma referência pronta para os utilizadores que criam os seus próprios modelos. Os detalhes deste modelo padrão são:

* **Nome** – Noites e fins de semana ilimitados
* **Horário** – Um único horário de segunda a sexta-feira que aplica uma taxa de largura de banda de 1 Mbps entre as 8:00 e as 17:00 horas do dispositivo. A largura de banda está definida para Ilimitado para o resto da semana.

O modelo predefinido pode ser editado. O uso deste modelo (incluindo versões editadas) é rastreado.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Crie um modelo de largura de banda durante todo o dia que começa num momento especificado

Siga este procedimento para criar um horário que comece a uma hora especificada e que se prolonge o dia todo. No exemplo, o horário começa às 9 da manhã e decorre até às 9 h da manhã seguinte. É importante notar que os tempos de início e fim de um determinado horário devem ser contidos no mesmo horário de 24 horas e não podem durar vários dias. Se precisar de configurar modelos de largura de banda que se estendem por vários dias, terá de utilizar vários horários (como mostra o exemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para criar um modelo de largura de banda durante todo o dia

1. Crie um horário que comece às 9 da manhã e que se prolonge até à meia-noite.
2. Adicione outro horário. Configure o segundo horário a decorrer entre a meia-noite e as 9 da manhã.
3. Guarde o modelo de largura de banda.

O horário composto começará então numa hora à sua escolha e correrá o dia todo.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Perguntas e respostas sobre modelos de largura de banda

**Q**. O que acontece aos controlos de largura de banda quando se está entre os horários? (Um horário terminou e outro ainda não começou.)

**A.** Nesses casos, não serão utilizados controlos de largura de banda. Isto significa que o dispositivo pode utilizar largura de banda ilimitada ao diferenciar os dados para a nuvem.

**Q**. Pode modificar os modelos de largura de banda num dispositivo offline?

**A.** Não poderá modificar os modelos de largura de banda em contentores de volumes se o dispositivo correspondente estiver offline.

**Q**. Pode editar um modelo de largura de banda associado a um recipiente de volume quando os volumes associados estão offline?

**A.** Pode modificar um modelo de largura de banda associado a um recipiente de volume cujos volumes estão offline. Note que quando os volumes estiverem offline, nenhum dado será nivida do dispositivo para a nuvem.

**Q**. Pode apagar um modelo predefinido?

**A.** Embora possa eliminar um modelo predefinido, não é uma boa ideia fazê-lo. O uso de um modelo padrão, incluindo versões editadas, é rastreado. Os dados de rastreio são analisados e, ao longo do tempo, é usado para melhorar o modelo padrão.

**Q**. Como determinar que os seus modelos de largura de banda precisam de ser modificados?

**A.** Um dos sinais de que precisa de modificar os modelos de largura de banda é quando começa a ver a rede abrandar ou sufocar várias vezes num dia. Se isso acontecer, monitorize a rede de armazenamento e utilização olhando para as tabelas de Desempenho e Despôs de Rede.

A partir dos dados de entrada da rede, identifique a hora do dia e os recipientes de volume em que ocorre o estrangulamento da rede. Se isso acontecer quando os dados estiverem a ser nividados para a nuvem (obtenha esta informação do desempenho de I/S para todos os recipientes de volume para o dispositivo para a nuvem), então terá de modificar os modelos de largura de banda associados aos seus recipientes de volume.

Depois de os modelos modificados estarem a ser utilizados, terá de monitorizar novamente a rede para obter atrasos significativos. Se estes ainda existirem, terá de revisitar os seus modelos de largura de banda.

**Q**. O que acontece se vários contentores de volume no meu dispositivo tiverem horários que se sobrepõem, mas limites diferentes se aplicam a cada um?

**A.** Vamos supor que tem um dispositivo com recipientes de 3 volumes. Os horários associados a estes contentores sobrepõem-se completamente. Para cada um destes recipientes, os limites de largura de banda utilizados são 5, 10 e 15 Mbps, respectivamente. Quando o I/S estiver a ocorrer em todos estes recipientes ao mesmo tempo, pode aplicar-se o mínimo dos 3 limites de largura de banda: neste caso, 5 Mbps à medida que estes pedidos de I/S de saída partilham a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Boas práticas para modelos de largura de banda

Siga estas melhores práticas para o seu dispositivo StorSimple:

* Configure os modelos de largura de banda no seu dispositivo para permitir o estrangulamento variável da entrada de rede pelo dispositivo em diferentes horas do dia. Estes modelos de largura de banda quando utilizados com horários de backup podem efetivamente aproveitar a largura de banda adicional da rede para operações na nuvem durante as horas fora do pico.
* Calcular a largura de banda real necessária para uma determinada implantação com base no tamanho da implantação e no objetivo de tempo de recuperação necessário (RTO).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

