---
title: Políticas personalizadas de resolução de problemas com Insights de Aplicação
titleSuffix: Azure AD B2C
description: Como configurar insights de aplicação para rastrear a execução das suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186272"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Colete logs de Diretório Ativo Azure B2C com Insights de Aplicação

Este artigo fornece passos para a recolha de registos do Ative Directory B2C (Azure AD B2C) para que possa diagnosticar problemas com as suas políticas personalizadas. Application Insights fornece uma forma de diagnosticar exceções e visualizar problemas de desempenho da aplicação. O Azure AD B2C inclui uma funcionalidade para o envio de dados para insights de aplicação.

Os registos de atividade detalhados aqui descritos só devem ser **ativados** durante o desenvolvimento das suas políticas personalizadas.

> [!WARNING]
> Não permita o modo de desenvolvimento na produção. Os registos recolhem todas as reclamações enviadas de e para fornecedores de identidade. Você como desenvolvedor assume a responsabilidade por quaisquer dados pessoais recolhidos nos registos do Seu Formulário de Aplicação. Estes registos detalhados só são recolhidos quando a apólice é colocada no **MODO DE DESENVOLVIMENTO**.

## <a name="set-up-application-insights"></a>Configurar insights de aplicação

Se ainda não tem um, crie uma instância de Informações de Aplicação na sua subscrição.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém a sua subscrição Azure (não o seu diretório Azure AD B2C).
1. Selecione **Criar um recurso** no menu de navegação à esquerda.
1. Procure e selecione **Insights de Aplicação,** em seguida, selecione **Criar**.
1. Complete o formulário, selecione **Rever + criar,** e depois selecione **Criar**.
1. Uma vez concluída a implantação, selecione **Ir para o recurso**.
1. No **menu Configure** no menu Insights de Aplicação, selecione **Propriedades**.
1. Grave a **CHAVE DE INSTRUMENTAÇÃO** para utilização num passo posterior.

## <a name="configure-the-custom-policy"></a>Configure a política personalizada

1. Abra o ficheiro da parte de fiação (RP), por *exemplo, SignUpOrSignin.xml*.
1. Adicione os seguintes `<TrustFrameworkPolicy>` atributos ao elemento:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se já não existir, adicione `<UserJourneyBehaviors>` um nó `<RelyingParty>` infantil ao nó. Deve ser localizado imediatamente `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`após .
1. Adicione o nó seguinte como `<UserJourneyBehaviors>` uma criança do elemento. Certifique-se `{Your Application Insights Key}` de substituir a chave de instrumentação de insights de **aplicação** que gravou anteriormente.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`diz ao ApplicationInsights para acelerar a telemetria através do oleoduto de processamento. Bom para o desenvolvimento, mas limitado em volumes elevados.
    * `ClientEnabled="true"`envia o script do lado do cliente ApplicationInsights para rastrear a visualização da página e erros do lado do cliente. Pode vê-las na tabela **browserTimings** no portal Insights da Aplicação. Ao `ClientEnabled= "true"`definir, adiciona insights de aplicação ao script da sua página e obtém timings de cargas de página e chamadas AJAX, conta, detalhes de exceções ao navegador e falhas do AJAX, e contagens de utilizador e sessão. Este campo é **opcional,** `false` e está definido por padrão.
    * `ServerEnabled="true"`envia o UserJourneyRecorder JSON existente como um evento personalizado para A Aplicação Insights.

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

1. Faça upload da apólice.

## <a name="see-the-logs-in-application-insights"></a>Consulte os registos em Insights de Aplicação

Há um curto atraso, tipicamente menos de cinco minutos, antes de poder ver novos registos em Insights de Aplicação.

1. Abra o recurso Application Insights que criou no [portal Azure.](https://portal.azure.com)
1. No menu **Overview,** selecione **Analytics**.
1. Abra um novo separador em Insights de Aplicação.

Aqui está uma lista de consultas que pode usar para ver os registos:

| Consulta | Descrição |
|---------------------|--------------------|
`traces` | Veja todos os registos gerados pelo Azure AD B2C |
`traces | where timestamp > ago(1d)` | Veja todos os registos gerados pelo Azure AD B2C para o último dia

As entradas podem ser longas. Exportar para cSV para um olhar mais atento.

Para mais informações sobre consulta, consulte [a visão geral das consultas de registo no Monitor Azure](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Passos seguintes

A comunidade desenvolveu um espectador de viagens de utilizador para ajudar desenvolvedores de identidade. Lê a partir da sua instância De insights de aplicação e proporciona uma visão bem estruturada dos eventos de viagem do utilizador. Obtém-se o código fonte e implementa-o na sua própria solução.

O leitor de viagens de utilizador não é suportado pela Microsoft, e está disponível estritamente como está.

Pode encontrar a versão do espectador que lê eventos a partir de Application Insights no GitHub, aqui:

[Azure-Samples/active-directy-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
