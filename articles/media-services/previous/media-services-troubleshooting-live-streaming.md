---
title: Guia de resolução de problemas para a transmissão em fluxo em direto | Documentos da Microsoft
description: Este tópico fornece sugestões sobre como solucionar problemas de transmissão em fluxo em direto.
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
ms.openlocfilehash: f502e3228274840d23b9f52512280fc0d9f0553b
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309899"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de resolução de problemas de transmissão em fluxo em direto  

Este artigo fornece sugestões sobre como solucionar alguns problemas de transmissão em fluxo em direto.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados com codificadores no local
Esta secção fornece sugestões sobre como resolver problemas relacionados com codificadores no local que estão configurados para enviar um fluxo de velocidade de transmissão única para canais de AMS ativados para live encoding.

### <a name="problem-would-like-to-see-logs"></a>Problema: Gostaria de ver registos
* **Problema em potencial**: Não é possível localizar os registos de codificador que podem ajudar a depurar problemas.
  
  * **Telestream Wirecast**: Normalmente, pode encontrar registos em C:\Users\{username} \AppData\Roaming\Wirecast\ 
  * **Elementar ao vivo**: Pode encontrar tem ligações para os registos no portal de gestão. Clique em **estatísticas**, em seguida, **registos**. Sobre o **ficheiros de registo** página, verá uma lista de registos para todos os itens de LiveEvent; selecione a sua sessão atual de correspondência. 
  * **Codificador de multimédia em direto de Flash**: Pode encontrar o **diretório de registo...**  ao navegar para o **registo de codificação** separador.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: Não existe nenhuma opção para produzir um fluxo de transferência progressivo
* **Problema em potencial**: O codificador que está a ser utilizado automaticamente não deinterlace. 
  
    **Passos de resolução de problemas**: Procure uma opção de anular interlacing dentro da interface do codificador. Depois de anular entrelaçamento estiver ativado, verifique novamente para as definições de saída de transferência progressiva. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Tentei várias configurações de saída do codificador e ainda não é possível ligar.
* **Problema em potencial**: Canal de codificação do Azure não foi reposta corretamente. 
  
    **Passos de resolução de problemas**: Certifique-se de que o codificador já não é enviar por push para o AMS, parar e reiniciar o canal. Depois de executar novamente, tente ligar seu codificador com as novas definições. Se isso ainda não corrigir o problema, tente criar um novo canal inteiramente, às vezes, canais podem tornar-se danificado após várias tentativas falhadas.  
* **Problema em potencial**: As definições de quadro-chave ou o tamanho de GOP não estejam otimizadas. 
  
    **Passos de resolução de problemas**: Intervalo recomendado de tamanho ou quadro-chave de GOP é de dois segundos. Alguns codificadores calculam esta definição no número de quadros, enquanto outros utilizam segundos. Por exemplo: Na saída de 30 fps, o tamanho de GOP seria 60 quadros, que é equivalente a 2 segundos.  
* **Problema em potencial**: Fechado portas estão a bloquear o fluxo. 
  
    **Passos de resolução de problemas**: Quando a transmissão em fluxo através de RTMP, verifique as definições de firewall e/ou proxy para confirmar que as portas de saída 1935 e 1936 estão abertas. 

> [!NOTE]
> Se depois de seguir os passos de resolução de problemas que ainda não é possível com êxito do stream, submeta um pedido de suporte com o portal do Azure.
> 
> 

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

