---
title: Cloud Partner Portal Referência API [ Mercado Azure
description: Descrição dos pré-requisitos para utilização e lista de operações de API no mercado.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256421"
---
# <a name="cloud-partner-portal-api-reference"></a>Referência da API do Cloud Partner Portal

> [!NOTE]
> As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as [Alterações às APIs do CPP](#changes-to-cpp-apis-after-the-migration-to-partner-center) listadas neste documento para garantir que o seu código continua a funcionar após a migração para o Partner Center.

O Portal de Parceiros cloud REST APIs permite a recuperação programática e manipulação de cargas de trabalho, ofertas e perfis de editor. As APIs utilizam o controlo de acesso baseado em funções (RBAC) para impor permissões corretas no momento do processamento.

Esta referência fornece os detalhes técnicos para as APIs REST REST Do Portal do Parceiro cloud. As amostras de carga útil neste documento são apenas para referência e estão sujeitas a alterações à medida que é adicionada uma nova funcionalidade.

## <a name="prerequisites-and-considerations"></a>Pré-requisitos e considerações

Antes de utilizar as APIs, deve rever:

- O artigo [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para aprender a adicionar um principal de serviço à sua conta, e obter um sinal de acesso azure Ative Directory (Azure AD) para autenticação.
- As duas estratégias de controlo de [moedas](./cloud-partner-portal-api-concurrency-control.md) disponíveis para chamar estas APIs.
- [Considerações](./cloud-partner-portal-api-considerations.md)adicionais da API, tais como versão e manipulação de erros.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Alterações nas APIs do PCP após a migração para partner Center

| **API** | **Alteração de descrição** | **Impacto** |
| ------- | ---------------------- | ---------- |
| POST Publicar, GoLive, Cancelar | Para ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o estado de funcionamento. | Ao enviar qualquer um dos pedidos post correspondentes para uma oferta, o cabeçalho de localização terá um de dois formatos dependendo do estado de migração da oferta:<ul><li>Ofertas não migradas<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| OPERAÇÃO GET | Para tipos de oferta que anteriormente suportavam o campo de "notificação-e-mail" na resposta, este campo será depreciado e não mais devolvido para ofertas migradas. | Para ofertas migradas, deixaremos de enviar notificações para a lista de e-mails especificados nos pedidos. Em vez disso, o serviço API irá alinhar-se com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações serão enviadas para o endereço de e-mail definido na secção de informações de contacto do Vendedor das definições da sua Conta no Partner Center, para notificá-lo do progresso da operação.<br><br>Por favor, reveja o endereço de e-mail definido na secção de informações de contacto do Vendedor das [definições da](https://partner.microsoft.com/dashboard/account/management) sua Conta no Partner Center para garantir que o e-mail correto é fornecido para notificações.  |

## <a name="common-tasks"></a>Tarefas comuns

Esta referência detalha as APIs para executar as seguintes tarefas comuns.

### <a name="offers"></a>Ofertas

- [Recuperar todas as ofertas](./cloud-partner-portal-api-retrieve-offers.md)
- [Recuperar uma oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Obter estado da oferta](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Criar uma oferta](./cloud-partner-portal-api-creating-offer.md)
- [Publicar uma oferta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operações

- [Obter operações](./cloud-partner-portal-api-retrieve-operations.md)
- [Cancelar operações](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicar uma aplicação

- [Entrar em direto](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Outras tarefas

- [Definir preços para ofertas de máquinas virtuais](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Resolução de problemas

- [Resolver erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
