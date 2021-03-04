---
title: Conector de gestão de serviços de TI - Exportação Segura em Monitor Azure
description: Este artigo mostra-lhe como ligar os seus produtos/serviços ITSM com a Secure Export in Azure Monitor para monitorizar e gerir centralmente os artigos de trabalho ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 330b97e66ba1a9b95f2ef7a34a7ea046414b8bd8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037519"
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

## <a name="next-steps"></a>Passos seguintes

* [Crie itens de trabalho ITSM a partir de alertas Azure](./itsmc-overview.md)