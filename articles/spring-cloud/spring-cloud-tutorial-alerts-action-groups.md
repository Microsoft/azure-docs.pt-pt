---
title: 'Tutorial: monitorar os recursos do Azure Spring Cloud usando alertas e grupos de ação | Microsoft Docs'
description: Saiba como usar os alertas do Spring Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694006"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Tutorial: monitorar recursos de nuvem Spring usando alertas e grupos de ações

Os alertas do Azure Spring Cloud dão suporte ao monitoramento de recursos com base em condições como o armazenamento disponível, a taxa de solicitações ou o uso de dados. Um alerta envia uma notificação quando as taxas ou condições atendem às especificações definidas.

Há duas etapas para configurar um pipeline de alerta: 
1. Configure um grupo de ações com as ações a serem tomadas quando um alerta for disparado, como email, SMS, runbook ou webhook. Os grupos de ações podem ser reutilizados entre diferentes alertas.
2. Configurar regras de alerta. As regras associam padrões de métrica com os grupos de ação com base no recurso de destino, métrica, condição, agregação de tempo, etc.

## <a name="prerequisites"></a>Pré-requisitos
Além dos requisitos do Azure Spring, este tutorial depende dos recursos a seguir.

* Uma instância do Azure Spring Cloud implantada.  Siga nosso [início rápido](spring-cloud-quickstart-launch-app-cli.md) para começar.

* Um recurso do Azure para monitorar, por exemplo, o banco de dados implementado neste artigo: [como usar o Spring Data API do Apache Cassandra com Azure Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
 
Os procedimentos a seguir inicializam o **grupo de ações** e o **alerta** a partir da opção **alertas** no painel de navegação esquerdo de uma instância do Spring Cloud. (O procedimento também pode ser iniciado na página **visão geral do monitor** do portal do Azure.) 

Navegue de um grupo de recursos para sua instância do Spring Cloud. Selecione **alertas** no painel esquerdo e, em seguida, selecione **Gerenciar ações**:

![Página do grupo de recursos do portal de captura de tela](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Configurar grupo de ações

Para iniciar o procedimento para inicializar um novo **grupo de ação**, selecione **+ Adicionar grupo de ação**.

![Portal de captura de tela Adicionar grupo de ação](media/alerts-action-groups/action-1.png)

Na página **Adicionar grupo de ações** :

 1. Especifique um **nome de grupo de ação** e um **nome curto**.

 1. Especifique a **assinatura** e o **grupo de recursos**.

 1. Especifique o **nome da ação**.

 1. Selecione o **tipo de ação**.  Isso abrirá outro painel à direita para definir a ação que será tomada na ativação.

 1. Defina a ação usando as opções no painel direito.  Esse caso usa a notificação por email.

 1. Clique em **OK** no painel de ação à direita.

 1. Clique em **OK** na caixa de diálogo **Adicionar grupo de ações** . 

  ![Portal de captura de tela definir ação](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Configurar alerta 

As etapas anteriores criaram um **grupo de ação** que usa email. Você também pode usar a notificação por telefone, WebHooks, Azure functions, etc.  

Para configurar um **alerta**, navegue de volta para a página **alertas** e clique em **gerenciar regras de alerta**.

  ![Portal de captura de tela definir alerta](media/alerts-action-groups/alerts-2.png)

1. Selecione o **recurso** para o alerta.

1. Clique em **+ nova regra de alerta**.

  ![Nova regra de alerta do portal de captura de tela](media/alerts-action-groups/alerts-3.png)

1. Na página **criar regra** , especifique o **recurso**, a **condição**e as **ações**.  No painel **ações** , selecione o **grupo**de ações definido anteriormente.

1. Em **detalhes do alerta**, nomeie a regra de alerta.

1. Clique em **criar regra de alerta**.

  ![Nova regra de alerta do portal de captura de tela](media/alerts-action-groups/alerts-4.png)

Verifique se a nova regra de alerta está habilitada.

  ![Nova regra de alerta do portal de captura de tela](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Passos seguintes
* [Criar e gerenciar grupos de ação no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Comportamento de alerta do SMS em grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Tutorial: usando o rastreamento distribuído com o Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
