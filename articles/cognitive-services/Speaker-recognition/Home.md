---
title: O que é a API de Reconhecimento de Orador?
titleSuffix: Azure Cognitive Services
description: Utilize algoritmos avançados para a verificação de orador e a identificação de orador com a API de Reconhecimento de Orador nos Serviços Cognitivos.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 9cdfd5d09451968487bafbcad643e179ffe82aa7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707170"
---
# <a name="speaker-recognition-api"></a>API de Reconhecimento de Orador

Bem-vindo às APIs de Reconhecimento de Orador dos Serviços Cognitivos do Azure. As APIs de Reconhecimento de Orador são APIs com base na cloud que fornecem os algoritmos mais avançados para verificação de orador e identificação de orador. O Reconhecimento de Orador pode ser dividido em duas categorias: verificação de orador e identificação de orador.


## <a name="speaker-verification"></a>Verificação de Orador

A voz tem caraterísticas únicas que podem ser utilizadas para identificar uma pessoa, tal como uma impressão digital.  A utilização da voz como um sinal para cenários de autenticação e controlo de acesso surgiu como uma nova ferramenta inovadora – oferecendo basicamente um nível de segurança que simplifica a experiência de autenticação para os clientes.

As APIs de Verificação de Orador podem verificar e autenticar os utilizadores com a voz deles automaticamente.

### <a name="enrollment"></a>Inscrição

A inscrição na verificação de orador é dependente de texto, o que significa que os oradores têm de escolher uma frase de acesso específica a utilizar durante as fases de inscrição e verificação.

Na inscrição, a voz do orador é registada a enunciar uma frase específica e, em seguida, várias caraterísticas são extraídas e a frase escolhida é reconhecida. Em conjunto, as caraterísticas extraídas e a frase escolhida forma uma assinatura de voz única.

### <a name="verification"></a>Verificação

Na verificação, a voz de entrada e a frase são comparadas em relação à assinatura e frase da voz de inscrição – para verificar se são ou não da mesma pessoa, e se está a enunciar a frase correta.

Para obter mais detalhes sobre a verificação do palestrante, consulte a [verificação de alto-falante](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652)da API.

## <a name="speaker-identification"></a>Identificação de Orador

As APIs de Identificação de Orador podem identificar automaticamente a pessoa que fala num ficheiro de áudio, tendo em conta um grupo de oradores potenciais. O áudio de entrada é comparado ao grupo fornecido de oradores e, caso seja encontrada uma correspondência, a identidade do orador é devolvida.

Todos os oradores devem passar primeiro por um processo de inscrição para registarem a voz no sistema e ser criada uma impressão de voz.


### <a name="enrollment"></a>Inscrição

A inscrição na identificação de orador é independente de texto, o que significa que não existem restrições ao que o orador diz no áudio. A voz do orador é registada e várias caraterísticas são extraídas para formar uma assinatura de voz única.


### <a name="recognition"></a>Reconhecimento

O áudio do orador desconhecido, juntamente com o grupo potencial de oradores, é fornecido durante o reconhecimento. A voz de entrada é comparada em relação a todos os oradores para determinar de quem é a voz e, se for encontrada uma correspondência, a identidade do orador é devolvida.

Para obter mais detalhes sobre a identificação do orador, consulte a [identificação do orador](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)da API.
