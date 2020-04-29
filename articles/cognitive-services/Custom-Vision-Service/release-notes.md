---
title: Notas de lançamento - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Obtenha as últimas informações sobre novos lançamentos da equipa Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73647563"
---
# <a name="custom-vision-service-release-notes"></a>Notas de lançamento do serviço de visão personalizada

## <a name="may-2-2019-and-may-10-2019"></a>2 de maio de 2019 e 10 de maio de 2019

- Bugfixos e melhorias no backend

## <a name="may-23-2019"></a>23 de maio de 2019

- Experiência DE ux do portal melhorada relacionada com subscrições do Azure, facilitando a seleção dos seus diretórios Azure.

## <a name="april-18-2019"></a>18 de abril de 2019 

- Exportação de deteção de objetos adicionada para o Kit Dev Vision AI.
- Ajustes de UI, incluindo pesquisa de projeto.

## <a name="april-3-2019"></a>3 de abril de 2019

- Limite acrescido do número de caixas de delimitação por imagem para 200. 
- Bugfixes, incluindo atualização substancial de desempenho para modelos exportados para o TensorFlow. 

## <a name="march-26-2019"></a>26 de março de 2019

- O Serviço de Visão Personalizada entrou na Disponibilidade Geral no Azure!
- Funcionalidade de Formação Avançada Adicionada com um novo backend de aprendizagem automática para um melhor desempenho, especialmente em conjuntos de dados desafiantes e classificação de grãos finos. Com formação avançada, pode especificar um orçamento de tempo de computação para treino e a Visão Personalizada identificará experimentalmente as melhores definições de treino e aumento. Para iterações rápidas, pode continuar a usar o treino rápido existente.
- Introduziu 3.0 APIs. Anunciada a próxima depreciação de APIs pré-3.0 em 1 de outubro de 2019. Veja a documentação de arranque rápido para [.Net,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial) [Python,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial) [Node,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial) [Java,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)ou [Go,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) por exemplo, sobre como começar.
- Substituído "Iterações Predefinidas" por Publicar/Não Publicar nas 3.0 APIs.
- Foram adicionados novos objetivos de exportação de modelos. A exportação de Dockerfile foi atualizada para apoiar a ARM para Raspberry Pi 3. O apoio à exportação foi adicionado ao [Vision AI Dev Kit. . .](https://visionaidevkit.com/)
- Limite aumentado de Tags por projeto para 500 para o nível S0. Limite aumentado de Imagens por projeto para 100.000 para o nível S0.
- Domínio adulto removido. Em vez disso, recomenda-se domínio geral.
- Anunciado [preço](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) para disponibilidade geral.  

## <a name="february-25-2019"></a>25 de fevereiro de 2019

- Anunciou o fim dos projetos de Ensaio Limitado (projetos não associados a um recurso Azure), uma vez que a Custom Vision está perto da conclusão da sua mudança para a pré-visualização pública do Azure. A partir de 25 de março de 2019, o site CustomVision.ai só apoiará projetos de visualização associados a um recurso Azure, como o recurso gratuito Custom Vision. Até 1 de outubro de 2019, ainda poderá aceder aos seus projetos de teste limitados existentes através das APIs de Visão Personalizada. Isto lhe dará tempo para atualizar as teclas API para quaisquer aplicações que tenha escrito com Visão Personalizada. Depois de 1 de outubro de 2019, quaisquer projetos de experimentação limitados que não tenha mudado para O Azure serão eliminados.

## <a name="january-22-2019"></a>22 de janeiro de 2019

- Apoio adicionado às novas regiões azure: Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Europa Ocidental, Norte da Europa, Sudeste Asiático, Austrália Leste, Índia Central, Reino Unido Sul, Japão Leste e Norte Central DOS EUA. O apoio continua para os EUA do Centro Sul.

## <a name="december-12-2018"></a>12 de dezembro de 2018

- Apoie a exportação para modelos de deteção de objetos (domínio compacto de deteção de objetos introduzido).
- Corrigiu uma série de problemas de acessibilidade para um melhor suporte de navegação de leitor de ecrã e teclado.
- Atualizações UX para visualizador de imagem e experiência de deteção de objetos melhorada para marcação mais rápida.  
- Modelo base atualizado para domínio de deteção de objetos para melhor qualidade de deteção de objetos.
- Correções de insetos.

## <a name="november-6-2018"></a>6 de novembro de 2018

- Suporte adicional para domínio de logotipo na deteção de objetos.

## <a name="october-9-2018"></a>9 de outubro de 2018

- A Deteção de Objetos entra na pré-visualização paga. Agora pode criar projetos de Deteção de Objetos com recurso Azure.
- Adicionou a funcionalidade "Move to Azure" ao site, para facilitar a atualização de um projeto de Teste Limitado para ligar a um Azure. projeto ligado aos recursos (F0 ou S0.) Pode encontrá-lo na página Definições do seu produto.  
- Adicionou exportação para onNX 1.2, para suportar a versão do Windows 2018 outubro Update do Windows ML.
Correções de bugs, incluindo para exportação ONNX com caracteres especiais.

## <a name="august-14-2018"></a>14 de agosto de 2018

- Adicionou widget "Get Started" a customvision.ai site para orientar os utilizadores através da formação de projetos.
- Outras melhorias no pipeline de aprendizagem automática para beneficiar projetos multi-rótulos (nova camada de perda).

## <a name="june-28-2018"></a>28 de junho de 2018

- O bug corrige & melhorias no backend.
- Classificação multiclasse habilitada, para projetos onde as imagens têm exatamente um rótulo. Nas Previsões para o modo multiclasse, as probabilidades resumir-se-ão a uma (todas as imagens estão classificadas entre as suas etiquetas especificadas).

## <a name="june-13-2018"></a>13 de junho de 2018

- Atualização UX, focada na facilidade de utilização e acessibilidade.
- Melhorias no pipeline de machine learning para beneficiar projetos multi-rótulos com um grande número de tags.
- Inseto fixo na exportação tensorFlow. Versão de modelo exportado habilitada, para que as iterações possam ser exportadas mais de uma vez.

## <a name="may-7-2018"></a>7 de maio de 2018

- Introduziu a funcionalidade de Deteção de Objeto de pré-visualização para projetos de Versão de Avaliação Limitada.
- Atualizar para APIs 2.0
- Escalão S0 expandido até 250 etiquetas e 50.000 imagens.
- Melhorias de back-end significativas para o pipeline de aprendizagem automática para projetos de classificação de imagem. Os projetos que foram preparados depois de 27 de Abril de 2018 irão beneficiar destas atualizações.
- Exportação de modelo adicionado a ONNX, para utilização com o Windows ML.
- Exportação de modelo adicionado ao Dockerfile. Isto permite-lhe transferir os artefactos para criar os seus próprios contentores do Windows ou Linux, incluindo um DockerFile, modelo do TensorFlow e código de serviço.
- Para os modelos recém-treinados exportados para o TensorFlow nos domínios gerais (compactos) e de marco (compacto), os [Valores Médios são agora (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)para a consistência em todos os projetos.

## <a name="march-1-2018"></a>1 de março de 2018

- Inserida pré-visualização paga e a bordo do portal Azure. Os projetos podem agora ser anexados a recursos do Azure com um escalão F0 (Gratuito) ou S0 (Standard). Os projetos de escalão S0 foram introduzidos, o que permite um máximo de 100 etiquetas e 25.000 imagens.
- Back-end muda para o parâmetro de pipeline/normalização de aprendizagem automática. Isto dará aos clientes um melhor controlo do equilíbrio de revocação de precisão ao ajustar o Limiar de Probabilidade. Como parte destas alterações, o Limiar de Probabilidade predefinido no portal CustomVision.ai foi definido como 50%.

## <a name="december-19-2017"></a>19 de dezembro de 2017

- Exportação para Android (TensorFlow) adicionada, além de exportação previamente lançada para iOS (CoreML.) Isto permite que a exportação de um modelo compacto treinado seja executada offline numa aplicação.
- Domínios "compactos" de Retalho e de Ponto de Referência adicionados para permitir a exportação de modelos para estes domínios.
- Versão de lançamento [1.2 API de Preparação](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 API de Predição](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas suportam a exportação de modelo, uma nova operação de Predição que não guarda imagens em "Predições" e introduziu operações em lote na API de Preparação.
- Ajustes na experiência do utilizador, incluindo a capacidade de ver qual foi o domínio utilizado para preparar uma iteração.
- [Exemplo e SDK de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows) atualizado.