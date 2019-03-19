---
title: Application Insights para resolver problemas de políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: como configurar o Application Insights para rastrear a execução de políticas personalizadas.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a6ec4c7d239754fe3211b528dd0ac64ee150ad3c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089374"
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Recolher registos

Este artigo fornece os passos para recolher registos do Azure AD B2C, para que pode diagnosticar problemas com as políticas personalizadas.

>[!NOTE]
>Atualmente, os registos de atividade detalhado descritos aqui são projetados **apenas** para ajudar no desenvolvimento de políticas personalizadas. Não utilize o modo de desenvolvimento em produção.  Registos de recolhem todas as afirmações enviadas de e para os fornecedores de identidade durante o desenvolvimento.  Se usado na produção, o desenvolvedor assume a responsabilidade de PII (em privado informações de identificação) recolhida no registo do App Insights que possuem.  Estes registos detalhados apenas são recolhidos quando a política for colocada em **modo de desenvolvimento**.


## <a name="use-application-insights"></a>Utilizar o Application Insights

O Azure AD B2C suporta uma funcionalidade para enviar dados para o Application Insights.  O Application Insights fornece uma forma de diagnosticar exceções e visualize os problemas de desempenho do aplicativo.

### <a name="setup-application-insights"></a>Configurar Application Insights

1. Aceda ao [Portal do Azure](https://portal.azure.com). Certifique-se de que está no inquilino com a sua subscrição do Azure (não o inquilino do Azure AD B2C).
1. Clique em **+ novo** no menu de navegação esquerdo.
1. Procure e selecione **Application Insights**, em seguida, clique em **criar**.
1. Preencha o formulário e clique em **criar**. Selecione **gerais** para o **tipo de aplicação**.
1. Quando o recurso tiver sido criado, abra o recurso do Application Insights.
1. Encontrar **propriedades** no menu da esquerda e clique no mesmo.
1. Copiar o **chave de instrumentação** e guarde-o para a próxima seção.

### <a name="set-up-the-custom-policy"></a>Configurar a política personalizada

1. Abra o ficheiro RP (por exemplo, SignUpOrSignin.xml).
1. Adicionar os seguintes atributos para o `<TrustFrameworkPolicy>` elemento:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se não existir já, adicione um nó subordinado `<UserJourneyBehaviors>` para o `<RelyingParty>` nó. Tem de estar localizado imediatamente após o `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Adicionar o nó seguinte como um filho do `<UserJourneyBehaviors>` elemento. Certifique-se de substituir `{Your Application Insights Key}` com o **chave de instrumentação** que obteve do Application Insights na secção anterior.

   ```XML
   <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   ```

   * `DeveloperMode="true"` informa ao Application Insights para agilizar a telemetria através do pipeline de processamento, bom para o desenvolvimento, mas restrita em grandes volumes.
   * `ClientEnabled="true"` envia o script de lado do cliente do Application Insights para rastrear erros de vista e do lado do cliente de página (não necessários).
   * `ServerEnabled="true"` envia o JSON de UserJourneyRecorder existente como um evento personalizado para o Application Insights.
   Exemplo:

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

3. Carregar a política.

### <a name="see-the-logs-in-application-insights"></a>Consulte os registos no Application Insights

>[!NOTE]
> Existe um curto atraso (menos de cinco minutos) antes de pode ver registos de novo no Application Insights.

1. Abra o recurso do Application Insights que criou no [portal do Azure](https://portal.azure.com).
1. Na **descrição geral** menu, clique em **Analytics**.
1. Abra um novo separador no Application Insights.
1. Aqui está uma lista de consultas que pode utilizar para ver os registos

| Consulta | Descrição |
|---------------------|--------------------|
rastreios | Ver todos os registos gerados pelo Azure AD B2C |
rastreios \| onde timestamp > ago(1d) | Ver todos os registos gerados pelo Azure AD B2C no último dia

As entradas podem ser longas.  Exportar para CSV para uma análise detalhada.

Pode saber mais sobre a ferramenta de análise [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>A Comunidade desenvolveu um Visualizador de percurso do utilizador para ajudar os desenvolvedores de identidade.  Não é suportado pela Microsoft e disponibilizado estritamente como-é.  Ele lê a partir da sua instância do Application Insights e fornece uma exibição bem estruturada do utilizador eventos de viagem.  Obter o código-fonte e implementá-lo em sua própria solução.

A versão do Visualizador de que lê eventos a partir do Application Insights está localizada [aqui](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>Atualmente, os registos de atividade detalhado descritos aqui são projetados **apenas** para ajudar no desenvolvimento de políticas personalizadas. Não utilize o modo de desenvolvimento em produção.  Registos de recolhem todas as afirmações enviadas de e para os fornecedores de identidade durante o desenvolvimento.  Se usado na produção, o desenvolvedor assume a responsabilidade de PII (em privado informações de identificação) recolhida no registo do App Insights que possuem.  Estes registos detalhados apenas são recolhidos quando a política for colocada em **modo de desenvolvimento**.

[Repositório do GitHub para exemplos de política personalizada não suportado e ferramentas relacionadas](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Próximos Passos

Explore os dados no Application Insights para o ajudar a compreender como ocorre com o Framework de experiência de identidade B2C subjacente funciona para fornecer sua própria identidade.
