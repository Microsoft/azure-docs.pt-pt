---
title: Visão geral da documentação da análise do código de segurança da Microsoft
description: Este artigo é uma visão geral da extensão da Análise de Código de Segurança da Microsoft
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
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851473"
---
# <a name="about-microsoft-security-code-analysis"></a>Sobre a Análise de Código de Segurança da Microsoft

Com a extensão da Análise de Código de Segurança da Microsoft, as equipas podem adicionar análise de código de segurança aos seus oleodutos de integração e entrega contínua seleções Azure DevOps (CI/CD). Esta análise é recomendada pelos especialistas do [Secure Development Lifecycle (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) na Microsoft.

Um UX consistente simplifica a segurança escondendo a complexidade das ferramentas de funcionamento. Com a entrega baseada em NuGet das ferramentas, as equipas já não precisam de gerir a instalação ou a tualização de ferramentas. Com interfaces de linha de comando e interfaces básicas para tarefas de construção, todos os utilizadores podem ter o controlo máximo sobre as ferramentas que quiserem.

As equipas também podem usar poderosas capacidades de pós-processamento tais como:

- Publicar registos para retenção.
- Gerando relatórios acionáveis e focados no desenvolvimento.
- Configurar quebras de construção em testes de regressão.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Por que devo usar a Análise do Código de Segurança da Microsoft?

### <a name="security-simplified"></a>Segurança simplificada

Adicionar ferramentas de análise de código de segurança da Microsoft ao seu pipeline Azure DevOps é tão simples como adicionar novas tarefas. Personalize as tarefas ou use o seu comportamento padrão. As tarefas funcionam como parte do seu oleoduto Azure DevOps e produzem registos que detalham muitos tipos de resultados.

### <a name="clean-builds"></a>Construções limpas

Depois de abordar os problemas iniciais relatados pelas ferramentas, pode configurar a extensão para quebrar as construções em novos problemas.A criação de uma integração contínua baseia-se em cada pedido de atração é fácil.

### <a name="set-it-and-forget-it"></a>Despumre-o e esqueça-o

Por padrão, as tarefas de construção e as ferramentas mantêm-se atualizadas. Se houver uma versão atualizada de uma ferramenta, não precisa de descarregar e instalar. A extensão cuida da atualização para si.

### <a name="under-the-hood"></a>Os bastidores

As tarefas de construção da extensão escondem as complexidades de:
  - Ferramentas de análise estática de segurança.
  - Processar os resultados dos ficheiros de registo para criar um relatório sumário ou quebrar a construção.

## <a name="microsoft-security-code-analysis-tool-set"></a>Conjunto de ferramentas de análise de código de segurança da Microsoft

A extensão de Análise de Código de Segurança da Microsoft disponibiliza prontamente as versões mais recentes de importantes ferramentas de análise. A extensão inclui ferramentas geridas pela Microsoft e ferramentas de código aberto.

Estas ferramentas são automaticamente descarregadas para o agente hospedado na nuvem depois de utilizar a tarefa de construção correspondente para configurar e executar o pipeline.

Esta secção lista o conjunto de ferramentas que estão atualmente disponíveis na extensão. Tenha cuidado com a adição de mais ferramentas. Além disso, envie-nos as suas sugestões para ferramentas que pretende que adicionemos.

### <a name="anti-malware-scanner"></a>Anti-Malware Scanner

A tarefa de construção do Scanner Anti-Malware está agora incluída na extensão de análise de código de segurança da Microsoft. Esta tarefa deve ser executada num agente de construção que já tenha o Windows Defender instalado. Para mais informações, consulte o [site do Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim é um scanner leve executável portátil (PE) que valida definições de compilador, definições de linker e outras características relevantes para a segurança de ficheiros binários. Esta tarefa de construção fornece um invólucro de linha de comando em torno da aplicação de consola binskim.exe. BinSkim é uma ferramenta de código aberto. Para mais informações, consulte [BinSkim no GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Scanner credencial

Palavras-passe e outros segredos armazenados no código fonte são um problema significativo. O Credential Scanner é uma ferramenta de análise estática proprietária que ajuda a resolver este problema. A ferramenta deteta credenciais, segredos, certificados e outros conteúdos sensíveis no seu código fonte e na sua saída de construção.

### <a name="microsoft-security-risk-detection"></a>Deteção de risco de segurança da Microsoft

Microsoft Security Risk Detection (MSRD) é um serviço baseado na nuvem para testes de difusão. Identifica bugs de segurança exploráveis em software. Este serviço requer uma subscrição e ativação separadas. Para mais informações, consulte o Centro de Desenvolvimento da [MSRD](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Analisadores Roslyn

Roslyn Analisers é a ferramenta integrada no compiler da Microsoft para analisar estáticamente o código C# e Visual Basic gerido. Para mais informações, consulte [os analisadores baseados em Roslyn.](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint é uma ferramenta de análise estática extensível que verifica o código TypeScript para a legibilidade, manutenção e erros na funcionalidade. É amplamente apoiado por editores modernos e sistemas de construção. Pode personalizá-lo com as suas próprias regras, configurações e forassuntos. TSLint é uma ferramenta de código aberto. Para mais informações, consulte [tSLint no GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Análise e pós-processamento de resultados

A extensão de Análise de Código de Segurança da Microsoft também tem três tarefas pós-processamento. Estas tarefas ajudam-no a analisar os resultados encontrados pelas tarefas da ferramenta de segurança. Quando adicionadas a um pipeline, estas tarefas geralmente seguem todas as outras tarefas da ferramenta.

### <a name="publish-security-analysis-logs"></a>Publicar registos de análise de segurança

A tarefa de construção de registos de análise de segurança de publicação preserva os ficheiros de registo das ferramentas de segurança que são executadas durante a construção. Você pode ler estes registos para investigação e acompanhamento.

Pode publicar os ficheiros de registo na Azure Artifacts como ficheiro .zip. Também pode copiá-los para uma partilha de ficheiroacessível do seu agente de construção privada.

### <a name="security-report"></a>Relatório de Segurança

O Relatório de Segurança constrói tarefas que analisa os ficheiros de registo. Estes ficheiros são criados pelas ferramentas de segurança que funcionam durante a construção. A tarefa de construção cria então um único ficheiro de relatório sumário. Este ficheiro mostra todos os problemas encontrados pelas ferramentas de análise.

Pode configurar esta tarefa para reportar resultados para ferramentas específicas ou para todas as ferramentas. Também pode escolher qual o nível de emissão a relatar, como apenas erros ou erros e avisos.

### <a name="post-analysis-build-break"></a>Pós-Análise (quebra de construção)

Com a tarefa de construção pós-análise, pode injetar uma rutura de construção que propositadamente faz com que uma construção falhe. Injeta-se uma rutura de construção se uma ou mais ferramentas de análise reportarem problemas no código.

Pode configurar esta tarefa para quebrar a construção para problemas encontrados por ferramentas específicas ou todas as ferramentas. Também pode configurá-lo com base na gravidade dos problemas encontrados, tais como erros ou avisos.

>[!NOTE]
>Por design, cada tarefa de construção tem sucesso se a tarefa terminar com sucesso. Isto é verdade se uma ferramenta encontra ou não problemas, para que a construção possa correr até à conclusão, permitindo que todas as ferramentas possam funcionar.

## <a name="next-steps"></a>Passos seguintes

Para obter instruções sobre como embarcar e instalar a Análise do Código de Segurança da Microsoft, consulte o nosso guia de [embarque e instalação](security-code-analysis-onboard.md).

Para mais informações sobre a configuração das tarefas de construção, consulte o nosso guia de [configuração](security-code-analysis-customize.md) ou [o guia de configuração YAML](yaml-configuration.md).

Se tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, consulte a nossa [página de PERGUNTAS FAQ](security-code-analysis-faq.md).
