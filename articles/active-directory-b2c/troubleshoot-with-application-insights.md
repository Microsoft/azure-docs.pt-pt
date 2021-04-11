---
title: Resolução de problemas de políticas personalizadas com Insights de Aplicação
titleSuffix: Azure AD B2C
description: Como configurar a Application Insights para rastrear a execução das suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028748"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Recolher registos B2C do Diretório Ativo Azure com Insights de Aplicação

Este artigo fornece passos para a recolha de registos do Ative Directory B2C (Azure AD B2C) para que possa diagnosticar problemas com as suas políticas personalizadas. O Application Insights disponibiliza uma forma de diagnosticar exceções e ver problemas de desempenho da aplicação. O AAD B2C inclui uma funcionalidade para enviar dados para o Application Insights.

Os registos de atividades detalhados aqui descritos devem ser ativados **apenas** durante o desenvolvimento das suas políticas personalizadas.

> [!WARNING]
> Não coloque o `DeploymentMode` ponto `Development` em ambientes de produção. Os registos recolhem todas as reclamações enviadas de e para fornecedores de identidade. O desenvolvedor assume a responsabilidade por quaisquer dados pessoais recolhidos nos registos do Application Insights. Estes registos detalhados só são recolhidos quando a política é colocada em **MODO DEVELOPER**.

## <a name="set-up-application-insights"></a>Configurar Insights de Aplicação

Se ainda não tiver um, crie uma instância de Application Insights na sua subscrição.

