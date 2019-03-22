---
title: Restaurar um volume a partir de cópia de segurança numa série StorSimple 8000 | Documentos da Microsoft
description: Explica como utilizar o serviço StorSimple Device Manager catálogo de cópia de segurança para restaurar um volume StorSimple a partir de um conjunto de cópia de segurança.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 6a2e022697ced90d968075b7a4abe4163be7a539
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082811"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume StorSimple a partir de um conjunto de cópia de segurança

## <a name="overview"></a>Descrição geral

Este tutorial descreve a operação de restauro executada num dispositivo da série StorSimple 8000 utilizando um conjunto de cópia de segurança existente. Utilize o **catálogo de cópia de segurança** painel para restaurar um volume de um local ou de cópia de segurança na cloud. O **catálogo de cópia de segurança** painel apresenta todos os conjuntos de cópia de segurança que são criados quando backups manuais ou automáticos são feitos. A operação de restauro a partir de um conjunto de cópia de segurança traz o volume online imediatamente enquanto os dados são transferidos em segundo plano.

Um método alternativo para iniciar o restauro é ir para **dispositivos > [seu dispositivo] > Volumes**. Na **Volumes** painel, selecione um volume, clique com botão direito para invocar o menu de contexto e, em seguida, selecione **restaurar**.

## <a name="before-you-restore"></a>Antes de restaurar

Antes de iniciar um restauro, reveja os seguintes avisos:

