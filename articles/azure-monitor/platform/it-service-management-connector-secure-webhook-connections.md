---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure
description: Este artigo fornece informações sobre como ligar os seus produtos/serviços ITSM com o Secure Export in Azure Monitor para monitorizar e gerir centralmente os artigos de trabalho itsm.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 160054e7e98dc2cb06c2c7daf325536766963daa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568647"
---
# <a name="connect-azure-to-itsm-tools-using-secure-export"></a>Ligue o Azure às ferramentas ITSM utilizando a Secure Export

Este artigo fornece informações sobre como configurar a ligação entre o seu produto/serviço ITSM utilizando a Secure Export.

A Secure Export é e versão atualizada do [ITSMC](./itsmc-overview.md) (IT Service Management). Ambas as versões permitem criar itens de trabalho numa ferramenta ITSM quando o Azure Monitor alerta para o fogo. A funcionalidade inclui alertas de registo métrico, de log e de atividade.

[O ITSMC](./itsmc-overview.md) utiliza credenciais de utilizador e palavra-passe, enquanto a Secure Export tem uma autenticação mais forte porque utiliza o Azure Ative Directory (Azure AD). Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. Ajuda os utilizadores a iniciar seduca e a aceder a recursos internos ou externos. A utilização do Azure AD com o ITSM ajuda a identificar os alertas do Azure (utilizando o ID da aplicação Azure) que foram enviados para o sistema externo.

> [!NOTE]
> As ferramentas Connect Azure to ITSM utilizando a Secure Export estão em pré-visualização

## <a name="secure-export-architecture"></a>Arquitetura de Exportação Segura

A arquitetura de exportação segura introduz as seguintes novas capacidades:

* **Novo Grupo de Ação** - Os alertas são enviados para a ferramenta ITSM utilizando o grupo de ação secure webhook (em vez do Grupo de Ação ITSM que utiliza no ITSMC).
* **Autenticação Azure AD** - A autenticação ocorre utilizando Azure AD em vez de credenciais de utilizador/palavra-passe.

## <a name="secure-export-data-flow"></a>Fluxo seguro de dados de exportação

As etapas de fluxo de dados de exportação segura são:

1) Um alerta configurado para utilizar incêndios de exportação seguros no Monitor Azure
2) A carga útil de alerta é enviada por ação de webhook segura para a ferramenta ITSM.
3) A aplicação ITSM verifica com Azure AD se o alerta está autorizado a entrar na ferramenta ITSM.
4) Se o alerta for autorizado, o pedido é:
    1) Cria um item de trabalho (por exemplo, incidente) na ferramenta ITSM.
    2) Liga o ID de configuração (CI) à base de dados de gestão do cliente (CMDB).
