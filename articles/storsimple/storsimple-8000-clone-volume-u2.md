---
title: Clone um volume na série StorSimple 8000 [ Microsoft Docs
description: Descreve os diferentes tipos de clones e utilização e explica como pode usar um conjunto de cópias de segurança para clonar um volume individual num dispositivo da série StorSimple 8000.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255005"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Usar o serviço do Gestor de Dispositivos do StorSimple no portal do Azure para clonar um volume

## <a name="overview"></a>Descrição geral

Este tutorial descreve como pode utilizar um conjunto de cópias de segurança para clonar um volume individual através da lâmina do **catálogo de backup.** Também explica a diferença entre clones *transitórios* e *permanentes.* A orientação neste tutorial aplica-se a todo o dispositivo da série StorSimple 8000 que executa o Update 3 ou mais tarde.

O serviço StorSimple Device Manager A lâmina de **catálogo de cópias** de segurança apresenta todos os conjuntos de backup que são criados quando as cópias de segurança manuais ou automáticas são tomadas. Em seguida, pode selecionar um volume num conjunto de cópia de segurança para clonar.

 ![Lista de conjuntos de backup](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Considerações para a clonagem de um volume

Considere as seguintes informações ao clonar um volume.

- Um clone comporta-se da mesma forma que um volume normal. Qualquer operação possível num volume está disponível para o clone.

- A monitorização e a cópia de segurança por defeito são automaticamente desativadas num volume clonado. Precisaria de configurar um volume clonado para quaisquer cópias de segurança.

- Um volume fixado localmente é clonado como um volume hierárquico. Se necessitar que o volume clonado seja fixado localmente, pode converter o clone num volume fixado localmente após a operação do clone ser concluída com sucesso. Para obter informações sobre a conversão de um volume hierárquico num volume fixado localmente, vá alterar [o tipo](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)de volume .

- Se tentar converter um volume clonado de camadas para fixações locais imediatamente após a clonagem (quando ainda é um clone transitório), a conversão falha com a seguinte mensagem de erro:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Este erro só é recebido se estiver a clonar num dispositivo diferente. Pode converter o volume com sucesso para fixação local se converter primeiro o clone transitório para um clone permanente. Tire uma imagem em nuvem do clone transitório para convertê-lo num clone permanente.

## <a name="create-a-clone-of-a-volume"></a>Criar um clone de um volume

Pode criar um clone no mesmo dispositivo, noutro dispositivo ou até mesmo num aparelho de nuvem utilizando um instantâneo local ou em nuvem.

O procedimento abaixo descreve como criar um clone a partir do catálogo de cópias de segurança.  Um método alternativo para iniciar o clone é ir a **Volumes,** selecionar um volume e, em seguida, clicar à direita para invocar o menu de contexto e selecionar **Clone**.

Execute os seguintes passos para criar um clone do seu volume a partir do catálogo de cópias de segurança.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Vá ao serviço StorSimple Device Manager e, em seguida, clique no **catálogo de backup**.

2. Selecione um conjunto de backup da seguinte forma:
   
   1. Selecione o dispositivo apropriado.
   2. Na lista de recaídas, escolha a política de volume ou cópia de segurança para o backup que pretende selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique **Em Aplicar** para executar esta consulta.

      As cópias de segurança associadas à política de volume ou cópia de segurança selecionada devem figurar na lista de conjuntos de backup.
   
      ![Lista de conjuntos de backup](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expanda o conjunto de backup para visualizar o volume associado e selecione um volume num conjunto de backup. Clique à direita e, em seguida, a partir do menu de contexto, **selecione Clone**.

   ![Lista de conjuntos de backup](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Na lâmina **Clone,** faça os seguintes passos:
   
   1. Identifique um dispositivo alvo. Este é o local onde o clone será criado. Pode escolher o mesmo dispositivo ou especificar outro dispositivo.

      > [!NOTE]
      > Certifique-se de que a capacidade necessária para o clone é inferior à capacidade disponível no dispositivo-alvo.
       
   2. Especifique um nome de volume único para o seu clone. O nome deve conter entre 3 e 127 caracteres.
      
       > [!NOTE]
       > O campo **Clone Volume As** será **nivelado** mesmo que esteja a clonar um volume fixado localmente. Não é possível alterar este cenário; no entanto, se precisar do volume clonado para ser fixado localmente também, pode converter o clone num volume localmente fixado após criar com sucesso o clone. Para obter informações sobre a conversão de um volume hierárquico num volume fixado localmente, vá alterar [o tipo](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)de volume .
          
   3. Nos **anfitriões ligados,** especifique um registo de controlo de acesso (ACR) para o clone. Pode adicionar um novo ACR ou escolher entre a lista existente. O ACR determinará quais os anfitriões que podem aceder a este clone.
      
       ![Lista de conjuntos de backup](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Clique em **Clone** para completar a operação.

4. Um trabalho de clone é iniciado e você é notificado quando o clone é criado com sucesso. Clique na notificação de emprego ou vá à lâmina **Jobs** para monitorizar o trabalho de clone.

    ![Lista de conjuntos de backup](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Depois de o trabalho do clone estar completo, vá ao seu dispositivo e, em seguida, clique em **Volumes**. Na lista de volumes, deve ver-se o clone que acabou de ser criado no mesmo recipiente de volume que tem o volume de origem.

    ![Lista de conjuntos de backup](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Um clone que é criado desta forma é um clone transitório. Para obter mais informações sobre os tipos de clones, consulte [Transiente vs. clones permanentes](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Transitóriovs. clones permanentes
Os clones transitórios só são criados quando se clona para outro dispositivo. Pode clonar um volume específico de um conjunto de backup para um dispositivo diferente gerido pelo StorSimple Device Manager. O clone transitório tem referências aos dados no volume original e utiliza esses dados para ler e escrever localmente no dispositivo-alvo.

Depois de tirar uma imagem em nuvem de um clone transitório, o clone resultante é um clone *permanente.* Durante este processo, é criada uma cópia dos dados na nuvem e o tempo para copiar estes dados é determinado pelo tamanho dos dados e pelas latenciências Azure (esta é uma cópia Azure-to-Azure). Este processo pode levar dias a semanas. O clone transitório torna-se um clone permanente e não tem qualquer referência aos dados originais de volume de que foi clonado.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones transitórios e permanentes
As seguintes secções descrevem situações de exemplo em que podem ser utilizados clones transitórios e permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação ao nível do item com um clone transitório
Tens de recuperar um ficheiro de apresentação do Microsoft PowerPoint com um ano de idade. O seu administrador de TI identifica a cópia de segurança específica desse tempo e, em seguida, filtra o volume. Em seguida, o administrador clona o volume, localiza o ficheiro que procura e fornece-o a si. Neste cenário, é utilizado um clone transitório.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Ensaios no ambiente de produção com um clone permanente
Tens de verificar um bug de teste no ambiente de produção. Cria-se um clone do volume no ambiente de produção e, em seguida, tira-se uma imagem em nuvem deste clone para criar um volume clonado independente. Neste cenário, é utilizado um clone permanente.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [restaurar um volume StorSimple a partir de um conjunto](storsimple-8000-restore-from-backup-set-u2.md)de backup .
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

