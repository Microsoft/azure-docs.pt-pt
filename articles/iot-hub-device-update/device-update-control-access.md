---
title: Compreenda a atualização do dispositivo para autenticação e autorização do IoT Hub | Microsoft Docs
description: Compreenda como a Atualização do Dispositivo para o IoT Hub utiliza o Azure RBAC para fornecer autenticação e autorização para os utilizadores e APIs de serviço.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 07310a5b6f275d4a35a3649c22aeea68045dde8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662840"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Controlo de acesso baseado em funções Azure (RBAC) e atualização do dispositivo

A Atualização do Dispositivo utiliza o Azure RBAC para fornecer autenticação e autorização para os utilizadores e APIs de serviço.

## <a name="configure-access-control-roles"></a>Configure funções de controlo de acesso

Para que outros utilizadores e aplicações tenham acesso à Atualização do Dispositivo, os utilizadores ou aplicações devem ter acesso a este recurso. Aqui estão as funções que são suportadas pela Atualização do Dispositivo

|   Nome da Função   | Descrição  |
| :--------- | :---- |
|  Administrador de atualização de dispositivos | Tem acesso a todos os recursos de atualização de dispositivos  |
|  Leitor de atualização de dispositivos| Pode ver todas as atualizações e implementações |
|  Administrador de conteúdo de atualização de dispositivos | Pode ver, importar e apagar atualizações  |
|  Leitor de conteúdo de atualização de dispositivos | Pode ver atualizações  |
|  Administrador de implementação de atualização de dispositivos | Pode gerir a implementação de atualizações para dispositivos|
|  Leitor de implementações de atualização de dispositivos| Pode ver implementações de atualizações para dispositivos |

Uma combinação de funções pode ser usada para fornecer o nível de acesso certo. Por exemplo, um desenvolvedor pode importar e gerir atualizações usando a função de Administrador de Conteúdo de Atualização de Dispositivos, mas precisa de uma função de leitor de implementações de atualização de dispositivos para visualizar o progresso de uma atualização. Inversamente, um operador de solução com a função de leitor de atualização de dispositivos pode visualizar todas as atualizações, mas precisa de utilizar a função de Administrador de Implementação de Atualizações de Dispositivos para implementar uma atualização específica para dispositivos.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Autenticar para atualização de dispositivos REST APIs para publicação e gestão

A Atualização do Dispositivo também utiliza Azure AD para autenticação para publicar e gerir conteúdos através de APIs de serviço. Para começar, é necessário criar e configurar uma aplicação de cliente.

### <a name="create-client-azure-ad-app"></a>Criar app AD Azure cliente

Para integrar uma aplicação ou serviço com a Azure AD, [primeiro registe](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) uma aplicação com Azure AD. A configuração da aplicação do cliente varia consoante o fluxo de autorização utilizado.  A configuração abaixo é para orientação ao utilizar as APIs de atualização do dispositivo.

* Definir autenticação do cliente: 'redirecionar URIs para cliente nativo ou web'.
* Definir permissões API - Atualização do dispositivo para ioT hub expõe:
  * Permissões delegadas: "user_impersonation"
  * **Opcional**, conceder consentimento administrativo

[Próximos passos: Criar recursos de atualização de dispositivos e configurar funções de controlo de acesso](./create-device-update-account.md)
