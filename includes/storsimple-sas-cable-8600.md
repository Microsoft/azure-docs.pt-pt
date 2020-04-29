---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184293"
---
#### <a name="to-attach-the-sas-cables"></a>Para fixar os cabos SAS
1. Identifique os recintos primários e ebod. Os dois recintos podem ser identificados olhando para os respetivos aviões traseiros. Consulte a seguinte imagem para obter orientação. 
   
    ![Plano de volta dos recintos primários e EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Vista traseira dos recintos primários e EBOD**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Recinto primário |
   | 2 |Recinto eBOD |
2. Localize os números de série nos recintos primários e ebod. O autocolante do número de série é afixado no ouvido traseiro de cada recinto. Os números de série devem ser idênticos em ambos os recintos. [Contacte](../articles/storsimple/storsimple-contact-microsoft-support.md) imediatamente o Microsoft Support se os números de série não corresponderem. Consulte a seguinte ilustração para localizar os números de série.
   
    ![Vista traseira do recinto mostrando número de série](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Localização do adesivo de número de série**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Orelha do recinto |
3. Utilize os cabos SAS fornecidos para ligar o recinto EBOD ao recinto primário da seguinte forma:
   
   1. Identifique as quatro portas SAS no recinto primário e no recinto EBOD. As portas SAS são rotuladas como EBOD no recinto primário e correspondem à porta A no recinto EBOD, como mostra a ilustração de cablagem SAS, abaixo.
   2. Utilize os cabos SAS fornecidos para ligar a porta EBOD à porta A.
   3. A porta EBOD do controlador 0 deve ser ligada à porta A do controlador EBOD 0. A porta EBOD do controlador 1 deve ser ligada à porta A do controlador EBOD 1. Consulte a seguinte ilustração para obter orientação. 
      
      ![Cabo SAS para o seu dispositivo](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Cabo SAS**
      
      | Etiqueta | Descrição |
      |:--- |:--- |
      | A |Recinto primário |
      | B |Recinto eBOD |
      | 1 |Controlador 0 |
      | 2 |Controlador 1 |
      | 3 |Controlador EBOD 0 |
      | 4 |Controlador EBOD 1 |
      | 5, 6 |Portas SAS no recinto primário (eBOD com etiqueta) |
      | 7, 8 |Portas SAS no recinto eBOD (Porta A) |

