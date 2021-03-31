---
title: Integração contínua e implantação para Azure Stream Analytics
description: Este artigo fornece uma visão geral de um pipeline de integração e implantação contínua (CI/CD) para a Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 940813f12d542715db47781731144a75e854a98e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019572"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Integração e implantação contínuas (CI/CD) para Azure Stream Analytics

Pode implementar continuamente o seu trabalho Azure Stream Analytics utilizando a integração do controlo de fontes. A integração do controlo de origem permite um fluxo de trabalho no qual uma atualização de código desencadeia uma implementação de recursos para o Azure. Este artigo descreve os passos básicos para a criação de um gasoduto de integração e implantação contínua (CI/CD).

Se é novo no Azure Stream Analytics, começa com o [quickstart Azure Stream Analytics](stream-analytics-quick-create-portal.md).

## <a name="create-a-cicd-pipeline"></a>Criar um pipeline de CI/CD

Siga os passos deste guia para criar um pipeline CI/CD para stream analytics.

1. Desenvolver uma consulta Azure Stream Analytics.

   Utilize ferramentas Azure Stream Analytics para [o Visual Studio Code](./quick-create-visual-studio-code.md) ou o Visual [Studio](stream-analytics-quick-create-vs.md) para desenvolver e testar [consultas localmente.](develop-locally.md) Você também pode [exportar um emprego existente](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) para um projeto local.

2. Comprometa os seus projetos Azure Stream Analytics ao seu sistema de controlo de fontes, como um repositório de Git.

3. Utilize [ferramentas CI/CD do Azure Stream Analytics](cicd-tools.md) para construir os projetos e gerar modelos de gestão de recursos Azure para a implementação.

4. Executar [testes de script automatizados](cicd-tools.md#automated-test) para regressão de qualidade.

5. [Desloque o trabalho](cicd-tools.md#deploy-to-azure) para Azure automaticamente.

## <a name="auto-build-test-and-deploy"></a>Construção automática, teste e implantação

Pode utilizar a linha de comando e as [ferramentas Azure Stream Analytics CI/CD](cicd-tools.md) para construir, testar e implantar automaticamente. Também pode configurar um gasoduto CI/CD em [Gasodutos Azure](set-up-cicd-pipeline.md). Azure Pipelines para permitir capacidades mais avançadas, tais como gestão de gasodutos, visualização e gatilhos.

## <a name="next-steps"></a>Passos seguintes

* [Automatizar construções, testes e implementações de um trabalho Azure Stream Analytics utilizando ferramentas CI/CD](cicd-tools.md)
* [Crie um pipeline CI/CD para trabalho stream analytics utilizando gasodutos Azure](set-up-cicd-pipeline.md)