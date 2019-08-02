---
title: Acessar e personalizar o novo portal do desenvolvedor-gerenciamento de API do Azure | Microsoft Docs
description: Saiba como usar o novo portal do desenvolvedor no gerenciamento de API.
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
ms.openlocfilehash: d902a65c4e36ff698e486e2af899d055de1afca0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638548"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Acessar e personalizar o novo portal do desenvolvedor no gerenciamento de API do Azure

Este artigo mostra como acessar o novo portal do desenvolvedor de gerenciamento de API do Azure. Ele orienta você pela experiência do editor visual – adicionando e editando conteúdo, bem como Personalizando a aparência do site.

![Novo portal do desenvolvedor de gerenciamento de API](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Pré-requisitos

- Conclua o início rápido a seguir: [Crie uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).
- Importe e publique uma instância de gerenciamento de API do Azure. Para obter mais informações, consulte [importar e publicar](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> O novo portal do desenvolvedor está atualmente em versão prévia.

## <a name="managed-vs-self-hosted"></a>Versões gerenciadas e auto-hospedadas

Você pode criar seu portal do desenvolvedor de duas maneiras:

- **Versão gerenciada** – editando e personalizando o portal, que é incorporado à sua instância de gerenciamento de API e pode ser `<your-api-management-instance-name>.developer.azure-api.net`acessado por meio da URL.
- **Versão hospedada internamente** – Implantando e hospedando internamente seu portal fora de uma instância de gerenciamento de API. Essa abordagem permite que você edite a base de código do portal e estenda a funcionalidade básica fornecida. Para obter detalhes e instruções, consulte o [repositório do GitHub com o código-fonte do portal][1].

## <a name="managed-access"></a>Acessar a versão gerenciada do portal

Siga as etapas abaixo para acessar a versão gerenciada do Portal.

1. Vá para a instância do serviço de gerenciamento de API no portal do Azure.
1. Clique no botão **novo portal do desenvolvedor (versão prévia)** na barra de navegação superior. Uma nova guia do navegador com uma versão administrativa do portal será aberta. Se você estiver acessando o portal pela primeira vez, o conteúdo padrão será automaticamente provisionado.

## <a name="managed-tutorial"></a>Editar e personalizar a versão gerenciada do portal

No vídeo abaixo, demonstramos como editar o conteúdo do portal, personalizar a aparência do site e publicar as alterações.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Perguntas frequentes

Nesta seção, respondemos a perguntas comuns sobre o novo portal do desenvolvedor, que são de natureza geral. Para perguntas específicas para a versão hospedada internamente, consulte [a seção wiki do repositório do GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Como posso migrar o conteúdo do portal do desenvolvedor antigo para o novo?

Você não pode. Os portais são incompatíveis.

### <a name="when-will-the-portal-become-generally-available"></a>Quando o portal ficará disponível para o público geral?

O portal está atualmente em visualização e será disponibilizado ao público em geral no final do ano civil (2019). A versão de visualização não deve ser usada para fins de produção.

### <a name="will-the-old-portal-be-deprecated"></a>O portal antigo será preterido?

Sim, ele será preterido depois que o novo for disponibilizado para o público geral. Se você tiver preocupações, gere-as [em um problema dedicado do GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>O novo portal tem todos os recursos do portal antigo?

O objetivo da disponibilidade geral é fornecer uma paridade de recursos baseada em cenário com o portal antigo. Até lá, a versão de visualização pode não ter os recursos selecionados implementados.

As exceções são os *aplicativos* e *problemas* do portal antigo, que não estarão disponíveis no novo Portal. Se você usar *problemas* no portal antigo e precisar deles em um novo, poste um comentário em [um problema dedicado do GitHub](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Quero mover o conteúdo do novo portal entre ambientes. Como posso fazer isso e preciso usar a versão hospedada internamente?

Você pode fazer isso nas versões do portal – gerenciado e auto-hospedado. O novo portal do desenvolvedor dá suporte à extração de conteúdo por meio da API de gerenciamento do seu serviço de gerenciamento de API. As APIs são documentadas [na seção wiki do repositório GitHub](https://github.com/Azure/api-management-developer-portal/wiki/). Também escrevemos [um script](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat), que pode ajudá-lo a começar.

Ainda estamos trabalhando para alinhar esse processo com o DevOps Resource Kit de gerenciamento de API.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Como posso selecionar um *layout* ao criar uma nova *página*?

Um *layout* é aplicado a uma página, correspondendo seu modelo de URL à URL *da página* . Por exemplo, o *layout* com um modelo de `/wiki/*` URL será aplicado a cada *página* com o `/wiki/` segmento: `/wiki/getting-started`, `/wiki/styles`e assim por diante.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Por que o console interativo do desenvolvedor não funciona?

É provável que ele esteja relacionado ao CORS. O console interativo faz uma solicitação de API do lado do cliente do navegador. Você pode resolver o problema de CORS adicionando [uma política de CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) em suas API (s). Você pode especificar todos os parâmetros manualmente (por exemplo, origem como https://contoso.com) ou usar um valor curinga. `*`

### <a name="where-can-i-file-bugs"></a>Onde posso arquivar bugs?

[No repositório do GitHub](http://aka.ms/apimdevportal).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o novo portal do desenvolvedor:

- [Repositório GitHub com o código-fonte][1]
- [Instruções sobre hospedagem interna do portal][2]
- [Roteiro público do projeto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects