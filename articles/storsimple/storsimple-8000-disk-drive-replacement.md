---
title: Substitua uma unidade de disco num dispositivo da série StorSimple 8000 [ StorSimple 8000] Microsoft Docs
description: Explica como substituir uma unidade de disco num recinto primário StorSimple ou num recinto EBOD.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79267914"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Substitua uma unidade de disco no seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
Este tutorial explica como pode remover e substituir uma unidade de disco rígido avariada ou falhada num dispositivo Microsoft Azure StorSimple. Para substituir uma unidade de disco, é necessário:

* Desengatar a fechadura anti-adulteração
* Retire a unidade do disco
* Instale a unidade de disco de substituição

> [!IMPORTANT]
> Antes de remover e substituir uma unidade de disco, reveja as informações de segurança na substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Desengatar a fechadura anti-adulteração
Este procedimento explica como as fechaduras antitamper no seu dispositivo StorSimple podem ser ativadas ou desativadas quando substituir as unidades do disco. As fechaduras anti-tamper estão montadas nas pegas do porta-mato e são acedidas através de uma pequena abertura na secção do trinco da pega. As unidades são fornecidas com as fechaduras colocadas na posição de bloqueio.

#### <a name="to-unlock-the-antitamper-lock"></a>Para desbloquear o bloqueio anti-adulteração
1. Insira cuidadosamente a chave de bloqueio (uma chave de fendas T10 "à prova de violação" que a Microsoft forneceu) na abertura da pega e na tomada. 
   
   Se o bloqueio anti-adulterador for ativado, o indicador vermelho é visível na abertura.
  
    ![Unidade de disco bloqueada](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Figura 1** Bloqueio anti-adulteração engatado
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Abertura indicadora |
   | 2 |Bloqueio anti-adulteração |
2. Rode a tecla no sentido anti-horário até que o indicador vermelho não seja visível na abertura acima da tecla.
3. Retire a chave.
   
    ![Unidade de disco desbloqueada](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Figura 2** Unidade de disco desbloqueada
4. A unidade do disco pode agora ser removida.

Siga os passos ao contrário para engatar a fechadura.

## <a name="remove-the-disk-drive"></a>Retire a unidade do disco
O seu dispositivo StorSimple suporta uma configuração de espaços de armazenamento semelhantes a RAID 10. Isto implica que pode funcionar normalmente com um disco falhado, unidade de estado sólido (SSD) ou disco rígido (HDD).

> [!IMPORTANT]
> * Se o seu sistema tiver mais de um disco falhado, não remova mais do que um SSD ou HDD do sistema em qualquer momento. Fazê-lo pode resultar na perda de dados.
> * Certifique-se de que coloca um SSD de substituição numa ranhura que previamente continha um SSD. Da mesma forma, coloque um HDD de substituição numa ranhura que previamente continha um HDD.
> * No portal Azure, as faixas horárias são numeradas de 0 a 11. Portanto, se o portal mostrar que um disco na ranhura 2 falhou, no dispositivo, procure o disco falhado na terceira ranhura a partir da parte superior esquerda.
> 
> 

As unidades podem ser removidas e substituídas enquanto o sistema estiver a funcionar.

#### <a name="to-remove-a-drive"></a>Para remover uma unidade
1. Para identificar o disco falhado, no portal Azure, vá ao seu dispositivo **Definições > Saúde**de Hardware . Como um disco pode falhar no recinto primário e/ou num recinto EBOD (se estiver a utilizar um modelo 8600), veja o estado dos discos sob **componentes partilhados** e sob **componentes partilhados da EBOD**. Um disco falhado em qualquer um dos recintos será mostrado com um estado vermelho.
2. Localize os discos na frente do recinto primário ou no recinto EBOD. 
3. Se o disco estiver desbloqueado, proceda ao próximo passo. Se o disco estiver bloqueado, desbloqueie-o seguindo o procedimento em [desativar a fechadura anti-tamper](#disengage-the-antitamper-lock).
4. Pressione o trinco preto no módulo do porta-baga e puxe o cabo do porta-baga para fora e para longe da frente do chassis.
   
    ![Liberando a pega de acionador do disco](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Figura 3** Libertando a pega de acionamento
5. Quando o cabo do porta-unidade estiver completamente estendido, deslize o porta-unidade para fora do chassis. 
   
    ![Disco deslizante fora da unidade do disco](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Figura 4** Deslizando a unidade do disco para fora do porta-aviões

## <a name="install-the-replacement-disk-drive"></a>Instale a unidade de disco de substituição
Depois de uma unidade ter falhado no seu dispositivo StorSimple e o tiver removido, siga este procedimento para substituí-lo por uma nova unidade.

#### <a name="to-insert-a-drive"></a>Para inserir uma unidade
1. Certifique-se de que o cabo do porta-unidade está completamente estendido, como mostra a seguinte imagem.
   
    ![Unidade de disco com pega estendida](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Figura 5** Unidade com pega estendida
2. Deslize o porta-unidade até ao chassis.
   
    ![Disco deslizante no porta-discos do disco](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Figura 6**  Deslizando o porta-unidade para dentro do chassis
3. Com o porta-malas inserido, feche o cabo do porta-unidades enquanto continua a empurrar o porta-unidade para dentro do chassis, até que o cabo do acionador encaixe numa posição bloqueada.
4. Utilize a chave de bloqueio fornecida pela Microsoft (chave de fendas Torx à prova de erros) para fixar a pega do porta-aviões no lugar, rodando o parafuso de bloqueio um quarto no sentido dos ponteiros do relógio.
5. Verifique se a substituição foi bem sucedida e que a unidade está operacional. Aceda ao portal Azure e navegue para **as definições do Dispositivo** > saúde de **hardware.** Em **componentes partilhados** ou **componentes partilhados EBOD,** o estado de unidade deve ser verde, indicando que é saudável.

   
   > [!NOTE]
   > Pode levar várias horas para que o estado do disco se vire verde após a substituição.
  
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

