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
ms.date: 10/12/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ddc0dc433a5d8c09c692e6304647fb391694e8c8
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993161"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Recolher registos B2C do Diretório Ativo Azure com Insights de Aplicação

Este artigo fornece passos para a recolha de registos do Ative Directory B2C (Azure AD B2C) para que possa diagnosticar problemas com as suas políticas personalizadas. O Application Insights fornece uma forma de diagnosticar exceções e visualizar problemas de desempenho da aplicação. O Azure AD B2C inclui uma funcionalidade para o envio de dados para o Application Insights.

Os registos de atividades detalhados aqui descritos devem ser ativados **apenas** durante o desenvolvimento das suas políticas personalizadas.

> [!WARNING]
> Não ative o modo de desenvolvimento na produção. Os registos recolhem todas as reclamações enviadas de e para fornecedores de identidade. O desenvolvedor assume a responsabilidade por quaisquer dados pessoais recolhidos nos registos do Application Insights. Estes registos detalhados só são recolhidos quando a política é colocada em **MODO DEVELOPER**.

## <a name="set-up-application-insights"></a>Configurar Insights de Aplicação

Se ainda não tiver um, crie uma instância de Application Insights na sua subscrição.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém a sua subscrição Azure (não o seu diretório Azure AD B2C).
1. **Selecione Criar um recurso** no menu de navegação à esquerda.
1. Procure e selecione **Insights de Aplicação**e, em seguida, selecione **Criar**.
1. Preencha o formulário, **selecione 'Rever + criar'** e, em seguida, selecione **Criar**.
1. Uma vez concluída a implementação, selecione **Ir para o recurso**.
1. No menu **Configurar** no menu Application Insights, selecione **Propriedades**.
1. Grave a **chave de instrumentação** para utilização num passo posterior.

## <a name="configure-the-custom-policy"></a>Configure a política personalizada

1. Abra o ficheiro do partido de base (RP), por * exemplo,SignUpOrSignin.xml*.
1. Adicione os seguintes atributos ao `<TrustFrameworkPolicy>` elemento:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se já não existir, adicione um `<UserJourneyBehaviors>` nó de criança ao `<RelyingParty>` nó. Deve ser colocado imediatamente após `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Adicione o nó seguinte como uma criança do `<UserJourneyBehaviors>` elemento. Certifique-se de que substitui a Chave de `{Your Application Insights Key}` Instrumentação de **Insights de Aplicação** que gravou anteriormente.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` diz à ApplicationInsights para acelerar a telemetria através do gasoduto de processamento. Bom para o desenvolvimento, mas limitado em grandes volumes.
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
`traces` | Veja todos os registos gerados por Azure AD B2C |
`traces | where timestamp > ago(1d)` | Veja todos os registos gerados pela Azure AD B2C para o último dia

As entradas podem ser longas. Exportar para CSV para um olhar mais atento.

Para obter mais informações sobre consultas, consulte [a visão geral das consultas de registo no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passos seguintes

A comunidade desenvolveu um espectador de viagem de utilizador para ajudar os desenvolvedores de identidade. Lê a partir da sua instância Application Insights e fornece uma visão bem estruturada dos eventos de viagem do utilizador. Obtém o código fonte e implanta-o na sua própria solução.

O jogador de viagem de utilizador não é suportado pela Microsoft e é disponibilizado estritamente como está.

Pode encontrar a versão do espectador que lê eventos a partir de Application Insights no GitHub, aqui:

[Azure-Samples/active-directy-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
