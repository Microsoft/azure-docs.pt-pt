---
title: O que é a API de Reconhecimento de Orador?
titleSuffix: Azure Cognitive Services
description: Verificação de altifalantes e identificação de altifalantes com a API de Reconhecimento de Altifalantes em Serviços Cognitivos.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73464911"
---
# <a name="speaker-recognition-api---preview"></a>Reconhecimento de Altifalantes API - Pré-visualização

As APIs de Reconhecimento de Altifalantes são APIs baseados na nuvem que fornecem os algoritmos avançados de IA para verificação de altifalantes e identificação de colunas. O reconhecimento do orador é dividido em duas categorias: verificação de altifalantes e identificação de altifalantes.

## <a name="speaker-verification"></a>Verificação de Orador

A voz tem características únicas que podem ser associadas a um indivíduo.  As aplicações podem usar a voz como um fator adicional para a verificação, em cenários como call centers e serviços web.

As APIs de Verificação de Altifalantes servem como uma ferramenta inteligente para ajudar a verificar os utilizadores usando as suas frases de voz e de fala.

### <a name="enrollment"></a>Inscrição

A inscrição para verificação de altifalantes é dependente de texto, o que significa que os falantes precisam escolher uma palavra-passe específica para usar durante as fases de inscrição e verificação.

Na fase de inscrição do orador, a voz do orador é gravada dizendo uma frase específica. As características de voz são extraídas para formar uma assinatura de voz única enquanto a frase escolhida é reconhecida. Em conjunto, estes dados de inscrição do orador seriam utilizados para verificar o altifalante. Os dados de inscrição dos altifalantes são armazenados num sistema seguro. O Cliente controla quanto tempo deve ser mantido. Os clientes podem criar, atualizar e remover dados de inscrição para colunas individuais através de chamadas API.  Quando a subscrição for eliminada, todos os dados de inscrição do orador associados à subscrição também serão eliminados.

Os clientes devem certificar-se de que receberam as permissões adequadas dos utilizadores para a verificação do altifalante.

### <a name="verification"></a>Verificação

Na fase de verificação, o Cliente deve ligar para a API de verificação do altifalante com o ID associado ao indivíduo a verificar.  O serviço extrai as características de voz e a frase-passe da gravação do discurso de entrada. Em seguida, compara as funcionalidades com os elementos correspondentes dos dados de inscrição do altifalante para o altifalante que o Cliente procura verificar e determina qualquer correspondência.  A resposta devolve "aceitar" ou "rejeitar" com diferentes níveis de confiança.  Em seguida, o Cliente determina como utilizar os resultados para ajudar a decidir se esta pessoa é o orador inscrito.

O nível de confiança limiar deve ser fixado com base no cenário e noutros fatores de verificação que estão a ser utilizados. Recomendamos que experimente com o nível de confiança e considere a definição adequada para cada aplicação. As APIs não se destinam a determinar se o áudio é de uma pessoa viva ou de uma imitação ou de uma gravação de um altifalante matriculado.

O serviço não retém a gravação da fala nem as características de voz extraídas que são enviadas ao serviço durante a fase de verificação.

Para obter mais detalhes sobre a verificação de orador, consulte a API [Orador - Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identificação de Orador

As aplicações podem usar a voz para identificar "quem está a falar" dado um grupo de oradores inscritos. As APIs de identificação de colunas podem ser usadas em cenários como o cumprimento da produtividade, personalização e transcrição de call center.

### <a name="enrollment"></a>Inscrição

A inscrição na identificação de orador é independente de texto, o que significa que não existem restrições ao que o orador diz no áudio. Não é necessária nenhuma palavra-passe.

Na fase de inscrição, a voz do orador é gravada e as características de voz são extraídas para formar uma assinatura de voz única. O áudio da fala e as características extraídas são armazenadas num sistema seguro. O Cliente controla o tempo que é retido. Os clientes podem criar, atualizar e remover estes dados de inscrição de colunas para altifalantes individuais através de chamadas API. Quando a subscrição for eliminada, todos os dados de inscrição do orador associados à subscrição também serão eliminados.

Os clientes devem certificar-se de que receberam as permissões adequadas dos utilizadores para identificação de colunas.

### <a name="identification"></a>Identificação

Na fase de identificação, o serviço de identificação do altifalante extrai características de voz da gravação da fala de entrada. Em seguida, compara as funcionalidades com os dados de inscrição da lista especificada de oradores. Quando um fósforo é encontrado com um altifalante inscrito, a resposta devolve a identificação do altifalante com um nível de confiança.  Caso contrário, a resposta retorna "rejeitada" quando nenhum orador é compatível com um altifalante inscrito.

O nível de confiança limiar deve ser fixado com base no cenário. Recomendamos que experimente com o nível de confiança e considere a definição adequada para cada aplicação. As APIs não se destinam a determinar se o áudio é de uma pessoa viva ou de uma imitação ou de uma gravação de um altifalante matriculado.

O serviço não retém a gravação da fala nem as características de voz extraídas que são enviadas ao serviço para a fase de identificação.

Para mais detalhes sobre a identificação do altifalante, consulte o Altifalante da API [- Identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