* **Terá de ter o volume offline** – coloque o volume para a offline no anfitrião e o dispositivo antes de iniciar a operação de restauro. Embora a operação de restauro apresenta automaticamente o volume online no dispositivo, deve fornecer manualmente o dispositivo online no anfitrião. Pode dar o volume online no anfitrião, assim que o volume está online no dispositivo. (Não é necessário aguardar até que a operação de restauro está concluída.) Para obter os procedimentos, vá para [colocar um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Tipo de volume após a restauração** – Deleted volumes são restaurados com base no tipo no instantâneo; ou seja, volumes afixados localmente são restaurados como volumes afixados localmente e os volumes que foram em camadas são restaurados como volumes em camadas.

    Para os volumes existentes, o tipo de utilização atual do volume substitui o tipo que é armazenado no instantâneo. Por exemplo, se restaurar um volume a partir de um instantâneo que foi tomado quando o tipo de volume foi em camadas e que tipo de volume agora está afixado localmente (devido a uma operação de conversão que foi executada), em seguida, o volume será restaurado como um volume afixado localmente. Da mesma forma, se um volume localmente afixado existente foi expandido e, em seguida, restaurado a partir de um instantâneo mais antigo tomado quando o volume for menor, o volume restaurado irão manter o tamanho expandido atual.

    Não é possível converter um volume a partir de um volume em camadas para um volume afixado localmente ou a partir de um volume localmente afixado a um volume em camadas, enquanto o volume está a ser restaurado. Aguarde até que a operação de restauro está concluída e, em seguida, pode converter o volume de outro tipo. Para informações sobre como converter um volume, aceda a [alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **O tamanho do volume é refletido no volume restaurado** – esta é uma consideração importante se estiver a restaurar um volume afixado localmente que tenha sido eliminado (porque os volumes afixados localmente são totalmente aprovisionados). Certifique-se de que tem espaço suficiente antes de tentar restaurar um volume afixado localmente que tenha sido eliminado anteriormente.

* **Não é possível expandir um volume enquanto ele está a ser restaurado** – aguardar até que a operação de restauro está concluída antes de tentar expandir o volume. Para informações sobre a expansão de um volume, aceda a [modificar um volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Pode efetuar uma cópia de segurança enquanto restaura um volume local** – para procedimentos, vá para [utilizar o serviço StorSimple Device Manager para gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md).

* **Pode cancelar uma operação de restauro** – se cancelar a tarefa de restauro, em seguida, o volume será revertido para o estado que estava no antes de iniciou a operação de restauro. Para obter os procedimentos, vá para [cancelar uma tarefa](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Como restaurar o trabalho

Para dispositivos que executam a atualização 4 ou posterior, é implementado um restauro com base no mapa térmico. Como o host pedidos para aceder a dados a alcançar o dispositivo, estes pedidos são controlados e um mapa térmico é criado. Taxa de pedidos de alto resulta em segmentos de dados com maior calor, ao passo que o menor taxa de pedidos que se traduz em segmentos com térmico inferior. Precisa acessar os dados, pelo menos, duas vezes para ser marcado como _frequente_. Um ficheiro que é modificado também está marcado como _frequente_. Depois de iniciar o restauro, ocorre com base no mapa térmico hidratação proativa de dados. Para versões anteriores à atualização 4, os dados forem baixados durante o restauro com base no acesso apenas.

Os seguintes avisos se aplicam a baseada em mapa térmico restauros:

* Controlo de mapa térmico está ativado apenas para volumes em camadas e os volumes afixados localmente não são suportados.

* Com base no mapa térmico restauro não é suportado ao clonar um volume para outro dispositivo. 

* Se existir uma restauração no local e um instantâneo local para o volume para serem restaurados existe no dispositivo, em seguida, podemos não reidratação (como os dados já estão disponíveis localmente). 

* Por predefinição, ao restaurar, são iniciadas as tarefas de "reidratação" que proativamente realimentar dados com base no mapa térmico. 

Na atualização 4, os cmdlets do Windows PowerShell pode servir para consultar tarefas em execução de "reidratação", cancelar uma tarefa de "reidratação" e obter o estado da tarefa "reidratação".

* `Get-HcsRehydrationJob` -Este cmdlet obtém o estado da tarefa "reidratação". Uma tarefa única reidratação seja acionada para um volume.

* `Set-HcsRehydrationJob` -Este cmdlet permite-lhe colocar em pausa, interromper, retomar a tarefa de "reidratação", enquanto a "reidratação" está em curso.

Para obter mais informações sobre os cmdlets do "reidratação", aceda a [referência de cmdlets do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Normalmente, com automática "reidratação", é esperado maior desempenho de leitura transitório. A magnitude real dos aprimoramentos depende de vários fatores, como o padrão de acesso, alterações a dados e tipo de dados. 

Para cancelar uma tarefa de "reidratação", pode utilizar o cmdlet do PowerShell. Se pretender desativar permanentemente tarefas reidratação para restauros futuras, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Como utilizar o catálogo de cópia de segurança

O **catálogo de cópia de segurança** painel fornece a seleção de definir uma consulta que o ajuda a restringir a cópia de segurança. Pode filtrar os conjuntos cópia de segurança que são obtidos a com base nos parâmetros seguintes:

* **Intervalo de tempo** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.
* **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
* **Política de cópia de segurança** ou **Volume** – a política de cópia de segurança ou volume associado este conjunto de cópias de segurança.

Os conjuntos de cópia de segurança filtrados, em seguida, são apresentados com base nos seguintes atributos:

* **Nome** – o nome da política de cópia de segurança ou volume associada ao conjunto de cópia de segurança.
* **Tipo de** – conjuntos de cópia de segurança podem ser os instantâneos locais ou instantâneos da cloud. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto que um instantâneo de cloud refere-se para a cópia de segurança dos dados do volume que residem na cloud. Os instantâneos locais fornecem um acesso mais rápido, ao passo que os instantâneos de cloud são escolhidos para resiliência de dados.
* **Tamanho** – o tamanho real do conjunto de cópia de segurança.
* **Criado em** – a data e hora quando foram criadas as cópias de segurança. 
* **Volumes** -o número de volumes associados com o conjunto de cópias de segurança.
* **Iniciada** – as cópias de segurança podem ser iniciadas automaticamente, de acordo com uma agenda ou manualmente por um utilizador. (Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar o **efetuar cópia de segurança** opção para efetuar uma cópia de segurança interativa ou sob demanda.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume do StorSimple a partir de uma cópia de segurança

Pode utilizar o **catálogo de cópia de segurança** painel para restaurar o volume do StorSimple a partir de uma cópia de segurança específica. Lembre-se, no entanto, essa restauração de um volume irá reverter o volume para o estado que estava quando foi feita a cópia de segurança. Todos os dados que foi adicionados após a operação de cópia de segurança serão perdidos.

> [!WARNING]
> Restaurar a partir de uma cópia de segurança irá substituir os volumes existentes da cópia de segurança. Isso pode causar a perda de todos os dados que foi escritos depois da cópia de segurança.


### <a name="to-restore-your-volume"></a>Para restaurar o volume
1. Aceda ao seu serviço StorSimple Device Manager e, em seguida, clique em **catálogo de cópia de segurança**.

2. Selecione uma cópia de segurança definida da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo apropriado.
   3. Na lista pendente, escolha a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   4. Clique em **aplicar** para executar esta consulta.

      As cópias de segurança associadas com o volume selecionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.
   
      ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expanda a cópia de segurança definida para ver os volumes associados. Estes volumes devem ser colocados offline no anfitrião e do dispositivo antes de pode restaurá-las. Aceder aos volumes no **Volumes** painel do seu dispositivo e, em seguida, siga os passos [colocar um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) levá-las offline.
   
   > [!IMPORTANT]
   > Certifique-se de que seguiu os volumes offline no anfitrião em primeiro lugar, antes de colocar offline os volumes no dispositivo. Se não realizar os volumes offline no anfitrião, pode potencialmente conduzir a danos em dados.
   
4. Navegue de volta para o **catálogo de cópia de segurança** separador e selecionar um conjunto de cópia de segurança. Com o botão direito e, em seguida, no menu de contexto, selecione **restaurar**.

    ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Será solicitado para confirmação. Reveja as informações de restauro e, em seguida, selecione a caixa de verificação confirmação.
   
    ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Clique em **restaurar**. Esta ação inicia uma tarefa de restauro que pode ver ao aceder a **tarefas** página.

   ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Depois do restauro estiver concluído, certifique-se de que o conteúdo do seus volumes é substituído por volumes da cópia de segurança.


## <a name="if-the-restore-fails"></a>Se falhar o restauro

Receberá um alerta se a operação de restauro falhar por algum motivo. Se isto ocorrer, atualize a lista de cópia de segurança para verificar que a cópia de segurança ainda é válida. Se a cópia de segurança é válida e está a restaurar a partir da cloud, em seguida, problemas de conectividade poderão estar a causar o problema.

Para concluir a operação de restauro, coloque o volume offline no anfitrião e repita a operação de restauro. Tenha em atenção que quaisquer modificações para os dados de volume que tenham sido efetuadas durante o processo de restauração serão perdidas.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [volumes do StorSimple gerir](storsimple-8000-manage-volumes-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

