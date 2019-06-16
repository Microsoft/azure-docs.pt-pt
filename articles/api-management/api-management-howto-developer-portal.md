---
title: Aceder e personalizar o novo portal do programador - gestão de API do Azure | Documentos da Microsoft
description: Saiba como utilizar o novo portal do programador na gestão de API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743579"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Aceder e personalizar o novo portal do programador na gestão de API do Azure

Este artigo mostra como acessar o novo portal do Programador de API Management do Azure. Ele descreve a experiência de editor do visual - adicionar e editar conteúdo, bem como personalizando a aparência do site.

![Novo portal de programador da gestão de API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido seguinte: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).
- Importar e publicar uma instância de gestão de API do Azure. Para obter mais informações, consulte [importar e publicar](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> O novo portal do programador está atualmente em pré-visualização.

## <a name="managed-and-self-hosted-versions"></a>Versões de hospedagem interna e não geridos

Pode criar o seu portal do Programador de duas formas:

- **Versão gerenciada** - ao editar e personalizar o portal incorporado no sua instância de gestão de API e acessível por meio da URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Versão autoalojado** - ao implementar e hospedando internamente seu portal fora de uma instância de gestão de API. Esta abordagem permite-lhe editar do portal de base de código e estender a funcionalidade principal fornecido. Para obter detalhes e instruções, consulte a [repositório do GitHub com o código-fonte do portal][1].

## <a name="access-the-managed-version-of-the-portal"></a>Acesso a versão gerida do portal

Siga os passos abaixo para aceder a versão gerida do portal.

1. Aceda à sua instância do serviço de gestão de API no portal do Azure.
1. Clique nas **novo portal do desenvolvedor (pré-visualização)** botão na barra de navegação superior. É aberto um novo separador do browser com uma versão administrativa do portal. Se estiver a aceder ao portal pela primeira vez, o conteúdo predefinido será fornecido automaticamente.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Editar e personalizar a versão gerida do portal

O vídeo abaixo, demonstramos como editar o conteúdo do portal, personalizar a aparência do Web site e publicar as alterações.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o novo portal do programador:

- [Repositório do GitHub com o código-fonte][1]
- [Instruções sobre a hospedagem interna no portal][2]
- [Público plano do projeto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects