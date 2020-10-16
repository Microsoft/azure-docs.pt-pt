---
title: Guia de resolução de problemas para transmissão em direto Microsoft Docs
description: Este artigo dá sugestões sobre como resolver problemas de problemas com o Azure Media Services em direto.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 97ad3048fccc4f8ed6baf0f1d5304f1c8d81c3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269238"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de resolução de problemas de transmissão em fluxo em direto

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Este artigo dá sugestões sobre como resolver alguns problemas de streaming ao vivo.

## <a name="issues-related-to-on-premises-encoders"></a>Questões relacionadas com codificadores no local
Esta secção dá sugestões sobre como resolver problemas relacionados com os codificadores no local que estão configurados para enviar um único fluxo de bitrate para canais AMS que estão habilitados para codificação ao vivo.

### <a name="problem-would-like-to-see-logs"></a>Problema: Gostaria de ver registos
* **Problema potencial**: Não é possível encontrar registos codificadores que possam ajudar a depurar problemas.
  
  * **Telestream Wirecast**: Normalmente pode encontrar registos em C:\Username}\AppData\Roaming\Wirecast\ \{ 
  * **Elemental Live**: Pode encontrar links para registos no portal de gestão. Clique em **Estatísticas**e, em seguida, **Regista**. Na página **'Ficheiros de Registo',** verá uma lista de registos de todos os itens LiveEvent; selecione a que corresponde à sua sessão atual. 
  * **Flash Media Live Encoder**: Pode encontrar o **Diretório de Registos...** navegando no **separador Registo de Codificação.**

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: Não há opção para a saída de um fluxo progressivo
* **Problema potencial**: O codificante que está a ser utilizado não desinterla automaticamente. 
  
    **Etapas de resolução de problemas**: Procure uma opção de desligar-se dentro da interface do codificante. Uma vez ativado o desligar, verifique novamente se existem definições progressivas de saída. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Experimente várias definições de saída do codificadores e ainda não conseguiu ligar-se.
* **Problema potencial**: O canal de codificação Azure não foi corretamente reposto. 
  
    **Etapas de resolução de problemas**: Certifique-se de que o codificadora já não está a empurrar para a AMS, pare e reinicie o canal. Uma vez em execução, tente ligar o codificadora às novas definições. Se isto ainda não corrigir a questão, tente criar um novo canal inteiramente, por vezes os canais podem tornar-se corruptos após várias tentativas falhadas.  
* **Problema potencial**: O tamanho do GOP ou as definições do quadro da chave não são ideais. 
  
    **Etapas de resolução de problemas**: O tamanho recomendado do GOL ou o intervalo do quadro de chaves são de dois segundos. Alguns codificadores calculam esta definição em número de quadros, enquanto outros usam segundos. Por exemplo: Ao desausar 30 fps, o tamanho do GOP seria de 60 fotogramas, o que equivale a 2 segundos.  
* **Problema potencial**: As portas fechadas estão a bloquear o fluxo. 
  
    **Etapas de resolução de problemas**: Quando transmitir através de RTMP, verifique as definições de firewall e/ou proxy para confirmar que as portas de saída de saída 1935 e 1936 estão abertas. 

> [!NOTE]
> Se depois de seguir as etapas de resolução de problemas ainda não conseguir transmitir com sucesso, envie um bilhete de apoio utilizando o portal Azure.
> 
> 

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

