---
title: Glossário de termos - serviço de visão personalizada
titlesuffix: Azure Cognitive Services
description: Glossário de termos de serviço de visão personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 3530dbfe15f6dbdf481df70de6d03979750aa38e
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58352107"
---
# <a name="glossary-of-terms-for-custom-vision-service"></a>Glossário de termos de serviço de visão personalizada

Seguem-se alguns termos usados no serviço de visão personalizada:

## <a name="classifier"></a>Classificador

Um classificador é um modelo criados com o serviço de visão personalizada, utilizando algumas imagens de formação. Quando tiver concluído a treinar um classificador de novo, receberá um endpoint de avaliação (HTTPS), que pode adicionar à sua aplicação. Cada classificador que é criar é em seu próprio projeto e pode ver todos os projetos assim que tiver iniciado sessão.

## <a name="domain"></a>Domain

Quando cria um projeto, selecione um "domínio" para esse projeto. O domínio otimiza um classificador para um tipo específico de objeto em suas imagens. Por exemplo, se o seu cenário é classificar entre as imagens do setor de apple versus imagens de bolo de cenoura, em seguida, selecione o domínio de "Food". Se tiver a certeza de qual escolher o domínio, em seguida, selecione o domínio "Genérico".

- **O domínio de alimento.** Otimizado para pratos, que verá um menu do restaurante. Não foi otimizado para reconhecer frutos individuais ou vegetables. Se quiser classificar fotografias de frutas individuais ou vegetables, utilize o domínio genérico para essa finalidade.
- **O domínio de ponto de referência.** Otimizado para pontos de referência reconhecíveis, naturais e artificiais. Este domínio funciona melhor quando o ponto de referência é claramente visível no fotografia, mesmo que o ponto de referência é um pouco obstructed por um grupo de pessoas que está a representar na sua frente.
- **O domínio de varejo.** Otimizado para classificar imagens num catálogo de compra ou o Web site de compra. Se pretender alta precisão quando classificar dresses, intuição, shirts, etc., em seguida, utilizar o domínio de varejo.
- **O domínio para adultos.** Otimizado para definir melhor entre o conteúdo para adultos e o conteúdo não adulto. Por exemplo, se deseja bloquear imagens de pessoas em bathing de encriptação, este domínio permite-lhe criar um classificador personalizado para fazer isso.
- **O domínio geral.** Adequado para uma ampla variedade de tarefas de classificação de imagens.

Os modelos gerados pelo **compact domínios** possa ser exportada com a funcionalidade de exportação de iteração. Eles são otimizados para as restrições de classificação em tempo real em dispositivos móveis. Classificadores criadas com um domínio compact poderão ser um pouco menos exatos um domínio padrão com a mesma quantidade de dados de treinamento. A desvantagem é que eles são suficientemente pequenos para serem executados localmente na quase em tempo real. 

## <a name="evaluation"></a>Avaliação

Depois de ter preparado o seu classificador, pode enviar qualquer imagem de avaliação com o ponto final https gerado automaticamente. Seu classificador devolve um conjunto de marcas previstas, por ordem de confiança.

## <a name="iteration"></a>Iteração

Cada tempo Train ou treinar novamente o seu classificador, criar uma nova iteração do seu modelo. Mantemos várias iterações anteriores para que possa comparar o seu progresso ao longo do tempo. Pode eliminar uma repetição que já não ser úteis. Lembre-se de que a eliminação de uma iteração é permanente e também eliminar as imagens ou as alterações que foram exclusivas para essa iteração. 

## <a name="precision"></a>Precisão

Quando classifica uma imagem, o quão provável é o seu classificador para classificar corretamente a imagem? Fora de todas as imagens usadas para treinar o classificador (cães e ponies), que percentagem o modelo de obteve correto? 99 etiquetas corretas fora de 100 imagens dá uma precisão de 99%.

## <a name="predictions"></a>Previsões

Como o seu classificador aceita novas imagens para classificar, armazena as imagens para. Pode utilizar estas imagens para melhorar a precisão do seu classificador ao marcá corretamente as imagens incorrectamente previstas. Em seguida, pode utilizar estas novas imagens para treinar o seu classificador novamente.

## <a name="recall"></a>Recuperar

Fora de todas as imagens que devem ter sido classificadas corretamente, quantos seu classificador identificar corretamente? 38 cães um Recall de 100% significa que, se houvesse 38 imagens de dog nas imagens usadas para treinar o classificador, foram encontrados pelo classificador.

## <a name="settings"></a>Definições

Existem dois tipos de definições, definições de nível de projeto e definições de nível de usuário.

- Definições de nível de projeto:
  
  Definições de nível de projeto são aplicadas a um projeto ou o classificador. Eles incluem:

   - Domínio de projeto
   - Nome do Projeto
   - Descrição do Projeto
   - Utilização:
      - Número de imagens de formação
      - Número de etiquetas criado
      - Número de iterações criado

- Definições de nível de usuário: 
   - Chaves de subscrição: uma para formação, um para avaliação predição.
   - Utilização:
      - Número de projetos criados
      - Número de chamadas à API de predição avaliação feitas.

## <a name="tags"></a>Etiquetas

Utilize etiquetas para os objetos em suas imagens de formação da etiqueta. Se estiver a criar um classificador para identificar cães e ponies, poderia colocar uma marca de "cachorro" nas imagens que contenham cães, uma etiqueta de "pony" nas imagens que contenham ponies e tanto "cachorro" e uma etiqueta de "pony" nas imagens que contêm um cachorro e um pony.

## <a name="training-image"></a>Imagem de treinamento

Para criar um classificador de alta precisão, o serviço de visão personalizada tem várias imagens de formação. Uma imagem de treinamento é uma fotografia da imagem que pretende que o serviço de visão personalizada para classificar. Por exemplo, para classificar laranjas, precisaria carregar várias imagens de laranjas para o serviço de visão personalizada para permitir que o serviço criar um classificador que possam reconhecer laranjas. Recomendamos, pelo menos, 30 imagens por etiqueta.

## <a name="workspace"></a>Área de trabalho

A área de trabalho contém todas as imagens de formação e reflete todas as alterações de sua iteração mais recente como removidas ou adicionadas imagens. Quando preparar o seu classificador, vai criar uma nova iteração do seu classificador, utilizando as imagens presentes na sua área de trabalho.