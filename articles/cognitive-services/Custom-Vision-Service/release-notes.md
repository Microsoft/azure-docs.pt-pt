---
title: Notas de versão – serviço de visão personalizada
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 2b0d8b8a86c3105b1bda7fb0d72cbcb72ed82995
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816381"
---
# <a name="custom-vision-service-release-notes"></a>Notas de versão do serviço de visão personalizada

## <a name="march-26-2019"></a>26 de Março de 2019

- Serviço de visão personalizada entrou em disponibilidade geral no Azure!
- Funcionalidade de formação de avançada foi adicionada com uma nova máquina back-end para um melhor desempenho, especialmente em conjuntos de dados de um desafio e classificação refinada de aprendizado. Com a formação de avançada, pode especificar que um orçamento de tempo de computação de formação e de visão personalizada experimentalmente identificará as definições de treinamento e o aumento de melhor. Para iterações rápidas, pode continuar a utilizar o treinamento rápido existente.
- Introduzida 3.0 APIs. Anunciada a descontinuação próximo das APIs de pré-3.0 de 1 de Outubro de 2019. Veja os inícios rápidos de documentação para [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [nó](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), ou [ir](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) para obter exemplos sobre como começar a utilizar.
- Substituído "Predefinido iterações" publicar/anular a publicação no 3.0 APIs.
- Foram adicionados novos destinos de exportação de modelo. Exportação do Dockerfile foi atualizada para oferecer suporte ARM para Raspberry Pi 3. Foi adicionado suporte de exportação para o [Kit de desenvolvimento de ia de imagem digitalizada.](https://visionaidevkit.com/).
- Maior limite de etiquetas por projeto para 500 para escalão S0. Maior limite de imagens por projeto para 100 000 para escalão S0.
- Remover domínio para adultos. Domínio geral, é recomendado em vez disso.
- Anunciámos [preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) para disponibilidade geral.  

## <a name="february-25-2019"></a>25 de Fevereiro de 2019

- Anunciámos o fim de projetos de versão de avaliação limitada (projetos não associados a um recurso do Azure), como de visão personalizada está prestes a conclusão de sua mudança para a pré-visualização pública do Azure. A partir de 25 de Março de 2019, o site de CustomVision.ai só irá suportar projetos de visualização associados a um recurso do Azure, como o recurso de visão personalizada gratuito. Por meio de 1 de Outubro de 2019, ainda será capaz de aceder aos seus projetos de avaliação limitados existentes por meio das APIs de visão personalizada. Isso dará a tempo às chaves de API de atualização para todas as aplicações que escreveu com visão personalizada. Após 1 de Outubro de 2019 serão eliminados quaisquer projetos de avaliação limitados, ainda não mudou para o Azure.

## <a name="january-22-2019"></a>22 de Janeiro de 2019

- Suporte adicionado para novas regiões do Azure: E.U.A. oeste 2, E.U.A. leste, E.U.A. Leste 2, Europa Ocidental, Europa do Norte, Sudeste asiático, leste da Austrália, Índia Central, sul do Reino Unido, leste do Japão e Centro-Norte. Suporte continua para o Centro-Sul.

## <a name="december-12-2018"></a>12 de Dezembro de 2018

- Suporte à exportação para modelos de deteção de objetos (introduzida objeto deteção Compact domínio).
- Um número fixo de problemas de acessibilidade do leitor de ecrã aprimoradas e suporte à navegação do teclado.
- Atualizações de experiência do Usuário para o Visualizador de imagens e deteção de objetos melhorada experiência para marcar o mais rápido de marcação.  
- Atualizado base modelo para o domínio de deteção de objeto para deteção de objetos do melhor qualidade.
- Correções de erros.

## <a name="november-6-2018"></a>6 de Novembro de 2018

- Foi adicionado suporte para o domínio de logótipo na deteção de objetos.

## <a name="october-9-2018"></a>9 de outubro de 2018

- Deteção de objetos introduz a pré-visualização paga. Agora, pode criar projetos de deteção de objetos com um recurso do Azure.
- Funcionalidade de "Mover para o Azure" foi adicionada ao site, para que seja mais fácil atualizar um projeto de versão de avaliação limitada para ligar a do Azure. projeto de ligado de recursos (F0 ou S0). Pode encontrar isto na página Definições do seu produto.  
- Exportação foi adicionada para 1.2 ONNX, para suportar a versão de atualização de Outubro de 2018 do Windows do Windows ML.
Correções de erros, incluindo para exportação ONNX com carateres especiais.

## <a name="august-14-2018"></a>14 de Agosto de 2018

- Foi adicionado widget "Iniciar" para o site de customvision.ai orientar os usuários por meio de treinamento do project.
- Ainda mais melhorias para o pipeline de machine learning para beneficiar multilabel projetos (nova camada de perda).

## <a name="june-28-2018"></a>28 de junho de 2018

- Correções de erros e melhorias de back-end.
- Ativar a classificação de várias classes, para projetos em que as imagens têm exatamente uma etiqueta. Somam em previsões para o modo de várias classes, probabilidades para um (todas as imagens são classificadas entre as suas etiquetas especificadas).

## <a name="june-13-2018"></a>13 de junho de 2018

- Atualização da experiência do Usuário, se concentrou na facilidade de utilização e acessibilidade.
- Melhorias para o pipeline de aprendizagem para se beneficiar multilabel projetos com um grande número de etiquetas.
- Foi corrigido o erro na exportação do TensorFlow. Ativado o controle de versão do modelo exportado, para que as iterações podem ser exportadas a mais de uma vez.

## <a name="may-7-2018"></a>7 de maio de 2018

- Introduziu a funcionalidade de Deteção de Objeto de pré-visualização para projetos de Versão de Avaliação Limitada.
- Atualizar para APIs 2.0
- Escalão S0 expandido até 250 etiquetas e 50.000 imagens.
- Melhorias de back-end significativas para o pipeline de aprendizagem automática para projetos de classificação de imagem. Os projetos que foram preparados depois de 27 de Abril de 2018 irão beneficiar destas atualizações.
- Exportação de modelo adicionado a ONNX, para utilização com o Windows ML.
- Exportação de modelo adicionado ao Dockerfile. Isto permite-lhe transferir os artefactos para criar os seus próprios contentores do Windows ou Linux, incluindo um DockerFile, modelo do TensorFlow e código de serviço.
- Para recentemente Modelos exportados para o TensorFlow no geral (Compact) e domínios de ponto de referência (Compact), de aprendizagem [valores significa que agora estão (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), para manter a consistência em todos os projetos.

## <a name="march-1-2018"></a>1 de março de 2018

- A pré-visualização paga introduzida e carregadas para o portal do Azure. Os projetos podem agora ser anexados a recursos do Azure com um escalão F0 (Gratuito) ou S0 (Standard). Os projetos de escalão S0 foram introduzidos, o que permite um máximo de 100 etiquetas e 25.000 imagens.
- Back-end muda para o parâmetro de pipeline/normalização de aprendizagem automática. Isto dará aos clientes um melhor controlo do equilíbrio de revocação de precisão ao ajustar o Limiar de Probabilidade. Como parte destas alterações, o Limiar de Probabilidade predefinido no portal CustomVision.ai foi definido como 50%.

## <a name="december-19-2017"></a>19 de dezembro de 2017

- Exportar para o Android (TensorFlow) adicionado, além da exportação lançada anteriormente para iOS (CoreML.) Isto permite que a exportação de um modelo compacto preparado seja executado offline numa aplicação.
- Domínios "compactos" de Retalho e de Ponto de Referência adicionados para permitir a exportação de modelos para estes domínios.
- Versão de lançamento [1.2 API de Preparação](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 API de Predição](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas suportam a exportação de modelo, uma nova operação de Predição que não guarda imagens em "Predições" e introduziu operações em lote na API de Preparação.
- Ajustes na experiência do utilizador, incluindo a capacidade de ver qual foi o domínio utilizado para preparar uma iteração.
- [Exemplo e SDK de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows) atualizado.