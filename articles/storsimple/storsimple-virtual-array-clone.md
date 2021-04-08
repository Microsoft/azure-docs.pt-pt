---
title: Clone um backup StorSimple Virtual Array | Microsoft Docs
description: Saiba como clonar uma cópia de segurança e recuperar um ficheiro do seu StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023045"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Clone a partir de uma cópia de segurança do seu StorSimple Virtual Array

## <a name="overview"></a>Descrição Geral

Este artigo descreve passo a passo como clonar um conjunto de backup das suas ações ou volumes no seu Microsoft Azure StorSimple Virtual Array. A cópia de segurança clonada é utilizada para recuperar um ficheiro apagado ou perdido. O artigo também inclui etapas detalhadas para realizar uma recuperação de nível de item no seu StorSimple Virtual Array configurado como um servidor de ficheiros.

## <a name="clone-shares-from-a-backup-set"></a>Clonar partilhas a partir de um conjunto de cópias de segurança

**Antes de tentar clonar ações, certifique-se de que tem espaço suficiente no dispositivo para completar esta operação.** Para clonar a partir de uma cópia de segurança, no [portal Azure,](https://portal.azure.com/)execute os seguintes passos.

#### <a name="to-clone-a-share"></a>Para clonar uma parte

1. Navegue pela lâmina **dos dispositivos.** Selecione e clique no seu dispositivo e, em seguida, clique em **Partilhars**. Selecione a partilha que pretende clonar, clique com o botão direito para invocar o menu de contexto. Selecione **Clonar**.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Na lâmina **Clone,** clique **em Cópia de Segurança > Selecione** e, em seguida, faça o seguinte: 
   
   a.    Filtre uma cópia de segurança deste dispositivo com base no intervalo de tempo. Pode escolher entre **os últimos 7 dias,** **os últimos 30 dias** e o ano **passado.**
   
   b.    Na lista de cópias de segurança filtradas apresentadas, selecione uma cópia de segurança para clonar.
   
   c.    Clique em **OK**.
   
   ![Clone um backup 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Na lâmina **Clone,** clique nas **definições do Target** e, em seguida, faça o seguinte:
   
   a.    Forneça um nome de partilha. O nome da partilha deve conter 3-127 caracteres.
   
   b.    Opcionalmente, forneça uma descrição para a parte clonada.
   
   c.    Não pode alterar o tipo de parte a que está a restaurar. Uma parte hierárquica é clonada como uma parte hierárquica e uma participação localmente fixada como fixada localmente.
   
   d.    A capacidade é definida como igual ao tamanho da parte de que está a clonar.
   
   e.    Atribua os administradores para esta parte. Poderá modificar as propriedades de partilha através do File Explorer após a conclusão do clone.
   
   f.    Clique em **OK**.
   
   ![Clone um backup 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Clique em **Clone** para iniciar um trabalho de clone. Depois do trabalho estar concluído, a operação do clone começa e é notificado. Para monitorizar o progresso do clone, vá à lâmina **Jobs** e clique no trabalho para ver detalhes do trabalho.
5. Depois de o clone ter sido criado com sucesso, volte para a lâmina **Shares** do seu dispositivo.
6. Pode agora ver a nova participação clonada na lista de ações do seu dispositivo. Uma parte hierárquica é clonada como hierarquizada e uma participação localmente fixada como uma parte fixa local.
   
   ![Clone um backup 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Clonar volumes a partir de um conjunto de cópias de segurança

Para clonar a partir de uma cópia de segurança, no portal Azure, você tem que executar passos semelhantes aos que clonam uma parte. A operação clone clona a cópia de segurança para um novo volume no mesmo dispositivo virtual; não se pode clonar para um dispositivo diferente.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Navegue pela lâmina **dos dispositivos.** Selecione e clique no seu dispositivo e, em seguida, clique em **Volumes**. Selecione o volume que pretende clonar, clique com o botão direito para invocar o menu de contexto. Selecione **Clonar**.
   
   ![Clonar um volume](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Na lâmina **Clone,** clique **em Backup** e, em seguida, faça o seguinte: 
   
   a.    Filtre uma cópia de segurança deste dispositivo com base no intervalo de tempo. Pode escolher entre **os últimos 7 dias,** **os últimos 30 dias** e o ano **passado.** 
   
   b.    Na lista de cópias de segurança filtradas apresentadas, selecione uma cópia de segurança para clonar.
   
   c.    Clique em **OK**.
   
   ![Clone um volume 2](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Na lâmina **Clone,** clique nas **definições de volume do Destino** e, em seguida, faça o seguinte::
   
   a. O nome do dispositivo é automaticamente povoado.
   
   b. Fornecer um nome de volume para o **volume clonado**. O nome do volume deve conter 3 a 127 caracteres.
   
   c. O tipo de volume é automaticamente definido para o volume original. Um volume hierárquico é clonado como hierarquizado e um volume fixado localmente como fixado localmente.
   
   d. Para os **anfitriões ligados,** clique **em Select**.
   
   ![Clone um volume 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Na lâmina **de anfitriões conectados,** selecione a partir de um ACR existente ou adicione um novo ACR. Para adicionar um novo ACR, terá de fornecer um nome ACR e o IQN do anfitrião. Clique em **Selecionar**.
   
   ![Clone um volume 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Clique **em Clone** para lançar um trabalho de clone.
   
   ![Clone um volume 5](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Após a criação do trabalho do clone, a clonagem começará. Uma vez criado o clone, é apresentado na lâmina volumes do seu dispositivo. Note que um volume hierárquico é clonado como hierarquizado e que um volume fixado localmente é clonado como um volume fixado localmente.
   
   ![Clone um volume 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Uma vez que o volume aparece on-line na lista de volumes, o volume está disponível para utilização. No anfitrião do iniciador iSCSI, atualize a lista de alvos na janela de propriedades do iniciador iSCSI. Um novo alvo que contenha o nome do volume clonado deve aparecer como "inativo" sob a coluna de estado.
8. Selecione o alvo e clique em **Ligar**. Depois de o iniciador estar ligado ao alvo, o estado deve mudar para **Connected**.
9. Na janela **de Gestão** do Disco, os volumes montados aparecem como mostrado na seguinte ilustração. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.

> [!IMPORTANT]
> Ao tentar clonar um volume ou uma parte de um conjunto de backup, se o trabalho do clone falhar, um volume ou partilha-alvo ainda pode ser criado no portal. É importante que elimine este volume-alvo ou partilhe no portal para minimizar quaisquer problemas futuros decorrentes deste elemento.
> 
> 

## <a name="item-level-recovery-ilr"></a>Recuperação ao nível do item (ILR)

Esta versão introduz a recuperação do nível de item (ILR) num StorSimple Virtual Array configurado como um servidor de ficheiros. A funcionalidade permite-lhe fazer a recuperação granular de ficheiros e pastas a partir de uma cópia de segurança em nuvem de todas as ações do dispositivo StorSimple. Pode recuperar ficheiros apagados de cópias de segurança recentes utilizando um modelo de self-service.

Cada ação tem uma pasta *.backups* que contém as cópias de segurança mais recentes. Pode navegar para a cópia de segurança desejada, copiar ficheiros e pastas relevantes a partir da cópia de segurança e restaurá-los. Esta funcionalidade elimina chamadas para administradores para restaurar ficheiros a partir de cópias de segurança.

1. Ao executar o ILR, pode visualizar as cópias de segurança através do File Explorer. Clique na partilha específica para a que deseja ver a cópia de segurança. Verá uma pasta *.backups* criada sob a partilha que armazena todas as cópias de segurança. Expanda a pasta *.backups* para visualizar as cópias de segurança. A pasta mostra a visão explodida de toda a hierarquia de reserva. Esta vista é criada a pedido e geralmente leva apenas alguns segundos para criar.
   
   As últimas cinco cópias de segurança são apresentadas desta forma e podem ser utilizadas para realizar uma recuperação ao nível do item. As cinco cópias de segurança recentes incluem tanto o programado padrão como as cópias de segurança manuais.
   
   * **Backups programados nomeados** como &lt; nome de dispositivo &gt; DailySchedule-YYYYMMDD-HHMMSS-UTC.
   * **Cópias de segurança manuais** chamadas Ad-hoc-YYYMMDD-HHMMSS-UTC.
     
     ![Screenshot do Explorador de Ficheiros mostrando a pasta .backups. No interior desta pasta, é selecionada a pasta de cópias de segurança manual.](./media/storsimple-virtual-array-clone/image14.png)

2. Identifique a cópia de segurança que contém a versão mais recente do ficheiro eliminado. Embora o nome da pasta contenha um relógio UTC em cada um dos casos anteriores, o momento em que a pasta foi criada é a hora real do dispositivo quando a cópia de segurança começou. Utilize o tempo de tempo da pasta para localizar e identificar as cópias de segurança.

3. Localize a pasta ou o ficheiro que pretende restaurar na cópia de segurança que identificou no passo anterior. Note que só pode ver os ficheiros ou pastas para os que tem permissões. Se não conseguir aceder a determinados ficheiros ou pastas, contacte um administrador de partilha. O administrador pode utilizar o File Explorer para editar as permissões de partilha e dar-lhe acesso ao ficheiro ou pasta específico. É uma boa prática recomendada que o administrador de ações seja um grupo de utilizadores em vez de um único utilizador.

4. Copie o ficheiro ou a pasta para a parte apropriada no seu servidor de ficheiros StorSimple.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [administrar o seu StorSimple Virtual Array utilizando a UI web local.](storsimple-ova-web-ui-admin.md)

