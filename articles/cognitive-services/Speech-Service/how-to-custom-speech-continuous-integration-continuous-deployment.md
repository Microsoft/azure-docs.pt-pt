---
title: CI/CD para Discurso Personalizado - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Aplicar DevOps com fluxos de trabalho de fala personalizada e ci/CD. Implemente uma solução de DevOps existente para o seu próprio projeto.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: d37fca06a3d2eafa0af9e31c3a30ac66be5404f0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020609"
---
# <a name="cicd-for-custom-speech"></a>CI/CD para Voz Personalizada

Implementar formação automatizada, teste e gestão de lançamento para permitir a melhoria contínua dos modelos de Discurso Personalizado à medida que aplica atualizações aos dados de treino e teste. Através de uma implementação eficaz dos fluxos de trabalho ci/CD, pode garantir que o ponto final para o modelo de Discurso Personalizado com melhor desempenho está sempre disponível.

[A integração contínua](/azure/devops/learn/what-is-continuous-integration) (CI) é a prática de engenharia de frequentemente comprometer atualizações num repositório partilhado, e realizar uma construção automatizada sobre ele. Os fluxos de trabalho da CI para Custom Speech treinam um novo modelo a partir das suas fontes de dados e realizam testes automatizados no novo modelo para garantir que este funciona melhor do que o modelo anterior.

[A entrega contínua](/azure/devops/learn/what-is-continuous-delivery) (CD) retira os modelos do processo de CI e cria um ponto final para cada modelo de Discurso Personalizado melhorado. O CD disponibiliza facilmente pontos finais para serem integrados em soluções.

As soluções personalizadas de CI/CD são possíveis, mas para uma solução robusta e pré-construída, utilize o [repositório de modelos DevOps de discurso,](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)que executa fluxos de trabalho CI/CD utilizando as ações do GitHub.

## <a name="cicd-workflows-for-custom-speech"></a>Fluxos de trabalho CI/CD para discurso personalizado

O objetivo destes fluxos de trabalho é garantir que cada modelo de Discurso Personalizado tenha uma melhor precisão de reconhecimento do que a construção anterior. Se as atualizações aos dados de teste e/ou treino melhorarem a precisão, estes fluxos de trabalho criam um novo ponto final de Discurso Personalizado.

Servidores git como GitHub e Azure DevOps podem executar fluxos de trabalho automatizados quando eventos específicos de Git acontecem, tais como fusões ou pedidos de puxar. Por exemplo, um fluxo de trabalho de CI pode ser desencadeado quando as atualizações aos dados de teste são empurradas para o ramo *principal.* Diferentes Servidores Git terão diferentes ferramentas, mas permitirão a scripting de interface de linha de comando (CLI) para que possam executar num servidor de construção.

Ao longo do caminho, os fluxos de trabalho devem nomear e armazenar dados, testes, ficheiros de teste, modelos e pontos finais de modo a que possam ser rastreados até ao compromisso ou versão de onde vieram. Também é útil nomear estes ativos de modo a que seja fácil ver quais foram criados após a atualização de dados de testes versus dados de formação.

### <a name="ci-workflow-for-testing-data-updates"></a>Fluxo de trabalho ci para testar atualizações de dados

O principal objetivo dos fluxos de trabalho ci/CD é construir um novo modelo utilizando os dados de formação e testar esse modelo utilizando os dados de teste para determinar se a [Taxa de Erro](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) de Palavra (WER) melhorou em comparação com o modelo anterior de melhor desempenho (o "modelo de referência"). Se o novo modelo tiver um melhor desempenho, torna-se o novo modelo de referência com o qual os futuros modelos são comparados.

O fluxo de trabalho do CI para testar atualizações de dados deve voltar a testar o modelo de referência atual com os dados de teste atualizados para calcular o WER revisto. Isto garante que quando o WER de um novo modelo é comparado com o WER do benchmark, ambos os modelos foram testados com os mesmos dados de teste e você está comparando como com similares.

