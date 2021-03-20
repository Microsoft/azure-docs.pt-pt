---
title: Upgrade Azure Application Insights Snapshot Debugger
description: Como atualizar o Snapshot Debugger para aplicações .NET para a versão mais recente nos Serviços de Aplicações Azure ou através de pacotes Nuget
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77671397"
---
# <a name="upgrading-the-snapshot-debugger"></a>Modernização do Debugger Snapshot

Para fornecer a melhor segurança possível para os seus dados, a Microsoft está a afastar-se dos TLS 1.0 e TLS 1.1, que se mostraram vulneráveis a atacantes determinados. Se estiver a utilizar uma versão mais antiga da extensão do site, será necessário um upgrade para continuar a funcionar. Este documento descreve os passos necessários para atualizar o seu depurar Snapshot para a versão mais recente. Existem dois caminhos de atualização primários dependendo se ativou o Debugger Snapshot usando uma extensão do site ou se usou um SDK/Nuget adicionado à sua aplicação. Ambos os caminhos de upgrade são discutidos abaixo. 

## <a name="upgrading-the-site-extension"></a>Atualizar a extensão do site

> [!IMPORTANT]
> Versões mais antigas do Application Insights usaram uma extensão do site privado chamada _extensão de Insights de Aplicação para o Serviço de Aplicações Azure_. A experiência atual do Application Insights é ativada através da definição de Definições de Aplicações para iluminar uma extensão do site pré-instalada.
> Para evitar conflitos, que podem fazer com que o seu site deixe de funcionar, é importante eliminar primeiro a extensão do site privado. Veja o passo 4 abaixo.

Se ativou o depurador Snapshot utilizando a extensão do site, pode atualizar utilizando o seguinte procedimento:

1. Inicie sessão no portal do Azure.
2. Navegue para o seu recurso que tem Insights de Aplicação e depuração Snapshot ativados. Por exemplo, para uma Web App, navegue para o recurso Serviço de Aplicações:

   ![Screenshot de recurso individual do Serviço de Aplicações chamado DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Depois de navegar para o seu recurso, clique na lâmina extensões e aguarde que a lista de extensões preencha:

   ![Screenshot das extensões do serviço de aplicações mostrando extensão de Insights de aplicação para serviço de aplicações Azure instalado](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Se alguma versão da _extensão de Application Insights para o Serviço de Aplicações Azure_ estiver instalada, selecione-a e clique em Eliminar. Confirme **Sim** para eliminar a extensão e aguarde que a eliminação esteja concluída antes de passar para o passo seguinte.

   ![Screenshot das extensões do Serviço de Aplicações mostrando extensão de Insights de Aplicação para Serviço de Aplicações Azure com o botão Eliminar realçado](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Vá à lâmina de visão geral do seu recurso e clique em Insights de Aplicação:

   ![Screenshot de três botões. O botão central com o nome Application Insights é selecionado](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Se esta for a primeira vez que vê a lâmina de Insights de Aplicação para este Serviço de Aplicações, será solicitado que ligue o Application Insights. Selecione **Ligue as informações de aplicação**.
 
   ![Screenshot da primeira experiência para a lâmina Application Insights com o botão Turn on Application Insights realçado](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. As atuais definições de Insights de Aplicação são apresentadas. A menos que queira aproveitar a oportunidade para alterar as suas definições, pode deixá-las como está. O botão **Aplicar** na parte inferior da lâmina não está ativado por predefinição e terá de alternar uma das definições para ativar o botão. Não tem de alterar as definições reais, mas pode alterar a definição e, em seguida, alterá-la imediatamente. Recomendamos que se ressoem a definição do Profiler e, em seguida, selecione **Aplicar**.

   ![Screenshot da página de configuração do serviço de aplicações do Application Insights com botão Aplicar realçado a vermelho](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Assim que clicar em **Aplicar,** ser-lhe-á pedido que confirme as alterações.

    > [!NOTE]
    > O site será reiniciado como parte do processo de atualização.

   ![Screenshot do pedido de monitorização do Serviço de Aplicação de Aplicações. A caixa de texto exibe mensagem: "Vamos agora aplicar alterações às definições da sua aplicação e instalar as nossas ferramentas para ligar o seu recurso Application Insights à aplicação web. Isto reiniciará o site. Quer continuar?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Clique **em Sim** para aplicar as alterações e esperar que o processo esteja concluído.

O site foi agora atualizado e está pronto a ser utilizado.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Upgrade snapshot Debugger usando SDK/Nuget

Se a aplicação estiver a utilizar uma versão da `Microsoft.ApplicationInsights.SnapshotCollector` versão 1.3.1 abaixo, terá de ser atualizada para uma [versão mais recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) para continuar a funcionar.
