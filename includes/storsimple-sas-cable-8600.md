---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155906"
---
#### <a name="to-attach-the-sas-cables"></a>Para anexar os cabos SAS
1. Identifique a primária e as inclusões EBOD. As dois inclusões podem ser identificadas ao observar a seus respectivos planos de back. Veja a imagem seguinte para obter orientações. 
   
    ![Fazer uma cópia de plano de principal e as inclusões EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Criar uma vista de principal e as inclusões EBOD**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Bastidor principal |
   | 2 |Bastidor EBOD |
2. Localize os números de série no principal e as inclusões EBOD. O número de série autocolante é afixação o back-esmarcar de cada bastidor. Os números de série tem de ser idênticos em ambas as caixas. [Contacte o Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) imediatamente se os números de série não coincidem. Ver a ilustração a seguir para localizar os números de série.
   
    ![Vista de trás do que mostra o número de série de bastidor](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Localização do número de série autocolante**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Na sabedoria do bastidor |
3. Utilize os cabos SAS fornecidos para ligar o bastidor EBOD ao bastidor principal da seguinte forma:
   
   1. Identifique as quatro portas SAS no bastidor principal e a inclusão EBOD. As portas SAS estão identificadas como EBOD no bastidor principal e correspondem a uma porta na caixa EBOD, como mostra a SAS cablagem ilustração, abaixo.
   2. Utilize os cabos SAS fornecidos para ligar a porta a porta EBOD A.
   3. A porta EBOD no controlador 0 deve estar ligada a uma porta no controlador EBOD 0. A porta EBOD no controlador 1 deve estar ligada a uma porta no controlador EBOD 1. Ver a ilustração a seguir para obter orientações. 
      
      ![SAS cablagem para o seu dispositivo](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Cablagem de SAS**
      
      | Etiqueta | Descrição |
      |:--- |:--- |
      | A |Bastidor principal |
      | B |Bastidor EBOD |
      | 1 |Controlador 0 |
      | 2 |Controlador 1 |
      | 3 |Controlador 0 de EBOD |
      | 4 |1 o controlador de EBOD |
      | 5, 6 |Portas SAS no bastidor principal (etiquetada EBOD) |
      | 7, 8 |Portas SAS no bastidor EBOD (A porta) |

