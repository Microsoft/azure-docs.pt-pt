---
title: Gerencie o seu catálogo de backup StorSimple | Microsoft Docs
description: Explica como utilizar a página de catálogo de backup do serviço StorSimple Device Manager para listar, selecionar e eliminar conjuntos de backup.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 6ab4694159c6269f2f72d3d591147d43aef34a0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017377"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Utilize o serviço StorSimple Device Manager para gerir o seu catálogo de backup
## <a name="overview"></a>Descrição Geral
A lâmina **do catálogo de backup** do catálogo do StorSimple Device Manager exibe todos os conjuntos de backup que são criados quando são recolhidas cópias de segurança manual ou programadas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de backup ou volume, selecionar ou eliminar backups, ou utilizar uma cópia de segurança para restaurar ou clonar um volume.

Este tutorial explica como listar, selecionar e eliminar um conjunto de backup. Para aprender a restaurar o seu dispositivo a partir de cópias de segurança, vá [para restaurar o seu dispositivo a partir de um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md). Para aprender a clonar um volume, vá ao [Clone a StorSimple volume](storsimple-8000-clone-volume-u2.md).

![Catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

A lâmina **do Catálogo de Cópias de Segurança** fornece uma consulta para reduzir a sua seleção de conjunto de backup. Pode filtrar os conjuntos de backup que são recuperados, com base nos seguintes parâmetros:

* **Dispositivo** – O dispositivo no qual foi criado o conjunto de backup.
* **Política de backup ou Volume** – A política de backup ou volume associado a este conjunto de backup.
* **De e para** – A data e o intervalo de tempo quando o conjunto de backup foi criado.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou volume associado ao conjunto de backup.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado On** – A data e hora em que os backups foram criados. 
* **Tipo** – Conjuntos de backup podem ser instantâneos locais ou instantâneos em nuvem. Um instantâneo local é uma cópia de segurança de todos os dados de volume armazenados localmente no dispositivo, enquanto um instantâneo em nuvem refere-se à cópia de segurança dos dados de volume que residem na nuvem. Os instantâneos locais proporcionam um acesso mais rápido, enquanto os instantâneos em nuvem são escolhidos para a resiliência dos dados.
* **Iniciados por** – As cópias de segurança podem ser iniciadas automaticamente por um horário ou manualmente por um utilizador. Pode usar uma política de backup para agendar backups. Em alternativa, pode utilizar a opção **de backup Take** para fazer uma cópia de segurança manual.

## <a name="list-backup-sets-for-a-backup-policy"></a>Lista conjuntos de backup para uma política de backup
Preencha os seguintes passos para listar todos os backups para uma política de backup.

#### <a name="to-list-backup-sets"></a>Para listar conjuntos de backup
1. Vá ao seu serviço StorSimple Device Manager e clique **no catálogo Backup**.

2. Filtrar as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo apropriado.
   3. Filtrar por **política de backup** para visualizar as cópias de segurança correspondentes.
   3. A partir da lista de retirada da política de backup, escolha **All** para visualizar todas as cópias de segurança no dispositivo selecionado.
   4. Clique **em Aplicar** para executar esta consulta.
      
      As cópias de segurança associadas à política de backup selecionada devem figurar na lista de conjuntos de backup.

      ![Vá para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Selecione um conjunto de backup
Preencha os seguintes passos para selecionar um conjunto de backup para uma política de volume ou backup.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de backup
1. Vá ao seu serviço StorSimple Device Manager e clique **no catálogo Backup**.
2. Filtrar as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo apropriado. 
   3. Filtre por volume ou política de reserva para a cópia de segurança que deseja selecionar.
   4. Clique **em Aplicar** para executar esta consulta.
      
      As cópias de segurança associadas à política de volume ou backup selecionada devem constar da lista de conjuntos de backup.

      ![Vá ao catálogo de backup 2](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecione e expanda um conjunto de backup. Agora é possível ver os conjuntos de backup avariados pelos volumes que contém. As opções **De Restaurar** e **Eliminar** estão disponíveis através do menu de contexto (clique à direita) para o conjunto de cópias de segurança. Pode executar qualquer uma destas ações no conjunto de backup que selecionou.

    ![Vá ao catálogo de backup 3](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Excluir um conjunto de backup
Elimine uma cópia de segurança quando já não pretender reter os dados que lhe estão associados. Execute os seguintes passos para eliminar um conjunto de backup.

#### <a name="to-delete-a-backup-set"></a>Para eliminar um conjunto de backup
 Vá ao seu serviço StorSimple Device Manager e clique **no catálogo Backup**.
1. Filtrar as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo apropriado. 
   3. Filtre por volume ou política de reserva para a cópia de segurança que deseja selecionar.
   4. Clique **em Aplicar** para executar esta consulta.
      
      As cópias de segurança associadas à política de volume ou backup selecionada devem constar da lista de conjuntos de backup.

      ![Vá ao catálogo de backup 4](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Selecione e expanda um conjunto de backup. Agora é possível ver os conjuntos de backup avariados pelos volumes que contém. As opções **De Restaurar** e **Eliminar** estão disponíveis através do menu de contexto (clique à direita) para o conjunto de cópias de segurança. Clique com o botão direito no conjunto de backup selecionado e no menu de contexto, selecione **Delete**.

    ![Vá ao catálogo de backup 5](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Quando solicitado para confirmação, reveja as informações apresentadas e clique em **Eliminar**. A cópia de segurança selecionada é eliminada permanentemente.

    ![Vá ao catálogo de backup 6](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Será notificado quando a supressão estiver em curso e quando terminar com sucesso. Depois de terminar a eliminação, refresque a consulta nesta página. O conjunto de backup eliminado deixará de aparecer na lista de conjuntos de backup.

    ![Vá ao catálogo de backup 7](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o catálogo de backup para restaurar o seu dispositivo a partir de um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

