---
title: Modelo de segurança de Centros de Notificação
description: Saiba mais sobre o modelo de segurança para a Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76263766"
---
# <a name="notification-hubs-security"></a>Segurança dos Centros de Notificação

## <a name="overview"></a>Descrição geral

Este tópico descreve o modelo de segurança dos Hubs de Notificação Azure.

## <a name="shared-access-signature-security"></a>Segurança de assinatura de acesso compartilhado

O Notification Hubs implementa um sistema de segurança ao nível da entidade chamado *Assinatura de Acesso Partilhado* (SAS). Cada regra contém um nome, um valor chave (segredo partilhado) e um conjunto de direitos, como explicado mais tarde nas [alegações de Segurança](#security-claims). 

Ao criar um hub, são criadas automaticamente duas regras: uma com direitos **de escuta** (que a aplicação do cliente utiliza) e outra com **todos os** direitos (que o backend da app utiliza):

- **DefaultListenSharedAccessSignature:** concessões **Ouvir** apenas permissão.
- **DefaultFulFullSharedAccessSignature**: grants **Ouvir,** **Gerir**e **Enviar** permissões. Esta política deve ser utilizada apenas no seu backend de aplicações. Não o utilize em aplicações de clientes; usar uma política apenas **Ouvir** o acesso. Para criar uma nova política de acesso personalizado com um novo token SAS, consulte [os tokens SAS para políticas](#sas-tokens-for-access-policies) de acesso mais tarde neste artigo.

Ao realizar a gestão de registo a partir de aplicações de clientes, se as informações enviadas através de notificações não forem sensíveis (por exemplo, atualizações meteorológicas), uma forma comum de aceder a um Hub de Notificação é dar o valor-chave da regra Apenas aceder à aplicação do cliente e dar o valor chave da regra acesso total ao backend da app.

As aplicações não devem incorporar o valor-chave nas aplicações de clientes do Windows Store; em vez disso, que a aplicação do cliente a recupere do backend da app no arranque.

A chave com o acesso **Listen** permite que uma aplicação do cliente se registe para qualquer tag. Se a sua aplicação tiver de restringir as inscrições a etiquetas específicas a clientes específicos (por exemplo, quando as etiquetas representam iDs de utilizador), o seu backend da sua aplicação deve efetuar as inscrições. Para mais informações, consulte [a gestão do Registo.](notification-hubs-push-notification-registration-management.md) Note que desta forma, a aplicação do cliente não terá acesso direto aos Centros de Notificação.

## <a name="security-claims"></a>Reclamações de segurança

À semelhança de outras entidades, as operações do Centro de Notificação são permitidas para três pedidos de segurança: **Ouvir,** **Enviar**e **Gerir**.

| Afirmação   | Descrição                                          | Operações permitidas |
| ------- | ---------------------------------------------------- | ------------------ |
| Escutar  | Criar/Atualizar, Ler e Eliminar registos individuais | Criar/Atualizar registo<br><br>Ler registo<br><br>Leia todos os registos para um cabo<br><br>Apagar registo |
| Enviar    | Enviar mensagens para o Centro de Notificação                | Enviar mensagem |
| Gerir  | CRUDs em Centros de Notificação (incluindo atualização de credenciais de PNS e chaves de segurança) e ler registos com base em etiquetas |Criar/Atualizar/Ler/Eliminar centros<br><br>Ler registos por etiqueta |

Os Centros de Notificação aceitam fichas SAS geradas com teclas partilhadas configuradas diretamente no hub.

Não é possível enviar uma notificação para mais de um espaço de nome. Os espaços de nome são recipientes lógicos para centros de notificação e não estão envolvidos no envio de notificações.

Utilizar as políticas de acesso ao nível do espaço de nome (credenciais) para operações de nível de espaço de nome; por exemplo: agrupamentos de listagem, criação ou eliminação de centros, etc. Apenas as políticas de acesso ao nível do hub permitem enviar notificações.

### <a name="sas-tokens-for-access-policies"></a>Fichas SAS para políticas de acesso

Para criar uma nova reclamação de segurança ou para visualizar as chaves SAS existentes, faça o seguinte:

1. Inicie sessão no Portal do Azure.
2. Selecione **Todos os recursos**.
3. Selecione o nome do Centro de Notificação para o qual pretende criar a reclamação ou ver a tecla SAS.
4. No menu à esquerda, selecione Políticas de **Acesso**.
5. Selecione **Nova Política** para criar uma nova reivindicação de segurança. Dê um nome à apólice e selecione as permissões que pretende conceder. Em seguida, selecione **OK**.
6. A cadeia de ligação completa (incluindo a nova chave SAS) é exibida na janela 'Políticas de Acesso'. Pode copiar esta cadeia para a prancheta para utilização posterior.

Para extrair a chave SAS de uma política específica, selecione o botão **Copiar** ao lado da política que contém a chave SAS desejada. Cole este valor num local temporário e, em seguida, copie a parte chave SAS da cadeia de ligação. Este exemplo utiliza um espaço de nomes de Centros de Notificação chamado **mytestnamespace1**, e uma política chamada **policy2**. A chave SAS é o valor próximo da extremidade da cadeia, especificada por **SharedAccessKey:**

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Obter chaves SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral dos Hubs de Notificação](notification-hubs-push-notification-overview.md)