![Diagrama do SeuM](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="connection-with-bmc-helix"></a>Conexão com a BMC Helix

A exportação segura suporta a BMC Helix. Alguns benefícios da integração são:

* **Melhor autenticação** – A AZure AD proporciona uma autenticação mais segura sem os intervalos que normalmente ocorrem no ITSMC.
* **Alertas resolvidos na ferramenta ITSM** – Os alertas métricos implementam um estado "disparado" e "resolvido". Quando a condição é satisfeita, o estado de alerta é "Disparado". Quando a condição já não é cumprida, o estado de alerta é "Resolvido". No ITSMC, os alertas não podiam ser resolvidos automaticamente. Com uma exportação segura, o estado resolvido flui para a ferramenta ITSM e assim atualiza automaticamente.
* **[O Esquema Comum permite](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)** – No ITSMC, o esquema da carga útil de alerta diferiu com base no tipo de alerta. Na exportação segura, somos um esquema comum para todos os tipos de alerta. Este novo esquema comum contém o CI para todos os tipos de alerta. Com isso, todos os tipos de alerta poderão ligar o seu CI ao CMDB.

Comece a utilizar o conector ITSM com estes passos:

1. Registe a sua aplicação com o Azure Ative Directory.
2. Crie um grupo de ação webhook seguro.
3. Configure o ambiente do seu parceiro.

## <a name="register-with-azure-active-directory"></a>Registe-se no Azure Ative Directory

Siga estes passos para registar a aplicação AD AZure com diretório ativo Azure

1) [Criação AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
2) No Diretório Ativo Azure selecione "expor aplicação"
3) Selecione Definir na aplicação ID URI [ ![ Azure AD](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4) Clique em Guardar.

## <a name="create-a-secure-webhook-action-group"></a>Criar um grupo de ação Secure Webhook

Uma vez registado o Azure AD, pode criar artigos de trabalho na sua ferramenta ITSM com base em alertas Azure, utilizando a Ação Secure Webhook em Grupos de Ação.
Os Grupos de Ação fornecem uma forma modular e reutilizável de desencadear ações para os seus Alertas Azure. Pode utilizar grupos de ação com alertas métricos, alertas de registo de atividade e alertas Azure Log Analytics no portal Azure.
Para saber mais sobre grupos de ação, consulte [Criar e gerir grupos de ação no portal Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
Utilize o seguinte procedimento:

No ambiente BMC Helix:

1. Faça login no Integration Studio.
2. Procure o fluxo de Alertas Create.
3. Copie o URL WebHook.
![BMC URL](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)

Para adicionar um webhook a uma ação, siga as instruções para um webhook seguro:

1. No [portal Azure,](https://portal.azure.com/)procure e selecione **Monitor**. O **painel monitor** consolida todas as suas definições e dados de monitorização numa única vista.
2. Selecione **Alertas** e, em seguida, selecione **Gerir ações**.
3. [Selecione Adicionar grupo de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#create-an-action-group-by-using-the-azure-portal)e preencha os campos.
4. Introduza um nome na caixa **de nome do grupo Action** e introduza um nome na caixa de **nomes Curto.** O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.
5. Selecione **Webhook seguro**
6. Selecione detalhes de edição. A imagem a seguir mostra uma ação webhook segura da amostra:
    1. Selecione o ID do objeto certo do Diretório Ativo Azure que registou
    2. Cole o campo URI no campo WebHook que copiou do "ambiente BMC Helix"
    3. Desa estação **de alerta comum para** **Sim**. 
7. A imagem a seguir mostra uma configuração de ação webhook segura: ![ Webhook seguro](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-partner-environment"></a>Configurar ambiente de parceiro

### <a name="connect-bmc-helix-to-azure-monitor"></a>Ligue a Helix BMC ao Monitor Azure

A secção seguinte fornece detalhes sobre como ligar o seu produto BMC Helix e garantir a exportação em Azure.

### <a name="prerequisites"></a>Pré-requisitos

Assegurar que os seguintes pré-requisitos sejam cumpridos:

* A Azure AD está registada.
* Você é a versão suportada da BMC Helix Multi-Cloud Service Management: versão 20.02 ou posterior

Para configurar a ligação BMC Helix:

1) [Permitir a integração pré-construída com o Azure Monitor para a versão 20.2](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)

2) Como parte da configuração da ligação na Hélice BMC, entre na sua integração BMC instância e siga as instruções:

1. Selecione **catálogo**
2. Selecione **alertas Azure**
3. Selecione **conectores**
4. Selecione **configuração**
5. Selecione adicionar nova configuração **de ligação**
6. Preencha as informações para a secção de configuração.
    1. **Nome** - Faça o seu próprio
    2. **Tipo de autorização** - NENHUM
    3. **Descrição**- Faça o seu próprio
    4. **Site**- Nuvem
    5. **Número de ocorrências** - 2 - valor padrão
    6. **Check** - selecionado por predefinição e ativar a utilização
    7. ID do inquilino Azure, ID de aplicação Azure são retirados da aplicação que foram definidas no passo "Diretório Ativo Azure Criado".
![Configuração BMC](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>Próximos passos

* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
