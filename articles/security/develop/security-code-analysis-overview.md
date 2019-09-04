---
title: Visão geral da documentação de análise de código de segurança da Microsoft
description: Este artigo é uma visão geral da extensão de análise de código de segurança da Microsoft
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a7ab908673f60a651db48ae0485592a362741b6d
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242308"
---
# <a name="about-microsoft-security-code-analysis"></a>Sobre a análise de código de segurança da Microsoft

Com a extensão de análise de código de segurança da Microsoft, as equipes podem adicionar análise de código de segurança aos pipelines de CI/CD (integração e entrega contínuas) do Azure DevOps. Essa análise é recomendada pelos especialistas do [SDL (ciclo de vida de desenvolvimento seguro)](https://www.microsoft.com/securityengineering/sdl/practices) da Microsoft.

Uma UX consistente simplifica a segurança, ocultando a complexidade das ferramentas em execução. Com a entrega baseada em NuGet das ferramentas, as equipes não precisam mais gerenciar a instalação ou a atualização de ferramentas. Com as interfaces básica e de linha de comando para tarefas de compilação, todos os usuários podem ter o máximo de controle sobre as ferramentas que desejarem.

As equipes também podem usar poderosos recursos de pré-processamento, como:

- Publicando logs para retenção.
- Geração de relatórios acionáveis, focados no desenvolvedor.
- Configuração de quebras de compilação em testes de regressão.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Por que devo usar a análise de código de segurança da Microsoft?

### <a name="security-simplified"></a>Segurança simplificada

Adicionar ferramentas de análise de código de segurança da Microsoft ao pipeline DevOps do Azure é tão simples quanto adicionar novas tarefas. Personalize as tarefas ou use seu comportamento padrão. As tarefas são executadas como parte do pipeline DevOps do Azure e produzem logs que detalham muitos tipos de resultados.

### <a name="clean-builds"></a>Limpar compilações

Depois de abordar os problemas iniciais relatados pelas ferramentas, você pode configurar a extensão para interromper as compilações sobre novos problemas. A configuração de compilações de integração contínua em todas as solicitações pull é fácil.

### <a name="set-it-and-forget-it"></a>Defini-lo e esquecê-lo

Por padrão, as tarefas e as ferramentas de compilação permanecem atualizadas. Se houver uma versão atualizada de uma ferramenta, você não precisará baixá-la e instalá-la. A extensão cuida da atualização para você.

### <a name="under-the-hood"></a>Nos bastidores

As tarefas de compilação da extensão ocultam as complexidades de:
  - Executando ferramentas de análise estática de segurança.
  - Processando os resultados de arquivos de log para criar um relatório de resumo ou interromper a compilação.

## <a name="microsoft-security-code-analysis-tool-set"></a>Conjunto de ferramentas de análise de código de segurança da Microsoft

A extensão de análise de código de segurança da Microsoft torna as versões mais recentes de ferramentas de análise importantes disponíveis para você. A extensão inclui ferramentas gerenciadas pela Microsoft e ferramentas de código-fonte aberto.

Essas ferramentas são baixadas automaticamente para o agente hospedado na nuvem depois que você usa a tarefa de compilação correspondente para configurar e executar o pipeline.

Esta seção lista o conjunto de ferramentas que estão disponíveis atualmente na extensão. Assista à adição de mais ferramentas. Além disso, envie-nos suas sugestões para as ferramentas que você deseja adicionar.

### <a name="anti-malware-scanner"></a>Scanner antimalware

A tarefa de compilação de scanner antimalware agora está incluída na extensão de análise de código de segurança da Microsoft. Essa tarefa deve ser executada em um agente de compilação que já tenha o Windows Defender instalado. Para obter mais informações, consulte o [site do Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

O BinSkim é um scanner leve executável (PE) que valida as configurações do compilador, as configurações do vinculador e outras características relevantes de segurança dos arquivos binários. Essa tarefa de compilação fornece um wrapper de linha de comando em torno do aplicativo de console binskim. exe. BinSkim é uma ferramenta de código-fonte aberto. Para obter mais informações, consulte [BinSkim no GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Verificador de credenciais

As senhas e outros segredos armazenados no código-fonte são um problema significativo. O verificador de credenciais é uma ferramenta de análise estática proprietária que ajuda a resolver esse problema. A ferramenta detecta credenciais, segredos, certificados e outros conteúdos confidenciais no código-fonte e na saída da compilação.

### <a name="microsoft-security-risk-detection"></a>Detecção de riscos de segurança da Microsoft

A MSRD (detecção de riscos de segurança da Microsoft) é um serviço baseado em nuvem para teste de fuzzing. Ele identifica bugs de segurança exploráveis no software. Esse serviço requer um processo de integração separado. Para obter mais informações, consulte o [centro de desenvolvedores do MSRD](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Analisadores de Roslyn

Analisadores de Roslyn é a ferramenta integrada ao compilador da Microsoft para analisar estaticamente C# o código gerenciado e Visual Basic. Para obter mais informações, consulte [analisadores baseados em Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/).

### <a name="tslint"></a>TSLint

TSLint é uma ferramenta de análise estática extensível que verifica o código TypeScript para facilitar a leitura, a manutenção e os erros na funcionalidade. É amplamente compatível com editores modernos e sistemas de compilação. Você pode personalizá-lo com suas próprias regras de refiapo, configurações e formatadores. TSLint é uma ferramenta de código-fonte aberto. Para obter mais informações, consulte [TSLint no GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Análise e pós-processamento de resultados

A extensão de análise de código de segurança da Microsoft também tem três tarefas de pré-processamento. Essas tarefas ajudam a analisar os resultados encontrados pelas tarefas de ferramenta de segurança. Quando adicionado a um pipeline, essas tarefas geralmente seguem todas as outras tarefas de ferramenta.

### <a name="publish-security-analysis-logs"></a>Publicar logs de análise de segurança

A tarefa publicar logs de análise de segurança preserva os arquivos de log das ferramentas de segurança executadas durante a compilação. Você pode ler esses logs para investigação e acompanhamento.

Você pode publicar os arquivos de log para Azure Artifacts como um arquivo. zip. Você também pode copiá-los para um compartilhamento de arquivos acessível do seu agente de compilação particular.

### <a name="security-report"></a>Relatório de segurança

A tarefa de compilação de relatório de segurança analisa os arquivos de log. Esses arquivos são criados pelas ferramentas de segurança executadas durante a compilação. Em seguida, a tarefa de compilação cria um único arquivo de relatório de resumo. Esse arquivo mostra todos os problemas encontrados pelas ferramentas de análise.

Você pode configurar essa tarefa para relatar resultados para ferramentas específicas ou para todas as ferramentas. Você também pode escolher o nível de problema a ser relatado, como somente erros ou erros e avisos.

### <a name="post-analysis-build-break"></a>Pós-análise (quebra de compilação)

Com a tarefa de compilação após a análise, você pode injetar uma quebra de compilação que faz com que uma compilação falhe. Você injetará uma quebra de compilação se uma ou mais ferramentas de análise relatarem problemas no código.

Você pode configurar essa tarefa para interromper a compilação em busca de problemas encontrados por ferramentas específicas ou por todas as ferramentas. Você também pode configurá-lo com base na gravidade dos problemas encontrados, como erros ou avisos.

>[!NOTE]
>Por design, cada tarefa de compilação é bem-sucedida se a tarefa for concluída com êxito. Isso é verdadeiro se uma ferramenta encontra ou não problemas, para que a compilação possa ser executada até a conclusão, permitindo que todas as ferramentas sejam executadas.

## <a name="next-steps"></a>Passos Seguintes

Para obter instruções sobre como carregar e instalar a análise de código de segurança da Microsoft, consulte nosso [Guia de integração e instalação](security-code-analysis-onboard.md).

Para obter mais informações sobre como configurar as tarefas de compilação, consulte nosso [Guia de configuração](security-code-analysis-customize.md).

Se você tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, Confira nossa [página de perguntas frequentes](security-code-analysis-faq.md).
