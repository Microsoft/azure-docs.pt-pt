---
title: Notas de lançamento para extensão de aplicativo web Azure - Insights de Aplicação
description: Lança notas para extensão de aplicações web Azure para instrumentação de tempo de execução com Insights de Aplicação.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: b9da3b0647d5abe60dd8f6cb21163ff21f07205b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539725"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Notas de lançamento para extensão da App Web Azure para Insights de Aplicações

Este artigo contém as notas de lançamento para Azure Web Apps Extension para instrumentação de tempo de execução com Insights de aplicação. Isto aplica-se apenas às extensões pré-instaladas.

[Saiba](azure-web-apps.md) mais sobre a extensão da aplicação web Azure para insights de aplicações.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

- Como descobrir qual é a versão da extensão em que estou atualmente?
    - Aceda a `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Visite [o guia de resolução de problemas passo a passo para monitorização baseada em extensões/agentes](./azure-web-apps.md?tabs=net#troubleshooting) para obter mais informações.

- E se eu estiver a usar extensões privadas?
    - Desinstale as extensões privadas do site uma vez que já não é suportado.

## <a name="release-notes"></a>Notas de versão

### <a name="2833"></a>2.8.33

- .NET, .NET core, Java e Node.js agentes e a extensão do Windows: Suporte para nuvens soberanas. As cordas de ligações podem ser usadas para enviar dados para nuvens soberanas.

### <a name="2831"></a>2.8.31

- ASP.NET Agente Principal: Problema fixo relacionado com uma das referências atualizadas da SDK (ver edições conhecidas para 2.8.26). Se a versão incorreta `System.Diagnostics.DiagnosticSource.dll` já estiver carregada por tempo de execução, a extensão sem código agora não vai falhar a aplicação e recuar. Para os clientes afetados por este problema, é aconselhável remover a `System.Diagnostics.DiagnosticSource.dll` pasta do caixote do lixo ou utilizar a versão mais antiga da extensão, definindo "ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24"; caso contrário, a monitorização da aplicação não está ativada.

### <a name="2826"></a>2.8.26

- ASP.NET Agente Core: Problema fixo relacionado com a atualização da SDK de Insights de Aplicação. O agente não tentará carregar `AiHostingStartup` se o ApplicationInsights.dll já estiver presente na pasta do lixo. Isto resolve questões relacionadas com a reflexão através da \<AiHostingStartup\> Assembleia. GetTypes().
- Questões conhecidas: A exceção `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` poderia ser lançada se outra versão do `DiagnosticSource` dll for carregada. Isto pode acontecer, por exemplo, se `System.Diagnostics.DiagnosticSource.dll` estiver presente na pasta de publicação. Como mitigação, utilize a versão anterior da extensão definindo as definições de aplicações nos serviços de aplicações: ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24.

### <a name="2824"></a>2.8.24

- Versão reembalada de 2.8.21.

### <a name="2823"></a>2.8.23

- Adicionado ASP.NET suporte de monitorização codificante Core 3.0.
- Atualizado ASP.NET Core SDK para [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) para as versões de tempo de execução 2.1, 2.2 e 3.0. As aplicações direcionadas para .NET Core 2.0 continuam a utilizar 2.1.1 do SDK.

### <a name="2814"></a>2.8.14

- Atualizado ASP.NET versão Core SDK de 2.3.0 para a mais recente (2.6.1) para aplicações direcionadas para .NET Core 2.1, 2.2. As aplicações direcionadas para .NET Core 2.0 continuam a utilizar 2.1.1 do SDK.

### <a name="2812"></a>2.8.12

- Suporte para ASP.NET aplicações Core 2.2.
- Corrigiu um erro na extensão do núcleo ASP.NET causando a injeção de SDK mesmo quando a aplicação já está instrumentada com o SDK. Para as aplicações 2.1 e 2.2, a presença de ApplicationInsights.dll na pasta da aplicação faz com que a extensão recue. Para aplicações 2.0, a extensão só recua se o ApplicationInsights estiver ativado com uma `UseApplicationInsights()` chamada.

- Correção permanente para resposta HTML incompleta para ASP.NET aplicações Core. Esta correção é agora alargada ao trabalho das aplicações .NET Core 2.2.

- Suporte adicionado para desligar a injeção javaScript para ASP.NET aplicações Core `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` (). Para ASP.NET núcleo, a injeção JavaScript está no modo "Opt-Out" por predefinição, a menos que seja explicitamente desligada. (A definição padrão é feita para manter o comportamento atual.)

- Fixo ASP.NET falha de extensão do núcleo que causou a injeção mesmo que o ikey não estivesse presente.
- Corrigiu um bug na lógica do prefixo da versão SDK que causou uma versão SDK incorreta na telemetria.

- Adicionou prefixo de versão SDK para ASP.NET aplicações Core para identificar como a telemetria foi recolhida.
- Página Fixa SCM- AplicaçõesInsights para mostrar corretamente a versão da extensão pré-instalada.

### <a name="2810"></a>2.8.10

- Correção para resposta HTML incompleta para ASP.NET aplicações Core.

## <a name="next-steps"></a>Passos seguintes

- Visite a documentação do [Azure App Service](azure-web-apps.md) para obter mais informações sobre como configurar a monitorização dos Serviços de Aplicações Azure. 
