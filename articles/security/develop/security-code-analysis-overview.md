---
title: Visão geral da documentação de análise de código de segurança Microsoft Azure
description: Este artigo é uma visão geral da extensão de análise de código de segurança
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718291"
---
# <a name="about-microsoft-security-code-analysis"></a>Sobre a análise de código de segurança da Microsoft

A **extensão de análise de código de segurança da Microsoft** capacita as equipes a integrar perfeitamente a análise de código de segurança em seus pipelines de CI/CD do Azure DevOps, conforme recomendado pelos especialistas do [SDL (ciclo de vida de desenvolvimento seguro)](https://www.microsoft.com/securityengineering/sdl/practices) da Microsoft. A segurança é simplificada por meio de uma UX consistente que abstrai as complexidades da execução de várias ferramentas. Com uma entrega baseada em NuGet das ferramentas, as equipes não precisam mais gerenciar a instalação ou a atualização das ferramentas. Com as interfaces de tarefa de compilação básica e de linha de comando, todos os usuários, de especialistas de ferramentas experientes a desenvolvedores diários, podem ter o mínimo ou maior controle sobre as ferramentas que desejarem. As equipes também podem aproveitar recursos avançados de pós-processamento, como a publicação de logs para retenção, gerando relatórios acionáveis focados em desenvolvedores & configurar quebras de compilação em regressões.

## <a name="why-microsoft-security-code-analysis"></a>Por que a análise de código de segurança da Microsoft

### <a name="security-simplified"></a>Segurança simplificada

Adicionar ferramentas de análise de código de segurança ao pipeline DevOps do Azure é tão simples quanto adicionar novas tarefas. Personalize-os ou vá com os padrões. As tarefas são executadas como parte do pipeline do DevOps e produzem logs que detalham todos os tipos de conclusões.

### <a name="clean-builds"></a>Limpar compilações

Depois de abordar os problemas iniciais relatados pelas ferramentas, você pode configurar a extensão para interromper as compilações sobre novos problemas. A configuração da compilação de integração contínua em cada solicitação de pull nunca foi tão fácil!

### <a name="set-it-and-forget-it"></a>Defini-lo e esquecê-lo

As tarefas e ferramentas de compilação podem ser definidas para se manter atualizadas (e são por padrão). Se houver uma versão atualizada da ferramenta, não será necessário baixá-la e instalá-la; Essa extensão cuida disso para você. 

>>>
### <a name="under-the-hood"></a>Nos bastidores

As tarefas de compilação da extensão de análise de código de segurança da Microsoft abstraim as complexidades de:
  - Executando ferramentas de análise estática de segurança e
  - Processando os resultados de arquivos de log para criar um relatório de resumo ou interromper a compilação.
>>>

## <a name="security-code-analysis-toolset"></a>Conjunto de ferramentas de análise de código de segurança

A extensão de análise de código de segurança da Microsoft torna-se prontamente disponível para você, as versões mais recentes das importantes ferramentas de análise. A extensão inclui ferramentas internas da Microsoft e de código aberto. As ferramentas são baixadas automaticamente no agente hospedado na nuvem depois que você configura & executar o pipeline usando a tarefa de compilação correspondente. Abaixo está o conjunto de ferramentas que estão disponíveis na extensão hoje. Fique atento a mais e envie-nos suas sugestões para ferramentas que poderiam ser adicionadas.

### <a name="anti-malware-scanner"></a>Scanner antimalware

A tarefa de compilação de scanner antimalware agora está incluída na extensão de análise de código de segurança da Microsoft. Ele deve ser executado em um agente de compilação que já tenha o Windows Defender instalado. Para obter mais informações, visite [site do defender](https://aka.ms/defender) 

### <a name="binskim"></a>BinSkim

BinSkim é um mecanismo de varredura leve PE (executável portátil) que valida as configurações de compilador/vinculador e outras características binárias relevantes de segurança. A tarefa de compilação fornece um wrapper de linha de comando em torno do aplicativo BinSkim. exe. BinSkim é uma ferramenta de código-fonte aberto e para obter mais informações, visite [BinSkim no GitHub](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Verificador de credenciais

As senhas e outros segredos armazenados no código-fonte são um problema significativo no momento. O verificador de credenciais é uma ferramenta de análise estática proprietária que detecta credenciais, segredos, certificados e outros conteúdos confidenciais no código-fonte e na saída da compilação.

### <a name="microsoft-security-risk-detection"></a>Detecção de riscos de segurança da Microsoft

A detecção de riscos de segurança é o serviço de teste de difusão exclusivo baseado em nuvem da Microsoft para identificar bugs de segurança exploráveis no software. Esse serviço requer um processo de integração separado. Para obter mais informações, visite [MSRD em docs.Microsoft.com](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Analisadores de Roslyn

A ferramenta de análise estática integrada ao compilador da Microsoft para analisar oC# código gerenciado (e o VB). Para obter mais informações, visite [Roslyn Analyzers on docs.Microsoft.com](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint é uma ferramenta de análise estática extensível que verifica o código TypeScript para facilitar a leitura, a manutenção e os erros de funcionalidade. Ele tem amplo suporte em editores modernos e sistemas de compilação e pode ser personalizado com suas próprias regras, configurações e formatadores de fiapos. TSLint é uma ferramenta de código-fonte aberto e para obter mais informações, visite [TSLint no GitHub](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>Análise e pós-processamento de resultados

A extensão de análise de código de segurança da Microsoft também tem três tarefas úteis de pós-processamento para ajudá-lo a processar e analisar os resultados encontrados pelas tarefas de ferramentas de segurança. Normalmente, eles são adicionados ao pipeline após todas as outras tarefas de ferramenta.

### <a name="publish-security-analysis-logs"></a>Publicar logs de análise de segurança
A tarefa publicar logs de análise de segurança preserva os arquivos de log das ferramentas de segurança executadas durante a compilação para investigação e acompanhamento.

Eles podem ser publicados nos artefatos do servidor do Azure (como um arquivo zip) ou cópias em um compartilhamento de arquivos acessível do seu agente de compilação particular.

### <a name="security-report"></a>Relatório de segurança
A tarefa de compilação de relatório de segurança analisa os arquivos de log criados pelas ferramentas de segurança executadas durante a compilação e cria um único arquivo de relatório de resumo com todos os problemas encontrados pelas ferramentas de análise.

A tarefa pode ser configurada para relatar resultados para ferramentas específicas ou para todas as ferramentas, e você também pode escolher qual nível de problemas (erros ou erros e avisos) deve ser relatado.

### <a name="post-analysis-build-break"></a>Pós-análise (quebra de compilação)
A tarefa de compilação após a análise permite que o cliente Insira uma quebra de compilação e falhe na compilação no caso de mais uma ou mais ferramentas de análise relatam conclusões ou problemas no código.

A tarefa pode ser configurada para interromper a compilação de problemas encontrados por ferramentas específicas ou para todas as ferramentas e também com base na gravidade dos problemas encontrados (erros ou avisos).

>[!NOTE]
>Tarefas de compilação individuais terão êxito, por design, desde que a ferramenta seja concluída com êxito, seja há conclusões ou não para que a compilação possa ser executada até a conclusão, permitindo que todas as ferramentas sejam executadas.

## <a name="next-steps"></a>Passos seguintes

Para obter instruções sobre a integração e a instalação da análise de código de segurança, consulte nosso [Guia de instalação e integração](security-code-analysis-onboard.md)

Para obter mais informações sobre como configurar as tarefas de compilação, consulte nosso [Guia de configuração](security-code-analysis-customize.md)

Se você tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, [Consulte nossa página perguntas frequentes.](security-code-analysis-faq.md)