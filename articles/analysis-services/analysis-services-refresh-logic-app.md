---
title: Refresque com aplicativos lógicos para modelos de Serviços de Análise Azure | Microsoft Docs
description: Este artigo descreve como codificar atualização assíncrona para os Serviços de Análise Azure utilizando apps Azure Logic.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 8a8d434fca7cab4432f38fc64093cf1fe060bd5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019091"
---
# <a name="refresh-with-logic-apps"></a>Atualizar com o Logic Apps

Ao utilizar as aplicações lógicas e as chamadas REST, pode realizar operações automatizadas de atualização de dados nos seus modelos tabulares Azure Analysis, incluindo sincronização de réplicas apenas de leitura para a escala de consulta.

Para saber mais sobre a utilização de APIs REST com Serviços de Análise Azure, consulte [atualização Assíncrona com a API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token Azure Ative (OAuth 2) válido.  Os exemplos deste artigo utilizarão um Principal de Serviço (SPN) para autenticar os Serviços de Análise Azure. Para saber mais, consulte [Criar um principal de serviço utilizando o portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="design-the-logic-app"></a>Desenhe o aplicativo de lógica

> [!IMPORTANT]
> Os exemplos a seguir assumem que a firewall dos Serviços de Análise Azure está desativada. Se a firewall estiver ativada, o endereço IP público do iniciador de pedidos deve ser adicionado à lista aprovada na firewall dos Serviços de Análise Azure. Para saber mais sobre as gamas IP de Apps Lógicas Azure por região, consulte [limites e informações de configuração para Apps Azure Logic](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="create-a-service-principal-spn"></a>Criar um diretor de serviço (SPN)

Para saber mais sobre a criação de um Diretor de Serviços, consulte [Criar um principal de serviço utilizando o portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configure permissões em Serviços de Análise Azure
 
O Diretor de Serviço que cria deve ter permissões de administrador de servidores no servidor. Para saber mais, consulte [Adicionar um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configure a App Lógica

Neste exemplo, a Aplicação Lógica foi concebida para desencadear quando um pedido HTTP é recebido. Isto permitirá a utilização de uma ferramenta de orquestração, como a Azure Data Factory, para desencadear a atualização do modelo Azure Analysis Services.

Uma vez criada uma App Lógica:

1. No designer da Aplicação Lógica, escolha a primeira ação como **Quando um pedido HTTP for recebido**.

   ![Adicionar atividade recebida HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Este passo irá povoar-se com o URL HTTP POST assim que a Aplicação Lógica for guardada.

2. Adicione um novo passo e procure **http**.  

   ![Screenshot da secção "Escolha uma ação" com o azulejo "HTTP" selecionado.](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Screenshot da janela "HTTP" com o azulejo "HTTP - HTTP" selecionado.](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecione **HTTP** para adicionar esta ação.

   ![Adicionar atividade HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configure a atividade HTTP da seguinte forma:

|Propriedade  |Valor  |
|---------|---------|
|**Método**     |POST         |
|**URI**     | https://*região do servidor*/servidores/*nome do servidor aas*/modelos/*o nome da base de dados*/refreshes <br /> <br /> Por exemplo: https: \/ /westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Cabeçalhos**     |   Tipo de Conteúdo, aplicação/json <br /> <br />  ![Cabeçalhos](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Para saber mais sobre a formação do corpo de pedido, consulte [atualização Assíncrono com a API REST - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticação**     |Diretório Ativo OAuth         |
|**Inquilino**     |Preencha o seu Azure Ative Directory TenantId         |
|**Audiência**     |https://*.asazure.windows.net         |
|**ID de Cliente**     |Insira o seu nome principal de serviço ClienteID         |
|**Tipo credencial**     |Segredo         |
|**Segredo**     |Insira o seu serviço principal nome secreto         |

Exemplo:

![Atividade HTTP concluída](./media/analysis-services-async-refresh-logic-app/7.png)

Agora teste a App Lógica.  No designer da Aplicação Lógica, clique em **Executar.**

![Testar a Aplicação Lógica](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Consumir a App Lógica com Azure Data Factory

Uma vez guardada a Aplicação Lógica, reveja a atividade **quando um pedido HTTP é recebido** e, em seguida, copie o URL HTTP **POST** que agora é gerado.  Este é o URL que pode ser usado pela Azure Data Factory para fazer a chamada assíncrona para desencadear a App Lógica.

Aqui está um exemplo Azure Data Factory Web Activity que faz esta ação.

![Atividade web da fábrica de dados](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Use uma app lógica independente

Se não planeia utilizar uma ferramenta de Orquestração, como a Data Factory, para desencadear a atualização do modelo, pode definir a aplicação lógica para desencadear a atualização com base numa programação.

Utilizando o exemplo acima, elimine a primeira atividade e substitua-a por uma atividade **Agendar.**

![Screenshot que mostra a página "Logic Apps" com o azulejo "Agendar" selecionado.](./media/analysis-services-async-refresh-logic-app/12.png)

![Screenshot que mostra a página "Triggers".](./media/analysis-services-async-refresh-logic-app/13.png)

Este exemplo utilizará **a Recorrência.**

Uma vez adicionada a atividade, configuure o Intervalo e a Frequência, em seguida, adicione um novo parâmetro e escolha **nestas horas**.

![Screenshot que mostra a secção "Recorrência" com o parâmetro "A estas horas" selecionado.](./media/analysis-services-async-refresh-logic-app/16.png)

Selecione as horas desejadas.

![Atividade de Agenda](./media/analysis-services-async-refresh-logic-app/15.png)

Salve a App Lógica.

## <a name="next-steps"></a>Passos seguintes

[Amostras](analysis-services-samples.md)  
[API REST](/rest/api/analysisservices/servers)