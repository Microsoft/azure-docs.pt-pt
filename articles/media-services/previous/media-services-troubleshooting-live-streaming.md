---
title: Guia de solução de problemas para streaming ao vivo | Microsoft Docs
description: Este artigo fornece sugestões sobre como solucionar problemas de transmissão ao vivo dos serviços de mídia do Azure.
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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885607"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guia de resolução de problemas de transmissão em fluxo em direto  

Este artigo fornece sugestões sobre como solucionar alguns problemas de transmissão ao vivo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados a codificadores locais
Esta seção fornece sugestões sobre como solucionar problemas relacionados a codificadores locais que são configurados para enviar um fluxo de taxa de bits única para os canais do AMS que estão habilitados para codificação ativa.

### <a name="problem-would-like-to-see-logs"></a>Problema: deseja ver os logs
* **Possível problema**: não é possível encontrar logs do codificador que podem ajudar na depuração de problemas.
  
  * **Telestream Wirecast**: geralmente, você pode encontrar logs em C:\Users\{username} \AppData\Roaming\Wirecast\ 
  * **Elemento ao vivo**: você pode encontrar links para logs no portal de gerenciamento. Clique em **estatísticas**e em **logs**. Na página **arquivos de log** , você verá uma lista de logs para todos os itens LiveEvent; Selecione aquele correspondente à sessão atual. 
  * **Codificador do Flash Media Live**: você pode encontrar o **diretório de log...** navegando até a guia **log de codificação** .

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: não há nenhuma opção para gerar um fluxo progressivo
* **Possível problema**: o codificador que está sendo usado não desentrelaça automaticamente. 
  
    **Etapas de solução de problemas**: Procure uma opção de desentrelaçamento na interface do codificador. Depois de habilitar o desentrelaçamento, verifique novamente as configurações de saída progressivas. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: tentativa de várias configurações de saída de codificador e ainda não é possível se conectar.
* **Possível problema**: o canal de codificação do Azure não foi redefinido corretamente. 
  
    **Etapas de solução de problemas**: Verifique se o codificador não está mais enviando para o AMS, pare e redefina o canal. Depois de executar novamente, tente conectar o codificador com as novas configurações. Se isso ainda não corrigir o problema, tente criar um novo canal inteiramente, às vezes os canais podem se tornar corrompidos após várias tentativas com falha.  
* **Possível problema**: o tamanho do GOP ou as configurações do quadro chave não são ideais. 
  
    **Etapas de solução de problemas**: o tamanho recomendado do GOP ou o intervalo de quadro-chave é de dois segundos. Alguns codificadores calculam essa configuração em número de quadros, enquanto outros usam segundos. Por exemplo: ao gerar 30 fps, o tamanho de GOP seria de 60 quadros, que é equivalente a 2 segundos.  
* **Possível problema**: as portas fechadas estão bloqueando o fluxo. 
  
    **Etapas de solução de problemas**: ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas de saída 1935 e 1936 estão abertas. 

> [!NOTE]
> Se depois de seguir as etapas de solução de problemas que ainda não puderem ser transmitidas com êxito, envie um tíquete de suporte usando o portal do Azure.
> 
> 

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

