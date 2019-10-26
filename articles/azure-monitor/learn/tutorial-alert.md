---
title: Enviar alertas de informações do Aplicativo Azure | Microsoft Docs
description: Tutorial para enviar alertas em resposta a erros em seu aplicativo usando o Aplicativo Azure insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 370d86ae28e49bba9681c6bdc81cc05b4e12a97b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894865"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorar e alertar sobre a integridade do aplicativo com informações do Aplicativo Azure

Aplicativo Azure insights permite monitorar seu aplicativo e enviar alertas quando ele estiver indisponível, apresentando falhas ou sofrendo problemas de desempenho.  Este tutorial orienta você pelo processo de criação de testes para verificar continuamente a disponibilidade do seu aplicativo.

Saiba como:

> [!div class="checklist"]
> * Criar teste de disponibilidade para verificar continuamente a resposta do aplicativo
> * Enviar email para administradores quando ocorrer um problema

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

Crie um [recurso de Application insights](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar teste de disponibilidade

Os testes de disponibilidade no Application Insights permitem que você teste automaticamente seu aplicativo de vários locais em todo o mundo.   Neste tutorial, você executará um teste de URL para garantir que seu aplicativo Web esteja disponível.  Você também pode criar uma explicação completa para testar sua operação detalhada. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  

2. Selecione **disponibilidade** no menu **investigar** e clique em **criar teste**.

    ![Adicionar teste de disponibilidade](media/tutorial-alert/add-test-001.png)

3. Digite um nome para o teste e deixe os outros padrões.  Essa seleção disparará solicitações para a URL do aplicativo a cada 5 minutos, a partir de cinco locais geográficos diferentes.

4. Selecione **alertas** para abrir o menu suspenso **alertas** , em que você pode definir detalhes sobre como responder se o teste falhar. Escolha **quase em tempo real** e defina o status como **habilitado.**

    Digite um endereço de email para enviar quando os critérios de alerta forem atendidos.  Opcionalmente, você pode digitar o endereço de um webhook para chamar quando os critérios de alerta forem atendidos.

    ![Criar teste](media/tutorial-alert/create-test-001.png)

5. Retorne ao painel de teste, selecione as reticências e edite o alerta para inserir a configuração para o alerta quase em tempo real.

    ![Editar alerta](media/tutorial-alert/edit-alert-001.png)

6. Defina locais com falha como maior ou igual a 3. Crie um [grupo de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) para configurar quem será notificado quando o limite do alerta for violado.

    ![Salvar a interface do usuário do alerta](media/tutorial-alert/save-alert-001.png)

7. Depois de configurar o alerta, clique no nome do teste para exibir os detalhes de cada local. Os testes podem ser exibidos no grafo de linha e no formato de gráfico de dispersão para visualizar o êxito/falhas de um determinado intervalo de tempo.

    ![Detalhes do teste](media/tutorial-alert/test-details-001.png)

8. Você pode fazer uma busca detalhada nos detalhes de qualquer teste clicando em seu ponto no gráfico de dispersão. Isso iniciará a exibição de detalhes da transação de ponta a ponta. O exemplo a seguir mostra os detalhes de uma solicitação com falha.

    ![Resultado do teste](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como alertar sobre problemas, avance para o próximo tutorial para aprender a analisar como os usuários estão interagindo com seu aplicativo.

> [!div class="nextstepaction"]
> [Entender os usuários](../../azure-monitor/learn/tutorial-users.md)