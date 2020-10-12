---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184295"
---
#### <a name="to-cable-your-device-for-power"></a>Para cabo o seu dispositivo para obter energia
> [!NOTE]
> Ambos os recintos do seu dispositivo StorSimple incluem PCMs redundantes. Para cada caixa, os PCMs devem ser instalados e ligados a diferentes fontes de energia para garantir uma elevada disponibilidade.
> 
> 

1. Certifique-se de que os interruptores de alimentação ligados a todos os PCMs estão na posição OFF.
2. No compartimento primário, ligue os cabos de alimentação a ambos os PCMs. Os cabos de alimentação são identificados a vermelho no diagrama de cablagem de potência, abaixo.
3. Certifique-se de que os dois PCMs no compartimento primário utilizam fontes de alimentação separadas.
4. Fixe os cabos de alimentação à alimentação das unidades de distribuição da cremalheira, tal como indicado no diagrama de cablagem de potência.
5. Repita os passos 2 a 4 para o recinto EBOD.
6. Ligue o compartimento EBOD ligando o interruptor de alimentação de cada PCM para a posição ON.
7. Verifique se o invólucro EBOD está ligado, verificando se os LED verdes na parte de trás do controlador EBOD estão ligados.
8. Ligue o compartimento primário rodando cada interruptor PCM para a posição ON.
9. Verifique se o sistema está ligado, certificando-se de que os LED do controlador do dispositivo estão ligados.
10. Certifique-se de que a ligação entre o controlador EBOD e o controlador do dispositivo está ativa, verificando se os quatro LEDs junto à porta SAS do controlador EBOD são verdes.
    
    > [!IMPORTANT]
    > Para garantir uma elevada disponibilidade para o seu sistema, recomendamos que cumpra rigorosamente o esquema de cablagem de alimentação indicado no diagrama seguinte.
    > 
    > 
    
    ![Envie o seu dispositivo 4U para alimentação](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Cablagem de energia**
    
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
    | 9 |PDUs |

