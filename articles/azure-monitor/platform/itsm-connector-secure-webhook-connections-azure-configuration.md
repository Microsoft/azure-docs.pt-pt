---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure - Configurações Azure
description: Este artigo mostra-lhe como configurar o Azure de forma a ligar os seus produtos/serviços ITSM com a Secure Export in Azure Monitor para monitorizar e gerir centralmente os artigos de trabalho da ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 5eb58c48acc7974a4379cf1993a73228c99f5e6d
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857579"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Configure Azure para ligar ferramentas ITSM usando a Exportação Segura

Este artigo fornece informações sobre como configurar o Azure para utilizar a "Exportação Segura".
Para utilizar a "Exportação Segura", siga estes passos:

1. [Registar a aplicação com o Azure AD.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Defina o diretor de serviço.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Crie um grupo de ação Secure Webhook.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Configure o ambiente do seu parceiro.
    A Secure Export suporta ligações com as seguintes ferramentas ITSM:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [Hélice BMC](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Registe-se no Azure Ative Directory

Siga estes passos para registar a candidatura com a Azure AD:

1. Siga os passos no [Registo de uma aplicação com a plataforma de identidade da Microsoft.](../../active-directory/develop/quickstart-register-app.md)
2. Em Azure AD, selecione **a aplicação Expor**.
3. Selecione **Conjunto** para **ID URI de aplicação.**

   [![Screenshot da opção para definir o U R I da aplicação I D.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Selecione **Guardar**.

## <a name="define-service-principal"></a>Definir diretor de serviço

O serviço Action Group necessitará de permissão para adquirir fichas de autenticação da sua aplicação AAD para autenticar agora com o Serviço. Para conceder essas permissões, terá de criar um principal serviço para o serviço action Group no seu inquilino.
Pode utilizar estes [comandos PowerShell](./action-groups.md#secure-webhook-powershell-script) para este fim. (Requer privilégios administrativos de inquilinos).
Como um passo opcional pode definir o papel da aplicação no manifesto da aplicação criada, o que lhe permite restringir ainda mais, o acesso de uma forma que apenas certas aplicações com esse papel específico podem enviar mensagens. Esta função tem então de ser atribuída ao diretor de serviço do Grupo de Ação. \
Este passo pode ser feito através dos [mesmos comandos PowerShell](./action-groups.md#secure-webhook-powershell-script).

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

A configuração contém dois passos:

1. Obtenha o URI para a definição de exportação segura.
2. Definições de acordo com o fluxo da ferramenta ITSM.

## <a name="next-steps"></a>Passos seguintes

* [Configuração de exportação segura do Serviço](./itsmc-secure-webhook-connections-servicenow.md)
* [Configuração de exportação segura da BMC](./itsmc-secure-webhook-connections-bmc.md)
