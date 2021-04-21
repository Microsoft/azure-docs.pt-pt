---
title: Visão geral do portal de desenvolvimento na Azure API Management
titleSuffix: Azure API Management
description: Conheça o portal de desenvolvedores em API Management - um site personalizável, onde os consumidores de API podem explorar as suas APIs.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f5da5ed6c828278eb25a9fa4fa5cfb90e16f8f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815964"
---
# <a name="overview-of-the-developer-portal"></a>Visão geral do portal do desenvolvedor

O portal do desenvolvedor é um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs. É onde os consumidores da API podem descobrir as suas APIs, aprender a usá-las, solicitar acesso e experimentá-las.

Conforme introduzido neste artigo, pode personalizar e alargar o portal do desenvolvedor para os seus cenários específicos. 

![Portal de desenvolvimento de gestão da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migração do portal do legado

> [!IMPORTANT]
> O portal de desenvolvedores legados está agora deprecado e receberá apenas atualizações de segurança. Pode continuar a usá-lo, como de costume, até à sua reforma em outubro de 2023, altura em que será removido de todos os serviços de Gestão da API.

A migração para o novo portal de desenvolvimento é descrita no [artigo de documentação dedicado.](developer-portal-deprecated-migration.md)

## <a name="customization-and-styling"></a>Personalização e styling

O portal de desenvolvedores pode ser personalizado e modelado através do editor visual incorporado, arrastado e drop. Consulte [este tutorial](api-management-howto-developer-portal-customize.md) para mais detalhes.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Extensibilidade

O seu serviço de Gestão API inclui um portal de desenvolvedores gerido, sempre atualizado e **atualizado.** Pode aceder-lhe a partir da interface do portal Azure.

Se precisar estender com lógica personalizada, que não é suportada fora da caixa, pode modificar a sua base de código. A base de código do portal está [disponível num repositório GitHub.](https://github.com/Azure/api-management-developer-portal) Por exemplo, pode implementar um novo widget, que se integra com um sistema de suporte de terceiros. Quando implementar uma nova funcionalidade, pode escolher uma das seguintes opções:

- **Auto-hospedar** o portal resultante fora do seu serviço de Gestão API. Quando se auto-hospeda o portal, torna-se o seu criador e é responsável pelas suas atualizações. A assistência do Azure Support [limita-se apenas à configuração básica de portais auto-hospedados](developer-portal-self-host.md).
- Abra um pedido de puxar para que a equipa de Gestão da API funda nova funcionalidade com a base de código do portal **gerido.**

Para obter detalhes e instruções de extensibilidade, consulte o [repositório GitHub](https://github.com/Azure/api-management-developer-portal) e o tutorial para [implementar um widget](developer-portal-implement-widgets.md). O tutorial para [personalizar o portal gerido](api-management-howto-developer-portal-customize.md) acompanha-o através do painel administrativo do portal, que é comum para **versões geridas** e **auto-hospedadas.**


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o novo portal de desenvolvedores:

- [Aceda e personalize o portal de desenvolvimento gerido](api-management-howto-developer-portal-customize.md)
- [Configurar a versão auto-hospedada do portal](developer-portal-self-host.md)
- [Implemente o seu próprio widget](developer-portal-implement-widgets.md)

Procurar outros recursos:

- [Repositório GitHub com o código fonte](https://github.com/Azure/api-management-developer-portal)
- [Perguntas frequentes sobre o portal de desenvolvedores](developer-portal-faq.md)
