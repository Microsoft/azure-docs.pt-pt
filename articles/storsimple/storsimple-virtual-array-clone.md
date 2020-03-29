---
title: Clone a StorSimple Virtual Array backup [ Microsoft Docs
description: Aprenda a clonar uma cópia de segurança e a recuperar um ficheiro do seu StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: feffbb634af62d70a840febcf2a04afb7bdeeddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580942"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Clone a partir de uma cópia de segurança do seu StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Este artigo descreve passo a passo como clonar um conjunto de backup das suas ações ou volumes no seu Microsoft Azure StorSimple Virtual Array. A cópia de segurança clonada é utilizada para recuperar um ficheiro apagado ou perdido. O artigo também inclui passos detalhados para realizar uma recuperação ao nível do item no seu StorSimple Virtual Array configurado como um servidor de ficheiros.

## <a name="clone-shares-from-a-backup-set"></a>Clonar partilhas a partir de um conjunto de cópias de segurança

**Antes de tentar clonar ações, certifique-se de que tem espaço suficiente no dispositivo para completar esta operação.** Para clonar de uma cópia de segurança, no [portal Azure,](https://portal.azure.com/)execute os seguintes passos.

#### <a name="to-clone-a-share"></a>Para clonar uma parte

1. Navegue na lâmina **do Dispositivos.** Selecione e clique no seu dispositivo e, em seguida, clique em **Partilhas**. Selecione a parte que pretende clonar, clique na parte para invocar o menu de contexto. Selecione **Clone**.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Na lâmina **Clone,** clique em **Backup > Select** e, em seguida, faça o seguinte: 
   
   a.    Filtre uma cópia de segurança neste dispositivo com base no intervalo de tempo. Pode escolher entre **Os Últimos 7 dias,** **Últimos 30 dias,** e **Ano Passado.**
   
   b.    Na lista de cópias de segurança filtradas apresentadas, selecione uma cópia de segurança para clonar.
   
   c.    Clique em **OK**.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Na lâmina **Clone,** clique nas **definições** do Target e, em seguida, faça o seguinte:
   
   a.    Forneça um nome de partilha. O nome da partilha deve conter 3-127 caracteres.
   
   b.    Facultativamente, forneça uma descrição para a parte clonada.
   
   c.    Não pode alterar o tipo de parte a que está a restaurar. Uma parte hierárquica é clonada como uma base hierarquizada e uma parte fixada localmente como fixada localmente.
   
   d.    A capacidade é definida como igual ao tamanho da parte de onde está a clonar.
   
   e.    Designar os administradores para esta ação. Poderá modificar as propriedades de partilha através do File Explorer após o clone estar completo.
   
   f.    Clique em **OK**.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Clique em **Clone** para iniciar um trabalho de clone. Após a conclusão do trabalho, a operação do clone começa e você é notificado. Para monitorizar o progresso do clone, vá à lâmina **Jobs** e clique no trabalho para ver detalhes de trabalho.
5. Depois de o clone ser criado com sucesso, navegue de volta para a lâmina **Partilhas** do seu dispositivo.
6. Agora pode ver a nova parte clonada na lista de ações do seu dispositivo. Uma parte hierárquica é clonada como hierárquica e uma parte fixada localmente como uma parte fixada localmente.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Clonar volumes a partir de um conjunto de cópias de segurança

Para clonar de uma cópia de segurança, no portal Azure, é preciso executar passos semelhantes aos que estão a clonar uma parte. A operação do clone clona a cópia de segurança para um novo volume no mesmo dispositivo virtual; não se pode clonar para um dispositivo diferente.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Navegue na lâmina **do Dispositivos.** Selecione e clique no seu dispositivo e, em seguida, clique em **Volumes**. Selecione o volume que pretende clonar, clique no volume para invocar o menu de contexto. Selecione **Clone**.
   
   ![Clonar um volume](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Na lâmina **Clone,** clique em **Backup** e, em seguida, faça o seguinte: 
   
   a.    Filtre uma cópia de segurança neste dispositivo com base no intervalo de tempo. Pode escolher entre **Os Últimos 7 dias,** **Últimos 30 dias,** e **Ano Passado.** 
   
   b.    Na lista de cópias de segurança filtradas apresentadas, selecione uma cópia de segurança para clonar.
   
   c.    Clique em **OK**.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Na lâmina **Clone,** clique nas **definições** de volume do target e, em seguida, faça o seguinte::
   
   a. O nome do dispositivo é automaticamente povoado.
   
   b. Forneça um nome de volume para o **volume clonado**. O nome do volume deve conter 3 a 127 caracteres.
   
   c. O tipo de volume é automaticamente definido para o volume original. Um volume hierárquico é clonado como hierárquico e um volume fixado localmente como fixado localmente.
   
   d. Para os **anfitriões Conectados,** clique em **Selecionar**.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Na lâmina **de anfitriões Connected,** selecione a partir de um ACR existente ou adicione um novo ACR. Para adicionar um novo ACR, terá de fornecer um nome ACR e o anfitrião IQN. Clique em **Selecionar**.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Clique em **Clone** para lançar um trabalho de clone.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Após a criação do trabalho do clone, a clonagem começará. Uma vez criado o clone, é apresentado na lâmina Volumes no seu dispositivo. Note que um volume hierárquico é clonado como hierárquico e um volume fixado localmente é clonado como um volume fixado localmente.
   
   ![Clone um backup](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Uma vez que o volume aparece on-line na lista de volumes, o volume está disponível para utilização. No anfitrião do iniciador iSCSI, refresque a lista de alvos na janela de propriedades do iniciador iSCSI. Um novo alvo que contenha o nome do volume clonado deve aparecer como "inativo" sob a coluna de estado.
8. Selecione o alvo e clique em **Ligar**. Após a ligação do iniciador ao alvo, o estado deve mudar para **Ligado**.
9. Na janela **de Gestão** de Discos, os volumes montados aparecem como mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.

> [!IMPORTANT]
> Ao tentar clonar um volume ou uma parte de um conjunto de backup, se o trabalho de clone falhar, um volume de destino ou partilha ainda pode ser criado no portal. É importante que apague este volume de destino ou partilhe no portal para minimizar quaisquer problemas futuros decorrentes deste elemento.
> 
> 

## <a name="item-level-recovery-ilr"></a>Recuperação ao nível do item (ILR)

Esta versão introduz a recuperação do nível de item (ILR) num StorSimple Virtual Array configurado como servidor de ficheiros. A funcionalidade permite-lhe fazer a recuperação granular de ficheiros e pastas a partir de uma cópia de segurança em nuvem de todas as ações do dispositivo StorSimple. Pode recuperar ficheiros apagados de cópias de segurança recentes utilizando um modelo de self-service.

Cada ação tem uma pasta *.backups* que contém as cópias de segurança mais recentes. Pode navegar para a cópia de segurança desejada, copiar ficheiros e pastas relevantes a partir da cópia de segurança e restaurá-las. Esta funcionalidade elimina chamadas para administradores para restaurar ficheiros de backups.

1. Ao executar o ILR, pode visualizar as cópias de segurança através do File Explorer. Clique na parte específica para a sua necessidade de ver a cópia de segurança. Verá uma pasta *.backups* criada sob a parte que armazena todas as cópias de segurança. Expanda a pasta *.backups* para visualizar as cópias de segurança. A pasta mostra a visão explodida de toda a hierarquia de reserva. Esta vista é criada a pedido e geralmente leva apenas alguns segundos para criar.
   
   As últimas cinco cópias de segurança são apresentadas desta forma e podem ser usadas para realizar uma recuperação ao nível do item. As cinco cópias de segurança recentes incluem tanto o predefinido programado como as cópias de segurança manuais.
   
   * **Backups agendados nomeados** como &lt;Nome&gt;de Dispositivo DailySchedule-YYYMMDD-HHMMSS-UTC.
   * **Backups manuais nomeados** como Ad-hoc-YYYMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifique a cópia de segurança que contém a versão mais recente do ficheiro apagado. Embora o nome da pasta contenha uma marca de tempo UTC em cada um dos casos anteriores, o momento em que a pasta foi criada é o tempo real do dispositivo quando a cópia de segurança começou. Utilize a marca de tempo da pasta para localizar e identificar as cópias de segurança.

3. Localize a pasta ou o ficheiro que pretende restaurar na cópia de segurança que identificou no passo anterior. Note que só pode ver os ficheiros ou pastas para os quais tem permissões. Se não conseguir aceder a certos ficheiros ou pastas, contacte um administrador de ações. O administrador pode utilizar o File Explorer para editar as permissões de partilha e dar-lhe acesso ao ficheiro ou pasta específico. É uma boa prática recomendada que o administrador de ações seja um grupo de utilizadores em vez de um único utilizador.

4. Copie o ficheiro ou a pasta para a parte apropriada no servidor de ficheiros StorSimple.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [administrar o seu StorSimple Virtual Array utilizando a Web UI local](storsimple-ova-web-ui-admin.md).

