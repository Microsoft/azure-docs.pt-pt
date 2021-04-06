---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f08a6b3f7abfc79bff6baff2a339053905612535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026034"
---
#### <a name="to-attach-the-sas-cables"></a>Para fixar os cabos SAS
1. Identifique os recintos primários e ebod. Os dois recintos podem ser identificados olhando para os respetivos planos traseiros. Consulte a seguinte imagem para obter orientação. 
   
    ![Plano traseiro dos recintos primários e EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Vista traseira dos recintos primários e EBOD**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Recinto primário |
   | 2 |Recinto EBOD |
2. Localize os números de série nas caixas primárias e EBOD. O autocolante do número de série é afixado na orelha traseira de cada caixa. Os números de série devem ser idênticos em ambos os recintos. [Contacte](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) imediatamente o Microsoft Support se os números de série não corresponderem. Consulte a seguinte ilustração para localizar os números de série.
   
    ![Vista traseira do recinto mostrando o número de série](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Localização do autocolante do número de série**
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |Orelha do recinto |
3. Utilize os cabos SAS fornecidos para ligar o recinto EBOD ao recinto primário da seguinte forma:
   
   1. Identifique as quatro portas SAS no recinto primário e no recinto da EBOD. As portas SAS são rotuladas como EBOD no recinto primário e correspondem à porta A no recinto EBOD, como mostra a ilustração de cablagem SAS, abaixo.
   2. Utilize os cabos SAS fornecidos para ligar a porta EBOD à porta A.
   3. A porta EBOD no controlador 0 deve ser ligada à porta A no controlador EBOD 0. A porta EBOD do controlador 1 deve ser ligada à porta A no controlador EBOD 1. Consulte a seguinte ilustração para obter orientação. 
      
      ![Cabo SAS para o seu dispositivo](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Cablagem SAS**
      
      | Etiqueta | Description |
      |:--- |:--- |
      | A |Recinto primário |
      | B |Recinto EBOD |
      | 1 |Controlador 0 |
      | 2 |Controlador 1 |
      | 3 |Controlador EBOD 0 |
      | 4 |Controlador EBOD 1 |
      | 5, 6 |Portas SAS no recinto primário (etiquetada EBOD) |
      | 7, 8 |Portas SAS no recinto EBOD (Porto A) |