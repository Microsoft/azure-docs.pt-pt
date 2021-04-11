---
title: Visão geral da documentação da análise do código de segurança do Microsoft
description: Saiba mais sobre a extensão de Análise de Código de Segurança da Microsoft. Com esta extensão, pode adicionar análise de código de segurança aos gasodutos Azure DevOps CI/ID.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801201"
---
# <a name="about-microsoft-security-code-analysis"></a>Sobre a Análise do Código de Segurança da Microsoft

> [!Note]
> A partir de 1 de março de 2022, a extensão do Código de Segurança da Microsoft (MSCA) será retirada. Os clientes MSCA existentes manterão o seu acesso à MSCA até 1 de março de 2022. Consulte as [Ferramentas de Análise de Código Fonte OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) para obter opções alternativas em Azure DevOps. Para clientes que planeiam migrar para o GitHub, você pode verificar [GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Com a extensão de Análise de Código de Segurança da Microsoft, as equipas podem adicionar análise de código de segurança aos seus oleodutos de integração e entrega contínuas de Azure DevOps (CI/CD). Esta análise é recomendada pelos especialistas do [Secure Development Lifecycle (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) da Microsoft.

Um UX consistente simplifica a segurança escondendo a complexidade das ferramentas de execução. Com a entrega das ferramentas baseadas no NuGet, as equipas deixaram de precisar de gerir a instalação ou atualização da ferramenta. Com linhas de comando e interfaces básicas para criar tarefas, todos os utilizadores podem ter o máximo de controlo sobre as ferramentas que quiserem.

As equipas também podem usar poderosas capacidades de pós-processamento, tais como:

- Publicar registos para retenção.
- Gerando relatórios accuáveis e focados no desenvolvedor.
- Configurar ruturas de construção nos testes de regressão.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Por que devo usar a Análise de Código de Segurança do Microsoft?

### <a name="security-simplified"></a>Segurança simplificada

Adicionar ferramentas de Análise de Código de Segurança do Microsoft ao seu pipeline Azure DevOps é tão simples como adicionar novas tarefas. Personalize as tarefas ou use o seu comportamento padrão. As tarefas são executadas como parte do seu oleoduto Azure DevOps e produzem registos que detalham muitos tipos de resultados.

### <a name="clean-builds"></a>Construções limpas

Depois de abordar os problemas iniciais relatados pelas ferramentas, pode configurar a extensão para quebrar as construções em novos problemas. A criação de uma integração contínua baseia-se em cada pedido de puxar é fácil.

### <a name="set-it-and-forget-it"></a>Desema bem.

Por predefinição, as tarefas e ferramentas de construção mantêm-se atualizadas. Se houver uma versão atualizada de uma ferramenta, não precisa de descarregar e instalar. A extensão trata da atualização para si.

### <a name="under-the-hood"></a>Os bastidores

As tarefas de construção da extensão escondem as complexidades de:
  - A executar ferramentas de análise estática de segurança.
  - Processar os resultados dos ficheiros de registo para criar um relatório sumário ou quebrar a construção.

## <a name="microsoft-security-code-analysis-tool-set"></a>Conjunto de ferramentas de análise de código de segurança da Microsoft

A extensão de Análise de Código de Segurança da Microsoft disponibiliza facilmente as versões mais recentes de importantes ferramentas de análise. A extensão inclui ferramentas geridas pela Microsoft e ferramentas de código aberto.

Estas ferramentas são automaticamente descarregadas para o agente hospedado na nuvem depois de utilizar a tarefa de construção correspondente para configurar e executar o pipeline.

Esta secção lista o conjunto de ferramentas que estão atualmente disponíveis na extensão. Atenção à adição de mais ferramentas. Além disso, envie-nos as suas sugestões para ferramentas que deseja que acrescentemos.

### <a name="anti-malware-scanner"></a>Anti-Malware Scanner

A tarefa de construção do Scanner Anti-Malware está agora incluída na extensão de Análise de Código de Segurança da Microsoft. Esta tarefa deve ser executada num agente de construção que já tenha o Windows Defender instalado. Para mais informações, consulte o site do [Windows Defender.](https://aka.ms/defender)

### <a name="binskim"></a>BinSkim

BinSkim é um scanner leve executável portátil (PE) que valida as definições do compilador, as definições do linker e outras características relevantes para a segurança dos ficheiros binários. Esta tarefa de construção fornece um invólucro de linha de comando em torno da aplicação binskim.exe consola. BinSkim é uma ferramenta de código aberto. Para mais informações, consulte [BinSkim no GitHub.](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Scanner credencial

As palavras-passe e outros segredos armazenados no código-fonte são um problema significativo. O Scanner Credencial é uma ferramenta de análise estática proprietária que ajuda a resolver este problema. A ferramenta deteta credenciais, segredos, certificados e outros conteúdos sensíveis no seu código fonte e na sua saída de construção.

### <a name="roslyn-analyzers"></a>Analisadores Roslyn

Roslyn Analyzers é a ferramenta integrada no compilador da Microsoft para analisar esteticamente o código C# e Visual Basic gerido. Para mais informações, consulte [os analisadores baseados em Roslyn.](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings)

### <a name="tslint"></a>TSLint

TSLint é uma ferramenta de análise estática extensível que verifica código TypeScript para legibilidade, manutenção e erros na funcionalidade. É amplamente apoiado por editores modernos e sistemas de construção. Você pode personalizá-lo com suas próprias regras de forto, configurações e formatters. TSLint é uma ferramenta de código aberto. Para mais informações, consulte [o TSLint no GitHub.](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>Análise e pós-processamento de resultados

A extensão de Análise de Código de Segurança da Microsoft também tem três tarefas pós-processamento. Estas tarefas ajudam-no a analisar os resultados encontrados pelas tarefas da ferramenta de segurança. Quando adicionadas a um oleoduto, estas tarefas geralmente seguem todas as outras tarefas da ferramenta.

### <a name="publish-security-analysis-logs"></a>Publicar registos de análise de segurança

A tarefa de construção de Registos de Análise de Segurança de Publicação preserva os ficheiros de registo das ferramentas de segurança que são executadas durante a construção. Pode ler estes registos para investigação e acompanhamento.

Pode publicar os ficheiros de registo para Azure Artifacts como um ficheiro .zip. Também pode copiá-los para uma partilha de ficheiros acessível do seu agente de construção privado.

### <a name="security-report"></a>Relatório de Segurança

O Relatório de Segurança constrói a análise dos ficheiros de registo. Estes ficheiros são criados pelas ferramentas de segurança que funcionam durante a construção. A tarefa de construção cria então um único ficheiro de relatório sumário. Este ficheiro mostra todos os problemas encontrados pelas ferramentas de análise.

Pode configurar esta tarefa para reportar resultados para ferramentas específicas ou para todas as ferramentas. Também pode escolher o nível de emissão a reportar, como erros apenas ou ambos erros e avisos.

### <a name="post-analysis-build-break"></a>Pós-Análise (rutura de construção)

Com a tarefa de construção pós-análise, você pode injetar uma rutura de construção que propositadamente faz com que uma construção falhe. Injeta-se uma rutura de construção se uma ou mais ferramentas de análise reportarem problemas no código.

Pode configurar esta tarefa para quebrar a construção para problemas encontrados por ferramentas específicas ou todas as ferramentas. Também pode configurgê-lo com base na gravidade dos problemas encontrados, tais como erros ou avisos.

>[!NOTE]
>Por design, cada tarefa de construção tem sucesso se a tarefa terminar com sucesso. Isto é verdade se uma ferramenta encontra ou não problemas, para que a construção possa ser concluída permitindo que todas as ferramentas sejam executadas.

## <a name="next-steps"></a>Passos seguintes

Para obter instruções sobre como embarcar e instalar a Microsoft Security Code Analysis, consulte o nosso [guia de embarque e instalação](security-code-analysis-onboard.md).

Para obter mais informações sobre a configuração das tarefas de construção, consulte o nosso [guia de configuração](security-code-analysis-customize.md) ou [guia de configuração YAML](yaml-configuration.md).

Se tiver mais perguntas sobre a extensão e as ferramentas oferecidas, consulte a nossa [página de PERGUNTAS Frequentes.](security-code-analysis-faq.md)