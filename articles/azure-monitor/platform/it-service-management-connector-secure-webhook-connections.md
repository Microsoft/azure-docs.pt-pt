---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure
description: Este artigo mostra-lhe como ligar os seus produtos/serviços ITSM com a Secure Export in Azure Monitor para monitorizar e gerir centralmente os artigos de trabalho ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 1cd8041f801a418f67d26461c5f4e9ebff7e5c30
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507307"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Ligue o Azure às ferramentas ITSM utilizando a Secure Export

Este artigo mostra-lhe como configurar a ligação entre o seu produto ou serviço de Gestão de Serviços de TI (ITSM) utilizando a Secure Export.

Secure Export é uma versão atualizada do Connector de Gestão de [Serviços de TI (ITSMC)](./itsmc-overview.md). Ambas as versões permitem criar itens de trabalho numa ferramenta ITSM quando o Azure Monitor envia alertas. A funcionalidade inclui alertas métricos, de log e de registo de atividade.

O ITSMC utiliza o nome de utilizador e as credenciais de senha. A Secure Export tem uma autenticação mais forte porque utiliza o Azure Ative Directory (Azure AD). O Azure AD é o serviço da Microsoft baseado na cloud para a gestão de identidades e acessos. Ajuda os utilizadores a iniciar seduca e a aceder a recursos internos ou externos. A utilização do Azure AD com o ITSM ajuda a identificar os alertas do Azure (através do ID da aplicação Azure) que foram enviados para o sistema externo.

> [!NOTE]
> A capacidade de ligar o Azure às ferramentas ITSM utilizando a Secure Export está em pré-visualização.

## <a name="secure-export-architecture"></a>Arquitetura de exportação segura

A arquitetura Secure Export introduz as seguintes novas capacidades:

* **Novo grupo de ação**: Os alertas são enviados para a ferramenta ITSM através do grupo de ação Secure Webhook, em vez do grupo de ação ITSM que o ITSMC utiliza.
* **Autenticação Azure AD**: A autenticação ocorre através do Azure AD em vez de credenciais de nome de utilizador/palavra-passe.

## <a name="secure-export-data-flow"></a>Fluxo seguro de dados de exportação

Os passos do fluxo de dados de exportação segura são:

1. O Azure Monitor envia um alerta configurado para usar a Exportação Segura.
2. A carga útil de alerta é enviada por uma ação Secure Webhook para a ferramenta ITSM.
3. A aplicação ITSM verifica com Azure AD se o alerta está autorizado a entrar na ferramenta ITSM.
4. Se o alerta for autorizado, o pedido:
   
   1. Cria um item de trabalho (por exemplo, um incidente) na ferramenta ITSM.
   2. Liga o ID do item de configuração (CI) à base de dados de gestão do cliente (CMDB).

