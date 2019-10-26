---
title: Aplicativo Azure insights Depurador de Instantâneos atualização para aplicativos .NET | Microsoft Docs
description: Como atualizar Depurador de Instantâneos para a versão mais recente no Azure App Services ou por meio de pacotes NuGet
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899840"
---
# <a name="upgrading-the-snapshot-debugger"></a>Atualizando o Depurador de Instantâneos

Para fornecer a melhor segurança possível para seus dados, a Microsoft está se afastando do TLS 1,0 e do TLS 1,1, que foram mostrados para serem vulneráveis a invasores determinados. Se você estiver usando uma versão mais antiga da extensão do site, será necessário uma atualização para continuar trabalhando. Este documento descreve as etapas necessárias para atualizar o depurador de instantâneos para a versão mais recente. Há dois caminhos de atualização principais, dependendo de se você habilitou o Depurador de Instantâneos usando uma extensão de site ou se usou um SDK/NuGet adicionado ao seu aplicativo. Os dois caminhos de atualização são discutidos abaixo. 

## <a name="upgrading-the-site-extension"></a>Atualizando a extensão do site

Se você habilitou o depurador de instantâneos usando a extensão de site, poderá atualizar facilmente usando o seguinte procedimento:

1. Inicie sessão no Portal do Azure.
2. Navegue até o recurso que tem o Application Insights e o depurador de instantâneos habilitados. Por exemplo, para um aplicativo Web, navegue até o recurso do serviço de aplicativo:

   ![Captura de tela do recurso de serviço de aplicativo individual chamado DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Depois de navegar até o recurso, clique em Application Insights na folha visão geral:

   ![Captura de tela de três botões. O botão central com o nome Application Insights está selecionado](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Uma nova folha será aberta com as configurações atuais. A menos que você queira tirar a oportunidade de alterar suas configurações, você pode deixá-las como estão. O botão **aplicar** na parte inferior da folha não é habilitado por padrão e você precisará alternar uma das configurações para ativar o botão. Você não precisa alterar as configurações reais, em vez disso, você pode alterar a configuração e, em seguida, alterá-la imediatamente. É recomendável alternar a configuração do criador de perfil e, em seguida, selecionar **aplicar**.

   ![Captura de tela da página de configuração do serviço de aplicativo Application Insights com o botão Aplicar realçado em vermelho](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Depois de clicar em **aplicar**, você será solicitado a confirmar as alterações.

    > [!NOTE]
    > O site será reiniciado como parte do processo de atualização.

   ![Captura de tela do prompt de aplicação de monitoramento do serviço de aplicativo. A caixa de texto exibe a mensagem: "agora aplicaremos as alterações às configurações do aplicativo e instalaremos nossas ferramentas para vincular o recurso de Application Insights ao aplicativo Web. Isso reiniciará o site. Deseja continuar? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Clique em **Sim** para aplicar as alterações. Durante o processo, aparecerá uma notificação mostrando que as alterações estão sendo aplicadas:

   ![Captura de tela da mensagem aplicar alterações – atualizando extensões que aparece no canto superior direito](./media/snapshot-debugger-upgrade/updating-extensions.png)

Depois de concluído, uma notificação **"alterações são aplicadas"** será exibida.

   ![Captura de tela da mensagem informando que as alterações são aplicadas](./media/snapshot-debugger-upgrade/changes-are-applied.png)

O site agora foi atualizado e está pronto para uso.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Atualizando Depurador de Instantâneos usando o SDK/NuGet

Se o aplicativo estiver usando uma versão do `Microsoft.ApplicationInsights.SnapshotCollector` abaixo da versão 1.3.1, será necessário atualizá-lo para uma [versão mais recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) para continuar trabalhando.
