---
title: Notas de versão-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 79f3f2a5545b8cdcee86e52f35bd22a31e93d387
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564090"
---
# <a name="custom-vision-service-release-notes"></a>Notas de versão do Serviço de Visão Personalizada

## <a name="may-2-2019-and-may-10-2019"></a>2 de maio de 2019 e 10 de maio de 2019

- Melhorias de bugs e back-end

## <a name="may-23-2019"></a>23 de maio de 2019

- Experiência aprimorada de UX do portal relacionada às assinaturas do Azure, facilitando a seleção de seus diretórios do Azure.

## <a name="april-18-2019"></a>18 de abril de 2019 

- Adicionada a exportação de detecção de objeto para o kit de desenvolvimento de ia de visão.
- Ajustes de interface do usuário, incluindo a pesquisa de projeto.

## <a name="april-3-2019"></a>3 de abril de 2019

- Maior limite de número de caixas delimitadas por imagem a 200. 
- Bugs, incluindo uma atualização de desempenho substancial para modelos exportados para o TensorFlow. 

## <a name="march-26-2019"></a>26 de março de 2019

- Serviço de Visão Personalizada entrou em disponibilidade geral no Azure!
- Adicionado recurso de treinamento avançado com um novo back-end de aprendizado de máquina para melhorar o desempenho, especialmente em conjuntos de valores desafiadores e classificação refinada. Com o treinamento avançado, você pode especificar um orçamento de tempo de computação para treinamento e Visão Personalizada irá identificar de forma experimental as melhores configurações de treinamento e aumento. Para iterações rápidas, você pode continuar a usar o treinamento rápido existente.
- Introduziu 3,0 APIs. Anunciou a substituição de APIs de 3,0 em 1º de outubro de 2019. Consulte os guias de início rápido da documentação para [.net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)ou [vá](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) para obter exemplos de como começar.
- Substituiu "iterações padrão" por publicar/cancelar publicação nas APIs 3,0.
- Novos destinos de exportação de modelo foram adicionados. A exportação do Dockerfile foi atualizada para dar suporte ao ARM para Raspberry Pi 3. O suporte de exportação foi adicionado ao [Kit de desenvolvimento de ia de visão.](https://visionaidevkit.com/)
- Maior limite de marcas por projeto para 500 para a camada S0. Maior limite de imagens por projeto para 100.000 para a camada S0.
- Domínio adulto removido. Em vez disso, o domínio geral é recomendado.
- [Preço](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) anunciado para disponibilidade geral.  

## <a name="february-25-2019"></a>25 de fevereiro de 2019

- Anunciou o fim dos projetos de avaliação limitados (projetos não associados a um recurso do Azure), pois Visão Personalizada perto da conclusão de sua mudança para a visualização pública do Azure. A partir de 25 de março de 2019, o site do CustomVision.ai dará suporte apenas à exibição de projetos associados a um recurso do Azure, como o recurso de Visão Personalizada gratuito. Até 1º de outubro de 2019, você ainda poderá acessar seus projetos de avaliação limitada existentes por meio das APIs de Visão Personalizada. Isso lhe dará tempo para atualizar as chaves de API para todos os aplicativos que você escreveu com Visão Personalizada. Após 1º de outubro de 2019, todos os projetos de avaliação limitados que você não moveu para o Azure serão excluídos.

## <a name="january-22-2019"></a>22 de janeiro de 2019

- Suporte adicionado para novas regiões do Azure: Oeste dos EUA 2, leste dos EUA, leste dos EUA 2, Europa Ocidental, Europa Setentrional, Sudeste Asiático, leste da Austrália, Índia central, Sul do Reino Unido, leste do Japão e EUA Central do Norte. O suporte continua para o EUA Central do Sul.

## <a name="december-12-2018"></a>12 de dezembro de 2018

- Suporte para exportação para modelos de detecção de objetos (introduzidos no domínio compactação de detecção de objetos).
- Correção de vários problemas de acessibilidade para melhor suporte à navegação por teclado e leitor de tela.
- Atualizações de UX para visualizador de imagem e experiência aprimorada de marcação de detecção de objetos para marcação mais rápida.  
- Modelo base atualizado para o domínio de detecção de objeto para melhor detecção de objeto de qualidade.
- Correções de bugs.

## <a name="november-6-2018"></a>6 de novembro de 2018

- Suporte adicionado para o domínio de logotipo na detecção de objeto.

## <a name="october-9-2018"></a>9 de outubro de 2018

- A detecção de objeto entra em visualização paga. Agora você pode criar projetos de detecção de objetos com um recurso do Azure.
- Adicionado o recurso "mover para o Azure" para o site, para facilitar a atualização de um projeto de avaliação limitado para vincular a um Azure. projeto vinculado de recursos (F0 ou S0) Você pode encontrá-lo na página Configurações do seu produto.  
- Adicionada a exportação para ONNX 1,2, para dar suporte à versão de atualização de outubro do Windows 2018 do Windows ML.
Correções de bugs, incluindo para ONNX a exportação com caracteres especiais.

## <a name="august-14-2018"></a>14 de agosto de 2018

- Adicionado o widget "introdução" ao site customvision.ai para orientar os usuários por meio do treinamento do projeto.
- Aprimoramentos adicionais no pipeline do Machine Learning para beneficiar os projetos de várias etiquetas (nova camada de perda).

## <a name="june-28-2018"></a>28 de junho de 2018

- Correções de bugs & melhorias de back-end.
- A classificação multiclasse foi habilitada para projetos em que as imagens têm exatamente um rótulo. Em previsões para o modo multiclasse, as probabilidades serão somadas a uma (todas as imagens serão classificadas entre as marcas especificadas).

## <a name="june-13-2018"></a>13 de junho de 2018

- Atualização de UX, focada na facilidade de uso e na acessibilidade.
- Melhorias no pipeline de aprendizado de máquina para beneficiar os projetos de vários rótulos com um grande número de marcas.
- Corrigido o bug na exportação de TensorFlow. Habilitado o controle de versão de modelo exportado, portanto, as iterações podem ser exportadas mais de uma vez.

## <a name="may-7-2018"></a>7 de maio de 2018

- Introduziu a funcionalidade de Deteção de Objeto de pré-visualização para projetos de Versão de Avaliação Limitada.
- Atualizar para APIs 2.0
- Escalão S0 expandido até 250 etiquetas e 50.000 imagens.
- Melhorias de back-end significativas para o pipeline de aprendizagem automática para projetos de classificação de imagem. Os projetos que foram preparados depois de 27 de Abril de 2018 irão beneficiar destas atualizações.
- Exportação de modelo adicionado a ONNX, para utilização com o Windows ML.
- Exportação de modelo adicionado ao Dockerfile. Isto permite-lhe transferir os artefactos para criar os seus próprios contentores do Windows ou Linux, incluindo um DockerFile, modelo do TensorFlow e código de serviço.
- Para modelos treinados recentemente exportados para TensorFlow nos domínios geral (compacto) e de ponto de referência (compacto), os [valores médios agora são (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), para fins de consistência em todos os projetos.

## <a name="march-1-2018"></a>1 de março de 2018

- A versão prévia paga foi inserida e integrada à portal do Azure. Os projetos podem agora ser anexados a recursos do Azure com um escalão F0 (Gratuito) ou S0 (Standard). Os projetos de escalão S0 foram introduzidos, o que permite um máximo de 100 etiquetas e 25.000 imagens.
- Back-end muda para o parâmetro de pipeline/normalização de aprendizagem automática. Isto dará aos clientes um melhor controlo do equilíbrio de revocação de precisão ao ajustar o Limiar de Probabilidade. Como parte destas alterações, o Limiar de Probabilidade predefinido no portal CustomVision.ai foi definido como 50%.

## <a name="december-19-2017"></a>19 de dezembro de 2017

- Exportar para o Android (TensorFlow) adicionado, além da exportação lançada anteriormente para iOS (CoreML.) Isto permite que a exportação de um modelo compacto preparado seja executado offline numa aplicação.
- Domínios "compactos" de Retalho e de Ponto de Referência adicionados para permitir a exportação de modelos para estes domínios.
- Versão de lançamento [1.2 API de Preparação](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [1.1 API de Predição](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas suportam a exportação de modelo, uma nova operação de Predição que não guarda imagens em "Predições" e introduziu operações em lote na API de Preparação.
- Ajustes na experiência do utilizador, incluindo a capacidade de ver qual foi o domínio utilizado para preparar uma iteração.
- [Exemplo e SDK de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows) atualizado.