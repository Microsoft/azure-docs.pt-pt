---
title: Gerir modelos de largura de banda para a série StorSimple 8000 | Documentos da Microsoft
description: Descreve como gerir modelos de largura de banda do StorSimple, que permitem-lhe controlar o consumo de largura de banda.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699476"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Utilizar o serviço StorSimple Device Manager para gerir modelos de largura de banda do StorSimple

## <a name="overview"></a>Descrição geral

Modelos de largura de banda permitem-lhe configurar a utilização de largura de banda de rede em várias agendas de hora do dia a uma camada de dados do dispositivo StorSimple para a cloud.

Com agendas de limitação de largura de banda, pode:

* Especifica agendamentos personalizados de largura de banda, consoante as utilizações de rede de carga de trabalho.
* Centralize o gerenciamento e reutilizar as agendas em vários dispositivos de uma maneira fácil e direta.

> [!NOTE]
> Esta funcionalidade está disponível apenas para dispositivos físicos do StorSimple (modelos 8100 e 8600) e não para a StorSimple Cloud Appliances (modelos 8010 e 8020).


## <a name="the-bandwidth-templates-blade"></a>O painel de modelos de largura de banda

O **modelos de largura de banda** painel tem todos os modelos de largura de banda para o seu serviço num formato tabular e contém as seguintes informações:

* **Nome** – um nome exclusivo atribuído ao modelo da largura de banda quando foram criada.
* **Agenda** – o número de agendas contidas num modelo de largura de banda especificada.
* **Utilizado por** – o número de volumes com os modelos de largura de banda.

Também pode encontrar informações adicionais para ajudar a configurar modelos de largura de banda no:

* [Perguntas e respostas sobre os modelos de largura de banda](#questions-and-answers-about-bandwidth-templates)
* [Melhores práticas de modelos de largura de banda](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Adicionar um modelo de largura de banda

Execute os seguintes passos para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda

1. Aceda ao seu serviço StorSimple Device Manager, clique em **modelos de largura de banda** e, em seguida, clique em **+ modelo de largura de banda de adicionar**.

    ![Clique em + adicionar o modelo de largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Na **modelo de largura de banda de adicionar** painel, siga os passos abaixo:
   
    1. Especifique um nome exclusivo para o modelo de largura de banda.
    2. Defina um agendamento de largura de banda. Para criar um agendamento:
   
        1. Na lista pendente, escolha o **dias** da semana a agenda está configurada para. Pode selecionar vários dias.        
        
        2. Introduza um **Start Time** na _hh: mm_ formato. Isso é quando a agenda será iniciada.

        3. Introduza um **hora de fim** na _hh: mm_ formato. Isso é quando o agendamento irá parar.
      
           > [!NOTE]
           > Agendas de sobreposição não são permitidas. Se as horas de início e de fim irão resultar numa programação de sobreposição, verá uma mensagem de erro para o efeito.

        4. Especifique a **taxa de largura de banda**. Esta é a largura de banda em Megabits por segundo (Mbps) utilizado pelo seu dispositivo StorSimple em operações que envolvem a nuvem (carregamentos e transferências). Forneça um número entre 1 e 1000 para este campo.

            ![Definir a agenda de largura de banda](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Repita os passos acima para definir várias agendas para o modelo até que termine.

        5. Clique em **adicionar** para começar a criar um modelo de largura de banda. O modelo criado é adicionado à lista de modelos de largura de banda.
      

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda

Execute os seguintes passos para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda

1. Vá para o StorSimple Device Manager service e clique em **modelos de largura de banda**.
2. Na lista de modelos de largura de banda, selecione o modelo que pretende eliminar. Com o botão direito e no menu de contexto, selecione **eliminar**.
3. Quando lhe for pedida confirmação, clique em **OK**. Isso deve eliminar o modelo de largura de banda. 
4. A lista de atualizações de modelos de largura de banda para refletir a eliminação.

> [!NOTE]
> Não é possível guardar as alterações se o agendamento editado sobrepõe-se com uma agenda existente no modelo de largura de banda que está a modificar.

## <a name="delete-a-bandwidth-template"></a>Eliminar um modelo de largura de banda

Execute os passos seguintes para eliminar um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar um modelo de largura de banda

1. Vá para o StorSimple Device Manager service e clique em **modelos de largura de banda**.
2. Na lista de modelos de largura de banda, selecione o modelo que pretende eliminar. Com o botão direito e no menu de contexto, selecione eliminar.
3. Quando lhe for pedida confirmação, clique em **OK**. Isso deve eliminar o modelo de largura de banda.
4. A lista de atualizações de modelos de largura de banda para refletir a eliminação.

Se o modelo está a ser utilizada por quaisquer volumes, não terá permissão para eliminá-lo. Verá uma mensagem de erro indicando que o modelo está em uso. Uma caixa de diálogo de mensagem de erro será apresentada a aconselhá-lo de que todas as referências para o modelo devem ser removidas.

Pode eliminar todas as referências para o modelo ao aceder a **contentores de volumes** página e modificar os contentores de volumes que utilizam este modelo para que utilize outro modelo ou utilizar uma definição de largura de banda personalizados ou ilimitado. Quando tiverem sido removidas todas as referências, é possível eliminar o modelo.

## <a name="use-a-default-bandwidth-template"></a>Utilizar um modelo de largura de banda predefinido

Um modelo de largura de banda de padrão é fornecido e é utilizado pelos contentores de volumes por predefinição para impor controlos de largura de banda ao aceder a cloud. O modelo padrão também serve como uma referência pronta para os utilizadores que criar seus próprios modelos. Os detalhes deste modelo padrão são:

* **Nome** – ilimitados noites e fins de semana
* **Agenda** – um agendamento único de segunda-feira a sexta-feira, que se aplica uma taxa de largura de banda de 1 Mbps entre 8 AM e PM de 5 a hora do dispositivo. A largura de banda é definida como ilimitado para o resto da semana.

O modelo padrão pode ser editado. A utilização deste modelo (incluindo versões editadas) é rastreada.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Criar um modelo de largura de banda de dia inteiro que começa num tempo especificado

Siga este procedimento para criar uma agenda que inicia a uma hora especificada e executa todo o dia. No exemplo, a agenda começa às 9h da manhã e é executado até 9h na manhã seguinte. É importante observar que as horas de início e de fim para uma determinada agenda têm ambos de estar contidos na mesma agenda de 24 horas e não pode abranger vários dias. Se precisar de configurar modelos de largura de banda que duram vários dias, terá de utilizar várias agendas (conforme mostrado no exemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para criar um modelo de largura de banda de dia inteiro

1. Crie uma agenda que inicia às 9h da manhã e é executada à meia-noite.
2. Adicione outro agendamento. Configure a agenda de segundo para executar a partir da meia-noite até 9h da manhã.
3. Guarde o modelo de largura de banda.

A agenda composta, em seguida, irá iniciar num momento à sua escolha e executar o dia inteiro.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Perguntas e respostas sobre os modelos de largura de banda

**Q**. O que acontece aos controles de largura de banda quando estiver entre as agendas? (Um agendamento terminou e outro ainda não começou.)

**A**. Nesses casos, irão ser utilizados sem controlos de largura de banda. Isso significa que o dispositivo pode utilizar a largura de banda ilimitada quando a criação de camadas de dados para a cloud.

**Q**. Pode modificar os modelos de largura de banda num dispositivo offline?

**A**. Não será capaz de modificar os modelos de largura de banda em contentores de volumes, se o dispositivo correspondente estiver offline.

**Q**. Pode editar um modelo de largura de banda associado um contentor de volume quando os volumes associados offline?

**A**. É possível modificar um modelo de largura de banda associado um contentor de volume cujos volumes estão offline. Tenha em atenção que quando volumes estiverem offline, não existem dados serão colocado em camadas do dispositivo para a cloud.

**Q**. Pode eliminar um modelo padrão?

**A**. Apesar de poder eliminar um modelo padrão, não é uma boa idéia fazer isso. A utilização de um modelo padrão, incluindo versões editadas, é rastreada. Os dados de controle são analisados e ao longo do tempo, são utilizados para melhorar o modelo predefinido.

**Q**. Como determina que seus modelos de largura de banda tem de ser modificado?

**A**. Um dos sinais que terá de modificar os modelos de largura de banda é quando começa a ver a rede lentos ou são propositalmente várias vezes num dia. Se isto acontecer, monitorize a rede de armazenamento e de utilização ao examinar os gráficos de desempenho de e/s e débito de rede.

A partir de dados de débito de rede, identifique a hora do dia e os contentores de volumes em que ocorre o afunilamento de rede. Se isto acontece quando dados estão a ser camadas para a cloud (obter essas informações do desempenho de e/s para todos os contentores de volume para o dispositivo para a cloud), então, terá de modificar os modelos de largura de banda associados com os contentores de volume.

Depois dos modelos modificados em utilização, terá de monitorizar a rede novamente para latências significativas. Se estes ainda existirem, terá de rever os modelos de largura de banda.

**Q**. O que acontece se tem vários contentores de volumes no meu dispositivo agenda essa sobreposição mas limites diferentes se aplicam a cada?

**A**. Vamos supor que tem um dispositivo com 3 contentores de volumes. Os agendamentos associados a estes contentores totalmente se podem sobrepor. Para cada um desses contêineres, os limites de largura de banda utilizados são 5, 10 e 15 Mbps, respetivamente. Quando ocorrem em todos estes contentores e/s ao mesmo tempo, o mínimo de 3 limites de largura de banda pode ser aplicado: neste caso, 5 Mbps como estes pedidos de e/s de saída partilhar a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Melhores práticas de modelos de largura de banda

Siga estas práticas recomendadas para o dispositivo StorSimple:

* Configure modelos de largura de banda no seu dispositivo para ativar a variável limitação da taxa de transferência de rede por parte do dispositivo em alturas diferentes do dia. Estes modelos de largura de banda quando utilizado com agendas de cópia de segurança com eficiência podem tirar partido da largura de banda de rede adicional das operações na cloud horas de ponta.
* Calcule a largura de banda real necessária para uma implementação específica, com base no tamanho da implantação e o objetivo de tempo necessária de recuperação (RTO).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

