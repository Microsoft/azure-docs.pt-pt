---
title: Ligue provance ao conector de gestão de serviços de TI
description: Este artigo fornece informações sobre como provance com o It Service Management Connector (ITSMC) em Azure Monitor para monitorizar e gerir centralmente os itens de trabalho ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 542b127823a73058f319e6a39c001bd563f042ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045382"
---
# <a name="connect-provance-with-it-service-management-connector"></a>Ligue provance ao conector de gestão de serviços de TI

Este artigo fornece informações sobre como configurar a ligação entre a sua instância Provance e o It Service Management Connector (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho.

> [!NOTE]
> A partir de 1 de outubro-2020 a integração do ITSM com o Azure Alert deixará de ser ativada para novos clientes. As novas Ligações ITSM não serão suportadas.
> As ligações ITSM existentes serão suportadas.

As seguintes secções fornecem detalhes sobre como ligar o seu produto Provance ao ITSMC em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Assegurar que os seguintes pré-requisitos sejam cumpridos:

- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-definition.md#add-it-service-management-connector).
- A App Provance deve ser registada no Azure AD - e o ID do cliente está disponível. Para obter informações [detalhadas, consulte como configurar a autenticação ativa do diretório](../../app-service/configure-authentication-provider-aad.md).

- Função de utilizador: Administrador.

## <a name="connection-procedure"></a>Procedimento de ligação

Utilize o seguinte procedimento para criar uma ligação à Provance:

1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (Seu Nome De Espaço-Trabalho)**

2. No **âmbito do WORKSPACE DATA SOURCES** clique em **ItsM Connections**.
    ![Nova ligação](media/itsmc-overview/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações como descrito na tabela seguinte e clique **em OK** para criar a ligação.

> [!NOTE]
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome de conexão**   | Digite um nome para a instância Provance que pretende ligar com o ITSMC.  Utilize este nome mais tarde quando configurar itens de trabalho neste ITSM/ ver análises detalhadas de registo. |
| **Tipo de parceiro**   | Selecione **Provance**. |
| **Nome de Utilizador**   | Digite o nome de utilizador que pode ligar-se ao ITSMC.    |
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome de utilizador e a palavra-passe são utilizados apenas para gerar fichas de autenticação e não são armazenados em qualquer parte dentro do serviço ITSMC.|
| **URL do servidor**   | Digite o URL da sua instância provance que pretende ligar ao ITSMC. |
| **ID de Cliente**   | Digite o ID do cliente para autenticar esta ligação, que gerou no seu caso Provance.  Mais informações sobre o ID do cliente, veja [como configurar a autenticação ativa do diretório.](../../app-service/configure-authentication-provider-aad.md) |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho provance que pretende sincronizar com o Azure Log Analytics, através do ITSMC.  Estes artigos de trabalho são importados para análise de registos.   **Opções:**   Incidentes, Pedidos de Alteração.|
| **Dados de Sincronização** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os TEC afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado.|

![Screenshot que realça as listas de Nome de Ligação e Tipo de Parceiro.](media/itsmc-connections-provance/itsm-connections-provance-latest.png)

**Quando conectado com sucesso, e sincronizado:**

- Os artigos de trabalho selecionados desta instância provance são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure neste caso provance.

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral do Conector do ITSM](itsmc-overview.md)
* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)