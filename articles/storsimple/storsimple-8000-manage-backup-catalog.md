---
title: Gerencie o seu catálogo de backup StorSimple / Microsoft Docs
description: Explica como utilizar a página de catálogo de backup do serviço StorSimple Device Manager para listar, selecionar e eliminar conjuntos de backup.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 07d9e03f1631ebce88a7a7c2e33be62f21dda522
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319676"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Utilize o serviço StorSimple Device Manager para gerir o seu catálogo de backup
## <a name="overview"></a>Descrição geral
A lâmina de **backup** do serviço StorSimple Device Manager mostra todos os conjuntos de backup que são criados quando as cópias de segurança manual ou programada são tomadas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de backup ou um volume, selecionar ou eliminar cópias de segurança, ou utilizar uma cópia de segurança para restaurar ou clonar um volume.

Este tutorial explica como listar, selecionar e eliminar um conjunto de backup. Para aprender a restaurar o seu dispositivo a partir de cópia seletiva, vá restaurar o seu dispositivo a partir de um conjunto de [cópias](storsimple-8000-restore-from-backup-set-u2.md)de segurança . Para aprender a clonar um volume, vá a [Clone a StorSimple volume](storsimple-8000-clone-volume-u2.md).

![Catálogo de cópias de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

A lâmina **de backup Catalog** fornece uma consulta para reduzir a sua seleção de conjunto de backup. Pode filtrar os conjuntos de backup que são recuperados, com base nos seguintes parâmetros:

* **Dispositivo** – O dispositivo no qual foi criado o conjunto de backup.
* **Política de backup ou Volume** – A política de backup ou volume associado a este conjunto de backup.
* **De e Para** – O intervalo de data e hora quando o conjunto de backup foi criado.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou volume associado ao conjunto de backup.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado on** – A data e a hora em que os backups foram criados. 
* **Tipo** – Os conjuntos de backup podem ser instantâneos locais ou imagens em nuvem. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto um instantâneo em nuvem refere-se à cópia de segurança dos dados de volume que residem na nuvem. Os instantâneos locais proporcionam um acesso mais rápido, enquanto os instantâneos em nuvem são escolhidos para a resiliência dos dados.
* **Iniciado Por** – As cópias de segurança podem ser iniciadas automaticamente por um horário ou manualmente por um utilizador. Pode usar uma política de reserva para agendar backups. Em alternativa, pode utilizar a opção de cópia de **segurança Take** para obter uma cópia de segurança manual.

## <a name="list-backup-sets-for-a-backup-policy"></a>Lista de conjuntos de backup para uma política de backup
Complete os seguintes passos para listar todos os backups para uma política de backup.

#### <a name="to-list-backup-sets"></a>Para listar conjuntos de backup
1. Vá ao serviço StorSimple Device Manager e clique no **catálogo de backup**.

2. Filtrar as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo apropriado.
   3. Filtrar por **política de backup** para visualizar as cópias de segurança correspondentes.
   3. A partir da lista de desativação da política de backup, escolha **All** para ver todas as cópias de segurança do dispositivo selecionado.
   4. Clique **Em Aplicar** para executar esta consulta.
      
      Os backups associados à política de backup selecionada devem figurar na lista de conjuntos de backup.

      ![Ir para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Selecione um conjunto de backup
Complete os seguintes passos para selecionar um conjunto de cópias de segurança para uma política de volume ou cópia de segurança.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de backup
1. Vá ao serviço StorSimple Device Manager e clique no **catálogo de backup**.
2. Filtrar as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo apropriado. 
   3. Filtre por volume ou política de cópia de segurança para a cópia de segurança que pretende selecionar.
   4. Clique **Em Aplicar** para executar esta consulta.
      
      As cópias de segurança associadas à política de volume ou cópia de segurança selecionada devem figurar na lista de conjuntos de backup.

      ![Ir para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecione e expanda um conjunto de backup. Agora pode ver os conjuntos de backup avariados pelos volumes que contém. As opções **de Restauro** e **Exclusão** estão disponíveis através do menu de contexto (clique à direita) para o conjunto de backup. Pode executar qualquer uma destas ações no conjunto de backup que selecionou.

    ![Ir para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Eliminar um conjunto de backup
Elimine uma cópia de segurança quando já não pretender reter os dados associados. Execute os seguintes passos para eliminar um conjunto de cópias de segurança.

#### <a name="to-delete-a-backup-set"></a>Para eliminar um conjunto de backup
 Vá ao serviço StorSimple Device Manager e clique no **catálogo de backup**.
1. Filtrar as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo apropriado. 
   3. Filtre por volume ou política de cópia de segurança para a cópia de segurança que pretende selecionar.
   4. Clique **Em Aplicar** para executar esta consulta.
      
      As cópias de segurança associadas à política de volume ou cópia de segurança selecionada devem figurar na lista de conjuntos de backup.

      ![Ir para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Selecione e expanda um conjunto de backup. Agora pode ver os conjuntos de backup avariados pelos volumes que contém. As opções **de Restauro** e **Exclusão** estão disponíveis através do menu de contexto (clique à direita) para o conjunto de backup. Clique no conjunto de backup selecionado e no menu de contexto, **selecione Delete**.

    ![Ir para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Quando solicitado para confirmação, reveja as informações apresentadas e clique em **Eliminar**. A cópia de segurança selecionada é eliminada permanentemente.

    ![Ir para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Será notificado quando a eliminação estiver em curso e quando tiver terminado com sucesso. Depois da eliminação, refresque a consulta nesta página. O conjunto de cópias de segurança eliminados deixará de figurar na lista de conjuntos de backup.

    ![Ir para o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a utilizar o catálogo de cópias de [segurança para restaurar o seu dispositivo a partir de um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md).
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

