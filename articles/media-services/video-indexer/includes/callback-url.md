---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994503"
---
Um URL que é usado para notificar o cliente (usando um pedido POST) sobre os seguintes eventos:

- Alteração do estado de indexação: 
    - Propriedades:    
    
        |Nome|Descrição|
        |---|---|
        |ID|A iD de vídeo|
        |state|O estado do vídeo|  
    - Exemplo: https: \/ /test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processado
- Pessoa identificada em vídeo:
  - Propriedades
    
      |Nome|Descrição|
      |---|---|
      |ID| A iD de vídeo|
      |faceId|O ID do rosto que aparece no índice de vídeo|
      |conhecidoPersonId|A pessoa ID que é única dentro de um modelo de rosto|
      |nome de pessoa|O nome da pessoa|
        
    - Exemplo: https: \/ /test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&conhecidoPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 
