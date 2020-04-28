---
title: Guia de resolução de problemas para streaming ao vivo [ Microsoft Docs
description: Este artigo dá sugestões sobre como resolver problemas de streaming da Azure Media Services.
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
ms.openlocfilehash: b00df5e8176aaad86be5cf3ae4e04c736f36cf5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74885607"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de resolução de problemas de transmissão em fluxo em direto  

Este artigo dá sugestões sobre como resolver alguns problemas de streaming ao vivo.

## <a name="issues-related-to-on-premises-encoders"></a>Questões relacionadas com codificadores no local
Esta secção dá sugestões sobre como resolver problemas relacionados com codificadores no local que são configurados para enviar um único fluxo bitrate para canais AMS que estão habilitados para codificação ao vivo.

### <a name="problem-would-like-to-see-logs"></a>Problema: Gostaria de ver registos
* **Problema potencial**: Não consigo encontrar registos codificadores que possam ajudar a depurar problemas.
  
  * **Telestream Wirecast**: Normalmente pode encontrar registos em C:\User\{username}\AppData\Roaming\Wirecast\ 
  * **Elemental Live**: Você pode encontrar links para logs no portal de gestão. Clique em **Estatísticas,** em **seguida, Logs**. Na página **Registos,** verá uma lista de registos para todos os itens liveEvent; selecione o que corresponde à sua sessão atual. 
  * **Flash Media Live Encoder:** Pode encontrar o Diretório de **Registo...** navegando para o **separador DeScodificação.**

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: Não há opção para eliminar um fluxo progressivo
* **Problema potencial**: O codificador utilizado não desliga automaticamente. 
  
    **Passos de resolução**de problemas : Procure uma opção de desalocação dentro da interface codificadora. Uma vez ativada a desligação, verifique novamente se há definições de saída progressivas. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Experimentei várias definições de saída do codificador e ainda não conseguiu ligar.
* **Problema potencial**: O canal de codificação Azure não foi devidamente reposto. 
  
    **Passos de resolução de problemas:** Certifique-se de que o codificador já não está a empurrar para a AMS, pare e reinstale o canal. Uma vez em execução novamente, tente ligar o seu codificador às novas definições. Se isto ainda não corrigir a questão, tente criar um novo canal inteiramente, por vezes os canais podem tornar-se corruptos após várias tentativas falhadas.  
* **Problema potencial**: As definições de tamanho GOP ou de quadro-chave não são ótimas. 
  
    **Passos de resolução de problemas**: O tamanho recomendado do GOP ou o intervalo do quadro de chaves é de dois segundos. Alguns codificadores calculam esta definição em número de quadros, enquanto outros usam segundos. Por exemplo: Ao distribuir 30 fps, o tamanho gop seria de 60 fotogramas, o que equivale a 2 segundos.  
* **Problema potencial**: As portas fechadas estão a bloquear o fluxo. 
  
    **Passos de resolução**de problemas : Ao transmitir através de RTMP, verifique as definições de firewall e/ou proxy para confirmar que as portas de saída 1935 e 1936 estão abertas. 

> [!NOTE]
> Se depois de seguir os passos de resolução de problemas, ainda não consegue transmitir com sucesso, envie um bilhete de apoio utilizando o portal Azure.
> 
> 

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

