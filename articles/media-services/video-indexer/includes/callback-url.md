---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
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
