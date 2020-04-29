---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184295"
---
#### <a name="to-cable-your-device-for-power"></a>Para cabo o seu dispositivo para energia
> [!NOTE]
> Ambos os recintos do seu dispositivo StorSimple incluem PCMs redundantes. Para cada recinto, os PCMs devem ser instalados e ligados a diferentes fontes de energia para garantir uma elevada disponibilidade.
> 
> 

1. Certifique-se de que os interruptores de alimentação de todos os PCMs estão na posição OFF.
2. No recinto primário, ligue os cabos de alimentação a ambos os PCMs. Os cabos de alimentação são identificados a vermelho no diagrama de cablagem de energia, abaixo.
3. Certifique-se de que os dois PCMs do recinto primário utilizam fontes de alimentação separadas.
4. Fixe os cabos de alimentação à potência das unidades de distribuição do rack, tal como mostrado no diagrama de cablagem elétrica.
5. Repita os passos 2 a 4 para o recinto ebod.
6. Ligue o recinto EBOD ligando o interruptor de alimentação de cada PCM para a posição ON.
7. Verifique se o recinto ebod está ligado verificando se os LEDs verdes na parte de trás do controlador EBOD estão ligados.
8. Ligue o recinto primário, virando cada interruptor PCM para a posição ON.
9. Verifique se o sistema está ligado, certificando-se de que os LEDs do controlador do dispositivo ligaram.
10. Certifique-se de que a ligação entre o controlador EBOD e o controlador do dispositivo está ativa, verificando se os quatro LEDs ao lado da porta SAS do controlador EBOD são verdes.
    
    > [!IMPORTANT]
    > Para garantir uma elevada disponibilidade para o seu sistema, recomendamos que cumpra estritamente o esquema de cablagem de energia mostrado no seguinte diagrama.
    > 
    > 
    
    ![Cabo o seu dispositivo 4U para energia](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Cabo de energia**
    
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
    | 9 |PDUs |

