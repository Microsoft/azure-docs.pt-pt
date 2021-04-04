---
title: Problemas de autenticação e autorização - Azure Event Hubs
description: Este artigo fornece informações sobre problemas de autenticação e autorização com os Hubs de Eventos Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329615"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Problemas de autenticação e autorização - Azure Event Hubs
O artigo [de conectividade Troubleshoot](troubleshooting-guide.md) fornece dicas para resolver problemas de conectividade com os Azure Event Hubs. Este artigo fornece dicas e recomendações para resolver problemas de problemas de autenticação e problemas de autorização com a Azure Event Hubs. 

## <a name="if-you-are-using-azure-active-directory"></a>Se estiver a utilizar o Azure Ative Directory
Se estiver a utilizar o Azure Ative Directory (Azure AD) para autenticar e autorizar com a Azure Event Hubs, confirme que a identidade que acede ao centro de eventos é membro da **função Azure** certa no âmbito de **recursos** certos (grupo de consumidores, centro de eventos, espaço de identificação, grupo de recursos ou subscrição).

### <a name="azure-roles"></a>Funções do Azure
- [Azure Event Hubs Data proprietário](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) para acesso completo aos recursos do Event Hubs.
- [Remetente de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) para o acesso ao envio.
- Recetor de [dados Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) para o acesso ao acesso.

Para funções incorporadas no Registo de Schema, consulte [as funções de Registo de Schema](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Âmbitos de recursos
- **Grupo de consumidores**: Neste âmbito, a atribuição de funções aplica-se apenas a esta entidade. Atualmente, o portal Azure não suporta a atribuição de um papel de Azure a um diretor de segurança a este nível. 
- **Centro de eventos**: A atribuição de funções aplica-se à entidade Event Hub e ao grupo de consumidores ao seu abrigo.
- **Namespace**: A atribuição de funções abrange toda a topologia dos Centros de Eventos sob o espaço de nome e para o grupo de consumidores que lhe está associado.
- **Grupo de recursos**: A atribuição de funções aplica-se a todos os recursos do Event Hubs no âmbito do grupo de recursos.
- **Subscrição**: A atribuição de funções aplica-se a todos os recursos do Event Hubs em todos os grupos de recursos da subscrição.

Para obter mais informações, veja os seguintes artigos:

- [Autenticar uma aplicação com o Azure Ative Directory para aceder aos recursos do Event Hubs](authenticate-application.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Se estiver a utilizar assinaturas de acesso partilhado (SAS)
Se estiver a utilizar [SAS,](authenticate-shared-access-signature.md)siga estes passos: 

- Certifique-se de que a chave SAS que está a utilizar está correta. Caso contrário, utilize a chave SAS certa.
- Verifique se a chave tem as permissões certas (enviar, receber ou gerir). Caso contrário, utilize uma chave que tenha a permissão necessária. 
- Verifique se a chave expirou. Recomendamos que renove o SAS bem antes de expirar. Se houver um desvio de relógio entre os nós de serviço do Cliente e do Event Hubs, o sinal de autenticação pode expirar antes que o cliente perceba. O relógio das contas de implementação corrente desviou até 5 minutos, ou seja, o cliente renova o token 5 minutos antes de expirar. Portanto, se o relógio distorcer é maior do que 5 minutos, o cliente pode observar falhas de autenticação intermitentes.
- Se **a hora de início do SAS** estiver definida para **agora,** pode ver falhas intermitentes durante os primeiros minutos devido a distorção do relógio (diferenças no tempo atual em diferentes máquinas). Desa esta hora de início seja de pelo menos 15 minutos no passado ou não o coloque em tudo. O mesmo se aplica, em geral, também ao prazo de validade. 

Para obter mais informações, veja os seguintes artigos: 

- [Autenticar utilizando assinaturas de acesso partilhado (SAS)](authenticate-shared-access-signature.md). 
- [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

* [Resolver problemas de conectividade](troubleshooting-guide.md)
