---
title: Substitua um controlador StorSimple 8600 EBOD Microsoft Docs
description: Explica como remover e substituir um ou ambos os controladores EBOD num dispositivo StorSimple 8600.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: bdafb4288bf818928aba43d09f15d4dd75092b24
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014844"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Substitua um controlador EBOD no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como substituir um módulo de controlador EBOD defeituoso no seu dispositivo Microsoft Azure StorSimple. Para substituir um módulo de controlador EBOD, é necessário:

* Remova o controlador EBOD defeituoso
* Instale um novo controlador EBOD

Considere as seguintes informações antes de começar:

* Os módulos EBOD em branco devem ser inseridos em todas as ranhuras não ureadas. O invólucro não arrefece corretamente se uma ranhura for deixada aberta.
* O controlador EBOD é permutável a quente e pode ser removido ou substituído. Não remova um módulo defeituoso até ter uma substituição. Quando iniciar o processo de substituição, deve terminá-lo dentro de 10 minutos.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente StorSimple, certifique-se de que revê as [convenções](storsimple-8000-safety.md#safety-icon-conventions) de ícones de segurança e [outras precauções de segurança](storsimple-8000-safety.md).

## <a name="remove-an-ebod-controller"></a>Remover um controlador EBOD
Antes de substituir o módulo de controlador EBOD falhado no seu dispositivo StorSimple, certifique-se de que o outro módulo do controlador EBOD está ativo e em funcionamento. O procedimento e tabela seguintes explicam como remover o módulo controlador EBOD.

#### <a name="to-remove-an-ebod-module"></a>Para remover um módulo EBOD
1. Abra o portal do Azure.
2. Vá ao seu dispositivo e navegue para **a saúde** do hardware de definições  >  **Hardware health**, e verifique se o estado do LED para o módulo controlador EBOD ativo é verde e o LED para o módulo controlador EBOD falhado é vermelho.
3. Localize o módulo de controlador EBOD falhado na parte de trás do dispositivo.
4. Retire os cabos que ligam o módulo do controlador EBOD ao controlador antes de retirar o módulo EBOD do sistema.
5. Tome nota da porta SAS exata do módulo controlador EBOD que foi ligado ao controlador. Será necessário restaurar o sistema a esta configuração depois de substituir o módulo EBOD.
   
   > [!NOTE]
   > Tipicamente, este será o Porto A, que é rotulado como **Anfitrião no** diagrama seguinte.
   
    ![Backplane do controlador EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Figura 1** Parte de trás do módulo EBOD
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |LED de avaria |
   | 2 |LED de potência |
   | 3 |Conectores SAS |
   | 4 |SAS LEDs |
   | 5 |Portas em série apenas para uso de fábrica |
   | 6 |Porto A (Anfitrião em) |
   | 7 |Porta B (Anfitrião fora) |
   | 8 |Porto C (apenas utilização da fábrica) |

## <a name="install-a-new-ebod-controller"></a>Instale um novo controlador EBOD
O procedimento e a tabela seguintes explicam como instalar um módulo controlador EBOD no seu dispositivo StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Para instalar um controlador EBOD
1. Verifique se o dispositivo EBOD está danificado, especialmente no conector de interface. Não instale o novo controlador EBOD se houver pinos dobrados.
2. Com os fechos na posição aberta, deslize o módulo para dentro do compartimento até os fechos se engatarem.
   
    ![Instalação do controlador EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Figura 2**  Instalação do módulo de controlador EBOD
3. Feche o trinco. Deve ouvir um clique à medida que o trinco se ativa.
   
    ![Libertação do trinco EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Figura 3**  Fecho do fecho do módulo EBOD
4. Voltar a ligar os cabos. Utilize a configuração exata que estava presente antes da substituição. Consulte o diagrama e tabela a seguir para obter mais informações sobre como ligar os cabos.
   
    ![Envie o seu dispositivo 4U para alimentação](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Figura 4**. Cabos de religação
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Recinto primário |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Controlador 0 |
   | 5 |Controlador 1 |
   | 6 |Controlador EBOD 0 |
   | 7 |Controlador EBOD 1 |
   | 8 |Recinto EBOD |
   | 9 |Unidades de Distribuição de Energia |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

