---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155899"
---
#### <a name="to-cable-your-device-for-power"></a>Para instalar os cabos do dispositivo para poder
> [!NOTE]
> Ambas as caixas no dispositivo StorSimple incluem PCMs redundantes. Para cada bastidor, os PCMs tem de ser instalados e ligados a diferentes origens de dados para garantir a elevada disponibilidade.
> 
> 

1. Certifique-se de que as opções de energia em todo os PCMs são na posição OFF.
2. Na caixa primária, ligar os cabos de energia para ambos os PCMs. Os cabos de energia são identificados em vermelho no diagrama de cablagem de energia, abaixo.
3. Certifique-se de que os dois PCMs no bastidor principal utilizam origens de dados separado.
4. Anexe os cabos de energia para o poder das unidades de distribuição de rack como mostra o poder de cablagem diagrama.
5. Repita os passos 2 a 4 para a inclusão EBOD.
6. Ative o bastidor EBOD ao acionar a chave de energia em cada PCM para a posição de ON.
7. Certifique-se de que o bastidor EBOD é ativado ao selecionar a que o LEDs verde na parte de trás do controlador de EBOD são ativadas.
8. Ative o bastidor principal por inversão cada comutador PCM para a posição de ON.
9. Certifique-se de que o sistema é ao garantir que o controlador de dispositivo que LEDs têm ativada.
10. Certifique-se de que a ligação entre o controlador EBOD e o controlador de dispositivo está ativa, verificando que o quatro LEDs junto a porta SAS no controlador de EBOD são verdes.
    
    > [!IMPORTANT]
    > Para garantir a elevada disponibilidade para o seu sistema, é recomendável que adira estritamente à potência cablagem esquema mostrada no diagrama seguinte.
    > 
    > 
    
    ![Instalar os cabos do dispositivo 4U para power](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Cablagem de energia**
    
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
    | 9 |PDUs |

