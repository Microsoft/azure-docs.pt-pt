---
title: O que é a API de Reconhecimento de Orador?
titleSuffix: Azure Cognitive Services
description: Verificação de palestrante e identificação do orador com o API de Reconhecimento do Locutor em serviços cognitivas.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464911"
---
# <a name="speaker-recognition-api---preview"></a>API de Reconhecimento do Locutor-visualização

As APIs de Reconhecimento do Locutor são APIs baseadas em nuvem que fornecem os algoritmos de ia avançados para verificação de palestrante e identificação do orador. Reconhecimento do Locutor é dividido em duas categorias: verificação do alto-falante e identificação do orador.

## <a name="speaker-verification"></a>Verificação de Orador

A voz tem características exclusivas que podem ser associadas a um indivíduo.  Os aplicativos podem usar voz como um fator adicional para verificação, em cenários como call centers e serviços Web.

Verificação do Locutor APIs funcionam como uma ferramenta inteligente para ajudar a verificar os usuários que usam suas senhas de voz e fala.

### <a name="enrollment"></a>Inscrição

O registro para verificação do orador é dependente de texto, o que significa que os alto-falantes precisam escolher uma frase secreta específica para usar durante as fases de registro e de verificação.

Na fase de registro do palestrante, a voz do orador é registrada dizendo uma frase específica. Os recursos de voz são extraídos para formar uma assinatura de voz exclusiva, enquanto a frase escolhida é reconhecida. Juntos, esses dados de registro do orador seriam usados para verificar o palestrante. Os dados de registro do orador são armazenados em um sistema protegido. O cliente controla por quanto tempo ele deve ser retido. Os clientes podem criar, atualizar e remover dados de registro para palestrantes individuais por meio de chamadas à API.  Quando a assinatura for excluída, todos os dados de registro do orador associados à assinatura também serão excluídos.

Os clientes devem garantir que receberam as permissões apropriadas dos usuários para verificação do palestrante.

### <a name="verification"></a>Verificação

Na fase de verificação, o cliente deve chamar a API de verificação do viva-voz com a ID associada à pessoa a ser verificada.  O serviço extrai recursos de voz e a frase secreta da gravação de fala de entrada. Em seguida, ele compara os recursos com os elementos correspondentes dos dados de registro do orador para o alto-falante que o cliente está procurando verificar e determina qualquer correspondência.  A resposta retorna "Accept" ou "Reject" com diferentes níveis de confiança.  Em seguida, o cliente determina como usar os resultados para ajudar a decidir se essa pessoa é o palestrante registrado.

O nível de confiança limite deve ser definido com base no cenário e em outros fatores de verificação que estão sendo usados. Recomendamos que você experimente o nível de confiança e considere a configuração apropriada para cada aplicativo. As APIs não se destinam a determinar se o áudio é de uma pessoa em tempo real ou de um imitação ou de uma gravação de um palestrante registrado.

O serviço não retém a gravação de fala ou os recursos de voz extraídos que são enviados para o serviço durante a fase de verificação.

Para obter mais detalhes sobre a verificação de orador, consulte a API [Orador - Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identificação de Orador

Os aplicativos podem usar a voz para identificar "quem está falando", dado um grupo de alto-falantes registrados. Identificação do Locutor APIs podem ser usadas em cenários como a produtividade da reunião, a personalização e a transcrição do Call Center.

### <a name="enrollment"></a>Inscrição

A inscrição na identificação de orador é independente de texto, o que significa que não existem restrições ao que o orador diz no áudio. Nenhuma frase secreta é necessária.

Na fase de registro, a voz do orador é registrada e os recursos de voz são extraídos para formar uma assinatura de voz exclusiva. Os recursos e áudio de fala extraídos são armazenados em um sistema protegido. O cliente controla por quanto tempo ele é retido. Os clientes podem criar, atualizar e remover esses dados de registro do orador para palestrantes individuais por meio de chamadas à API. Quando a assinatura for excluída, todos os dados de registro do orador associados à assinatura também serão excluídos.

Os clientes devem garantir que receberam as permissões apropriadas dos usuários para a identificação do palestrante.

### <a name="identification"></a>ID

Na fase de identificação, o serviço de identificação do orador extrai recursos de voz da gravação de fala de entrada. Em seguida, ele compara os recursos com os dados de registro da lista de alto-falantes especificada. Quando uma correspondência é encontrada com um orador registrado, a resposta retorna a ID do palestrante com um nível de confiança.  Caso contrário, a resposta retornará "rejeitar" quando nenhum orador for uma correspondência com um palestrante registrado.

O nível de confiança limite deve ser definido com base no cenário. Recomendamos que você experimente o nível de confiança e considere a configuração apropriada para cada aplicativo. As APIs não se destinam a determinar se o áudio é de uma pessoa em tempo real ou de um imitação ou de uma gravação de um palestrante registrado.

O serviço não retém a gravação de fala ou os recursos de voz extraídos que são enviados para o serviço para a fase de identificação.

Para obter mais detalhes sobre a identificação do orador, consulte a [identificação do orador](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)da API.
