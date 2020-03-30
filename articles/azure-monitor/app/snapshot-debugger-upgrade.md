---
title: Upgrade Azure Application Insights Snapshot Debugger
description: Como atualizar o Snapshot Debugger para aplicações .NET para a versão mais recente nos Serviços de Aplicações Do Azure, ou através de pacotes Nuget
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671397"
---
# <a name="upgrading-the-snapshot-debugger"></a>Upgrade do Debugger Snapshot

Para fornecer a melhor segurança possível para os seus dados, a Microsoft está a afastar-se do TLS 1.0 e do TLS 1.1, que se mostraram vulneráveis a determinados atacantes. Se estiver a utilizar uma versão mais antiga da extensão do site, será necessário um upgrade para continuar a funcionar. Este documento descreve os passos necessários para atualizar o seu debugger Snapshot para a versão mais recente. Existem dois caminhos de atualização primários dependendo se ativou o Debugger Snapshot usando uma extensão do site ou se usou um SDK/Nuget adicionado à sua aplicação. Ambos os caminhos de upgrade são discutidos abaixo. 

## <a name="upgrading-the-site-extension"></a>Atualizar a extensão do site

> [!IMPORTANT]
> Versões mais antigas de Application Insights usaram uma extensão de site privado chamada extensão De insights de _aplicação para o Serviço de Aplicações Azure_. A experiência atual de Insights de Aplicação é ativada através da definição de Definições de Aplicações para iluminar uma extensão de site pré-instalada.
> Para evitar conflitos, o que pode fazer com que o seu site deixe de funcionar, é importante eliminar primeiro a extensão do site privado. Veja o passo 4 abaixo.

Se ativar o debugger Snapshot utilizando a extensão do site, pode atualizar utilizando o seguinte procedimento:

1. Inicie sessão no Portal do Azure.
2. Navegue para o seu recurso que tenha Application Insights e Snapshot debugger habilitado. Por exemplo, para uma Aplicação Web, navegue para o recurso do Serviço de Aplicações:

   ![Screenshot de recurso individual do Serviço de Aplicações chamado DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Depois de navegar para o seu recurso, clique na lâmina das Extensões e aguarde que a lista de extensões para povoar:

   ![Screenshot das extensões do serviço de aplicações mostrando extensão de Insights de aplicação para o Serviço de Aplicações Azure instalado](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Se for instalada qualquer versão da extensão De insights de aplicação para o Serviço de _Aplicações Azure,_ selecione-a e clique em Eliminar. Confirme **Sim** para apagar a extensão e aguarde que a eliminação esteja concluída antes de passar para o próximo passo.

   ![Screenshot das extensões do serviço de aplicações mostrando extensão de Insights de aplicação para o Serviço de Aplicações Azure com o botão Delete realçado](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Vá à lâmina de visão geral do seu recurso e clique em Insights de Aplicação:

   ![Imagem de três botões. Botão central com nome Application Insights é selecionado](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Se for a primeira vez que vê a lâmina de Insights de Aplicação para este Serviço de Aplicações, será solicitado a ativar os Insights da Aplicação. Selecione **Turn on Application Insights**.
 
   ![Screenshot da experiência pela primeira vez para a lâmina Insights de Aplicação com o botão Turn on Application Insights realçado](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. As definições de Insights de Aplicação atuais são apresentadas. A menos que queira aproveitar a oportunidade para alterar as suas definições, pode deixá-las como está. O botão **Aplicar** na parte inferior da lâmina não está ativado por defeito e terá de alternar uma das definições para ativar o botão. Não é preciso alterar nenhuma definição real, mas pode alterar a definição e depois troque-a imediatamente. Recomendamos que toque a definição do Profiler e, em seguida, selecione **Aplicar**.

   ![Screenshot da página de configuração do serviço de aplicação insights de aplicação com botão Aplicar realçado a vermelho](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Assim que clicar em **Aplicar,** será-lhe pedido que confirme as alterações.

    > [!NOTE]
    > O site será reiniciado como parte do processo de upgrade.

   ![Screenshot do pedido de monitorização do Serviço de Aplicações. A caixa de texto exibe mensagem: "Vamos agora aplicar alterações às definições da sua aplicação e instalar as nossas ferramentas para ligar o seu recurso Desinformação de Aplicação à aplicação web. Isto reiniciará o site. Quer continuar?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Clique em **Sim** para aplicar as alterações e aguarde que o processo esteja concluído.

O site foi agora atualizado e está pronto a ser utilizado.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Upgrade Snapshot Debugger usando SDK/Nuget

Se a aplicação estiver `Microsoft.ApplicationInsights.SnapshotCollector` a utilizar uma versão abaixo da versão 1.3.1, terá de ser atualizada para uma [versão mais recente](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) para continuar a funcionar.
