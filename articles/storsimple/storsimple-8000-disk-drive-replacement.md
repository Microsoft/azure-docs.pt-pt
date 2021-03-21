---
title: Substitua uma unidade de disco num dispositivo da série StorSimple 8000 | Microsoft Docs
description: Explica como substituir uma unidade de disco num compartimento primário StorSimple ou num compartimento EBOD.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014878"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Substituir uma unidade de disco no seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
Este tutorial explica como pode remover e substituir uma unidade de disco rígido avariada ou falhada num dispositivo Microsoft Azure StorSimple. Para substituir uma unidade de disco, é necessário:

* Desengatar a fechadura antitamper
* Remover a unidade do disco
* Instale a unidade de disco de substituição

> [!IMPORTANT]
> Antes de remover e substituir uma unidade de disco, reveja as informações de segurança na [substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Desengatar a fechadura antitamper
Este procedimento explica como as fechaduras antitamper no seu dispositivo StorSimple podem ser ativadas ou desativadas quando substitui as unidades do disco. As fechaduras antitamper são instaladas nas pegas do porta-accionamento e são acedidas através de uma pequena abertura na secção do fecho da pega. As unidades são fornecidas com as fechaduras definidas na posição de bloqueio.

#### <a name="to-unlock-the-antitamper-lock"></a>Para desbloquear a fechadura anti-primeiro-estar
1. Insira cuidadosamente a chave de bloqueio (uma chave de fendas T10 "à prova de adulteração" que a Microsoft forneceu) na abertura da pega e na sua tomada. 
   
   Se o bloqueio anti-corrente for ativado, o indicador vermelho fica visível na abertura.
  
    ![Unidade de disco bloqueada](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Figura 1** Fechadura anti-adulteração ativada
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Abertura indicadora |
   | 2 |Fechadura antitamper |
2. Rode a tecla no sentido contrário ao dos ponteiros do relógio até que o indicador vermelho não esteja visível na abertura acima da tecla.
3. Retire a chave.
   
    ![Unidade de disco desbloqueada](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Figura 2** Unidade de disco desbloqueada
4. A unidade do disco pode agora ser removida.

Siga os passos ao contrário para engatar a fechadura.

## <a name="remove-the-disk-drive"></a>Remover a unidade do disco
O seu dispositivo StorSimple suporta uma configuração de espaços de armazenamento RAID de 10. Isto implica que pode funcionar normalmente com um disco falhado, unidade de estado sólido (SSD) ou disco rígido (HDD).

> [!IMPORTANT]
> * Se o seu sistema tiver mais do que um disco falhado, não remova mais do que um SSD ou HDD do sistema em qualquer momento. Fazê-lo pode resultar na perda de dados.
> * Certifique-se de que coloca um SSD de substituição numa ranhura que previamente continha um SSD. Da mesma forma, coloque um HDD de substituição numa ranhura que previamente continha um HDD.
> * No portal Azure, as faixas horárias são numeradas de 0 a 11. Portanto, se o portal mostrar que um disco na ranhura 2 falhou, no dispositivo, procure o disco falhado na terceira ranhura a partir da parte superior esquerda.
> 
> 

As unidades podem ser removidas e substituídas enquanto o sistema estiver a funcionar.

#### <a name="to-remove-a-drive"></a>Para remover uma unidade
1. Para identificar o disco falhado, no portal Azure, aceda ao seu dispositivo **Definições > saúde do hardware**. Como um disco pode falhar no recinto primário e/ou num recinto EBOD (se estiver a utilizar um modelo 8600), veja o estado dos discos em **componentes partilhados** e em **componentes partilhados da EBOD.** Um disco falhado em qualquer dos recintos será mostrado com um estado vermelho.
2. Localize as unidades na parte da frente do recinto primário ou no recinto EBOD. 
3. Se o disco estiver desbloqueado, proceda ao passo seguinte. Se o disco estiver bloqueado, desbloqueie-o seguindo o procedimento de [desengatar o bloqueio anti-axaduto](#disengage-the-antitamper-lock).
4. Pressione o trinco preto no módulo do porta-accionamento e puxe a pega do porta-accionamento para fora e para longe da frente do chassis.
   
    ![Libertação do cabo de unidade de disco](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Figura 3** Libertação da pega de acionamento
5. Quando a pega do porta-accionamento estiver completamente estendida, deslize o porta-accionamento para fora do chassis. 
   
    ![Disco deslizante para fora da unidade do disco](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Figura 4** Deslizando a unidade de disco para fora do porta-aviões

## <a name="install-the-replacement-disk-drive"></a>Instale a unidade de disco de substituição
Depois de uma unidade ter falhado no seu dispositivo StorSimple e removê-la, siga este procedimento para substituí-lo por uma nova unidade.

#### <a name="to-insert-a-drive"></a>Para inserir uma unidade
1. Certifique-se de que a pega do porta-accionamento está completamente estendida, como mostra a seguinte imagem.
   
    ![Unidade de disco com pega estendida](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Figura 5** Unidade com alça estendida
2. Deslize o porta-accionamento até ao chassis.
   
    ![Disco deslizante no porta-discos de disco](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Figura 6**  Deslizando o porta-unidades para o chassis
3. Com a porta-accionadora inserida, feche a pega do porta-accionamento enquanto continua a empurrar o porta-accionamento para dentro do chassis, até que o manípulo do porta-accionamento se encaixe numa posição bloqueada.
4. Utilize a chave de bloqueio fornecida pela Microsoft (chave de fendas Torx à prova de violação) para fixar a pega da transportadora no lugar, rodando o parafuso de bloqueio num quarto de curva no sentido dos ponteiros do relógio.
5. Verifique se a substituição foi bem sucedida e que a unidade está operacional. Aceda ao portal Azure e navegue para **definições de dispositivo**  >  **saúde hardware**. Nos **componentes partilhados** ou **nos componentes partilhados da EBOD,** o estado da unidade deve ser verde, indicando que é saudável.

   
   > [!NOTE]
   > Pode levar várias horas para que o estado do disco fique verde após a substituição.
  
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

