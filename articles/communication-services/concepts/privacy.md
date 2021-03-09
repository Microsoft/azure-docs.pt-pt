---
title: Disponibilidade da região e residência de dados para serviços de comunicação da Azure
description: Conheça a residência de dados e questões relacionadas com a privacidade nos Serviços de Comunicação Azure
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 50b8bc0882a5a219e70a44fdefe7dff30ae9e0ae
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487649"
---
# <a name="region-availability-and-data-residency"></a>Disponibilidade de região e residência dos dados

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

A Azure Communication Services está empenhada em ajudar os nossos clientes a cumprir os seus requisitos de privacidade e dados pessoais. Como desenvolvedor que usa serviços de comunicação com uma relação direta com os seres humanos usando a aplicação, você é potencialmente um controlador dos seus dados. Uma vez que os Serviços de Comunicação da Azure estão a armazenar e a encriptar estes dados em seu nome, provavelmente somos um processador destes dados. Esta página resume como o serviço retém dados e como pode identificar, exportar e eliminar estes dados.

## <a name="data-residency"></a>Residência dos dados

Ao criar um recurso de Serviços de Comunicação, especifique uma **geografia** (não um centro de dados Azure). Todos os dados armazenados pelos Serviços de Comunicação em repouso serão conservados nessa geografia, num centro de dados selecionado internamente pelos Serviços de Comunicação. Os dados podem transitar ou ser processados noutras geografias. Estes pontos finais globais são necessários para proporcionar uma experiência de alto desempenho e baixa latência aos utilizadores finais, independentemente da sua localização.

## <a name="data-residency-and-events"></a>Residência de dados e eventos

Qualquer tópico do sistema de Grade de Eventos configurado com os Serviços de Comunicação Azure será criado numa localização global. Para suportar uma entrega fiável, um tópico global do sistema Event Grid pode armazenar os dados do evento em qualquer centro de dados da Microsoft. Ao configurar a Grade de Eventos com os Serviços de Comunicação Azure, está a entregar os seus dados de eventos à Grade de Eventos, que é um recurso Azure sob o seu controlo. Embora os Serviços de Comunicação Azure possam estar configurados para utilizar a Azure Event Grid, é responsável pela gestão do seu recurso de Grelha de Eventos e dos dados armazenados no mesmo.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Relacionados com os humanos com as identidades dos Serviços de Comunicação da Azure

A sua aplicação gere a relação entre utilizadores humanos e identidades do Serviço de Comunicação. Quando pretende eliminar dados para um utilizador humano, deve eliminar dados que envolvam todas as identidades do Serviço de Comunicação correlacionadas para o utilizador.

Existem duas categorias de dados do Serviço de Comunicação:
- **Dados da API.** Estes dados são criados e geridos por APIs do Serviço de Comunicação, um exemplo típico sendo as mensagens de Chat geridas através de APIs de Chat.
- **Registos do Monitor Azure** Estes dados são criados pelo serviço e geridos através da plataforma de dados Azure Monitor. Estes dados incluem telemetria e métricas para ajudá-lo a entender o uso dos seus Serviços de Comunicação. Isto não é gerido por APIs do Serviço de Comunicação.

## <a name="api-data"></a>Dados da API

### <a name="identities"></a>Identidades

A Azure Communication Services mantém um diretório de identidades, use a [API da Entidade DeleteId](/rest/api/communication/communicationidentity/delete) para removê-las. A eliminação de uma identidade revogará todos os tokens de acesso associados e apagará as suas mensagens de chat. Para obter mais informações sobre como remover uma identidade [consulte esta página](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Utilizando o portal Azure ou Azure Resource Manager APIs com Serviços de Comunicação, pode criar dados pessoais. [Utilize esta página para aprender a gerir dados pessoais em sistemas Azure Resource Manager.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Gestão de números telefónicos

A Azure Communication Services mantém um diretório de números de telefone associados a um recurso de Serviços de Comunicação. Utilize estes APIs para recuperar números de telefone e eliminá-los:
- `Release Phone Number`

### <a name="chat"></a>Chat

As linhas de chat e as mensagens são mantidas até serem explicitamente eliminadas. Um fio totalmente ocioso será apagado automaticamente após 30 dias. Utilize [APIs de chat](/rest/api/communication/chat/deletechatmessage/deletechatmessage) para obter, listar, atualizar e eliminar mensagens.

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

As mensagens SMS enviadas e recebidas são processadas efemérides pelo serviço e não retidas.

### <a name="pstn-voice-calling"></a>Chamada de voz PSTN

A comunicação áudio e vídeo é efemericamente processada pelo serviço e nenhum dado é retido no seu recurso para além dos registos do Azure Monitor.

### <a name="internet-voice-and-video-calling"></a>Chamada de voz e vídeo da Internet

A comunicação áudio e vídeo é efemericamente processada pelo serviço e nenhum dado é retido no seu recurso para além dos registos do Azure Monitor.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor e Log Analytics

Os Serviços de Comunicação Azure irão alimentar os dados de registo do Azure Monitor para compreender a saúde operacional e a utilização do serviço. Alguns destes registos incluem identidades do Serviço de Comunicação e números de telefone como dados de campo. Para eliminar quaisquer dados potencialmente pessoais [utilize estes procedimentos para o Azure Monitor](../../azure-monitor/logs/personal-data-mgmt.md). Também pode querer configurar [o período de retenção predefinido para o Azure Monitor](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Pedidos de assunto de dados do Azure para o RGPD e CCPA](/microsoft-365/compliance/gdpr-dsr-azure?preserve-view=true&view=o365-worldwide)
- [Centro de Confiança da Microsoft](https://www.microsoft.com/trust-center/privacy/data-location)
- [Mapa Interativo Azure - Onde estão os dados dos meus clientes?](https://azuredatacentermap.azurewebsites.net/)
