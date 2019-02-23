---
title: Substituir uma unidade de disco num dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Explica como substituir uma unidade de disco num bastidor principal do StorSimple ou um bastidor EBOD.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673936"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Substituir uma unidade de disco no seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
Este tutorial explica como pode remover e substituir uma unidade de disco rígido com defeito ou com falha num dispositivo do Microsoft Azure StorSimple. Para substituir uma unidade de disco, terá de:

* Se desligue o bloqueio antitamper
* Remover a unidade de disco
* Instalar a unidade de disco de substituição

> [!IMPORTANT]
> Antes de remover e substituir uma unidade de disco, reveja as informações de segurança na [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Se desligue o bloqueio antitamper
Este procedimento explica como os bloqueios antitamper no dispositivo StorSimple podem ser comprometidos ou disengaged quando substituir as unidades de disco. Os bloqueios antitamper equipados nos identificadores de operadora de unidade e estes são acedidos através de uma pequeno abertura na secção de bloqueio temporário do identificador. Unidades são fornecidas com os bloqueios definidos como a posição bloqueada.

#### <a name="to-unlock-the-antitamper-lock"></a>Para desbloquear o bloqueio antitamper
1. Inserir cuidadosamente a chave de bloqueio (um screwdriver "à" T10 prova de violações que a Microsoft forneceu) para a abertura do identificador e em seu socket. 
   
   Se o bloqueio antitamper estiver ativado, o indicador de vermelho está visível na abertura.
  
    ![Unidade de disco bloqueada](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Figura 1** bloqueio de adulterações anti envolvido
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |Abertura de indicador |
   | 2 |Bloqueio antitamper |
2. Rotação da chave numa direção anticlockwise até que o indicador de vermelho não estiver visível na abertura acima a chave.
3. Remova a chave.
   
    ![Unidade de disco desbloqueada](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Figura 2** Unlocked unidade de disco
4. Agora pode ser removida da unidade de disco.

Siga os passos no sentido contrário para interagir com o bloqueio.

## <a name="remove-the-disk-drive"></a>Remover a unidade de disco
O dispositivo StorSimple suporta uma configuração de espaços de armazenamento semelhantes a 10 de RAID. Isso implica que pode funcionar normalmente com um disco com falha, a unidade de estado sólido (SSD), ou unidade de disco rígido (HDD).

> [!IMPORTANT]
> * Se o seu sistema tem mais do que um disco com falha, não remova mais de um SSD ou HDD do sistema em qualquer ponto no tempo. Fazer isso pode resultar na perda de dados.
> * Certifique-se de que coloque uma substituição SSD num bloco que continha anteriormente uma SSD. Da mesma forma, coloque uma substituição HDD num bloco que continha um HDD anteriormente.
> * No portal do Azure, os blocos são numerados de 0 – 11. Por conseguinte, se o portal mostra que um disco na ranhura 2 falhou, no dispositivo, procure o disco com falha na ranhura de terceiro da parte superior esquerda.
> 
> 

Unidades podem ser removidas e substituídas enquanto o sistema está a funcionar.

#### <a name="to-remove-a-drive"></a>Para remover uma unidade
1. Para identificar o disco com falha, no portal do Azure, aceda ao seu dispositivo **definições > Estado de funcionamento do Hardware**. Uma vez que um disco pode falhar no bastidor principal e/ou um bastidor EBOD (se estiver a utilizar um modelo 8600), ver o estado dos discos sob **componentes partilhados** e, em **componentes partilhados de EBOD**. Um disco com falha em qualquer um dos bastidor será apresentado com o estado vermelho.
2. Localize as unidades no início o bastidor principal ou o bastidor EBOD. 
3. Se o disco está desbloqueado, avance para o passo seguinte. Se o disco estiver bloqueado, desbloqueá-la ao seguir o procedimento [se desligue o bloqueio antitamper](#disengage-the-antitamper-lock).
4. Prima o bloqueio temporário preto sobre o módulo de deteção de carrier da unidade e extrair o identificador de operadora de unidade para fora e fora da frente do chassi.
   
    ![Lançar o identificador de unidade de disco](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Figura 3** lançar o identificador de unidade
5. Quando o identificador de operadora de unidade totalmente é expandido, faça deslize a operadora de unidade fora do chassi. 
   
    ![Disco deslizante fora de unidade de disco](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Figura 4** deslizante a unidade de disco fora da operadora

## <a name="install-the-replacement-disk-drive"></a>Instalar a unidade de disco de substituição
Depois de uma unidade de mensagens falhou no dispositivo StorSimple e removeu-lo, siga este procedimento para substituí-lo com uma nova unidade.

#### <a name="to-insert-a-drive"></a>Para inserir uma unidade
1. Certifique-se que o identificador de operadora de unidade está totalmente estendido, conforme mostrado na imagem seguinte.
   
    ![Unidade de disco com o identificador expandido](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Figura 5** unidade com o identificador expandido
2. Todo o caminho para os chassis, faça deslize a operadora de unidade.
   
    ![Disco deslizante para deteção de carrier da unidade de disco](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Figura 6** deslizante a operadora de unidade para os chassis
3. Com a operadora de unidade inserida, feche o identificador de operadora de unidade e continuar a enviar por push a operadora de unidade para os chassis, até que o identificador de operadora de unidade ajusta-se numa posição bloqueada.
4. Utilize a chave de bloqueio que foi fornecida pela Microsoft (à prova de violações Torx screwdriver) para proteger o identificador de operadora em lugar ao ativar o screw de bloqueio mão de um trimestre para a direita.
5. Certifique-se de que a substituição foi concluída com êxito e a unidade está operacional. Aceder ao portal do Azure e navegue para **definições do dispositivo** > **estado de funcionamento do Hardware**. Sob **componentes partilhados** ou **componentes partilhados de EBOD**, o estado da unidade deve estar verde, que indica que está em bom estado.

   
   > [!NOTE]
   > Pode demorar várias horas para que o estado do disco ativar a verde após a substituição.
  
## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

