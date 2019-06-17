---
title: Substituir um controlador de EBOD do StorSimple 8600 | Documentos da Microsoft
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578696"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Substituir um controlador EBOD no dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Este tutorial explica como substituir um módulo de controlador EBOD com falhas no seu dispositivo do Microsoft Azure StorSimple. Para substituir um módulo de controlador EBOD, terá de:

* Remover o controlador EBOD com falhas
* Instale um novo controlador EBOD

Antes de começar, considere as seguintes informações:

* Módulos EBOD em branco precisam ser inseridos em todas as ranhuras de não utilizadas. A inclusão não será legal corretamente se um bloco é deixado aberto.
* O controlador EBOD é fontes e podem ser removidas ou substituída. Não remova um módulo com falha até ter uma substituição. Quando inicia o processo de substituição, deverá ser concluída em 10 minutos.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente do StorSimple, certifique-se de que revê os [convenções do ícone de segurança](storsimple-safety.md#safety-icon-conventions) e outras [precauções de segurança](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Remover um controlador EBOD
Antes de substituir o módulo de controlador EBOD com falhas no dispositivo StorSimple, certifique-se de que o outro módulo do controlador EBOD está ativa e em execução. O procedimento e tabela seguintes explicam como remover o módulo de controlador EBOD.

#### <a name="to-remove-an-ebod-module"></a>Para remover um módulo EBOD
1. Abra o portal do Azure.
2. Vá para o seu dispositivo e navegue para **configurações** > **estado de funcionamento do Hardware**e certifique-se de que o estado do LED para o módulo de controlador EBOD Active Directory é verde e o LED para o controlador EBOD com falhas módulo é vermelho.
3. Localize o módulo de controlador EBOD falhado parte traseira desse dispositivo.
4. Remova os cabos que ligue o módulo de controlador EBOD para o controlador antes de fazer o módulo EBOD fora do sistema.
5. Tome nota da porta SAS exata do módulo de controlador EBOD foi ligado ao controlador. Será solicitado para restaurar o sistema para esta configuração depois de as substituir o módulo EBOD.
   
   > [!NOTE]
   > Normalmente, este será porta A, que é identificada como **hospedar em** no diagrama seguinte.
   
    ![Controlador de Backplane de EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Figura 1** módulo Back de EBOD
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |LED de falha |
   | 2 |Power LED |
   | 3 |Conectores SAS |
   | 4 |LEDs SAS |
   | 5 |Portas seriais para apenas a utilização de fábrica |
   | 6 |Porta (anfitrião no) |
   | 7 |Porta B (anfitrião de saída) |
   | 8 |Porta C (apenas para uso do Factory) |

## <a name="install-a-new-ebod-controller"></a>Instale um novo controlador EBOD
O procedimento e tabela seguintes explicam como instalar um módulo de controlador EBOD no dispositivo StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Para instalar um controlador EBOD
1. Verifique o dispositivo EBOD para danos, especialmente para o conector de interface. Não instale o novo controlador EBOD se, estão dobrados qualquer pins.
2. Com as travas na posição abrir, deslize o módulo o bastidor até que as travas interagir com.
   
    ![Instalar o controlador de EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Figura 2** instalar o módulo de controlador EBOD
3. Feche o bloqueio temporário. Deve ouvir um clique como bloqueio temporário é ativado.
   
    ![Liberar o bloqueio temporário EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Figura 3** fechar o travamento do módulo EBOD
4. Voltar a ligar os cabos. Utilize a configuração exata presentes antes da substituição. Consulte o seguinte diagrama e tabela para obter detalhes sobre como ligar os cabos.
   
    ![Instalar os cabos do dispositivo 4U para power](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Figura 4**. Restabelecer ligação cabos
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Bastidor principal |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Controlador 0 |
   | 5 |Controlador 1 |
   | 6 |Controlador 0 de EBOD |
   | 7 |1 o controlador de EBOD |
   | 8 |Bastidor EBOD |
   | 9 |Unidades de distribuição de energia |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

