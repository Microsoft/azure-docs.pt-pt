---
title: Clonar uma cópia de segurança do StorSimple Virtual Array | Documentos da Microsoft
description: Saiba como clonar uma cópia de segurança e recuperar um arquivo de sua matriz Virtual StorSimple.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580942"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Clonar a partir de uma cópia de segurança da sua matriz Virtual do StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve passo a passo como clonar um conjunto de cópias de segurança das suas partilhas ou volumes em sua matriz Virtual do Microsoft Azure StorSimple. A cópia de segurança clonada é usada para recuperar um arquivo perdido ou excluído. O artigo também inclui os passos detalhados para efetuar uma recuperação ao nível do item em sua matriz Virtual do StorSimple configurado como um servidor de ficheiros.

## <a name="clone-shares-from-a-backup-set"></a>Partilhas de clone a partir de um conjunto de cópia de segurança

**Antes de tentar clonar partilhas, certifique-se de que tem espaço suficiente no dispositivo para concluir esta operação.** Para clonar a partir de uma cópia de segurança, no [portal do Azure](https://portal.azure.com/), execute os seguintes passos.

#### <a name="to-clone-a-share"></a>Para clonar uma partilha

1. Navegue até **dispositivos** painel. Selecione e clique no seu dispositivo e, em seguida, clique em **partilhas**. Selecione a partilha que deseja clonar, com o botão direito a partilha para invocar o menu de contexto. Selecione **Clone**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Na **Clone** painel, clique em **cópia de segurança > selecione** e, em seguida, faça o seguinte: 
   
   a.    Filtre uma cópia de segurança neste dispositivo com base no intervalo de tempo. Pode escolher entre **nos últimos 7 dias**, **nos últimos 30 dias**, e **último ano**.
   
   b.    Na lista de cópias de segurança filtradas apresentado, selecione uma cópia de segurança para clonar a partir dos.
   
   c.    Clique em **OK**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Na **Clone** painel, clique em **definições de destino** e, em seguida, faça o seguinte:
   
   a.    Forneça um nome de partilha. O nome da partilha tem de conter 3 127 carateres.
   
   b.    Opcionalmente, indique uma descrição para a partilha clonada.
   
   c.    Não é possível alterar o tipo de partilha que está a restaurar. Uma partilha em camadas for clonada como um em camadas e uma partilha localmente afixada, como localmente afixado.
   
   d.    A capacidade está definida como igual ao tamanho da partilha que são a clonagem de.
   
   e.    Atribua administradores para esse compartilhamento. Será capaz de modificar as propriedades de partilha através do Explorador de ficheiros após a conclusão da clonagem.
   
   f.    Clique em **OK**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Clique em **Clone** para iniciar uma tarefa de clone. Depois da tarefa estiver concluída, a operação de clonagem é iniciada e será notificado. Para monitorizar o progresso de clone, vá para o **tarefas** painel e clique na tarefa para ver os detalhes da tarefa.
5. Depois do clone for criado com êxito, navegue de volta para o **partilhas** painel no seu dispositivo.
6. Agora pode ver a nova partilha clonada na lista de partilhas no seu dispositivo. Uma partilha em camadas for clonada como em camadas e uma partilha de afixado localmente como uma partilha de afixado localmente.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Clonar volumes a partir de um conjunto de cópia de segurança

Para clonar a partir de uma cópia de segurança, no portal do Azure, terá de efetuar passos semelhantes para aqueles quando uma partilha de clonagem. A operação de clonagem clona a cópia de segurança para um novo volume no mesmo dispositivo virtual; Não é possível clonar a um dispositivo diferente.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Navegue até **dispositivos** painel. Selecione e clique no seu dispositivo e, em seguida, clique em **Volumes**. Selecione o volume que deseja clonar, com o botão direito do volume para invocar o menu de contexto. Selecione **Clone**.
   
   ![Clonar um volume](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Na **Clone** painel, clique em **cópia de segurança** e, em seguida, faça o seguinte: 
   
   a.    Filtre uma cópia de segurança neste dispositivo com base no intervalo de tempo. Pode escolher entre **nos últimos 7 dias**, **nos últimos 30 dias**, e **último ano**. 
   
   b.    Na lista de cópias de segurança filtradas apresentado, selecione uma cópia de segurança para clonar a partir dos.
   
   c.    Clique em **OK**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Na **Clone** painel, clique em **definições do volume de destino** e, em seguida, faça o seguinte::
   
   a. O nome do dispositivo é preenchido automaticamente.
   
   b. Forneça um nome de volume para o **clonar volume**. O nome do volume tem de conter 3 e 127 carateres.
   
   c. O tipo de volume é automaticamente definido para o volume original. Um volume em camadas for clonado como em camadas e um volume afixado localmente como localmente afixado.
   
   d. Para o **anfitriões ligados**, clique em **selecione**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Na **anfitriões ligados** painel, selecione a partir de um ACR existente ou adicione um novo ACR. Para adicionar um novo ACR, terá de fornecer um nome ACR e o IQN de anfitrião. Clique em **Selecionar**.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Clique em **Clone** para iniciar uma tarefa de clonagem.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Quando a tarefa de clone é criada, a clonagem será iniciado. Depois de criar o clone, é apresentado no painel de Volumes no dispositivo. Tenha em atenção que um volume em camadas for clonado como em camadas e um volume localmente afixado é clonado como um volume afixado localmente.
   
   ![Clonar uma cópia de segurança](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Assim que o volume é apresentada online na lista de volumes, o volume está disponível para utilização. No anfitrião de iniciador iSCSI, atualize a lista de destinos na janela de propriedades do iniciador de iSCSI. Um novo destino que contém o nome do volume clonado deve aparecer como "inativo" sob a coluna de estado.
8. Selecione o destino e clique em **Connect**. Depois do iniciador está ligado ao destino, o estado deve ser alterado para **ligado**.
9. Na **gestão de discos** apresentada dos volumes montados de janela, conforme mostrado na ilustração seguinte. Clique com o botão direito do rato no volume detetado (clique no nome do disco) e, em seguida, clique em **Online**.

> [!IMPORTANT]
> Quando tentar clonar um volume ou uma partilha de um conjunto de cópia de segurança, se falhar a tarefa de clonagem, um volume de destino ou partilha ainda pode ser criada no portal. É importante que eliminar o volume de destino ou partilhar no portal para minimizar qualquer futuros problemas resultantes desse elemento.
> 
> 

## <a name="item-level-recovery-ilr"></a>Recuperação ao nível do item (ILR)

Essa versão apresenta a recuperação ao nível do item (ILR) numa matriz Virtual StorSimple configurado como um servidor de ficheiros. A funcionalidade permite-lhe efetuar a recuperação granular de ficheiros e pastas a partir de uma cópia de segurança de cloud de todas as partilhas no dispositivo StorSimple. Pode recuperar arquivos excluídos de cópias de segurança recentes através de um modelo de self-service.

Cada partilha tem um *. backups* pasta que contém as cópias de segurança mais recentes. Pode navegar para a cópia de segurança pretendida, copiar arquivos relevantes e pastas da cópia de segurança e restaurá-las. Esta funcionalidade elimina a chamadas para os administradores para restaurar arquivos de cópias de segurança.

1. Quando efetuar a ILR, pode ver as cópias de segurança através do Explorador de ficheiros. Clique na partilha específica que pretende examinar a cópia de segurança. Verá uma *. backups* pasta criada abaixo da partilha que armazena todas as cópias de segurança. Expanda a *. backups* pasta para ver as cópias de segurança. A pasta mostra a vista de exploded de toda a hierarquia de cópia de segurança. Esta vista é criada sob demanda e demora apenas alguns segundos para criar.
   
   As últimos cinco cópias de segurança são apresentadas desta forma e podem ser utilizadas para efetuar uma recuperação ao nível do item. As cinco cópias de segurança recentes incluem o padrão agendadas e as cópias de segurança manuais.
   
   * **Cópias de segurança agendadas** com o nome como &lt;nome do dispositivo&gt;DailySchedule-AAAAMMDD-T'HHMMSS-UTC.
   * **As cópias de segurança manuais** com o nome como Ad-hoc-AAAAMMDD-T'HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Identifique a cópia de segurança que contém a versão mais recente do arquivo excluído. Embora o nome da pasta contém um carimbo de UTC em cada um dos casos descritos atrás, a hora em que a pasta foi criada é a hora do dispositivo real quando a cópia de segurança iniciada. Utilize o carimbo de hora de pasta para localizar e identificar as cópias de segurança.

3. Localize a pasta ou ficheiro que pretende restaurar na cópia de segurança que identificou no passo anterior. Tenha em atenção que só pode ver os ficheiros ou pastas que tem permissões para. Se não é possível aceder a certos arquivos ou pastas, contacte um administrador de partilha. O administrador pode utilizar o Explorador de ficheiros para editar as permissões de partilha e lhe conceder acesso para a pasta ou ficheiro específico. É uma prática recomendada que o administrador de partilha é um grupo de utilizadores, em vez de um único utilizador.

4. Copie o ficheiro ou pasta ao compartilhamento apropriado no seu servidor de ficheiros do StorSimple.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como [administrar a sua matriz Virtual do StorSimple com a IU web local](storsimple-ova-web-ui-admin.md).

