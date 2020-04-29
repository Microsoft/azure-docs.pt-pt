---
title: Envie alertas da Azure Application Insights [ Azure Application Insights] Microsoft Docs
description: Tutorial para enviar alertas em resposta a erros na sua aplicação usando Insights de aplicação Azure.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 7195de1cf58e5dd2e1d0b49b309f3afc718cca92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77656267"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitor e alerta sobre a saúde da aplicação com insights de aplicação Azure

O Azure Application Insights permite-lhe monitorizar a sua aplicação e enviar-lhe alertas quando está indisponível, com falhas ou sofrendo de problemas de desempenho.  Este tutorial leva-o através do processo de criação de testes para verificar continuamente a disponibilidade da sua aplicação.

Saiba como:

> [!div class="checklist"]
> * Criar teste de disponibilidade para verificar continuamente a resposta da aplicação
> * Envie correio aos administradores quando ocorrer um problema

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

Criar um recurso Deinsights de [Aplicação.](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar teste de disponibilidade

Os testes de disponibilidade em Insights de Aplicação permitem-lhe testar automaticamente a sua aplicação em vários locais do mundo.   Neste tutorial, realizará um teste de url para garantir que a sua aplicação web está disponível.  Também pode criar uma passagem completa para testar o seu funcionamento detalhado. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  

2. **Selecione Disponibilidade** no menu **Investigar** e, em seguida, clique em **Criar teste**.

    ![Adicionar teste de disponibilidade](media/tutorial-alert/add-test-001.png)

3. Digite um nome para o teste e deixe os outros predefinidos.  Esta seleção irá desencadear pedidos para o url da aplicação a cada 5 minutos de cinco locais geográficos diferentes.

4. Selecione **Alertas** para abrir o dropdown dos **Alertas** onde pode definir detalhes para como responder se o teste falhar. Escolha **quase em tempo real** e detete o estado para **Ativado.**

    Digite um endereço de e-mail para enviar quando os critérios de alerta forem cumpridos.  Pode digitar opcionalmente o endereço de um webhook para ligar quando os critérios de alerta forem cumpridos.

    ![Criar teste](media/tutorial-alert/create-test-001.png)

5. Volte ao painel de testes, selecione as elipses e edite o alerta para introduzir a configuração para o seu alerta em tempo quase real.

    ![Editar alerta](media/tutorial-alert/edit-alert-001.png)

6. Definir locais falhados para maiores ou iguais a 3. Crie um grupo de [ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) para configurar quem é notificado quando o seu limiar de alerta é violado.

    ![Salvar o alerta UI](media/tutorial-alert/save-alert-001.png)

7. Depois de configurar o seu alerta, clique no nome do teste para ver detalhes de cada local. Os testes podem ser vistos em ambos os formatos de gráfico de linha e de enredo de dispersão para visualizar o sucesso/falhas para uma determinada faixa de tempo.

    ![Detalhes do teste](media/tutorial-alert/test-details-001.png)

8. Pode perfurar os detalhes de qualquer teste clicando no seu ponto na tabela de dispersão. Isto lançará a visão de detalhes de transações de ponta a ponta. O exemplo abaixo mostra os detalhes para um pedido falhado.

    ![Resultado do teste](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a alertar sobre questões, avance para o próximo tutorial para aprender a analisar como os utilizadores estão interagindo com a sua aplicação.

> [!div class="nextstepaction"]
> [Compreender os utilizadores](../../azure-monitor/learn/tutorial-users.md)
