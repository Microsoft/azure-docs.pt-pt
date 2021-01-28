---
title: Torne-se um parceiro codificadora no local
description: Este artigo discute como verificar os seus codificadores de streaming ao vivo no local.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: e07831ce4a016d49e0343da66efc0345effafd94
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954366"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Como verificar o seu codificadora de streaming ao vivo no local

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Como parceiro de codificação da Azure Media Services no local, a Media Services promove o seu produto recomendando o seu codificadora aos clientes empresariais. Para se tornar um parceiro codificadora no local, tem de verificar a compatibilidade do seu codificadora no local com os Media Services. Para tal, complete as seguintes verificações.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Verificação de eventos ao vivo de passagem

1. Na sua conta de Media Services, certifique-se de que o **Streaming Endpoint** está em execução. 
2. Criar e iniciar o **Evento** Ao Vivo. <br/> Para mais informações, consulte [os estados do Live Event e a faturação.](live-event-states-billing.md)
3. Obtenha os URLs ingerir e configuure o seu codificadora no local para usar o URL para enviar um stream ao vivo multi-bitrate para os Media Services.
4. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificante está realmente a ser recebida.
5. Crie um novo objeto **Ativo.**
6. Crie uma **Saída Ao Vivo** e use o nome de ativo que criou.
7. Crie um **localizador de streaming** com os tipos de Política de **Streaming** incorporados.
8. Enuse os caminhos no **Localizador de Streaming** para recuperar os URLs para usar.
9. Obtenha o nome de anfitrião para o **Streaming Endpoint** de onde pretende transmitir.
10. Combine o URL do passo 8 com o nome do anfitrião no passo 9 para obter o URL completo.
11. Coloque o codificar ao vivo durante aproximadamente 10 minutos.
12. Pare o evento ao vivo. 
13. Utilize um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis em todos os níveis de qualidade. Ou, assista e valide através do URL de pré-visualização durante a sessão ao vivo.
14. Grave o ID do ativo, o URL de streaming publicado para o arquivo ao vivo e as definições e versão utilizadas a partir do seu codificação ao vivo.
15. Reinicie o estado do Evento Vivo após a criação de cada amostra.
16. Repita os passos 5 a 15 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncio, legendas ou diferentes velocidades de codificação).

## <a name="live-encoding-live-event-verification"></a>Verificação de eventos ao vivo codificando ao vivo

1. Na sua conta de Media Services, certifique-se de que o **Streaming Endpoint** está em execução. 
2. Crie e inicie o **evento ao vivo** ao vivo ao vivo. <br/> Para mais informações, consulte [os estados do Live Event e a faturação.](live-event-states-billing.md)
3. Obtenha os URLs ingerir e configuure o seu codificante para empurrar um stream de bitrate ao vivo para os Media Services.
4. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificante está realmente a ser recebida.
5. Crie um novo objeto **Ativo.**
6. Crie uma **Saída Ao Vivo** e use o nome de ativo que criou.
7. Crie um **localizador de streaming** com os tipos de Política de **Streaming** incorporados.
8. Enuse os caminhos no **Localizador de Streaming** para recuperar os URLs para usar.
9. Obtenha o nome de anfitrião para o **Streaming Endpoint** de onde pretende transmitir.
10. Combine o URL do passo 8 com o nome do anfitrião no passo 9 para obter o URL completo.
11. Coloque o codificar ao vivo durante aproximadamente 10 minutos.
12. Pare o evento ao vivo.
13. Utilize um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis para todos os níveis de qualidade. Ou, assista e valide através do URL de pré-visualização durante a sessão ao vivo.
14. Grave o ID do ativo, o URL de streaming publicado para o arquivo ao vivo e as definições e versão utilizadas a partir do seu codificação ao vivo.
15. Reinicie o estado do Evento Vivo após a criação de cada amostra.
16. Repita os passos 5 a 15 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncio, legendas ou diferentes velocidades de codificação).

## <a name="longevity-verification"></a>Verificação da longevidade

Siga os mesmos passos que na [verificação do Evento Ao Vivo,](#pass-through-live-event-verification) exceto no passo 11. <br/>Em vez de 10 minutos, coloque o codificar ao vivo por uma semana ou mais. Utilize um leitor como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o streaming ao vivo de vez em quando (ou um ativo arquivado) para garantir que a reprodução não tem falhas visíveis.

## <a name="email-your-recorded-settings"></a>Envie por e-mail as suas definições gravadas

Por fim, envie um e-mail às suas definições gravadas e parâmetros de arquivo ao vivo para a Azure Media Services amshelp@microsoft.com como uma notificação de que todos os controlos de auto-verificação passaram. Além disso, inclua os seus dados de contacto para eventuais seguimentos. Pode contactar a equipa da Azure Media Services com quaisquer questões sobre este processo.

## <a name="see-also"></a>Ver também

[Codificadores testados nas instalações](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Passos seguintes

[Streaming em direto com media services v3](live-streaming-overview.md)