> [!TIP]
> Uma única instância de Application Insights pode ser usada para vários inquilinos Azure AD B2C. Em seguida, na sua consulta, você pode filtrar pelo inquilino, ou nome de política. Para obter mais informações, [consulte os registos nas amostras de Insights de Aplicação.](#see-the-logs-in-application-insights)

Para utilizar uma instância de saída de Insights de Aplicação na sua subscrição, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém a sua subscrição Azure (não o seu diretório Azure AD B2C).
1. Abra o recurso Application Insights que criou anteriormente.
1. Na página **'Visão Geral'** e grave a **Chave de Instrumentação**

Para criar uma instância de Insights de Aplicação na sua subscrição, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém a sua subscrição Azure (não o seu diretório Azure AD B2C).
1. **Selecione Criar um recurso** no menu de navegação à esquerda.
1. Procure e selecione **Insights de Aplicação** e, em seguida, selecione **Criar**.
1. Preencha o formulário, **selecione 'Rever + criar'** e, em seguida, selecione **Criar**.
1. Uma vez concluída a implementação, selecione **Ir para o recurso**.
1. No menu **Configurar** no menu Application Insights, selecione **Propriedades**.
1. Grave a **chave de instrumentação** para utilização num passo posterior.

## <a name="configure-the-custom-policy"></a>Configure a política personalizada

1. Abra o ficheiro do partido de base (RP), por *exemplo,SignUpOrSignin.xml*.
1. Adicione os seguintes atributos ao `<TrustFrameworkPolicy>` elemento:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se já não existir, adicione um `<UserJourneyBehaviors>` nó de criança ao `<RelyingParty>` nó. Deve ser colocado `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` depois.
1. Adicione o nó seguinte como uma criança do `<UserJourneyBehaviors>` elemento. Certifique-se de que substitui a Chave de `{Your Application Insights Key}` Instrumentação de **Insights de Aplicação** que gravou anteriormente.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` diz à ApplicationInsights para acelerar a telemetria através do gasoduto de processamento. Bom para o desenvolvimento, mas limitado em grandes volumes. Em produção, definir `DeveloperMode` o `false` .
    * `ClientEnabled="true"` envia o script do lado do cliente ApplicationInsights para visualização da página de rastreio e erros do lado do cliente. Pode vê-las na tabela **browserTimings** no portal Application Insights. Ao `ClientEnabled= "true"` configurar, adicione Application Insights ao seu script de página e obtém os horários das cargas de página e chamadas AJAX, contagens, detalhes das exceções do navegador e falhas do AJAX, e contagens de utilizador e sessão. Este campo é **opcional**, e está definido `false` por padrão.
    * `ServerEnabled="true"` envia o Atual UserJourneyRecorder JSON como um evento personalizado para a Application Insights.

    Por exemplo:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Faça o upload da apólice.

## <a name="see-the-logs-in-application-insights"></a>Ver os registos em Insights de Aplicação

Há um curto atraso, normalmente menos de cinco minutos, antes de poder ver novos registos em Application Insights.

1. Abra o recurso Application Insights que criou no [portal Azure](https://portal.azure.com).
1. Na página **'Vista Geral',** selecione **Registos**.
1. Abra um novo separador em Application Insights.

Aqui está uma lista de consultas que pode usar para ver os registos:

| Consulta | Description |
|---------------------|--------------------|
| `traces` | Obtenha todos os registos gerados por Azure AD B2C |
| `traces | where timestamp > ago(1d)` | Obtenha todos os registos gerados pelo Azure AD B2C para o último dia.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Obtenha todos os registos com erros das últimas duas horas.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Obtenha todos os registos gerados pelo Azure AD B2C *contoso.onmicrosoft.com* inquilino, e a viagem do utilizador é *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| Obtenha todos os registos gerados pelo Azure AD B2C para uma identificação de correlação. Substitua o ID da correlação pelo seu ID de correlação. | 

As entradas podem ser longas. Exportar para CSV para um olhar mais atento.

Para obter mais informações sobre consultas, consulte [a visão geral das consultas de registo no Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-logs-in-vs-code-extension"></a>Consulte os registos na extensão do Código VS

Recomendamos que instale a [extensão Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) para [o Código VS](https://code.visualstudio.com/). Com a extensão Azure AD B2C, os registos são organizados para si pelo nome da política, iD de correlação (os insights da aplicação apresentam o primeiro dígito do ID de correlação) e a marca de tempo de registo. Esta funcionalidade ajuda-o a encontrar o registo relevante com base no timetamp local e ver a viagem do utilizador executada por Azure AD B2C.

> [!NOTE]
> A comunidade desenvolveu a extensão de código vs para Azure AD B2C para ajudar os desenvolvedores de identidade. A extensão não é suportada pela Microsoft, e é disponibilizada estritamente como está.

### <a name="set-application-insights-api-access"></a>Definir Informações de Acesso API

Depois de configurar os Insights de Aplicação e configurar a política personalizada, precisa de obter o **ID API** dos Seus Insights de Aplicação e criar **a Chave API**. Tanto a tecla API ID como a API são utilizadas pela extensão AZURE AD B2C para ler os eventos Application Insights (telemetria). As suas chaves API devem ser geridas como senhas. Mantenha-o em segredo.

> [!NOTE]
> A chave de instrumentação de Insights de Aplicação que a sua criação anterior é usada pela Azure AD B2C para enviar telemetrias para Insights de Aplicação. Utilize a tecla de instrumentação apenas na sua política Azure AD B2C, e não na extensão do código vs.

Para obter ID e chave de Insights de Aplicação:

1. No portal Azure, abra o recurso Application Insights para a sua aplicação.
1. Selecione **Definições** e, em seguida, selecione **API Access**.
1. Copiar o **ID da aplicação**
1. Selecione **Criar Chave API**
1. Verifique a caixa **de telemetria Read.**
1. Copie a **chave** antes de fechar a lâmina da chave API Create e guarde-a em algum lugar seguro. Se perder a chave, terá de criar outra.

    ![Screenshot que demonstra como criar a chave de acesso API.](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Configurar extensão do Código Azure AD B2C VS

Agora que tem informações sobre a aplicação API ID e Key, pode configurar a extensão de código vs para ler os registos. A extensão do código Azure AD B2C VS fornece dois âmbitos para as definições:

- **Configurações Globais do Utilizador** - Configurações que se aplicam globalmente a qualquer instância do Código VS que abre.
- **Definições de espaço de trabalho** - Configurações armazenadas dentro do seu espaço de trabalho e só se aplicam quando o espaço de trabalho é aberto (utilizando a pasta **aberta** do Código VS).

1. A partir do explorador **Azure AD B2C Trace,** clique no ícone **Definições.**

    ![Screenshot que demonstra selecionar as definições de insights de aplicação.](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Forneça o ID e **a chave** **Azure** Application Insights .
1. Clique em **Guardar**

Depois de guardar as definições, os registos de insights da Aplicação aparecem na janela **Azure AD B2C Trace (App Insights).**

![Screenshot da extensão AZURE AD B2C para vscode, apresentando o traço de insights da Aplicação Azure.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>Configure insights de aplicação na produção

Para melhorar o desempenho do seu ambiente de produção e uma melhor experiência do utilizador, é importante configurar a sua política para ignorar mensagens que não são importantes. Utilize a seguinte configuração para enviar apenas mensagens de erro críticas para o seu Insights de Aplicação. 

1. Desconfiem do `DeploymentMode` atributo da [TrustFrameworkPolicy](trustframeworkpolicy.md) para `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Defina `DeveloperMode` o [journeyInsights](relyingparty.md#journeyinsights) para `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. Faça upload e teste a sua política.



## <a name="next-steps"></a>Passos seguintes

- Saiba como [resolver as políticas personalizadas Azure AD B2C](troubleshoot-custom-policies.md)
