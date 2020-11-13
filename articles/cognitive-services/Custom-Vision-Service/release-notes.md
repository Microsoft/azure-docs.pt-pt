---
title: Notas de lançamento - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Obtenha as últimas informações sobre novas versões da equipa da Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: ccf742e5ff612816cae1056ef7bf0e64fc9dbb5b
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616472"
---
# <a name="custom-vision-service-release-notes"></a>Notas de lançamento do serviço de visão personalizada

## <a name="may-2-2019-and-may-10-2019"></a>2 de maio de 2019 e 10 de maio de 2019

- Bugfixes e melhorias no backend

## <a name="may-23-2019"></a>23 de maio de 2019

- Experiência melhorada do portal UX relacionada com subscrições Azure, facilitando a seleção dos seus diretórios Azure.

## <a name="april-18-2019"></a>18 de abril de 2019 

- Exportação adicionada de deteção de objetos para o Kit Vision AI Dev.
- Ajustes de UI, incluindo pesquisa de projetos.

## <a name="april-3-2019"></a>3 de abril de 2019

- Limite aumentado no número de caixas de delimitação por imagem para 200. 
- Bugfixes, incluindo atualizações substanciais de desempenho para modelos exportados para TensorFlow. 

## <a name="march-26-2019"></a>26 de março de 2019

