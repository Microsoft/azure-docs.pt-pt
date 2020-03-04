---
title: Torne-se um parceiro codificador no local - Azure Media Services
description: Torne-se um parceiro codificador no local com a Azure Media Services.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6b00e430f960195e1badd2a73f9291997b94c833
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252977"
---
# <a name="become-an-on-premises-encoder-partner"></a>Torne-se um parceiro codificador no local

Como parceiro codificador da Azure Media Services no local, a Media Services promove o seu produto recomendando o seu codificador aos clientes empresariais. Para se tornar um parceiro codificador no local, deve verificar a compatibilidade do seu codificador no local com os Serviços de Media. Para isso, complete as seguintes verificações.

### <a name="pass-through-live-event-verification"></a>Verificação de eventos ao vivo pass-through

1. Na sua conta de Media Services, certifique-se de que o **Ponto Final de Streaming** está em funcionamento. 
2. Crie e inicie o **evento live pass-through.** <br/> Para mais informações, consulte os estados do [Live Event e a faturação.](live-event-states-billing.md)
3. Obtenha os URLs ingerir e configure o seu codificador no local para usar o URL para enviar um fluxo ao vivo multibitável para os Media Services.
4. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificador está realmente a ser recebida.
5. Criar um novo objeto **De Ativo.**
6. Crie uma **Saída Ao Vivo** e use o nome de ativo que criou.
7. Crie um Localizador de **Streaming** com os tipos de Política de **Streaming** incorporados.
8. Enumere os caminhos do Localizador de **Streaming** para recuperar os URLs para usar.
9. Obtenha o nome de anfitrião para o **Streaming Endpoint** que pretende transmitir.
10. Combine o URL do passo 8 com o nome do anfitrião no passo 9 para obter o URL completo.
11. Passe o seu codificador ao vivo durante aproximadamente 10 minutos.
12. Pare o Evento Ao Vivo. 
13. Utilize um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis em todos os níveis de qualidade. Ou, assista e valide através do URL de pré-visualização durante a sessão ao vivo.
14. Grave o ID do ativo, o URL de streaming publicado para o arquivo ao vivo e as definições e versão utilizadas a partir do seu codificador ao vivo.
15. Redefinir o estado do Evento Ao Vivo depois de criar cada amostra.
16. Repita os passos 5 a 15 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncios, legendas ou diferentes velocidades de codificação).

### <a name="live-encoding-live-event-verification"></a>Verificação de eventos ao vivo codificação ao vivo

1. Na sua conta de Media Services, certifique-se de que o **Ponto Final de Streaming** está em funcionamento. 
2. Crie e inicie o live codificando live **event.** <br/> Para mais informações, consulte os estados do [Live Event e a faturação.](live-event-states-billing.md)
3. Obtenha os URLs ingerir e configure o seu codificador para empurrar um stream ao vivo de um só bitrate para os Media Services.
4. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificador está realmente a ser recebida.
5. Criar um novo objeto **De Ativo.**
6. Crie uma **Saída Ao Vivo** e use o nome de ativo que criou.
7. Crie um Localizador de **Streaming** com os tipos de Política de **Streaming** incorporados.
8. Enumere os caminhos do Localizador de **Streaming** para recuperar os URLs para usar.
9. Obtenha o nome de anfitrião para o **Streaming Endpoint** que pretende transmitir.
10. Combine o URL do passo 8 com o nome do anfitrião no passo 9 para obter o URL completo.
11. Passe o seu codificador ao vivo durante aproximadamente 10 minutos.
12. Pare o Evento Ao Vivo.
13. Utilize um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o ativo arquivado para garantir que a reprodução não tem falhas visíveis para todos os níveis de qualidade. Ou, assista e valide através do URL de pré-visualização durante a sessão ao vivo.
14. Grave o ID do ativo, o URL de streaming publicado para o arquivo ao vivo e as definições e versão utilizadas a partir do seu codificador ao vivo.
15. Redefinir o estado do Evento Ao Vivo depois de criar cada amostra.
16. Repita os passos 5 a 15 para todas as configurações suportadas pelo seu codificador (com e sem sinalização de anúncios, legendas ou diferentes velocidades de codificação).

### <a name="longevity-verification"></a>Verificação da longevidade

Siga os mesmos passos que na verificação do [Evento Ao Vivo,](#pass-through-live-event-verification) exceto no passo 11. <br/>Em vez de 10 minutos, execute o seu codificador ao vivo por uma semana ou mais. Utilize um jogador como [o Azure Media Player](https://aka.ms/azuremediaplayer) para ver o streaming ao vivo de vez em quando (ou um ativo arquivado) para garantir que a reprodução não tem falhas visíveis.

### <a name="email-your-recorded-settings"></a>Envie um e-mail com as definições gravadas

Por fim, envie um e-mail para as suas definições gravadas e parâmetros de arquivo ao vivo para a Azure Media Services no amshelp@microsoft.com como uma notificação de que todos os controlos de auto-verificação passaram. Além disso, inclua as suas informações de contacto para quaisquer seguimentos. Pode contactar a equipa da Azure Media Services com quaisquer dúvidas sobre este processo.

## <a name="see-also"></a>Consulte também

[Codificadores recomendados no local](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Passos seguintes

[Streaming em direto com Media Services v3](live-streaming-overview.md)
