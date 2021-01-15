---
title: Envie alertas da Azure Application Insights | Microsoft Docs
description: Tutorial para enviar alertas em resposta a erros na sua aplicação usando Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: dbf2cbe08fa1ba1ab79446e9068b4fb1fce381b0
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210261"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitore e alerte para a saúde da aplicação com insights de aplicação Azure

O Azure Application Insights permite-lhe monitorizar a sua aplicação e enviar-lhe alertas quando este não está indisponível, experimentando falhas ou sofrendo de problemas de desempenho.  Este tutorial leva-o através do processo de criação de testes para verificar continuamente a disponibilidade da sua aplicação.

Saiba como:

> [!div class="checklist"]
> * Crie um teste de disponibilidade para verificar continuamente a resposta da aplicação
> * Envie correio para administradores quando ocorrer um problema

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

Criar um [recurso Application Insights](../app/create-new-resource.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar teste de disponibilidade

Os testes de disponibilidade em Application Insights permitem testar automaticamente a sua aplicação em vários locais do mundo.   Neste tutorial, irá realizar um teste url para garantir que a sua aplicação web está disponível.  Também pode criar uma passagem completa para testar o seu funcionamento detalhado. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  

2. Selecione **Disponibilidade** no menu **Investigar** e, em seguida, clique em **Criar teste.**

    ![Adicionar teste de disponibilidade](media/tutorial-alert/add-test-001.png)

3. Digite um nome para o teste e deixe os outros predefinidos.  Esta seleção irá desencadear pedidos para o url de aplicação a cada 5 minutos de cinco localizações geográficas diferentes.

4. Selecione **Alertas** para abrir o **dropdown de Alertas** onde pode definir detalhes para como responder se o teste falhar. Escolha **perto em tempo real** e desa esta qual é a situação **ativada.**

    Digite um endereço de e-mail para enviar quando os critérios de alerta estiverem cumpridos.  Pode, opcionalmente, digitar o endereço de um webhook para ligar quando os critérios de alerta estiverem cumpridos.

    ![Criar teste](media/tutorial-alert/create-test-001.png)

5. Volte ao painel de teste, selecione as elipses e edite o alerta para introduzir a configuração para o seu alerta em tempo real.

    ![Edição de alerta](media/tutorial-alert/edit-alert-001.png)

6. Desabar locais falhados para maiores ou iguais a 3. Crie um [grupo de ação](../platform/action-groups.md) para configurar quem é notificado quando o seu limiar de alerta é violado.

    ![Salvar uI alerta](media/tutorial-alert/save-alert-001.png)

7. Uma vez configurado o seu alerta, clique no nome do teste para ver os detalhes de cada local. Os testes podem ser vistos tanto no formato de gráfico de linha como no formato de enredo de dispersão para visualizar o sucesso/falhas de um determinado intervalo de tempo.

    ![Detalhes do teste](media/tutorial-alert/test-details-001.png)

8. Pode aprofundar os detalhes de qualquer teste clicando no seu ponto na tabela de dispersão. Isto lançará a visão de detalhes de transações de ponta a ponta. O exemplo abaixo mostra os detalhes de um pedido falhado.

    ![Resultado do teste](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a alertar sobre problemas, avance para o próximo tutorial para aprender a analisar como os utilizadores estão a interagir com a sua aplicação.

> [!div class="nextstepaction"]
> [Compreender os utilizadores](./tutorial-users.md)

