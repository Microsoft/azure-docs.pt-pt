---
title: Modelo de segurança de Centros de Notificação
description: Conheça o modelo de segurança dos Hubs de Notificação Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76263766"
---
# <a name="notification-hubs-security"></a>Segurança dos Centros de Notificação

## <a name="overview"></a>Descrição geral

Este tópico descreve o modelo de segurança dos Hubs de Notificação Azure.

## <a name="shared-access-signature-security"></a>Segurança de assinatura de acesso partilhado

O Notification Hubs implementa um sistema de segurança ao nível da entidade chamado *Signature shared access* (SAS). Cada regra contém um nome, um valor-chave (segredo partilhado) e um conjunto de direitos, como explicado mais tarde em reivindicações de [Segurança.](#security-claims) 

Ao criar um hub, são criadas automaticamente duas regras: uma com direitos **de escuta** (que a app do cliente utiliza) e outra com **todos os** direitos (que o backend da aplicação utiliza):

- **DefaultListenSharedAccessSignature**: concede apenas permissão **de escuta.**
- **DefaultFullSharedAccessSignature**: grants **Listen**, **Manage**, and **Send** permissions. Esta política deve ser usada apenas no seu backend de aplicações. Não o utilize em aplicações de clientes; usar uma política com apenas **ouvir** acesso. Para criar uma nova política de acesso personalizado com um novo token SAS, consulte [tokens SAS para políticas](#sas-tokens-for-access-policies) de acesso mais tarde neste artigo.

Ao executar a gestão de registo sabotadas de aplicações de clientes, se as informações enviadas através de notificações não forem sensíveis (por exemplo, atualizações meteorológicas), uma forma comum de aceder a um Centro de Notificação é dar o valor-chave da regra Apenas aceder à app do cliente, e dar o valor-chave da regra de acesso total ao backend da aplicação.

As aplicações não devem incorporar o valor-chave nas aplicações de clientes da Windows Store; em vez disso, fazer com que a aplicação do cliente a recupere do backend da app no arranque.

A chave com o acesso **Listen** permite que uma aplicação de cliente se registe para qualquer etiqueta. Se a sua aplicação tiver de restringir as inscrições a etiquetas específicas a clientes específicos (por exemplo, quando as etiquetas representam iDs de utilizador), o backend da sua aplicação deve efetuar as inscrições. Para mais informações, consulte [a gestão do registo.](notification-hubs-push-notification-registration-management.md) Note que desta forma, a aplicação do cliente não terá acesso direto aos Centros de Notificação.

## <a name="security-claims"></a>Reclamações de segurança

À semelhança de outras entidades, as operações do Centro de Notificação são permitidas para três pedidos de segurança: **Ouvir,** **Enviar**e **Gerir**.

| Afirmação   | Descrição                                          | Operações permitidas |
| ------- | ---------------------------------------------------- | ------------------ |
| Escutar  | Criar/Atualizar, Ler e Eliminar registos individuais | Criar/Atualizar o registo<br><br>Ler inscrição<br><br>Leia todas as inscrições para um cabo<br><br>Excluir o registo |
| Enviar    | Enviar mensagens para o Centro de Notificação                | Enviar mensagem |
| Gerir  | CRUDs em Centros de Notificação (incluindo a atualização de credenciais PNS e chaves de segurança), e ler registos com base em etiquetas |Criar/Atualizar/Ler/Eliminar centros<br><br>Ler inscrições por etiqueta |

O Notification Hubs aceita tokens SAS gerados com chaves partilhadas configuradas diretamente no centro.

Não é possível enviar uma notificação para mais do que um espaço de nome. Os espaços de nome são recipientes lógicos para Centros de Notificação e não estão envolvidos no envio de notificações.

Utilize as políticas de acesso ao nível do espaço de nome (credenciais) para operações ao nível do espaço de nome; por exemplo: listar centros, criar ou eliminar centros, etc. Apenas as políticas de acesso ao nível do hub permitem enviar notificações.

### <a name="sas-tokens-for-access-policies"></a>Fichas SAS para políticas de acesso

Para criar uma nova reivindicação de segurança ou para visualizar as chaves SAS existentes, faça o seguinte:

1. Inicie sessão no Portal do Azure.
2. Selecione **Todos os recursos**.
3. Selecione o nome do Centro de Notificação para o qual pretende criar a reclamação ou ver a chave SAS.
4. No menu à esquerda, selecione Políticas de **Acesso**.
5. Selecione **Nova Política** para criar uma nova reivindicação de segurança. Dê um nome à apólice e selecione as permissões que pretende conceder. Em seguida, selecione **OK**.
6. A cadeia de ligação completa (incluindo a nova tecla SAS) é exibida na janela Políticas de Acesso. Pode copiar esta cadeia para posterior utilização.

Para extrair a tecla SAS de uma política específica, selecione o botão **Copiar** ao lado da política que contém a tecla SAS deseja. Cole este valor numa localização temporária e, em seguida, copie a parte chave SAS da cadeia de ligação. Este exemplo usa um espaço de nome sinuoso de Centros de Notificação chamado **mytestnamespace1**, e uma política chamada **política2**. A tecla SAS é o valor próximo da extremidade da cadeia, especificada por **SharedAccessKey:**

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Obter chaves SAS](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral dos Centros de Notificação](notification-hubs-push-notification-overview.md)
