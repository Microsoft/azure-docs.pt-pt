---
title: Monitorize aplicações móveis com Insights de Aplicação Do Monitor Azure
description: Fornece instruções para configurar rapidamente uma aplicação móvel para monitorização com insights de aplicação do Monitor Azure e App Center
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.reviewer: daviste
ms.custom: mvc
ms.openlocfilehash: a59a6841a1db3cecfe52c54135b42f5b6a095ce3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77660296"
---
# <a name="start-analyzing-your-mobile-app-with-app-center-and-application-insights"></a>Começar a analisar a sua aplicação móvel com o App Center e o Application Insights

Este início rápido orienta-o ao longo da ligação da instância do App Center da sua aplicação ao Application Insights. Com o Application Insights, pode consultar, segmentar, filtrar e analisar a sua telemetria com ferramentas mais poderosas que estão disponíveis no serviço [Analytics](https://docs.microsoft.com/mobile-center/analytics/) do App Center.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, necessita de:

- Uma subscrição do Azure.
- Uma aplicação iOS, Android, Xamarin, Universal Windows ou React Native
 
Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-up-with-app-center"></a>Inscreva-se no App Center
Para começar, crie uma conta e [inscreva-se no App Center](https://appcenter.ms/signup?utm_source=ApplicationInsights&utm_medium=Azure&utm_campaign=docs).

## <a name="onboard-to-app-center"></a>Incluir no App Center

Antes de poder utilizar o Application Insights com a sua aplicação móvel, tem de incluir a sua aplicação no [App Center](https://docs.microsoft.com/mobile-center/). O Application Insights não recebe telemetria da sua aplicação móvel diretamente. Em vez disso, a aplicação envia telemetria de eventos personalizados para o App Center. Depois, o App Center exporta continuamente cópias destes eventos personalizados para o Application Insights à medida que os eventos são recebidos. (Isto não se aplica ao [Application Insights JS SDK](https://github.com/Microsoft/ApplicationInsights-JS) ou ao [plugin nativo react](https://github.com/Microsoft/ApplicationInsights-JS/tree/master/vNext/extensions/applicationinsights-react-native) onde a telemetria é enviada diretamente para insights de aplicação.)

Para incluir a aplicação, siga o início rápido do App Center para cada plataforma que aquela suporte. Crie instâncias separadas do App Center para cada plataforma:

* [iOS](https://docs.microsoft.com/mobile-center/sdk/getting-started/ios).
* [Android.](https://docs.microsoft.com/mobile-center/sdk/getting-started/android)
* [Xamarin.](https://docs.microsoft.com/mobile-center/sdk/getting-started/xamarin)
* [Janelas Universais](https://docs.microsoft.com/mobile-center/sdk/getting-started/uwp).
* [React Native](https://docs.microsoft.com/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Acompanhar eventos na sua aplicação

Após a inclusão da sua aplicação no App Center, aquela tem de ser modificada para enviar telemetria de eventos personalizados com o SDK do App Center. Os eventos personalizados são o único tipo de telemetria do App Center que é exportada para o Application Insights.

Para enviar eventos personalizados a partir de aplicações iOS, utilize o método `trackEvent` ou `trackEvent:withProperties` métodos no SDK do App Center. [Saiba mais sobre como acompanhar eventos de aplicações iOS.](https://docs.microsoft.com/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

Para enviar eventos personalizados a partir de aplicações Android, utilize o método `trackEvent` no SDK do App Center. [Saiba mais sobre como acompanhar eventos de aplicações Android.](https://docs.microsoft.com/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

Para enviar eventos personalizados a partir de outras plataformas de aplicações, utilize os métodos `trackEvent` nos respetivos SDKs do App Center.

Para confirmar que os eventos personalizados estão a ser recebidos, aceda ao separador **Eventos**, na secção **Analytics** (Análise) do App Center. Os eventos podem demorar alguns minutos a aparecer a partir do momento em que são enviados da aplicação.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando a aplicação estiver a enviar eventos personalizados e o App Center a recebê-los, tem de criar um recurso do Application Insights para o App Center no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um programador de recursos** > **Ferramentas** > **De aplicação Insights**.

    > [!NOTE]
    > Se for a primeira vez que cria um recurso de Insights de Aplicação, pode aprender mais visitando o [Create a Application Insights Resource](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc.

    É apresentada uma caixa de configuração. Utilize a tabela abaixo para preencher os campos de entrada.

    | Definições        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Um valor exclusivo globalmente, como “aminhaAplicação-iOS” | Nome que identifica a aplicação que está a monitorizar |
     | **Grupo de Recursos**     | Um grupo de recursos novo ou um já existente a partir do menu | O grupo de recursos no qual vai criar o recurso novo do Application Insights |
   | **Localização** | Uma localização a partir do menu | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

3. Clique em **Criar**.

Se a sua aplicação suportar várias plataformas (iOS, Android, etc.), é melhor criar recursos do Application Insights separados, um para cada plataforma.

## <a name="export-to-application-insights"></a>Exportar para o Application Insights

No seu novo recurso Application Insights na página **'Overview'.** Copie a chave de instrumentação do seu recurso.

No [App Center,](https://appcenter.ms/) por exemplo, para a sua aplicação:

1. Na página **Definições**, clique em **Exportar**.
2. Escolha **Nova Exportação**, escolha **Application Insights** e clique em **Personalizar**.
3. Cole a chave de instrumentação do Application Insights na caixa.
4. Autorize o aumento da utilização da subscrição do Azure que contém o recurso do Application Insights. Cada recurso do Application Insights é gratuito para o primeiro 1 GB de dados recebidos por mês. [Saiba mais sobre os preços do Application Insights.](https://azure.microsoft.com/pricing/details/application-insights/)

Não se esqueça de repetir este processo para cada plataforma que a sua aplicação suporte.

Quando a [exportação](https://docs.microsoft.com/mobile-center/analytics/export) estiver configurada, cada evento personalizado que o App Center recebe é copiado para o Application Insights. Os eventos podem demorar vários minutos a chegar ao Application Insights, pelo que, se não aparecerem de imediato, aguarde uns momentos antes de fazer mais diagnósticos.

Para lhe dar mais dados quando se liga pela primeira vez, são exportadas automaticamente para o Application Insights as últimas 48 horas de eventos personalizados no App Center.

## <a name="start-monitoring-your-app"></a>Começar a monitorizar a aplicação

O Application Insights pode consultar, segmentar, filtrar e analisar a telemetria dos eventos personalizados das suas aplicações de forma mais abrangente do que as ferramentas que o App Center oferece.

1. **Consulte a telemetria dos eventos personalizados.** A partir da página de **visão geral** da aplicação Insights, escolha **Registos (Analytics)**.

   O portal De Registos de Insights de Aplicação (Analytics) associado ao seu recurso Application Insights será aberto. O portal Logs (Analytics) permite consultar diretamente os seus dados através do idioma de consulta log analytics, para que possa fazer perguntas arbitrariamente complexas sobre a sua aplicação e os seus utilizadores.
   
   Abra um novo separador no portal Logs (Analytics) e, em seguida, comente na seguinte consulta. É devolvida uma contagem do número de utilizadores distintos que enviaram eventos personalizados a partir da sua aplicação nas últimas 24 horas, ordenados por estas contagens distintas.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Portal de registos (Analytics)](./media/mobile-center-quickstart/analytics-portal-001.png)

   1. Clique em qualquer parte da consulta no editor de texto para selecioná-la.
   2. Em seguida, clique em **Ir** para executar a consulta. 

   Saiba mais sobre o [Analytics do Application Insights](../../azure-monitor/app/analytics.md) e a [linguagem de consultas do Log Analytics](https://aka.ms/LogAnalyticsLanguageReference).


2. **Segmentar e filtrar a telemetria dos eventos personalizados.** Na página **Descrição Geral** do Application Insights, escolha **Utilizadores**, no índice.

   ![Ícone da ferramenta Utilizadores](./media/mobile-center-quickstart/users-icon-001.png)

   A ferramenta Utilizadores mostra o número de utilizadores da aplicação que clicaram em determinados botões, visitaram certos ecrãs ou realizaram outra ação qualquer que esteja a acompanhar como evento através do SDK do App Center. Se estiver à procura de uma forma de segmentar e filtrar os eventos do App Center, a ferramenta Utilizadores é uma excelente escolha.

   ![Ferramenta Utilizadores](./media/mobile-center-quickstart/users-001.png) 

   Por exemplo, segmente a sua utilização por geografia ao escolher **País ou Região**, no menu pendente **Dividir por**.

3. **Analise os padrões de conversão, retenção e navegação na sua aplicação.** Na página **Descrição Geral** do Application Insights, escolha **Fluxos de Utilizador**, no índice.

   ![Ferramenta Fluxos de Utilizador](./media/mobile-center-quickstart/user-flows-001.png)

   A ferramenta Fluxos de Utilizador visualiza os eventos que os utilizadores enviam após algum evento inicial. É útil para obter uma visão geral da forma como os utilizadores navegam pela aplicação. Também pode revelar os locais onde muitos utilizadores a abandonam ou repetem as mesmas ações repetidamente.

   Para além dos Fluxos de Utilizador, o Application Insights tem várias outras ferramentas de análise do comportamento de utilização, que permitem responder a perguntas específicas:

   * **Funis**, para analisar e monitorizar as taxas de conversão.
   * **Retenção**, para analisar até que ponto é que a aplicação retém os utilizadores ao longo do tempo.
   * **Livros**, para combinar visualizações e textos num relatório partilhável.
   * **Coortes**, para dar o nome e guardar grupos de utilizadores ou eventos específicos, para que outras ferramentas de análise possam referenciá-los facilmente.

## <a name="clean-up-resources"></a>Limpar recursos

Se não quiser continuar a utilizar o Application Insights com o App Center, desative a exportação no App Center e elimine o recurso do Application Insights. Desta forma, o Application Insights deixa de lhe cobrar o recurso.

Para desativar a exportação no App Center:

1. No App Center, aceda a **Definições** e escolha **Exportar**.
2. Clique na exportação do Application Insights que quer eliminar e clique em **Eliminar exportação**, na parte inferior, e confirme.

Para eliminar o recurso do Application Insights:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e escolha o grupo de recursos no qual o recurso do Application Insights foi criado.
2. Abra o recurso que pretende eliminar. Em seguida, clique em **Eliminar**, no menu superior do recurso, e confirme. Esta ação elimina definitivamente a cópia dos dados que foram exportados para o Application Insights.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Understand how customers are using your app](../../azure-monitor/app/usage-overview.md) (Saber como é que os clientes estão a utilizar a sua aplicação)
