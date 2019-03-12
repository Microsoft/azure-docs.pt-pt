---
title: Pré-requisitos do Catálogo de Dados do Azure
description: Saiba mais sobre os pré-requisitos que obrigatórios começar a utilizar o catálogo de dados do Azure.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 788e7a453e9879d9c901448659ff1c8cfa7201ff
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548322"
---
# <a name="azure-data-catalog-prerequisites"></a>Pré-requisitos do Catálogo de Dados do Azure

É necessário cuidar de algumas coisas antes de poder configurar o catálogo de dados do Azure. Não se preocupe, este processo não demorar muito tempo.

## <a name="azure-subscription"></a>Subscrição do Azure
Para configurar o catálogo de dados, tem de ser o proprietário ou coproprietário de uma subscrição do Azure.

As subscrições do Azure ajudam-na organizar o acesso aos recursos de serviço em nuvem, como o catálogo de dados. As subscrições também ajudar a controlar como utilização de recursos é comunicada, faturada e paga. Cada subscrição pode ter uma configuração de faturação e pagamento separada, para que possa ter subscrições e planos variam consoante o departamento, projeto, escritório regional e assim por diante. Todos os serviços cloud pertence a uma subscrição e tem de ter uma subscrição antes de configurar o catálogo de dados. Para saber mais, veja [Gerir contas, subscrições e funções administrativas](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Para configurar o catálogo de dados, precisa estar conectado com uma conta de utilizador do Azure Active Directory (Azure AD).

O Azure AD proporciona uma forma fácil de a sua empresa gerir a identidade e o acesso, tanto na nuvem, como no local. Os utilizadores podem utilizar uma conta escolar ou profissional de único para o início de sessão único para qualquer cloud e no local de aplicação web. Catálogo de dados utiliza o Azure AD para autenticar o início de sessão. Para obter mais informações, consulte [o que é o Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

> [!NOTE]
> Ao utilizar o [portal do Azure](https://portal.azure.com/), pode iniciar sessão com uma conta Microsoft pessoal ou um Azure Active Directory funcionam conta escolar ou profissional. Para configurar o catálogo de dados através do portal do Azure ou o [portal do catálogo de dados](https://www.azuredatacatalog.com), tem de iniciar sessão com uma conta do Azure Active Directory, não uma conta pessoal.
>
>

## <a name="active-directory-policy-configuration"></a>Configuração de política do Active Directory
Pode encontrar uma situação em que pode iniciar sessão portal do catálogo de dados, mas quando tenta iniciar sessão para a ferramenta de registo da origem de dados, recebe uma mensagem de erro que o impede de iniciar sessão. Esse comportamento de problema pode ocorrer apenas quando está na rede da empresa, ou poderão ocorrer apenas quando está a ligar a partir de fora da rede da empresa.

A ferramenta de registo da origem de dados utiliza a autenticação de formulários para validar as credenciais de utilizador no Active Directory. Para ajudar a iniciar sessão com êxito, o administrador do Active Directory tem de ativar a autenticação de formulários na política de autenticação Global.

Na política de autenticação Global, métodos de autenticação podem ser ativados em separado para intranet e extranet conexões, conforme mostrado na captura de ecrã seguinte. Erros de início de sessão poderão ocorrer se a autenticação de formulários não está ativada para a rede a partir do qual está se conectando.

 ![Política de autenticação Global do Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte [configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).
