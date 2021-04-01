---
title: Restaurar um volume de cópia de segurança numa série StorSimple 8000 | Microsoft Docs
description: Explica como utilizar o catálogo de backup do serviço StorSimple Device Manager para restaurar um volume StorSimple a partir de um conjunto de backup.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: ff70df225b5ed27960c96889d409d7005f0fbcc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94950739"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume StorSimple a partir de um conjunto de cópias de segurança

## <a name="overview"></a>Descrição Geral

Este tutorial descreve a operação de restauro realizada num dispositivo da série StorSimple 8000 utilizando um conjunto de backup existente. Utilize a lâmina **do catálogo Backup** para restaurar um volume a partir de uma cópia de segurança local ou na nuvem. A lâmina **do catálogo de cópias de segurança** exibe todos os conjuntos de backup que são criados quando são recolhidas cópias de segurança manuais ou automatizadas. A operação de restauro a partir de um conjunto de backup traz o volume on-line imediatamente enquanto os dados são descarregados em segundo plano.

## <a name="before-you-restore"></a>Antes de restaurar

Antes de iniciar uma restauração, reveja as seguintes ressalvas:

* **Deve desligar o volume** - Desative o volume tanto no hospedeiro como no dispositivo antes de iniciar a operação de restauro. Embora a operação de restauro leve automaticamente o volume on-line no dispositivo, deve colocar manualmente o dispositivo on-line no anfitrião. Pode colocar o volume on-line no anfitrião assim que o volume estiver online no dispositivo. (Não é necessário esperar até que a operação de restauro esteja terminada.) Para procedimentos, vá a [Retirar um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Tipo de volume após a restauração** – Os volumes apagados são restaurados com base no tipo no instantâneo; ou seja, os volumes que foram fixados localmente são restaurados como volumes e volumes fixados localmente e os volumes que foram tiered são restaurados como volumes hierárquicos.

    Para os volumes existentes, o tipo de utilização atual do volume sobrepõe-se ao tipo que é armazenado no instantâneo. Por exemplo, se restaurar um volume a partir de uma imagem tirada quando o tipo de volume foi nivelado e esse tipo de volume está agora fixado localmente (devido a uma operação de conversão que foi realizada), então o volume será restaurado como um volume fixado localmente. Da mesma forma, se um volume fixado localmente existente foi expandido e posteriormente restaurado a partir de um instantâneo mais antigo tirado quando o volume era menor, o volume restaurado manterá o tamanho expandido atual.

    Não é possível converter um volume de um volume hierarquizado para um volume fixado localmente ou de um volume fixado localmente para um volume hierárquico enquanto o volume estiver a ser restaurado. Aguarde até que a operação de restauro esteja terminada e, em seguida, pode converter o volume para outro tipo. Para obter informações sobre a conversão de um volume, vá para [Alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **O tamanho do volume reflete-se no volume restaurado** – Esta é uma consideração importante se estiver a restaurar um volume fixado localmente que foi eliminado (porque os volumes fixados localmente são totalmente a provisionados). Certifique-se de que tem espaço suficiente antes de tentar restaurar um volume fixado localmente que foi previamente apagado.

* **Não é possível expandir um volume enquanto estiver a ser restaurado** – Aguarde até que a operação de restauro esteja terminada antes de tentar expandir o volume. Para obter informações sobre a expansão de um volume, vá [modificar um volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Pode efetuar uma cópia de segurança enquanto restaura um volume local** – Para os procedimentos, utilize o serviço [StorSimple Device Manager para gerir as políticas de backup](storsimple-8000-manage-backup-policies-u2.md).

* **Pode cancelar uma operação de restauro** – Se cancelar a operação de restauro, o volume será devolvido ao estado em que se encontrava antes de iniciar a operação de restauro. Para procedimentos, vá [cancelar um trabalho.](storsimple-8000-manage-jobs-u2.md#cancel-a-job)

## <a name="how-does-restore-work"></a>Como restaurar o trabalho

Para os dispositivos em execução Update 4 ou posterior, é implementado um restauro baseado em mapas térmicos. À medida que os pedidos do anfitrião para aceder aos dados chegam ao dispositivo, estes pedidos são rastreados e é criado um mapa de calor. A elevada taxa de pedido resulta em pedaços de dados com calor mais elevado, enquanto a taxa de pedido mais baixa traduz-se em pedaços com calor mais baixo. Tem de aceder aos dados pelo menos duas vezes para ser marcado como _quente_. Um ficheiro modificado também está marcado como _quente._ Uma vez iniciado o restauro, então a hidratação proativa dos dados ocorre com base no mapa de calor. Para versões anteriores ao Update 4, os dados são descarregados durante a restauração apenas com base no acesso.

Aplicam-se as seguintes ressalvas às restaurações baseadas em mapas térmicos:

* O rastreio de mapas térmicos é ativado apenas para volumes hierárquicos e os volumes fixados localmente não são suportados.

* A restauração baseada em mapas térmicos não é suportada ao clonar um volume para outro dispositivo. 

* Se houver uma restauração no local e uma imagem local para o volume a ser restaurado existe no dispositivo, então não nos reidratamos (uma vez que os dados já estão disponíveis localmente). 

* Por padrão, quando restaura, os trabalhos de reidratação são iniciados que rehidratam proativamente dados com base no mapa de calor. 

No Update 4, os cmdlets Windows PowerShell podem ser usados para consultar trabalhos de reidratação, cancelar um trabalho de reidratação e obter o estado do trabalho de reidratação.

* `Get-HcsRehydrationJob` - Este cmdlet obtém o estado do trabalho de reidratação. Um único trabalho de reidratação é acionado para um volume.

* `Set-HcsRehydrationJob` - Este cmdlet permite-lhe parar, parar, retomar o trabalho de reidratação, quando a reidratação estiver em curso.

Para obter mais informações sobre os cmdlets de reidratação, consulte a [referência cmdlet do Windows PowerShell para storSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

Com a reidratação automática, espera-se um desempenho de leitura transitório tipicamente mais elevado. A magnitude real das melhorias depende de vários fatores, tais como o padrão de acesso, o churn de dados e o tipo de dados. 

Para cancelar um trabalho de reidratação, pode utilizar o cmdlet PowerShell. Se desejar desativar permanentemente os trabalhos de reidratação para todos os restauros futuros, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Como usar o catálogo de backup

A lâmina **do Catálogo de Cópias de Segurança** fornece uma consulta que o ajuda a reduzir a sua seleção de conjunto de backup. Pode filtrar os conjuntos de backup que são recuperados com base nos seguintes parâmetros:

* **Intervalo de tempo** - A data e o intervalo de tempo quando o conjunto de backup foi criado.
* **Dispositivo** – O dispositivo no qual foi criado o conjunto de backup.
* **Política de backup** ou **Volume** – A política de backup ou volume associado a este conjunto de backup.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou volume associado ao conjunto de backup.
* **Tipo** – Conjuntos de backup podem ser instantâneos locais ou instantâneos em nuvem. Um instantâneo local é uma cópia de segurança de todos os dados de volume armazenados localmente no dispositivo, enquanto um instantâneo em nuvem refere-se à cópia de segurança dos dados de volume que residem na nuvem. Os instantâneos locais proporcionam um acesso mais rápido, enquanto os instantâneos em nuvem são escolhidos para a resiliência dos dados.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado em** – A data e hora em que os backups foram criados. 
* **Volumes** - O número de volumes associados ao conjunto de backup.
* **Iniciado** – As cópias de segurança podem ser iniciadas automaticamente de acordo com um horário ou manualmente por um utilizador. (Pode utilizar uma política de backup para agendar backups. Em alternativa, pode utilizar a opção **de backup Take** para fazer uma cópia de segurança interativa ou a pedido.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o seu volume StorSimple a partir de uma cópia de segurança

Pode utilizar a lâmina **do Catálogo de Cópias de Segurança** para restaurar o volume StorSimple a partir de uma cópia de segurança específica. Tenha em mente, no entanto, que restaurar um volume reverterá o volume para o estado em que estava quando a cópia de segurança foi tomada. Todos os dados adicionados após a operação de backup serão perdidos.

> [!WARNING]
> Restaurar de uma cópia de segurança substituirá os volumes existentes da cópia de segurança. Isto pode causar a perda de quaisquer dados que tenham sido escritos após a recolha da cópia de segurança.


### <a name="to-restore-your-volume"></a>Para restaurar o seu volume
1. Vá ao seu serviço StorSimple Device Manager e, em seguida, clique **no catálogo backup**.

2. Selecione um conjunto de backup da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo apropriado.
   3. Na lista de drop-down, escolha a política de volume ou backup para a cópia de segurança que deseja selecionar.
   4. Clique **em Aplicar** para executar esta consulta.

      As cópias de segurança associadas à política de volume ou backup selecionada devem constar da lista de conjuntos de backup.
   
      ![Lista de conjuntos de backup](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expanda o conjunto de backup para visualizar os volumes associados. Estes volumes devem ser desligados no hospedeiro e no dispositivo antes de os poder restaurar. Aceda aos volumes da lâmina **Volumes** do seu dispositivo e, em seguida, siga os passos em [Retirar um volume offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) para os desligar.
   
   > [!IMPORTANT]
   > Certifique-se de que desligou primeiro os volumes do hospedeiro, antes de desligar os volumes do dispositivo. Se não tirar os volumes offline do hospedeiro, pode potencialmente levar à corrupção de dados.
   
4. Volte para o separador **Catálogo de Cópias de Segurança** e selecione um conjunto de backup. Clique à direita e, em seguida, a partir do menu de contexto, **selecione Restaurar**.

    ![Lista de conjunto de backup 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Ser-lhe-á pedido que confirme. Reveja a informação de restauro e, em seguida, selecione a caixa de verificação de confirmação.
   
    ![Página de confirmação](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Clique **em Restaurar**. Isto inicia um trabalho de restauro que pode ver acedendo à página **Jobs.**

   ![Página de confirmação 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Após a restauração estar concluída, verifique se o conteúdo dos seus volumes é substituído por volumes da cópia de segurança.


## <a name="if-the-restore-fails"></a>Se o restauro falhar

Receberá um alerta se a operação de restauro falhar por qualquer motivo. Se isto ocorrer, refresque a lista de cópias de segurança para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida e estiver a restaurar a partir da nuvem, então os problemas de conectividade podem estar a causar o problema.

Para completar a operação de restauro, desative o volume do hospedeiro e redoça a operação de restauro. Note que quaisquer modificações nos dados de volume que foram realizados durante o processo de restauração serão perdidas.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [gerir os volumes StorSimple](storsimple-8000-manage-volumes-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).