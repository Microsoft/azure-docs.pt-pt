---
title: Solucionar problemas de políticas personalizadas com Application Insights
titleSuffix: Azure AD B2C
description: Como configurar Application Insights para rastrear a execução de suas políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3aeef1614f22563e0fd348c5bc6ae7ff1e7b0b03
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950090"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Coletar logs de Azure Active Directory B2C com Application Insights

Este artigo fornece etapas para coletar logs de Active Directory B2C (Azure AD B2C) para que você possa diagnosticar problemas com suas políticas personalizadas. Application Insights fornece uma maneira de diagnosticar exceções e Visualizar problemas de desempenho do aplicativo. Azure AD B2C inclui um recurso para enviar dados para Application Insights.

Os logs de atividade detalhados descritos aqui devem ser habilitados **somente** durante o desenvolvimento de suas políticas personalizadas.

> [!WARNING]
> Não habilite o modo de desenvolvimento em produção. Os logs coletam todas as declarações enviadas para e de provedores de identidade. Você, como desenvolvedor, assume a responsabilidade por quaisquer dados pessoais coletados em seus logs de Application Insights. Esses logs detalhados são coletados somente quando a política é colocada no **modo de desenvolvedor**.

## <a name="set-up-application-insights"></a>Configurar Application Insights

Se você ainda não tiver uma, crie uma instância do Application Insights em sua assinatura.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém sua assinatura do Azure (não o diretório Azure ad B2C).
1. Selecione **criar um recurso** no menu de navegação à esquerda.
1. Procure e selecione **Application insights**e, em seguida, selecione **criar**.
1. Preencha o formulário, selecione **revisar + criar**e, em seguida, selecione **criar**.
1. Depois que a implantação for concluída, selecione **ir para o recurso**.
1. Em **Configurar** no menu Application insights, selecione **Propriedades**.
1. Registre a **chave de instrumentação** para uso em uma etapa posterior.

## <a name="configure-the-custom-policy"></a>Configurar a política personalizada

1. Abra o arquivo RP (terceira parte confiável), por exemplo, *SignUpOrSignin. xml*.
1. Adicione os seguintes atributos ao elemento `<TrustFrameworkPolicy>`:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se ele ainda não existir, adicione um `<UserJourneyBehaviors>` nó filho ao nó `<RelyingParty>`. Ele deve estar localizado imediatamente após `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`.
1. Adicione o nó a seguir como um filho do elemento `<UserJourneyBehaviors>`. Certifique-se de substituir `{Your Application Insights Key}` pela **chave de instrumentação** de Application insights que você registrou anteriormente.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` diz ao ApplicationInsights para agilizar a telemetria por meio do pipeline de processamento. Bom para desenvolvimento, mas restrita em grandes volumes.
    * `ClientEnabled="true"` envia o script do lado do cliente do ApplicationInsights para controlar a exibição de página e os erros do lado do cliente. Você pode exibi-los na tabela **browserTimings** no Portal Application insights. Ao definir `ClientEnabled= "true"`, você adiciona Application Insights ao script de página e obtém intervalos de carregamentos de página e chamadas AJAX, contagens, detalhes de exceções do navegador e falhas do AJAX, contagens de usuário e sessão. Esse campo é **opcional**e é definido como `false` por padrão.
    * `ServerEnabled="true"` envia o JSON UserJourneyRecorder existente como um evento personalizado para Application Insights.

    Por exemplo:

    ```XML
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

1. Carregue a política.

## <a name="see-the-logs-in-application-insights"></a>Consulte os logs em Application Insights

Há um pequeno atraso, normalmente menos de cinco minutos, antes que você possa ver novos logs em Application Insights.

1. Abra o recurso de Application Insights que você criou na [portal do Azure](https://portal.azure.com).
1. No menu **visão geral** , selecione **análise**.
1. Abra uma nova guia no Application Insights.

Aqui está uma lista de consultas que você pode usar para ver os logs:

| Consulta | Descrição |
|---------------------|--------------------|
`traces` | Ver todos os logs gerados por Azure AD B2C |
`traces | where timestamp > ago(1d)` | Ver todos os logs gerados por Azure AD B2C para o último dia

As entradas podem ser longas. Exporte para CSV para uma análise mais detalhada.

Para obter mais informações sobre como consultar, consulte [visão geral das consultas de log no Azure monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passos seguintes

A Comunidade desenvolveu um visualizador de jornada do usuário para ajudar os desenvolvedores de identidades. Ele lê de sua instância de Application Insights e fornece uma exibição bem estruturada dos eventos de jornada do usuário. Você Obtém o código-fonte e o implanta em sua própria solução.

O jogador de jornada do usuário não tem suporte da Microsoft e é disponibilizado estritamente no estado em que se encontra.

Você pode encontrar a versão do visualizador que lê eventos de Application Insights no GitHub, aqui:

[Azure-Samples/Active-Directory-B2C-Advanced-Policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
