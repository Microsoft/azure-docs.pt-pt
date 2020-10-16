---
title: Cloud Partner Portal API Reference - Mercado comercial da Microsoft
description: Descrição dos pré-requisitos para utilizar e lista de operações de API no mercado.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: bfb6e9cb510f5fe887f108dfdea5932406aafe0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292921"
---
# <a name="cloud-partner-portal-api-reference"></a>Referência da API do Cloud Partner Portal

> [!NOTE]
> As APIs do Portal do Parceiro Cloud estão integradas e continuarão a trabalhar no Partner Center. A transição introduz pequenas mudanças. Reveja as [Alterações às APIs de CPP](#changes-to-cpp-apis-after-the-migration-to-partner-center) listadas neste documento para garantir que o seu código continua a funcionar após a transição para o Partner Center. As APIs de CPP só devem ser utilizadas para produtos já integrados antes da transição para o Partner Center; novos produtos devem utilizar APIs de submissão do Partner Center.

As APIs do Portal cloud Partner REST permitem a recuperação programática e manipulação de cargas de trabalho, ofertas e perfis de editores. As APIs utilizam o controlo de acesso baseado em funções (RBAC) para impor permissões corretas no momento do processamento.

Esta referência fornece os detalhes técnicos para as APIs do Portal do Parceiro cloud REST. As amostras de carga útil neste documento são apenas para referência e estão sujeitas a alterações à medida que a nova funcionalidade é adicionada.

## <a name="prerequisites-and-considerations"></a>Pré-requisitos e considerações

Antes de utilizar as APIs, deve rever:

- O artigo [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para aprender a adicionar um principal de serviço à sua conta, e obter um token de acesso Azure Ative Directory (Azure AD) para autenticação.
- As duas estratégias [de controlo da concordância](./cloud-partner-portal-api-concurrency-control.md) disponíveis para chamar estas APIs.
- Considerações [considerations](./cloud-partner-portal-api-considerations.md)adicionais da API , tais como a versão e o tratamento de erros.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Alterações nas APIs do CPP após a migração para o Centro de Parceiros

| **API** | **Alterar descrição** | **Impacto** |
| ------- | ---------------------- | ---------- |
| POST Publish, GoLive, Cancele | Para as ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o estado de funcionamento. | Ao enviar qualquer um dos pedidos de ENVIO correspondentes para uma oferta, o cabeçalho localização terá um de dois formatos dependendo do estado de migração da oferta:<ul><li>Ofertas não migradas<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operação GET | Para tipos de oferta que suportassem anteriormente o campo 'notification-email' na resposta, este campo será depreciado e deixará de ser devolvido para ofertas migratórias. | Para ofertas migradas, não enviaremos mais notificações para a lista de e-mails especificados nos pedidos. Em vez disso, o serviço API irá alinhar-se com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações serão enviadas para o endereço de e-mail definido na secção de informações de conta de contacto do Vendedor das definições da sua Conta no Centro parceiro, para notificá-lo do progresso da operação.<br><br>Por favor, reveja o endereço de e-mail definido na secção de informações de conta de contacto do Vendedor no Centro de [Parceiros](https://partner.microsoft.com/dashboard/account/management) para garantir que o e-mail correto é fornecido para notificações.  |

## <a name="common-tasks"></a>Tarefas comuns

Esta referência detalha APIs para executar as seguintes tarefas comuns.

### <a name="offers"></a>Ofertas

- [Recuperar todas as ofertas](./cloud-partner-portal-api-retrieve-offers.md)
- [Recupere uma oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md)
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
