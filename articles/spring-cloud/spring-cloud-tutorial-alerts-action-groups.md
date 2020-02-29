---
title: 'Tutorial: Monitor Azure Spring Cloud recursos usando alertas e grupos de ação  Microsoft Docs'
description: Aprenda a usar alertas de Nuvem de primavera.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920081"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Tutorial: Monitor ize os recursos da Nuvem de primavera utilizando alertas e grupos de ação

A Azure Spring Cloud alerta para o suporte de recursos de monitorização com base em condições como armazenamento disponível, taxa de pedidos ou utilização de dados. Um alerta envia notificação quando as tarifas ou condições cumprem as especificações definidas.

Há dois passos para criar um oleoduto de alerta: 
1. Criar um Grupo de Ação com as ações a tomar quando um alerta é desencadeado, tais como e-mail, SMS, Runbook ou Webhook. Os Grupos de Ação podem ser reutilizados entre diferentes alertas.
2. Estabeleça miná-lo em regras de alerta. As regras ligam os padrões métricos com os grupos de ação baseados no recurso-alvo, métrica, condição, agregação do tempo, etc.

## <a name="prerequisites"></a>Pré-requisitos
Para além dos requisitos da primavera Azure, este tutorial depende dos seguintes recursos.

* Um caso de Nuvem de primavera Azure implantado.  Siga o nosso [início rápido](spring-cloud-quickstart-launch-app-cli.md) para começar.

* Um recurso Azure para monitorizar. Este exemplo monitoriza uma instância da Nuvem de primavera.
 
Os seguintes procedimentos inicializam tanto o **Grupo de Ação** como o Alerta **a** partir da opção **Alertas** no painel de navegação esquerdo de uma instância de Nuvem de primavera. (O procedimento também pode partir da página **de visão geral** do monitor do portal Azure.) 

Navegue de um grupo de recursos para a sua instância de Nuvem de primavera. Selecione **Alertas** no painel esquerdo e, em seguida, selecione **As ações de gestão:**

![Página do grupo de recursos do portal screenshot](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Criar grupo de ação

Para iniciar o procedimento para inicializar um novo Grupo de **Ação,** selecione **+ Adicionar grupo de ação**.

![Screenshot portal Adicionar grupo de ação](media/alerts-action-groups/action-1.png)

Na página do **grupo de ação Add:**

 1. Especifique um nome de **grupo de ação** e um nome **curto**.

 1. Especificar o grupo **de subscrição** e **recursos.**

 1. Especificar **o nome de ação**.

 1. Selecione **Tipo de ação**.  Isto abrirá mais um painel sobre o direito de definir a ação que será tomada na ativação.

 1. Defina a ação utilizando as opções no painel direito.  Este caso utiliza notificação por e-mail.

 1. Clique **OK** no painel de ação certo.

 1. Clique **OK** no diálogo do grupo de **ação Adicionar.** 

  ![Screenshot Portal define ação](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Configurar alerta 

Os passos anteriores criaram um **Grupo de Ação** que utiliza e-mails. Também pode utilizar notificações telefónicas, webhooks, funções Azure, etc.  

Para configurar um **Alerta,** navegue de volta para a página **Alertas** e clique em **Gerir regras**de alerta .

  ![Screenshot Portal definir alerta](media/alerts-action-groups/alerts-2.png)

1. Selecione o **Recurso** para o alerta.

1. Clique **+ Nova regra de alerta**.

   ![Screenshot Portal nova regra de alerta](media/alerts-action-groups/alerts-3.png)

1. Na página de **regra Criar,** especifique o **RECURSO**.

1. A definição **DE CONDIÇÃO** fornece muitas opções para monitorizar os seus recursos da Nuvem **de primavera.**  Clique **em Adicionar** para abrir o painel lógico de sinal **configurar.**

1. Selecione uma condição. Este exemplo utiliza **a Percentagem de Utilização**do CPU do Sistema .

   ![Screenshot Portal nova regra de alerta](media/alerts-action-groups/alerts-3-1.png)

1. Desloque-se para baixo do painel lógico de **sinal configurar** para definir o **valor limiar** para monitorizar.

   ![Screenshot Portal nova regra de alerta](media/alerts-action-groups/alerts-3-2.png)

1. Clique em **Concluído**.

Para mais detalhes sobre as condições disponíveis para monitorizar, consulte [as opções](spring-cloud-concept-metrics.md#user-metrics-options)de métricas do portal do utilizador .

 Em **AÇÕES,** clique em **Selecionar grupo de ação**. A partir de **AÇÕES,** selecione o Grupo de **Ação**previamente definido .

   ![Screenshot Portal nova regra de alerta](media/alerts-action-groups/alerts-3-3.png) 

1. Percorra para baixo e, em DETALHES DE **ALERTA,** nomeie a regra do alerta.

1. Definir a **gravidade.**

1. Clique **em Criar a regra**de alerta .

   ![Screenshot Portal nova regra de alerta](media/alerts-action-groups/alerts-3-4.png)

Verifique se a nova regra de alerta está ativada.

   ![Screenshot Portal nova regra de alerta](media/alerts-action-groups/alerts-4.png)

Uma regra também pode ser criada usando a página **Métricas:**

   ![Screenshot Portal nova regra de alerta](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Passos seguintes
* [Opções de métricas do portal do utilizador](spring-cloud-concept-metrics.md#user-metrics-options)
* [Criar e gerir grupos de ação no portal Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Comportamento de alerta sms em grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Tutorial: Utilização de Rastreio Distribuído com Nuvem de primavera Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