- O Serviço de Visão Personalizada inseriu disponibilidade geral no Azure!
- Adição de Formação Avançada com um novo backend de aprendizagem automática para um melhor desempenho, especialmente em conjuntos de dados desafiantes e classificação de grãos finos. Com treino avançado, você pode especificar um orçamento de tempo de computação para a formação e a Visão Personalizada irá identificar experimentalmente as melhores configurações de treino e aumento. Para iterações rápidas, pode continuar a utilizar o treino rápido existente.
- Introduziu 3.0 APIs. Anunciada a depreciação das APIs pré-3.0 no dia 1 de outubro de 2019. Consulte os quickstarts da documentação para [.Net,](./quickstarts/image-classification.md) [Python,](./quickstarts/image-classification.md) [Node,](./quickstarts/image-classification.md) [Java](./quickstarts/image-classification.md)ou [Go](./quickstarts/image-classification.md) por exemplos sobre como começar.
- Substituiu as "Iterações Predefinidos" por Publicar/Não publicar nas APIs 3.0.
- Foram adicionados novos objetivos de exportação de modelos. A exportação de dockerfile foi atualizada para apoiar a ARM para Raspberry Pi 3. O apoio à exportação foi adicionado ao [Kit Vision AI Dev.](https://visionaidevkit.com/). .
- Limite aumentado de Tags por projeto para 500 para nível S0. Limite aumentado de Imagens por projeto para 100.000 para nível S0.
- Removido domínio adulto. Em vez disso, recomenda-se o domínio geral.
- [Preço](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) anunciado para disponibilidade geral.  

## <a name="february-25-2019"></a>25 de fevereiro de 2019

- Anunciou o fim de projetos de Teste Limitado (projetos não associados a um recurso Azure), uma vez que a Custom Vision está perto de concluir a sua mudança para a pré-visualização pública do Azure. A partir de 25 de março de 2019, o site CustomVision.ai apenas apoiará projetos de visualização associados a um recurso Azure, como o recurso gratuito Custom Vision. Até 1 de outubro de 2019, ainda poderá aceder aos seus projetos de experimentação limitados existentes através das APIs de Visão Personalizada. Isto lhe dará tempo para atualizar as teclas API para quaisquer aplicações que tenha escrito com a Visão Personalizada. Depois de 1 de outubro de 2019, quaisquer projetos de teste limitados que não tenha mudado para Azure serão eliminados.

## <a name="january-22-2019"></a>22 de janeiro de 2019

- Apoio adicionado a novas regiões de Azure: West US 2, East US, East US 2, West Europe, North Europe, Southeast Asia, Australia East, Central India, UK South, Japan East e North Central US. O apoio continua para a Central Sul dos EUA.

## <a name="december-12-2018"></a>12 de dezembro de 2018

- Suporte à exportação para modelos de deteção de objetos (domínio compacto de deteção de objetos introduzido).
- Corrigiu uma série de problemas de acessibilidade para um melhor leitor de ecrãs e suporte à navegação de teclado.
- Atualizações UX para visualização de imagem e experiência de marcação de deteção de objetos melhorada para uma marcação mais rápida.  
- Modelo base atualizado para domínio de deteção de objetos para uma melhor qualidade de deteção de objetos.
- Correções de insetos.

## <a name="november-6-2018"></a>6 de novembro de 2018

- Suporte adicionado para domínio de logotipo na deteção de objetos.

## <a name="october-9-2018"></a>9 de outubro de 2018

- A Deteção de Objetos introduz pré-visualização paga. Agora pode criar projetos de Deteção de Objetos com recurso Azure.
- Adicionado recurso "Move to Azure" ao site, para facilitar a atualização de um projeto de Teste Limitado para ligar a um Azure. projeto ligado a recursos (F0 ou S0.) Pode encontrar isto na página Definições do seu produto.  
- Exportou para ONNX 1.2, para suportar a versão de Atualização de outubro do Windows 2018 do Windows ML.
Correções de bugs, incluindo para exportação ONNX com caracteres especiais.

## <a name="august-14-2018"></a>14 de agosto de 2018

- O widget "Get Started" adicionado para customvision.ai site para orientar os utilizadores através da formação de projetos.
- Melhorias adicionais no gasoduto de aprendizagem automática para beneficiar projetos multilabel (nova camada de perdas).

## <a name="june-28-2018"></a>28 de junho de 2018

- As correções de & as melhorias no backend.
- Classificação multiclasse ativada, para projetos onde as imagens têm exatamente um rótulo. Nas Previsões para o modo multiclasse, as probabilidades serão elevadas para uma (todas as imagens são classificadas entre as suas Tags especificadas).

## <a name="june-13-2018"></a>13 de junho de 2018

- UX refresh, focado na facilidade de utilização e acessibilidade.
- Melhorias no gasoduto de aprendizagem automática para beneficiar projetos multilabel com um grande número de tags.
- Bug fixo na exportação do TensorFlow. Versão de modelos exportadas ativadas, para que as iterações possam ser exportadas mais de uma vez.

## <a name="may-7-2018"></a>7 de maio de 2018

- Introduziu a funcionalidade de Deteção de Objeto de pré-visualização para projetos de Versão de Avaliação Limitada.
- Atualizar para APIs 2.0
- Escalão S0 expandido até 250 etiquetas e 50.000 imagens.
- Melhorias de back-end significativas para o pipeline de aprendizagem automática para projetos de classificação de imagem. Os projetos que foram preparados depois de 27 de Abril de 2018 irão beneficiar destas atualizações.
- Exportação de modelo adicionado a ONNX, para utilização com o Windows ML.
- Exportação de modelo adicionado ao Dockerfile. Isto permite-lhe transferir os artefactos para criar os seus próprios contentores do Windows ou Linux, incluindo um DockerFile, modelo do TensorFlow e código de serviço.
- Para os modelos recém-treinados exportados para TensorFlow nos Domínios Geral (Compacto) e Marco (Compacto), [os Valores Médios são agora (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), para a consistência em todos os projetos.

## <a name="march-1-2018"></a>1 de março de 2018

- Inseriu pré-visualização paga e entrou no portal Azure. Os projetos podem agora ser anexados a recursos do Azure com um escalão F0 (Gratuito) ou S0 (Standard). Os projetos de escalão S0 foram introduzidos, o que permite um máximo de 100 etiquetas e 25.000 imagens.
- Back-end muda para o parâmetro de pipeline/normalização de aprendizagem automática. Isto dará aos clientes um melhor controlo do equilíbrio de revocação de precisão ao ajustar o Limiar de Probabilidade. Como parte destas alterações, o Limiar de Probabilidade predefinido no portal CustomVision.ai foi definido como 50%.

## <a name="december-19-2017"></a>19 de dezembro de 2017

- Exportação para Android (TensorFlow) adicionada, além da exportação previamente lançada para iOS (CoreML.) Isto permite que a exportação de um modelo compacto treinado seja executado offline numa aplicação.
- Domínios "compactos" de Retalho e de Ponto de Referência adicionados para permitir a exportação de modelos para estes domínios.
- Versão de lançamento [1.2 API de Preparação](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 API de Predição](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas suportam a exportação de modelo, uma nova operação de Predição que não guarda imagens em "Predições" e introduziu operações em lote na API de Preparação.
- Ajustes na experiência do utilizador, incluindo a capacidade de ver qual foi o domínio utilizado para preparar uma iteração.
- [Exemplo e SDK de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows) atualizado.