---
title: 'Como: Restaurar ou remover uma aplicação recentemente eliminada com a plataforma de identidade microsoft | Rio Azure'
titleSuffix: Microsoft identity platform
description: Nesta forma de fazer, aprende-se a restaurar ou eliminar permanentemente uma aplicação recentemente eliminada registada na plataforma de identidade da Microsoft.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080341"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Restaurar ou remover uma aplicação recentemente eliminada com a plataforma de identidade da Microsoft
Depois de apagar um registo de aplicações, a aplicação permanece em estado suspenso por 30 dias. Durante essa janela de 30 dias, o registo da aplicação pode ser restaurado, juntamente com todas as suas propriedades. Após os passes de 30 dias, as inscrições das aplicações não podem ser restauradas e o processo de eliminação permanente pode ser iniciado automaticamente.  Esta funcionalidade aplica-se apenas às aplicações associadas a um diretório.  Não está disponível para aplicações a partir de uma conta pessoal da Microsoft, que não pode ser restaurada.

Pode ver as suas aplicações eliminadas, restaurar uma aplicação eliminada ou eliminar permanentemente uma aplicação utilizando a experiência de registos da App no âmbito do Azure Ative Directory (Azure AD) no portal Azure.

Note que nem você nem o suporte ao cliente da Microsoft podem restaurar uma aplicação permanentemente eliminada ou uma aplicação eliminada há mais de 30 dias.

> [!IMPORTANT]
> A funcionalidade UI do portal de aplicações eliminada [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>Permissões obrigatórias
Deve ter uma das seguintes funções para eliminar permanentemente as aplicações.

- Administrador global
- Administrador de aplicação
- Administrador de aplicação em nuvem
- Administrador de identidade híbrido
- Proprietário de aplicação

Deve ter uma das seguintes funções para restaurar as aplicações.

- Administrador global
- Proprietário de aplicação

### <a name="view-your-deleted-applications"></a>Ver as suas aplicações eliminadas
Pode ver todas as aplicações num estado suavemente apagado.  Apenas as aplicações eliminadas há menos de 30 dias podem ser restauradas.

#### <a name="to-view-your-restorable-applications"></a>Para ver as suas aplicações restauradoras
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Pesse e selecione **O Diretório Ativo Azure**, selecione **registos de Aplicações** e, em seguida, selecione o separador **aplicações eliminadas (Pré-visualização).**

Reveja a lista de candidaturas. Apenas as aplicações que foram eliminadas nos últimos 30 dias estão disponíveis para restaurar. Se utilizar a pré-visualização dos registos da App, pode filtrar pela coluna 'Data eliminada' para ver apenas estas aplicações.

## <a name="restore-a-recently-deleted-application"></a>Restaurar uma aplicação recentemente eliminada

Quando um registo de aplicações é eliminado da organização, a aplicação encontra-se em estado suspenso e as suas configurações são preservadas. Quando restaura um registo de aplicações, as suas configurações também são restauradas.  No entanto, se houver configurações específicas da organização nos **pedidos** da Enterprise para o inquilino do pedido, esses não serão restaurados.  

Isto porque as configurações específicas da organização são armazenadas num objeto separado, chamado principal de serviço.  As definições realizadas no principal serviço incluem consentimentos de permissão e atribuição de utilizadores e grupos para uma determinada organização; estas configurações não serão restauradas quando a aplicação for restaurada. Para mais informações, consulte [os objetos principais de aplicação e serviço.](app-objects-and-service-principals.md) 


### <a name="to-restore-an-application"></a>Para restaurar uma aplicação
1. No separador **aplicações eliminadas (Pré-visualização),** procure e selecione uma das aplicações eliminadas há menos de 30 dias.
2. Selecione **o registo de aplicativos Restore**.

## <a name="permanently-delete-an-application"></a>Eliminar permanentemente uma aplicação
Pode eliminar manualmente uma aplicação da sua organização. Uma aplicação permanentemente eliminada não pode ser restaurada por si, por outro administrador ou pelo suporte ao cliente da Microsoft.

### <a name="to-permanently-delete-an-application"></a>Para eliminar permanentemente uma aplicação

1. No separador **aplicações eliminadas (Pré-visualização),** procure e selecione uma das aplicações disponíveis.
2. **Selecione Eliminar permanentemente**.
3. Leia o texto de aviso e selecione **Sim**.

## <a name="next-steps"></a>Passos seguintes
Depois de restaurar ou apagar permanentemente a sua aplicação, pode:

- [Adicione uma aplicação](quickstart-register-app.md).
- Saiba mais sobre [aplicações e objetos principais](app-objects-and-service-principals.md) de serviço na plataforma de identidade da Microsoft.