![Diagrama que mostra como a ferramenta ITSM comunica com Azure A D, alertas Azure e um grupo de ação.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Benefícios da exportação segura

Os principais benefícios da integração são:

* **Melhor autenticação**: A Azure AD proporciona uma autenticação mais segura sem os intervalos que normalmente ocorrem no ITSMC.
* **Alertas resolvidos na ferramenta ITSM**: Alertas métricos implementam estados "disparados" e "resolvidos". Quando a condição é satisfeita, o estado de alerta é "disparado". Quando a condição já não é cumprida, o estado de alerta é "resolvido". No ITSMC, os alertas não podem ser resolvidos automaticamente. Com a Secure Export, o estado resolvido flui para a ferramenta ITSM e assim é atualizado automaticamente.
* **[Esquema comum de alerta](./alerts-common-schema.md)**: No ITSMC, o esquema da carga útil de alerta difere com base no tipo de alerta. Na Exportação Segura, há um esquema comum para todos os tipos de alerta. Este esquema comum contém o CI para todos os tipos de alerta. Todos os tipos de alerta poderão ligar o seu CI ao CMDB.

Comece a utilizar a ferramenta ITSM Connector com estes passos:

1. Registar a aplicação com o Azure AD.
2. Crie um grupo de ação Secure Webhook.
3. Configure o ambiente do seu parceiro. 

A Secure Export suporta ligações com as seguintes ferramentas ITSM:
* [ServiceNow](#connect-servicenow-to-azure-monitor)
* [Hélice BMC](#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>Registe-se no Azure Ative Directory

Siga estes passos para registar a candidatura com a Azure AD:

1. Siga os passos no [Registo de uma aplicação com a plataforma de identidade da Microsoft.](../../active-directory/develop/quickstart-register-app.md)
2. Em Azure AD, selecione **a aplicação Expor**.
3. Selecione **Conjunto** para **ID URI de aplicação.**

   [![Screenshot da opção para definir o U R I da aplicação I D.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Selecione **Guardar**.

## <a name="create-a-secure-webhook-action-group"></a>Criar um grupo de ação Secure Webhook

Depois da sua aplicação estar registada no Azure AD, pode criar itens de trabalho na sua ferramenta ITSM com base em alertas Azure, utilizando a ação Secure Webhook em grupos de ação.

Os grupos de ação fornecem uma forma modular e reutilizável de desencadear ações para alertas Azure. Pode utilizar grupos de ação com alertas métricos, alertas de Registo de Atividade e alertas Azure Log Analytics no portal Azure.
Para saber mais sobre grupos de ação, consulte [Criar e gerir grupos de ação no portal Azure.](./action-groups.md)

Para adicionar um webhook a uma ação, siga estas instruções para Secure Webhook:

1. No [portal Azure,](https://portal.azure.com/)procure e selecione **Monitor**. O **painel monitor** consolida todas as suas definições e dados de monitorização numa única vista.
2. Selecione **Alertas**  >  **Gerir ações**.
3. [Selecione Adicionar grupo de ação](./action-groups.md#create-an-action-group-by-using-the-azure-portal)e preencha os campos.
4. Introduza um nome na caixa **de nome do grupo Action** e introduza um nome na caixa de **nomes Curto.** O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.
5. Selecione **Secure Webhook**.
6. Selecione estes detalhes:
   1. Selecione o ID do objeto da instância do Diretório Ativo Azure que registou.
   2. Para o URI, cole no URL webhook que copiou do ambiente de [ferramentas ITSM](#configure-the-itsm-tool-environment).
   3. Definir **Ative o esquema de alerta comum** para **Sim**. 

   A imagem a seguir mostra a configuração de uma ação Secure Webhook de uma amostra:

   ![Screenshot que mostra uma ação Secure Webhook.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Configure o ambiente da ferramenta ITSM

A configuração contém 2 passos:
1. Obtenha o URI para a definição de exportação segura.
2. Definições de acordo com o fluxo da ferramenta ITSM.


### <a name="connect-servicenow-to-azure-monitor"></a>Ligue o ServiçoNow ao Monitor Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto ServiceNow e a Secure Export em Azure.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que cumpriu os seguintes pré-requisitos:

* A Azure AD está registada.
* Tem a versão suportada da ServiceNow Event Management - ITOM (versão Orlando ou posterior).

### <a name="configure-the-servicenow-connection"></a>Configure a ligação ServiceNow

1.Utilize o link https://(nome de instância).serviço-agora.com/api/sn_em_connector/em/inbound_event?source=azuremonitor o URI para a definição de exportação segura.

2. Siga as instruções de acordo com a versão:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Rio Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Nova Iorque](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)

### <a name="connect-bmc-helix-to-azure-monitor"></a>Ligue a Helix BMC ao Monitor Azure

As seguintes secções fornecem detalhes sobre como ligar o seu produto BMC Helix e a Secure Export em Azure.

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que cumpriu os seguintes pré-requisitos:

* A Azure AD está registada.
* Tem a versão suportada da BMC Helix Multi-Cloud Service Management (versão 19.08 ou posterior).

### <a name="configure-the-bmc-helix-connection"></a>Configure a ligação da BMC Helix

1.Utilizar o seguinte procedimento no ambiente da Hélice BMC, a fim de obter o URI para a exportação segura:

   1. Faça login no Integration Studio.
   2. Procure o **fluxo de Alertas Create.**
   3. Copie o URL webhook .
   
   ![Screenshot do webhook U R L in Integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. Siga as instruções de acordo com a versão:
   * [Permitir a integração pré-construída com o Azure Monitor para a versão 20.02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html).
   * [Permitir a integração pré-construída com o Azure Monitor para a versão 19.11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html).

3. Como parte da configuração da ligação na BMC Helix, entre na sua integração BMC instância e siga estas instruções:

   1. Selecione **catálogo**.
   2. Selecione **alertas Azure**.
   3. Selecione **conectores**.
   4. Selecione **a configuração**.
   5. Selecione a nova configuração **de ligação adicionar.**
   6. Preencha as informações para a secção de configuração:
      - **Nome:** Faça o seu próprio.
      - **Tipo de autorização**: **NENHUM**
      - **Descrição:** Invente o seu próprio.
      - **Local**: **Nuvem**
      - **Número de ocorrências**: **2**, o valor predefinido.
      - **Verifique:** Selecionado por predefinição para ativar a utilização.
      - O ID do inquilino Azure e o ID do pedido Azure são retirados da aplicação que definiu anteriormente.

![Screenshot que mostra a configuração do BMC.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)




## <a name="next-steps"></a>Passos seguintes

* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md)
