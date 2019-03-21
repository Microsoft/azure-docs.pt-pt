---
title: Clonar um volume em série StorSimple 8000 | Documentos da Microsoft
description: Descreve os tipos de clone diferentes e a utilização e explica como pode usar uma cópia de segurança definida para clonar um volume individual num dispositivo da série StorSimple 8000.
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
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097240"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Utilizar o serviço StorSimple Device Manager no portal do Azure para clonar um volume

## <a name="overview"></a>Descrição geral

Este tutorial descreve como pode utilizar uma cópia de segurança definida para clonar um volume individual através da **catálogo de cópia de segurança** painel. Também explica a diferença entre *transitório* e *permanente* clona. As orientações neste tutorial aplica-se para todos os dispositivos de série 8000 do StorSimple que executam a atualização 3 ou posterior.

O serviço de Gestor de dispositivos do StorSimple **catálogo de cópia de segurança** painel apresenta todos os conjuntos de cópia de segurança que são criados quando backups manuais ou automáticos são feitos. Em seguida, pode selecionar um volume em conjunto para clonar uma cópia de segurança.

 ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Considerações para a clonagem de um volume

Considere as seguintes informações ao clonar um volume.

- Um clone se comporta da mesma forma como um volume normal. Qualquer operação que é possível num volume está disponível para o clone.

- Padrão de monitorização e cópia de segurança automaticamente estão desativadas num volume clonado. Terá de configurar um volume clonado para quaisquer cópias de segurança.

- Um volume localmente afixado é clonado como um volume em camadas. Se precisar do volume clonado para ser afixado localmente, pode converter o clone para um volume localmente afixado após a operação de clonagem é concluída com êxito. Para informações sobre como converter um volume em camadas num volume localmente afixado, aceda a [alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Se tentar converter um volume clonado de camadas para localmente afixado imediatamente após a clonagem (quando ainda é um clone transitório), a conversão falhar com a seguinte mensagem de erro:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Este erro é recebido apenas se estiver a clonagem para um dispositivo diferente. Pode converter com êxito o volume afixado localmente se converter primeiro o clone transitório para um clone permanente. Tire um instantâneo de cloud do clone transitório convertê-lo num clone permanente.

## <a name="create-a-clone-of-a-volume"></a>Criar um clone de um volume

Pode criar um clone no mesmo dispositivo, outro dispositivo ou até mesmo uma aplicação da cloud com um local ou um instantâneo de cloud.

O procedimento a seguir descreve como criar um clone a partir do catálogo de cópia de segurança.  Um método alternativo para iniciar clone é ir para **Volumes**, selecione um volume, em seguida, clique com o botão direito para invocar o menu de contexto e selecione **Clone**.

Execute os seguintes passos para criar um clone do seu volume a partir do catálogo de cópia de segurança.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Aceda ao seu serviço StorSimple Device Manager e, em seguida, clique em **catálogo de cópia de segurança**.

2. Selecione uma cópia de segurança definida da seguinte forma:
   
   1. Selecione o dispositivo apropriado.
   2. Na lista pendente, escolha a política de cópia de segurança ou de volume para a cópia de segurança que pretende selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique em **aplicar** para executar esta consulta.

      As cópias de segurança associadas com o volume selecionado ou política de cópia de segurança deverá aparecer na lista de conjuntos de cópia de segurança.
   
      ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expanda a cópia de segurança definida para ver os volumes associados e selecionar um volume num conjunto de cópia de segurança. Com o botão direito e, em seguida, no menu de contexto, selecione **Clone**.

   ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Na **Clone** painel, siga os passos abaixo:
   
   1. Identifica um dispositivo de destino. Esta é a localização onde será criado o clone. Pode escolher o mesmo dispositivo ou especifique outro dispositivo.

      > [!NOTE]
      > Certifique-se de que a capacidade necessária para o clone é menor do que a capacidade disponível no dispositivo de destino.
       
   2. Especifique um nome de volume exclusivo para seu clone. O nome tem de conter entre 3 e 127 carateres.
      
       > [!NOTE]
       > O **Clone Volume como** campo será **em camadas** mesmo que a clonagem de um volume afixado localmente. Não é possível alterar esta definição; No entanto, se precisar do volume clonado para ser afixado localmente, bem, pode converter o clone para um volume localmente afixado, depois de criar com êxito o clone. Para informações sobre como converter um volume em camadas num volume localmente afixado, aceda a [alterar o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. Sob **anfitriões ligados**, especifique um registo de controlo de acesso (ACR) para o clone. Pode adicionar um novo ACR ou escolha a partir da lista existente. O ACR irá determinar os anfitriões que podem aceder a este clone.
      
       ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Clique em **Clone** para concluir a operação.

4. Uma tarefa de clonagem é iniciada e será notificado quando o clone for criado com êxito. Clique na notificação da tarefa ou aceda ao **tarefas** painel para monitorizar a tarefa de clone.

    ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Depois da tarefa de clone estiver concluída, vá para o seu dispositivo e, em seguida, clique em **Volumes**. Na lista de volumes, deverá ver o clone que acabou de ser criado no mesmo contentor de volume que tem o volume de origem.

    ![Lista de conjuntos de cópia de segurança](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Um clone criado dessa forma é um clone transitório. Para obter mais informações sobre os tipos de clone, consulte [transitório versus clones permanentes](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Transitório versus clones permanentes
Clones transitórias são criados apenas quando clona para outro dispositivo. Pode clonar um volume específico de uma cópia de segurança definido para um dispositivo diferente gerenciado pelo Gestor de dispositivos do StorSimple. O clone transitório tem referências aos dados do volume original e usa esses dados para ler e escrever localmente no dispositivo de destino.

Depois de tirar um instantâneo de cloud de um clone transitório, o clone resultante é uma *permanente* clone. Durante este processo, é criada uma cópia dos dados na cloud e a hora para copiar esses dados é determinada pelo tamanho dos dados e as latências do Azure (isto é uma cópia do Azure para o Azure). Este processo pode demorar dias, semanas. O clone transitório se torna um clone permanente e não tem quaisquer referências para os dados originais do volume que foi clonado a partir de.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones transitórios e permanentes
As secções seguintes descrevem as situações de exemplo em que podem ser utilizados clones transitórias e permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação ao nível do item com um clone transitório
Tem de recuperar um arquivo de apresentação do Microsoft PowerPoint um anos. O administrador de TI identifica a cópia de segurança específica a contar dessa hora e, em seguida, filtra o volume. Em seguida, o administrador clona o volume, localiza o arquivo que está procurando e fornece-lhe. Neste cenário, é utilizado um clone transitório.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testar no ambiente de produção com um clone permanente
Tem de validar um bug de teste no ambiente de produção. Criar um clone do volume no ambiente de produção e, em seguida, tire um instantâneo de cloud deste clone para criar um volume clonado independente. Neste cenário, é utilizado um clone permanente.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [restaurar um volume StorSimple a partir de um conjunto de cópia de segurança](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

