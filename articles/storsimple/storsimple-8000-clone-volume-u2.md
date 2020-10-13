---
title: Clone um volume na série StorSimple 8000 Microsoft Docs
description: Descreve os diferentes tipos de clones e utilização, e explica como pode usar um conjunto de backup para clonar um volume individual num dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 5ebb7f1d9440bc7a8b75afe64403762532213c1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767576"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Usar o serviço do Gestor de Dispositivos do StorSimple no portal do Azure para clonar um volume

## <a name="overview"></a>Descrição geral

Este tutorial descreve como pode utilizar um conjunto de backup para clonar um volume individual através da lâmina do **catálogo Backup.** Também explica a diferença entre clones *transitórios* e *permanentes.* A orientação neste tutorial aplica-se a todos os dispositivos da série StorSimple 8000 em execução Update 3 ou posterior.

A lâmina de **catálogo de backup** do serviço StorSimple Device Manager exibe todos os conjuntos de backup que são criados quando são recolhidas cópias de segurança manuais ou automatizadas. Em seguida, pode selecionar um volume num conjunto de cópias de segurança para clonar.

 ![Lista de conjuntos de backup](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Considerações para a clonagem de um volume

Considere as seguintes informações ao clonar um volume.

- Um clone comporta-se da mesma forma que um volume normal. Qualquer operação possível num volume está disponível para o clone.

- A monitorização e a cópia de segurança predefinida são automaticamente desativadas num volume clonado. Precisaria de configurar um volume clonado para qualquer reforço.

- Um volume fixado localmente é clonado como um volume hierárquico. Se precisar do volume clonado para ser fixado localmente, pode converter o clone num volume fixado localmente após a conclusão da operação do clone com sucesso. Para obter informações sobre a conversão de um volume hierarquizado para um volume fixado localmente, vá alterar [o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Se tentar converter um volume clonado de camadas para localmente fixados imediatamente após a clonagem (quando ainda é um clone transitório), a conversão falha com a seguinte mensagem de erro:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Este erro só é recebido se estiver a clonar para um dispositivo diferente. Pode converter o volume com sucesso para fixado localmente se converter primeiro o clone transitório num clone permanente. Tire uma imagem em nuvem do clone transitório para convertê-lo num clone permanente.

## <a name="create-a-clone-of-a-volume"></a>Criar um clone de um volume

Pode criar um clone no mesmo dispositivo, outro dispositivo ou até mesmo um aparelho em nuvem utilizando um instantâneo local ou em nuvem.

O procedimento abaixo descreve como criar um clone a partir do catálogo de backup.  

Execute os seguintes passos para criar um clone do seu volume a partir do catálogo de backup.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Vá ao seu serviço StorSimple Device Manager e, em seguida, clique **no catálogo backup**.

2. Selecione um conjunto de backup da seguinte forma:
   
   1. Selecione o dispositivo apropriado.
   2. Na lista de drop-down, escolha a política de volume ou backup para a cópia de segurança que deseja selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique **em Aplicar** para executar esta consulta.

      As cópias de segurança associadas à política de volume ou backup selecionada devem constar da lista de conjuntos de backup.
   
      ![Lista de conjunto de backup 2](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Expanda o conjunto de backup para visualizar o volume associado e selecione um volume num conjunto de backup. Clique à direita e, em seguida, a partir do menu de contexto, selecione **Clone**.

   ![Lista de conjunto de backup 3](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Na lâmina **Clone,** faça os seguintes passos:
   
   1. Identifique um dispositivo alvo. Este é o local onde o clone será criado. Pode escolher o mesmo dispositivo ou especificar outro dispositivo.

      > [!NOTE]
      > Certifique-se de que a capacidade necessária para o clone é inferior à capacidade disponível no dispositivo-alvo.
       
   2. Especifique um nome de volume único para o seu clone. O nome deve conter entre 3 e 127 caracteres.
      
       > [!NOTE]
       > O **volume clone Como** campo será **tiered** mesmo que você esteja clonando um volume fixado localmente. Não é possível alterar esta definição; no entanto, se precisar do volume clonado para ser fixado localmente também, pode converter o clone num volume fixado localmente depois de criar o clone com sucesso. Para obter informações sobre a conversão de um volume hierarquizado para um volume fixado localmente, vá alterar [o tipo de volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. Nos **anfitriões ligados,** especifique um registo de controlo de acesso (ACR) para o clone. Pode adicionar um novo ACR ou escolher entre a lista existente. O ACR determinará quais os anfitriões que podem aceder a este clone.
      
       ![Lista de conjunto de backup 4](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Clique em **Clone** para completar a operação.

4. Um trabalho de clone é iniciado e você é notificado quando o clone é criado com sucesso. Clique na notificação de trabalho ou vá à lâmina **Jobs** para monitorizar o trabalho do clone.

    ![Lista de conjunto de backup 5](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Depois de terminar o trabalho do clone, vá ao seu dispositivo e, em seguida, clique em **Volumes**. Na lista de volumes, deve ver o clone que foi criado no mesmo recipiente de volume que tem o volume de origem.

    ![Lista de conjunto de backup 6](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Um clone que é criado desta forma é um clone transitório. Para obter mais informações sobre tipos de clones, consulte [transiente vs. clones permanentes](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Transiente vs. clones permanentes
Os clones transitórios só são criados quando se clona para outro dispositivo. Pode clonar um volume específico de um conjunto de backup para um dispositivo diferente gerido pelo StorSimple Device Manager. O clone transitório tem referências aos dados no volume original e utiliza esses dados para ler e escrever localmente no dispositivo-alvo.

Depois de tirar uma imagem em nuvem de um clone transitório, o clone resultante é um clone *permanente.* Durante este processo, uma cópia dos dados é criada na nuvem e o tempo para copiar estes dados é determinado pelo tamanho dos dados e pelas latências Azure (esta é uma cópia Azure-to-Azure). Este processo pode levar dias a semanas. O clone transitório torna-se um clone permanente e não tem qualquer referência aos dados originais de volume de onde foi clonado.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones transitórios e permanentes
As secções seguintes descrevem situações de exemplo em que podem ser utilizados clones transitórios e permanentes.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação de nível de item com um clone transitório
Tens de recuperar um ficheiro de apresentação do Microsoft PowerPoint com um ano de idade. O seu administrador de TI identifica a cópia de segurança específica dessa altura e, em seguida, filtra o volume. Em seguida, o administrador clona o volume, localiza o ficheiro que procura e fornece-o a si. Neste cenário, é usado um clone transitório.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testes no ambiente de produção com um clone permanente
É necessário verificar um bug de teste no ambiente de produção. Você cria um clone do volume no ambiente de produção e, em seguida, tira uma imagem em nuvem deste clone para criar um volume clonado independente. Neste cenário, é usado um clone permanente.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [restaurar um volume StorSimple a partir de um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

