---
title: Atualizar com Aplicações Lógicas para modelos de Serviços de Análise Azure [ Microsoft Docs
description: Este artigo descreve como codificar a atualização assíncrona para os Serviços de Análise Azure utilizando aplicações da Azure Logic.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126992"
---
# <a name="refresh-with-logic-apps"></a>Atualizar com o Logic Apps

Ao utilizar aplicações lógicas e chamadas REST, pode realizar operações automatizadas de atualização de dados nos seus modelos tabular de Análise Azure, incluindo a sincronização de réplicas apenas de leitura para consulta de escala.

Para saber mais sobre a utilização de APIs REST com serviços de análise azure, consulte [atualização asíncrona com a API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um símbolo de Diretório Ativo Azure (OAuth 2) válido.  Os exemplos deste artigo usarão um Diretor de Serviço (SPN) para autenticar os Serviços de Análise Azure. Para saber mais, consulte [Criar um serviço principal utilizando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Desenhe a aplicação lógica

> [!IMPORTANT]
> Os exemplos que se seguem assumem que a firewall dos Serviços de Análise Azure está desativada. Se a firewall estiver ativada, o endereço IP público do iniciador de pedido deve ser listado na firewall dos Serviços de Análise do Azure. Para saber mais sobre as gamas IP das Aplicações Lógicas Azure por região, consulte [Limites e informações de configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="create-a-service-principal-spn"></a>Criar um Diretor de Serviço (SPN)

Para aprender sobre a criação de um Diretor de Serviço, consulte Criar um diretor de [serviço utilizando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configure permissões nos Serviços de Análise Do Azure
 
O Diretor de Serviço que cria deve ter permissões de administrador de servidor no servidor. Para saber mais, consulte [Adicionar um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configure a Aplicação Lógica

Neste exemplo, a Aplicação Lógica foi concebida para desencadear quando um pedido HTTP é recebido. Isto permitirá a utilização de uma ferramenta de orquestração, como a Azure Data Factory, para desencadear a atualização do modelo Azure Analysis Services.

Uma vez criado uma Aplicação Lógica:

1. No designer de Aplicações Lógicas, escolha a primeira ação como **Quando um pedido HTTP é recebido**.

   ![Adicionar HTTP recebeu atividade](./media/analysis-services-async-refresh-logic-app/1.png)

Este passo irá povoar com o URL HTTP POST assim que a Aplicação Lógica for guardada.

2. Adicione um novo passo e procure **HTTP**.  

   ![Adicionar atividade http](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Adicionar atividade http](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecione **HTTP** para adicionar esta ação.

   ![Adicionar atividade http](./media/analysis-services-async-refresh-logic-app/2.png)

Configure a atividade http da seguinte forma:

|Propriedade  |Valor  |
|---------|---------|
|**Método**     |POST         |
|**URI**     | https://*a região do servidor*/servidores/ nome /modelos do servidor*aas/* o seu nome de base de*dados*/refreshes <br /> <br /> Por exemplo:\/https: /westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Cabeçalhos**     |   Tipo de conteúdo, aplicação/json <br /> <br />  ![Cabeçalhos](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Para saber mais sobre a formação do organismo de pedido, consulte [atualização assíncrona com a REST API - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticação**     |Diretório Ativo OAuth         |
|**Inquilino**     |Preencha o seu Inquilino De Diretório Ativo Azure         |
|**Audiência**     |https://*.asazure.windows.net         |
|**ID do cliente**     |Insira o seu nome principal de serviço ClientID         |
|**Tipo credencial**     |Segredo         |
|**Segredo**     |Insira o seu segredo de nome principal de serviço         |

Exemplo:

![Atividade de HTTP concluída](./media/analysis-services-async-refresh-logic-app/7.png)

Agora teste a Aplicação Lógica.  No designer de aplicações lógicas, clique em **Executar**.

![Testar a Aplicação Lógica](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Consumir a App Lógica com fábrica de dados azure

Uma vez que a Aplicação Lógica seja guardada, reveja o **pedido Quando um pedido HTTP é recebido** e, em seguida, copie o URL HTTP **POST** que agora é gerado.  Este é o URL que pode ser usado pela Azure Data Factory para fazer a chamada assíncrona para desencadear a Aplicação Lógica.

Aqui está um exemplo da Azure Data Factory Web Activity que faz esta ação.

![Atividade Web da fábrica de dados](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Use uma aplicação lógica autossuficiente

Se não planeia utilizar uma ferramenta de Orquestração, como a Data Factory, para desencadear a atualização do modelo, pode definir a aplicação lógica para desencadear a atualização com base numa programação.

Utilizando o exemplo acima, elimine a primeira atividade e substitua-a por uma atividade de **Agenda.**

![Atividade de Agenda](./media/analysis-services-async-refresh-logic-app/12.png)

![Atividade de Agenda](./media/analysis-services-async-refresh-logic-app/13.png)

Este exemplo utilizará **a Recurrence**.

Uma vez adicionada a atividade, configure o Intervalo e a Frequência, adicione um novo parâmetro e escolha **nestas horas**.

![Atividade de Agenda](./media/analysis-services-async-refresh-logic-app/16.png)

Selecione as horas procuradas.

![Atividade de Agenda](./media/analysis-services-async-refresh-logic-app/15.png)

Salve a Aplicação Lógica.

## <a name="next-steps"></a>Passos seguintes

[Amostras](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
