---
title: Segurança de Hubs de notificação
description: Este tópico explica a segurança para os hubs de notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 2ca3c69178dde830e226812da34917246781c1ee
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762161"
---
# <a name="security-model-of-azure-notification-hubs"></a>Modelo de segurança dos Hubs de notificação do Azure

## <a name="overview"></a>Descrição geral

Este tópico descreve o modelo de segurança dos Hubs de notificação do Azure. Como os Hubs de notificação são uma entidade do Service Bus, que implementam o mesmo modelo de segurança do Service Bus. Para obter mais informações, consulte a [autenticação de barramento de serviço](https://msdn.microsoft.com/library/azure/dn155925.aspx) tópicos.

## <a name="shared-access-signature-security-sas"></a>Segurança de assinatura de acesso partilhado (SAS)

Os Notification Hubs implementa um esquema de segurança ao nível da entidade chamada SAS (assinatura de acesso partilhado). Esse esquema permite que as entidades de mensagens declarar até 12 regras de autorização na respetiva descrição concedem direitos nessa entidade.

Cada regra contém um nome, um valor de chave (segredo partilhado) e um conjunto de direitos, conforme explicado na seção "Afirmações de segurança". Ao criar um Hub de notificação, duas regras são criadas automaticamente: um com direitos de escuta (que utiliza a aplicação de cliente) e outro com todos os direitos (que utiliza o back-end de aplicação).

Quando efetuar a gestão de registos de aplicações de cliente, se as informações enviadas através de notificações não sejam confidenciais (por exemplo, atualizações de informações sobre o clima), uma forma comum de aceder a um Hub de notificação é fornecer o valor da chave da regra de acesso só de escuta para a aplicação de cliente, e para permitir que o valor da chave de regra de acesso completo ao back-end da aplicação.

Não é recomendável que incorporar o valor da chave em aplicações de cliente da Windows Store. Uma forma de evitar a incorporar o valor da chave é ter a aplicação de cliente recuperá-la a partir do back-end de aplicação na inicialização.

É importante compreender que a chave de acesso escuta permite que uma aplicação de cliente para se registar para qualquer marca. Se a sua aplicação tem de restringir registos a etiquetas específicas para clientes específicos (por exemplo, quando as marcas representam IDs de usuário), o back-end de aplicação tem de efetuar os registros. Para obter mais informações, consulte Gestão de registos. Tenha em atenção que desta forma, a aplicação de cliente não terá acesso direto aos Hubs de notificação.

## <a name="security-claims"></a>Afirmações de segurança

Assim como outras entidades, as operações de Hub de notificação são permitidas para três declarações de segurança: Ouça, enviar e gerir.

| Afirmação   | Descrição                                          | Operações permitidas |
| ------- | ---------------------------------------------------- | ------------------ |
| Vigiar  | Criar/atualizar, ler e eliminar registos únicos | Criar/atualizar registo<br><br>Registo de leitura<br><br>Ler todos os registos para um identificador<br><br>Eliminar registo |
| Enviar    | Enviar mensagens para o hub de notificação                | Enviar mensagem |
| Gerir  | CRUDs sobre os Hubs de notificação (incluindo a atualizar credenciais PNS e chaves de segurança) e registos de leitura com base em etiquetas |Hubs de notificação de criar/atualizar/ler/eliminar<br><br>Registos de leitura por etiqueta |

Os Hubs de notificação aceitam declarações concedidas ao tokens de controlo de acesso do Microsoft Azure e tokens de assinatura gerados com chaves partilhadas configuradas diretamente no Hub de notificação.

Não é possível enviar uma notificação para mais do que um espaço de nomes. Espaços de nomes são contentores lógicos nos hubs de notificação e não estão envolvidos com o envio de notificações. As políticas de acesso de nível de espaço de nomes (credenciais) podem ser utilizadas para operações de nível de espaço de nomes, por exemplo: listagem dos hubs de notificação, criar ou eliminar os hubs de notificação, etc. Apenas as políticas de acesso ao nível do hub permitiria que enviar notificações.
