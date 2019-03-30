---
title: Atualização do Snapshot Debugger do Application Insights do Azure para aplicações de .NET | Documentos da Microsoft
description: Como atualizar o depurador de instantâneos para a versão mais recente nos serviços de aplicações do Azure ou através de pacotes Nuget
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632628"
---
# <a name="upgrading-the-snapshot-debugger"></a>Atualizar o Snapshot Debugger

Para fornecer a melhor segurança possível para os seus dados, a Microsoft se afastar-se TLS 1.0 e TLS 1.1, que foi mostrado vulneráveis aos atacantes determinados. Se estiver a utilizar uma versão mais antiga da extensão do site, esta operação requer uma atualização para continuar a trabalhar. Este documento descreve os passos necessários para atualizar seu depurador de instantâneos para a versão mais recente. Existem dois caminhos de atualização principal dependendo se ativou o Snapshot Debugger com uma extensão de site ou se tiver utilizado um SDK/Nuget adicionado ao seu aplicativo. Ambos os caminhos de atualização são abordados abaixo. 

## <a name="upgrading-the-site-extension"></a>Atualizar a extensão de site

Se ativou o Snapshot debugger usando a extensão de site, pode facilmente atualizar utilizando o seguinte procedimento:

1. Inicie sessão no Portal do Azure.
2. Navegue para o recurso com o Application Insights e o depurador de instantâneos ativada. Por exemplo, para uma aplicação Web, navegue para o recurso de serviço de aplicações:

   ![Captura de ecrã do recurso individual do serviço de aplicações com o nome DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Depois de ter de navegar para o seu recurso, clique no Application Insights no painel de descrição geral:

   ![Captura de ecrã dos três botões. Botão de Center com o nome Application Insights está selecionada](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Um novo painel abre-se com as definições atuais. A menos que queira aproveitar a oportunidade para alterar suas configurações, pode deixá-los tal como está. O **aplicar** botão na parte inferior do painel não está ativado por predefinição e tem de ativar/desativar uma das definições para ativar o botão. Não precisa alterar quaisquer definições reais, em vez disso, pode alterar a definição e, em seguida, imediatamente alterá-la novamente. Recomendamos que alternar o Profiler, definição e, em seguida, selecionando **aplicar**.

   ![Página de captura de ecrã da aplicação serviço Configuração do Application Insights com o botão de aplicar realçada em vermelho](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Assim que clicar em **aplicar**, será solicitado a confirmar as alterações.

    > [!NOTE]
    > O site será reiniciado como parte do processo de atualização.

   ![Captura de ecrã do serviço de aplicações aplicam-se a monitorização de linha de comandos. Caixa de texto exibe a mensagem: "Iremos agora aplicar alterações às suas definições de aplicação e instalar as nossas ferramentas para ligar o seu recurso do Application Insights para a aplicação web. Esta ação irá reiniciar o site. Pretende continuar?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Clique em **Sim** para aplicar as alterações. Durante o processo de uma notificação será exibida mostrando que estão a ser aplicadas alterações:

   ![Captura de ecrã das alterações de aplicar - a atualizar a mensagem de extensões que aparece no canto superior direito](./media/snapshot-debugger-upgrade/updating-extensions.png)

Depois de concluir, uma **"As alterações são aplicadas"** notificação será exibida.

   ![Captura de ecrã da mensagem a indicar as alterações são aplicadas](./media/snapshot-debugger-upgrade/changes-are-applied.png)

O site foi agora atualizado e está pronto a utilizar.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Atualizar o Snapshot Debugger usando o Nuget do SDK

Se a aplicação estiver a utilizar uma versão do `Microsoft.ApplicationInsights.SnapshotCollector` inferior à versão 1.3.1, ele precisará ser atualizado para uma [versão mais recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) para continuar a trabalhar.
