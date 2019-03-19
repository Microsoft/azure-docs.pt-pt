---
title: Gerir o seu catálogo de cópia de segurança do StorSimple | Documentos da Microsoft
description: Explica como utilizar a página de catálogo de cópia de segurança do serviço StorSimple Device Manager para a lista, selecione e eliminar conjuntos de cópia de segurança.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076790"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Utilizar o serviço StorSimple Device Manager para gerir o seu catálogo de cópia de segurança
## <a name="overview"></a>Descrição geral
O serviço de Gestor de dispositivos do StorSimple **catálogo de cópia de segurança** painel apresenta todos os conjuntos de cópia de segurança que são criados quando são efetuadas cópias de segurança manuais ou agendadas. Pode utilizar esta página para listar todas as cópias de segurança para uma política de cópia de segurança ou um volume, selecione ou eliminar cópias de segurança ou utilizar uma cópia de segurança para restaurar ou clonar um volume.

Este tutorial explica como lista, selecione e eliminar um conjunto de cópia de segurança. Para saber como restaurar o seu dispositivo a partir de cópia de segurança, aceda a [restaurar o seu dispositivo a partir de um conjunto de cópias de segurança](storsimple-8000-restore-from-backup-set-u2.md). Para saber como clonar um volume, aceda a [clonar um volume StorSimple](storsimple-8000-clone-volume-u2.md).

![catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

O **catálogo de cópia de segurança** painel fornece a seleção de definir uma consulta para restringir a cópia de segurança. Pode filtrar os conjuntos de cópia de segurança que são obtidos, com base nos parâmetros seguintes:

* **Dispositivo** – o dispositivo no qual o conjunto de cópia de segurança foi criado.
* **Política de cópia de segurança ou Volume** – a política de cópia de segurança ou volume associado este conjunto de cópias de segurança.
* **De e para** – o intervalo de data e hora quando o conjunto de cópia de segurança foi criado.

Os conjuntos de cópia de segurança filtrados, em seguida, são apresentados com base nos seguintes atributos:

* **Nome** – o nome da política de cópia de segurança ou volume associada ao conjunto de cópia de segurança.
* **Tamanho** – o tamanho real do conjunto de cópia de segurança.
* **Criado em** – a data e hora quando foram criadas as cópias de segurança. 
* **Tipo de** – conjuntos de cópia de segurança podem ser os instantâneos locais ou instantâneos da cloud. Um instantâneo local é uma cópia de segurança de todos os seus dados de volume armazenados localmente no dispositivo, enquanto que um instantâneo de cloud refere-se para a cópia de segurança dos dados do volume que residem na cloud. Os instantâneos locais fornecem um acesso mais rápido, ao passo que os instantâneos de cloud são escolhidos para resiliência de dados.
* **Iniciado por** – as cópias de segurança podem ser iniciadas automaticamente, uma agenda ou manualmente por um utilizador. Pode utilizar uma política de cópia de segurança para agendar cópias de segurança. Em alternativa, pode utilizar o **efetuar cópia de segurança** opção para efetuar uma cópia de segurança manual.

## <a name="list-backup-sets-for-a-backup-policy"></a>Conjuntos de cópia de segurança de lista para uma política de cópia de segurança
Conclua os passos seguintes para listar todas as cópias de segurança para uma política de cópia de segurança.

#### <a name="to-list-backup-sets"></a>Para conjuntos de cópia de segurança de lista
1. Vá para o StorSimple Device Manager service e clique em **catálogo de cópia de segurança**.

2. Filtre as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo apropriado.
   3. Filtrar por **política de cópia de segurança** para ver as cópias de segurança correspondente.
   3. Na lista pendente de política de cópia de segurança, escolha **todos os** para ver todas as cópias de segurança no dispositivo selecionado.
   4. Clique em **aplicar** para executar esta consulta.
      
      As cópias de segurança associadas com a política de cópia de segurança selecionada devem aparecer na lista de conjuntos de cópia de segurança.

      ![Aceda ao catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Selecione um conjunto de cópias de segurança
Conclua os seguintes passos para selecionar uma cópia de segurança definido para um volume ou uma política de cópia de segurança.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de cópia de segurança
1. Vá para o StorSimple Device Manager service e clique em **catálogo de cópia de segurança**.
2. Filtre as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo apropriado. 
   3. Filtrar pela política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   4. Clique em **aplicar** para executar esta consulta.
      
      As cópias de segurança associadas com o volume selecionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.

      ![Aceda ao catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecione e expanda um conjunto de cópias de segurança. Agora, pode ver os conjuntos de cópia de segurança divididos pelos volumes nele contidos. O **restaurar** e **eliminar** opções estão disponíveis através do menu de contexto (contexto) para o conjunto de cópia de segurança. Pode efetuar qualquer uma destas ações no conjunto de cópia de segurança que selecionou.

    ![Aceda ao catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Eliminar um conjunto de cópia de segurança
Elimine uma cópia de segurança quando já não pretenda reter os dados associados a ele. Execute os seguintes passos para eliminar um conjunto de cópia de segurança.

#### <a name="to-delete-a-backup-set"></a>Para eliminar um conjunto de cópia de segurança
 Vá para o StorSimple Device Manager service e clique em **catálogo de cópia de segurança**.
1. Filtre as seleções da seguinte forma:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo apropriado. 
   3. Filtrar pela política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   4. Clique em **aplicar** para executar esta consulta.
      
      As cópias de segurança associadas com o volume selecionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.

      ![Aceda ao catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Selecione e expanda um conjunto de cópias de segurança. Agora, pode ver os conjuntos de cópia de segurança divididos pelos volumes nele contidos. O **restaurar** e **eliminar** opções estão disponíveis através do menu de contexto (contexto) para o conjunto de cópia de segurança. Com o botão direito do conjunto de cópia de segurança selecionado e no menu de contexto, selecione **eliminar**.

    ![Aceda ao catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Quando lhe for pedida confirmação, reveja as informações exibidas e clique em **eliminar**. A cópia de segurança selecionada é eliminada permanentemente.

    ![Aceda ao catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Será notificado quando a eliminação está em progresso e quando foi concluída com êxito. Após a eliminação estiver concluída, atualize a consulta nesta página. O conjunto de cópias de segurança eliminado já não irá aparecer na lista de conjuntos de cópia de segurança.

    ![Aceda ao catálogo de cópia de segurança](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilizar o catálogo de cópia de segurança para restaurar o seu dispositivo a partir de um conjunto de cópias de segurança](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

