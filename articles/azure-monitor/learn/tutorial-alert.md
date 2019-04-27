---
title: Enviar alertas do Azure Application Insights | Documentos da Microsoft
description: Tutorial para enviar alertas em resposta a erros na sua aplicação com o Azure Application Insights.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 05285a177827cd0dd1e0e39e779a395ccfdfc0cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541096"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorizar e alertar relativamente ao estado de funcionamento de aplicação com o Azure Application Insights

O Azure Application Insights permite-lhe monitorizar a sua aplicação e enviar-lhe alertas quando está indisponível, a experienciar falhas ou sofrer problemas de desempenho.  Este tutorial guia-o ao longo do processo de criação de testes para verificar continuamente a disponibilidade da sua aplicação.

Saiba como:

> [!div class="checklist"]
> * Criar teste de disponibilidade para verificar continuamente a resposta do aplicativo
> * Envie um e-mail para os administradores quando ocorrer um problema

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

Criar uma [recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Criar teste de disponibilidade

Testes de disponibilidade no Application Insights permitem-lhe testar automaticamente a sua aplicação de vários locais em todo o mundo.   Neste tutorial, é necessário executar um teste de url para se certificar de que a sua aplicação web está disponível.  Também é possível criar instruções completas para testar a sua operação detalhada. 

1. Selecione **Application Insights** e, em seguida, selecione a sua subscrição.  

2. Selecione **disponibilidade** sob a **investigar** menu e clique em **criar teste**.

    ![Adicione o teste de disponibilidade](media/tutorial-alert/add-test-001.png)

3. Escreva um nome para o teste e deixe as outras predefinições.  Esta seleção irá acionar pedidos para o url da aplicação a cada 5 minutos de cinco localizações geográficas diferentes.

4. Selecione **alertas** para abrir o **alertas** suspensa onde pode definir os detalhes sobre como responder se o teste é reprovado. Escolher **quase tempo real** e definir o estado como **ativado.**

    Tipo de um endereço de e-mail para enviar quando os critérios de alerta são cumpridos.  Pode opcionalmente digitar o endereço de um webhook para chamar e quando os critérios de alerta são cumpridos.

    ![Criar teste](media/tutorial-alert/create-test-001.png)

5. Regresse ao painel de teste, selecione as reticências e editar o alerta para introduzir a configuração para o alerta de tempo quase real.

    ![Editar alerta](media/tutorial-alert/edit-alert-001.png)

6. Definir localizações com falhas para maior ou igual a 3. Criar uma [grupo de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) para configurar a quem é notificada quando o limiar de alerta é quebrado.

    ![Guardar o alerta da interface do Usuário](media/tutorial-alert/save-alert-001.png)

7. Assim que tiver configurado o alerta, clique no nome do teste para ver os detalhes de cada localização. Testes podem ser visualizados em ambos os formato de desenho de linha e gráfico de dispersão, para visualizar as êxito/falhas de um intervalo de tempo especificado.

    ![Detalhes do teste](media/tutorial-alert/test-details-001.png)

8. Pode desagregar os detalhes de qualquer teste ao clicar no respetivo ponto no gráfico de dispersão. Este procedimento inicia o modo de exibição de detalhes de transação de ponta a ponta. O exemplo abaixo mostra os detalhes de um pedido falhado.

    ![Resultado do teste](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu alertar relativamente a problemas, avance para o próximo tutorial para saber como analisar a forma como os usuários estão interagindo com a sua aplicação.

> [!div class="nextstepaction"]
> [Compreender os utilizadores](../../azure-monitor/learn/tutorial-users.md)