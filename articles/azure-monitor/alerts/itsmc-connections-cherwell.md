---
title: Ligue Cherwell ao conector de gestão de serviços de TI
description: Este artigo fornece informações sobre como cherwell com o It Service Management Connector (ITSMC) em Azure Monitor para monitorizar e gerir centralmente os itens de trabalho ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 6d9d8136ef0936927af238726e30c7feed7d0b83
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618021"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Ligue Cherwell ao conector de gestão de serviços de TI

Este artigo fornece informações sobre como configurar a ligação entre a sua instância Cherwell e o It Service Management Connector (ITSMC) no Log Analytics para gerir centralmente os seus itens de trabalho.

> [!NOTE]
> A partir de 1 de Outubro-2020 a integração da Cherwell ITSM com o Azure Alert deixará de ser ativada para novos clientes. As novas Ligações ITSM não serão suportadas.
> As ligações ITSM existentes serão suportadas.

As seguintes secções fornecem detalhes sobre como ligar o seu produto Cherwell ao ITSMC em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Assegurar que os seguintes pré-requisitos sejam cumpridos:

- ITSMC instalado. Mais informações: [Adicionar a Solução de Conector de Gestão de Serviços de TI](./itsmc-definition.md#add-it-service-management-connector).
- Identificação do cliente gerada. Mais informações: [Gere a identificação do cliente para a Cherwell.](#generate-client-id-for-cherwell)
- Função de utilizador: Administrador.

## <a name="connection-procedure"></a>Procedimento de ligação

Utilize o seguinte procedimento para criar uma ligação Cherwell:

1. No portal Azure, vá a **Todos os Recursos** e procure o **ServiceDesk (Seu Nome De Espaço-Trabalho)**

2. No **âmbito do WORKSPACE DATA SOURCES** clique em **ItsM Connections**.
    ![Nova ligação](/media/itsmc-overview/add-new-itsm-connection.png)

3. Na parte superior do painel direito, clique em **Adicionar**.

4. Forneça as informações como descrito na tabela seguinte e clique **em OK** para criar a ligação.

> [!NOTE]
> Todos estes parâmetros são obrigatórios.

| **Campo** | **Descrição** |
| --- | --- |
| **Nome de conexão**   | Digite um nome para a instância Cherwell que pretende ligar ao ITSMC.  Utilize este nome mais tarde quando configurar itens de trabalho neste ITSM/ ver análises detalhadas de registo. |
| **Tipo de parceiro**   | Selecione **Cherwell.** |
| **Nome de Utilizador**   | Digite o nome de utilizador Cherwell que pode ligar-se ao ITSMC. |
| **Palavra-passe**   | Digite a palavra-passe associada a este nome de utilizador. **Nota:** O nome de utilizador e a palavra-passe são utilizados apenas para gerar fichas de autenticação e não são armazenados em qualquer parte dentro do serviço ITSMC.|
| **URL do servidor**   | Digite o URL da sua instância Cherwell que pretende ligar ao ITSMC. |
| **ID de Cliente**   | Digite o ID do cliente para autenticar esta ligação, que gerou no seu caso Cherwell.   |
| **Âmbito de sincronização de dados**   | Selecione os itens de trabalho cherwell que pretende sincronizar através do ITSMC.  Estes artigos de trabalho são importados para análise de registos.   **Opções:**  Incidentes, Pedidos de Alteração. |
| **Dados de Sincronização** | Digite o número de dias passados de onde deseja os dados. **Limite máximo:** 120 dias. |
| **Criar novo item de configuração na solução ITSM** | Selecione esta opção se pretender criar os itens de configuração no produto ITSM. Quando selecionado, o ITSMC cria os TEC afetados como itens de configuração (em caso de TEC não existentes) no sistema ITSM suportado. **Predefinição:** desativado. |

![Ligação cherwell](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**Quando conectado com sucesso, e sincronizado:**

- Os artigos de trabalho selecionados desta instância cherwell são importados para a Azure **Log Analytics.** Pode ver o resumo destes artigos de trabalho no azulejo do conector de gestão de serviços de TI.

- Pode criar incidentes a partir de alertas de Log Analytics ou de registos de registos, ou a partir de alertas Azure neste caso Cherwell.

Saiba mais: [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Gerar iD de cliente para Cherwell

Para gerar o ID/chave do cliente para Cherwell, utilize o seguinte procedimento:

1. Faça login no seu caso Cherwell como administrador.
2. Clique   >  **nas definições do cliente API de Edição de** Segurança .
3. **Selecione Criar novo** segredo do  >  **cliente** cliente.

    ![Id de utilizador cherwell](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral do Conector do ITSM](itsmc-overview.md)
* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)