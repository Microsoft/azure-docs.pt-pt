---
title: Atualizar com o Logic Apps para modelos do Azure Analysis Services | Documentos da Microsoft
description: Saiba como codificar atualização assíncrona com o Azure Logic Apps.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142547"
---
# <a name="refresh-with-logic-apps"></a>Atualizar com o Logic Apps

Ao utilizar o Logic Apps e chamadas REST, pode efetuar operações de atualização automática de dados em seus modelos em tabela de análise do Azure, incluindo a sincronização de réplicas só de leitura para aumentar horizontalmente a consulta.

Para saber mais sobre como utilizar as APIs REST com o Azure Analysis Services, veja [assíncrona atualizar com a API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Autenticação

Todas as chamadas têm de ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  Os exemplos neste artigo irão utilizar um Principal do serviço (SPN) para autenticar para o Azure Analysis Services. Para obter mais informações, consulte [criar um principal de serviço com o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Criar a aplicação lógica

> [!IMPORTANT]
> Os exemplos seguintes partem do princípio de que a firewall de Azure Analysis Services está desativada.  Se o firewall estiver ativado, o endereço IP público do Iniciador do pedido tem de ser na lista de permissões na firewall do Azure Analysis Services. Para saber mais sobre os intervalos de IP da aplicação lógica por região, veja [limites e informações de configuração para o Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="create-a-service-principal-spn"></a>Criar um Principal de serviço (SPN)

Para saber mais sobre como criar um Principal de serviço, veja [criar um principal de serviço com o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurar as permissões no Azure Analysis Services
 
O Principal de serviço que cria tem de ter permissões de administrador do servidor no servidor. Para obter mais informações, consulte [adicionar um principal de serviço para a função de administrador de servidor](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Configurar a aplicação lógica

Neste exemplo, a aplicação lógica foi concebida para acionar quando é recebido um pedido HTTP. Isto permite a utilização de uma ferramenta de orquestração, como o Azure Data Factory, para acionar a atualização de modelo do Azure Analysis Services.

Depois de criar uma aplicação lógica:

1. No Estruturador da aplicação de lógica, escolha a primeira ação como **pedido de HTTP de quando é recebido**.

   ![Adicionar a atividade HTTP recebido](./media/analysis-services-async-refresh-logic-app/1.png)

Este passo será preenchido com o URL do HTTP POST, assim que a aplicação lógica é guardada.

2. Adicionar um novo passo e procure **HTTP**.  

   ![Adicionar a atividade HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Adicionar a atividade HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecione **HTTP** para adicionar esta ação.

   ![Adicionar a atividade HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configure a atividade HTTP da seguinte forma:

|Propriedade  |Value  |
|---------|---------|
|**Método**     |POST         |
|**URI**     | https://*sua região de servidor*/servers/*nome do servidor aas*/models/*seu nome de base de dados*/ <br /> <br /> Por exemplo: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Headers** (Cabeçalhos)     |   Content-Type, application/json <br /> <br />  ![Cabeçalhos](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corpo**     |   Para saber mais sobre formando o corpo do pedido, consulte [assíncrona atualizar com a API REST - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Autenticação**     |Active Directory OAuth         |
|**Tenant**     |Preencha o TenantId do Azure Active Directory         |
|**Público-alvo**     |https://*.asazure.windows.net         |
|**ID de cliente**     |Introduza o seu ID de cliente de nome Principal de serviço         |
|**Tipo de credencial**     |Secret         |
|**Segredo**     |Introduza o seu segredo de nome Principal de serviço         |

Exemplo:

![Atividade HTTP concluída](./media/analysis-services-async-refresh-logic-app/7.png)

Agora teste a aplicação lógica.  No Estruturador da aplicação de lógica, clique em **executar**.

![Testar a aplicação lógica](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Consumir a aplicação lógica com o Azure Data Factory

Depois da aplicação lógica é guardada, reveja os **pedido de HTTP de quando é recebido** atividade e copie a **URL do HTTP POST** que agora é gerado.  Este é o URL que pode ser utilizado pelo Azure Data Factory para fazer a chamada assíncrona para o acionador da aplicação lógica.

Eis um exemplo do Azure Data Factory Web atividade que faz esta ação.

![Atividade de Web de fábrica de dados](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Utilizar uma aplicação de lógica autónomo

Se não pretender utilizar uma ferramenta de orquestração, como o Data Factory para acionar a atualização do modelo, pode definir a aplicação lógica para acionar a atualização com base numa agenda.

Com o exemplo acima, elimine a primeira atividade e substitua-o por um **agenda** atividade.

![Atividade de agenda](./media/analysis-services-async-refresh-logic-app/12.png)

![Atividade de agenda](./media/analysis-services-async-refresh-logic-app/13.png)

Este exemplo utilizará **periodicidade**.

Depois de adicionada a atividade, configurar o intervalo e a frequência, em seguida, adicionar um novo parâmetro e escolha **a estas horas**.

![Atividade de agenda](./media/analysis-services-async-refresh-logic-app/16.png)

Selecione as horas de desejadas.

![Atividade de agenda](./media/analysis-services-async-refresh-logic-app/15.png)

Guarde a aplicação lógica.

## <a name="next-steps"></a>Passos Seguintes

[Amostras](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
