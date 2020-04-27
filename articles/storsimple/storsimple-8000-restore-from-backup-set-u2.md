---
title: Restaurar um volume de backup numa série StorSimple 8000 [ Microsoft Docs
description: Explica como utilizar o Catálogo de Backup do Serviço StorSimple Device Manager para restaurar um volume StorSimple a partir de um conjunto de backup.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60723395"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume StorSimple a partir de um conjunto de cópias de segurança

## <a name="overview"></a>Descrição geral

Este tutorial descreve a operação de restauro realizada num dispositivo da série StorSimple 8000 utilizando um conjunto de backup existente. Utilize a lâmina do **catálogo De reserva** para restaurar um volume de uma cópia de segurança local ou em nuvem. A lâmina do **catálogo de backup** apresenta todos os conjuntos de backup que são criados quando as cópias de segurança manuais ou automáticas são tomadas. A operação de restauro de um conjunto de backup traz o volume on-line imediatamente enquanto os dados são descarregados em segundo plano.

Um método alternativo para iniciar a restauração é ir para **dispositivos > [O seu dispositivo] > Volumes**. Na lâmina **Volumes,** selecione um volume, clique à direita para invocar o menu de contexto e, em seguida, **selecione Restaurar**.

## <a name="before-you-restore"></a>Antes de restaurar

Antes de iniciar um restauro, reveja as seguintes ressalvas:

* **Tem de desligar o volume** – Desativar o volume tanto no hospedeiro como no dispositivo antes de iniciar a operação de restauro. Embora a operação de restauro traga automaticamente o volume on-line no dispositivo, deve colocar o dispositivo manualmente on-line no hospedeiro. Pode colocar o volume on-line no anfitrião assim que o volume estiver on-line no dispositivo. (Não precisa de esperar até que a operação de restauro esteja terminada.) Para os procedimentos, vá para [Tirar um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Tipo** de volume após restauro – Os volumes eliminados são restaurados com base no tipo no instantâneo; ou seja, os volumes que foram fixados localmente são restaurados à medida que os volumes e volumes fixados localmente são restaurados como volumes hierárquicos.

    Para os volumes existentes, o tipo de utilização atual do volume sobrepõe-se ao tipo que é armazenado no instantâneo. Por exemplo, se restaurar um volume de um instantâneo que foi tirado quando o tipo de volume foi nivilhado e esse tipo de volume é agora fixado localmente (devido a uma operação de conversão que foi realizada), então o volume será restaurado como um volume fixado localmente. Da mesma forma, se um volume fixado localmente existente foi expandido e posteriormente restaurado a partir de um instantâneo mais antigo tirado quando o volume era menor, o volume restaurado manterá o tamanho expandido atual.

    Não é possível converter um volume de um volume hierárquico para um volume fixado localmente ou de um volume fixado localmente para um volume hierárquico enquanto o volume está a ser restaurado. Aguarde até que a operação de restauro esteja terminada e, em seguida, poderá converter o volume para outro tipo. Para obter informações sobre a conversão de um volume, vá alterar [o tipo](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)de volume . 

* **O tamanho do volume reflete-se no volume restaurado** – Esta é uma consideração importante se estiver a restaurar um volume localmente fixado que foi eliminado (porque os volumes fixados localmente estão totalmente provisionados). Certifique-se de que tem espaço suficiente antes de tentar restaurar um volume fixado localmente que foi previamente eliminado.

* **Não é possível expandir um volume enquanto este estiver a ser restaurado** – Aguarde até que a operação de restauro esteja terminada antes de tentar expandir o volume. Para obter informações sobre a expansão de um volume, vá modificar [um volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Pode efetuar uma cópia** de segurança enquanto restaura um volume local – Para procedimentos vão utilizar o serviço [StorSimple Device Manager para gerir as políticas](storsimple-8000-manage-backup-policies-u2.md)de backup .

* **Pode cancelar uma operação** de restauro – Se cancelar o trabalho de restauro, o volume será revirado para o estado em que estava antes de iniciar a operação de restauro. Para procedimentos, vá cancelar [um trabalho.](storsimple-8000-manage-jobs-u2.md#cancel-a-job)

## <a name="how-does-restore-work"></a>Como restaurar o trabalho

Para dispositivos que executam o Update 4 ou mais tarde, é implementado um restauro baseado em mapas de calor. À medida que o anfitrião pede para aceder aos dados chegar ao dispositivo, estes pedidos são rastreados e é criado um mapa de calor. A elevada taxa de pedidos resulta em pedaços de dados com calor mais elevado, enquanto a taxa de pedido mais baixa traduz-se em pedaços com menor calor. Deve aceder aos dados pelo menos duas vezes para ser marcado como _quente_. Um ficheiro modificado também é marcado como _quente_. Assim que iniciar a restauração, a hidratação proactiva dos dados ocorre com base no mapa de calor. Para versões mais cedo do que o Update 4, os dados são descarregados durante o restauro apenas com base no acesso.

As seguintes ressalvas aplicam-se a restauros baseados em mapas de calor:

* O rastreio do mapa térmico está ativado apenas para volumes hierárquicos e os volumes fixados localmente não são suportados.

* A restauração baseada em mapas térmicos não é suportada ao clonar um volume para outro dispositivo. 

* Se houver uma restauração no local e um instantâneo local para o volume a restaurar existe no dispositivo, então não nos reidratamos (uma vez que os dados já estão disponíveis localmente). 

* Por padrão, quando restaura, iniciam-se os trabalhos de reidratação que rehidratam proativamente os dados com base no mapa de calor. 

No Update 4, os cmdlets do Windows PowerShell podem ser usados para consultar trabalhos de reidratação, cancelar um trabalho de reidratação e obter o estado do trabalho de reidratação.

* `Get-HcsRehydrationJob`- Este cmdlet obtém o estado do trabalho de reidratação. Um único trabalho de reidratação é desencadeado por um volume.

* `Set-HcsRehydrationJob`- Este cmdlet permite-lhe parar, parar, retomar o trabalho de reidratação, quando a reidratação está em curso.

Para obter mais informações sobre os cmdlets de reidratação, vá à [referência cmdlet do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Com a reidratação automática, espera-se um desempenho de leitura transitória tipicamente mais elevado. A magnitude real das melhorias depende de vários fatores, tais como padrão de acesso, recolha de dados e tipo de dados. 

Para cancelar um trabalho de reidratação, pode utilizar o cmdlet PowerShell. Se desejar desativar permanentemente os trabalhos de reidratação para todos os futuros restauros, [contacte](storsimple-8000-contact-microsoft-support.md)o Microsoft Support .

## <a name="how-to-use-the-backup-catalog"></a>Como utilizar o catálogo de cópias de segurança

A lâmina **de backup Catalog** fornece uma consulta que o ajuda a reduzir a sua seleção de conjunto de backup. Pode filtrar os conjuntos de backup que são recuperados com base nos seguintes parâmetros:

* **Intervalo** de tempo – O intervalo de data e hora quando o conjunto de backup foi criado.
* **Dispositivo** – O dispositivo no qual foi criado o conjunto de backup.
* **Política** de backup ou **Volume** – A política de backup ou volume associado a este conjunto de backup.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou volume associado ao conjunto de backup.
* **Tipo** – Os conjuntos de backup podem ser instantâneos locais ou imagens em nuvem. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto um instantâneo em nuvem refere-se à cópia de segurança dos dados de volume que residem na nuvem. Os instantâneos locais proporcionam um acesso mais rápido, enquanto os instantâneos em nuvem são escolhidos para a resiliência dos dados.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado em** – A data e a hora em que os backups foram criados. 
* **Volumes** - O número de volumes associados ao conjunto de backup.
* **Iniciados** – As cópias de segurança podem ser iniciadas automaticamente de acordo com um horário ou manualmente por um utilizador. (Pode usar uma política de backup para agendar backups. Em alternativa, pode utilizar a opção **de backup Take** para obter uma cópia de segurança interativa ou a pedido.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o seu volume StorSimple a partir de uma cópia de segurança

Pode utilizar a lâmina **do Catálogo de Cópias** de Segurança para restaurar o volume StorSimple a partir de uma cópia de segurança específica. Tenha em mente, no entanto, que restaurar um volume reverterá o volume para o estado em que estava quando o backup foi levado. Todos os dados adicionados após a operação de backup serão perdidos.

> [!WARNING]
> Restaurar de uma cópia de segurança substituirá os volumes existentes da cópia de segurança. Isto pode causar a perda de quaisquer dados que tenham sido escritos após a cópia de segurança ter sido tomada.


### <a name="to-restore-your-volume"></a>Para restaurar o seu volume
1. Vá ao serviço StorSimple Device Manager e, em seguida, clique no **catálogo de backup**.

2. Selecione um conjunto de backup da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo apropriado.
   3. Na lista de recaídas, escolha a política de volume ou cópia de segurança para o backup que pretende selecionar.
   4. Clique **Em Aplicar** para executar esta consulta.

      As cópias de segurança associadas à política de volume ou cópia de segurança selecionada devem figurar na lista de conjuntos de backup.
   
      ![Lista de conjuntos de backup](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expanda o conjunto de backup para visualizar os volumes associados. Estes volumes devem ser desligados no hospedeiro e no dispositivo antes de os poder restaurar. Aceda aos volumes da lâmina **Volumes** do seu dispositivo e, em seguida, siga os passos em [Tome um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) para os desligar.
   
   > [!IMPORTANT]
   > Certifique-se de que desligou os volumes do hospedeiro primeiro, antes de desligar os volumes do dispositivo. Se não desligar os volumes do hospedeiro, pode potencialmente levar à corrupção de dados.
   
4. Navegue de volta para o separador **'Backup Catalog'** e selecione um conjunto de backup. Clique à direita e, em seguida, a partir do menu de contexto, **selecione Restaurar**.

    ![Lista de conjuntos de backup](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Ser-lhe-á pedido que confirme. Reveja as informações de restauro e, em seguida, selecione a caixa de verificação de confirmação.
   
    ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Clique em **Restaurar**. Isto inicia um trabalho de restauro que você pode ver acedendo à página **Jobs.**

   ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Após a restauração estar concluída, verifique se o conteúdo dos seus volumes é substituído por volumes da cópia de segurança.


## <a name="if-the-restore-fails"></a>Se o restauro falhar

Receberá um alerta se a operação de restauro falhar por qualquer motivo. Se isto ocorrer, refresque a lista de cópias de segurança para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida e estiver a restaurar da nuvem, então os problemas de conectividade podem estar a causar o problema.

Para completar a operação de restauro, desative o volume do hospedeiro e volte a tentar a operação de restauro. Note que quaisquer modificações nos dados de volume que foram realizadas durante o processo de restauro serão perdidas.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [gerir volumes StorSimple](storsimple-8000-manage-volumes-u2.md).
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

