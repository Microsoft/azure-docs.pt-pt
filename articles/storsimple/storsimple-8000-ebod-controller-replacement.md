---
title: Substitua um controlador StorSimple 8600 EBOD [ StorSimple 8600 EBOD] Microsoft Docs
description: Explica como remover e substituir um ou ambos os controladores EBOD num dispositivo StorSimple 8600.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254888"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Substitua um controlador EBOD no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como substituir um módulo de controlador EBOD defeituoso no seu dispositivo Microsoft Azure StorSimple. Para substituir um módulo de controlador EBOD, é necessário:

* Remova o controlador EBOD defeituoso
* Instale um novo controlador EBOD

Considere as seguintes informações antes de começar:

* Os módulos EBOD em branco devem ser inseridos em todas as ranhuras não utilizadas. O recinto não arrefece corretamente se uma ranhura for deixada aberta.
* O controlador EBOD é permutável a quente e pode ser removido ou substituído. Não retire um módulo falhado até ter uma substituição. Quando iniciar o processo de substituição, deve terminá-lo dentro de 10 minutos.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente StorSimple, certifique-se de que revê as [convenções](storsimple-safety.md#safety-icon-conventions) do ícone de segurança e [outras precauções de segurança](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Remova um controlador EBOD
Antes de substituir o módulo de controlador EBOD falhado no seu dispositivo StorSimple, certifique-se de que o outro módulo de controlador EBOD está ativo e em funcionamento. O procedimento e a tabela que se aseguir explicam como remover o módulo do controlador EBOD.

#### <a name="to-remove-an-ebod-module"></a>Para remover um módulo EBOD
1. Abra o portal do Azure.
2. Vá ao seu dispositivo e navegue para a saúde do**hardware**de **Definições,** > e verifique se o estado do LED para o módulo de controlador EBOD ativo é verde e o LED para o módulo de controlador EBOD falhado é vermelho.
3. Localize o módulo de controlador EBOD falhado na parte de trás do dispositivo.
4. Retire os cabos que ligam o módulo do controlador EBOD ao controlador antes de retirar o módulo EBOD do sistema.
5. Tome nota da porta Exata SAS do módulo de comando EBOD que estava ligada ao controlador. Será necessário restaurar o sistema nesta configuração depois de substituir o módulo EBOD.
   
   > [!NOTE]
   > Tipicamente, este será o Porto A, que é rotulado como **Anfitrião** no diagrama seguinte.
   
    ![Backplane do controlador EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Figura 1** Parte de trás do módulo EBOD
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |LED de falha |
   | 2 |LED de alimentação |
   | 3 |Conectores SAS |
   | 4 |SAS LEDs |
   | 5 |Portas em série para uso de fábrica apenas |
   | 6 |Porto A (Anfitrião em) |
   | 7 |Porta B (Anfitrião fora) |
   | 8 |Porta C (apenas utilização da fábrica) |

## <a name="install-a-new-ebod-controller"></a>Instale um novo controlador EBOD
O procedimento e a tabela que se aseguir explicam como instalar um módulo de controlador EBOD no seu dispositivo StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Para instalar um controlador EBOD
1. Verifique se o dispositivo EBOD está danificado, especialmente no conector da interface. Não instale o novo controlador EBOD se os pinos estiverem dobrados.
2. Com os fechos na posição aberta, deslize o módulo para dentro do compartimento até que os fechos se encaixem.
   
    ![Instalação do controlador EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Figura 2**  Instalação do módulo de controlador EBOD
3. Feche o trinco. Deve ouvir um clique enquanto o trinco se engaça.
   
    ![Liberação do trinco EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Figura 3**  Fechar o trinco do módulo EBOD
4. Religue os cabos. Utilize a configuração exata que estava presente antes da substituição. Consulte o diagrama e a tabela que se aseguir procuram mais detalhes sobre como ligar os cabos.
   
    ![Cabo o seu dispositivo 4U para energia](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Figura 4**. Redeligando cabos
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Recinto primário |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Controlador 0 |
   | 5 |Controlador 1 |
   | 6 |Controlador EBOD 0 |
   | 7 |Controlador EBOD 1 |
   | 8 |Recinto eBOD |
   | 9 |Unidades de Distribuição de Energia |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