Este fluxo de trabalho deve desencadear atualizações para testar dados e:

- Teste o modelo de referência com os dados de teste atualizados.
- Guarde a saída de teste, que contém o WER do modelo de referência, utilizando os dados atualizados.
- O WER destes testes tornar-se-á o novo WER de referência que os futuros modelos devem vencer.
- O fluxo de trabalho do CD não executa para atualizações para testar dados.

### <a name="ci-workflow-for-training-data-updates"></a>Fluxo de trabalho ci para atualizações de dados de formação

As atualizações aos dados de formação significam atualizações para o modelo personalizado.

Este fluxo de trabalho deve desencadear atualizações aos dados de formação e:

- Treine um novo modelo com os dados de formação atualizados.
- Teste o novo modelo contra os dados de teste.
- Guarde a saída de teste, que contém o WER.
- Compare o WER do novo modelo com o WER do modelo de referência.
- Se o WER não melhorar, pare o fluxo de trabalho.
- Se o WER melhorar, execute o fluxo de trabalho do CD para criar um ponto final de discurso personalizado.

### <a name="cd-workflow"></a>Fluxo de trabalho do CD

Após uma atualização dos dados de formação melhorar o reconhecimento de um modelo, o fluxo de trabalho do CD deve executar automaticamente para criar um novo ponto final para esse modelo, e disponibilizar esse ponto final de modo a que possa ser utilizado numa solução.

#### <a name="release-management"></a>Gestão de Versões

A maioria das equipas requer um processo manual de revisão e aprovação para implantação num ambiente de produção. Para uma implantação de produção, talvez queira certificar-se de que isso acontece quando pessoas-chave da equipa de desenvolvimento estão disponíveis para apoio, ou durante períodos de tráfego reduzido.

### <a name="tools-for-custom-speech-workflows"></a>Ferramentas para fluxos de trabalho de discurso personalizados

Utilize as seguintes ferramentas para fluxos de trabalho de automatização CI/CD para discursos personalizados:

- [Azure CLI](/cli/azure/?view=azure-cli-latest) para criar uma autenticação principal do serviço Azure, consultar subscrições Azure e armazenar resultados de testes em Azure Blob.
- [Azure Speech CLI](spx-overview.md) para interagir com o Serviço de Fala a partir da linha de comando ou um fluxo de trabalho automatizado.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>DevOps solução para discurso personalizado usando ações do GitHub

Para uma solução de DevOps já implementada para discurso personalizado, vá ao [repo do modelo DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)do Discurso. Crie uma cópia do modelo e inicie o desenvolvimento de modelos personalizados com um sistema robusto de DevOps que inclui testes, treino e versão utilizando ações do GitHub. O repositório fornece dados de testes e formação de amostras para ajudar na instalação e explicar o fluxo de trabalho. Após a configuração inicial, substitua os dados da amostra pelos dados do seu projeto.

O [repo do modelo DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) do Discurso fornece a infraestrutura e orientação detalhada para:

- Copie o repositório do modelo para a sua conta GitHub e, em seguida, crie recursos Azure e um [principal de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) para os fluxos de trabalho CI/CD das ações do GitHub.
- Caminhe pelo "[dev loop interior](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)." Atualizar dados de treino e teste de um ramo de recurso, testar as alterações com um modelo de desenvolvimento temporário, e levantar um pedido de pull para propor e rever as alterações.
- Quando os dados de formação são atualizados num pedido de puxar para *os modelos principais,* treine os modelos com o fluxo de trabalho CI das Ações GitHub.
- Realize testes de precisão automatizados para estabelecer a Taxa de Erro de [Texto](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy) (WER) de um modelo. Guarde os resultados dos testes em Azure Blob.
- Execute o fluxo de trabalho do CD para criar um ponto final quando o WER melhorar.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre DevOps com Discurso:

- Use o [repo do modelo de DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) de discurso para implementar DevOps para discurso personalizado com ações do GitHub.