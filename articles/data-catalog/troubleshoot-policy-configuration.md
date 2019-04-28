---
title: Como configurar a política do Azure Active Directory para o catálogo de dados do Azure
description: Pode encontrar uma situação em que pode iniciar sessão portal do catálogo de dados do Azure, mas quando tenta iniciar sessão para a ferramenta de registo da origem de dados, recebe uma mensagem de erro.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: e69a7e3bd104d0fb82b248b6560d4fd082c88426
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116606"
---
# <a name="azure-active-directory-policy-configuration"></a>Configuração de políticas do Azure Active Directory

Pode deparar-se com casos em que consegue iniciar sessão no portal do Catálogo de Dados do Azure, mas quando tenta iniciá-la na ferramenta de registo da origem de dados, recebe uma mensagem de erro que o impede de o fazer. Este erro pode ocorrer quando está na rede da empresa ou quando está a ligar a partir de fora da rede da empresa.

## <a name="registration-tool"></a>Ferramenta de registo

A ferramenta de registo utiliza a *autenticação de formulários* para validar os inícios de sessão dos utilizadores relativamente ao Azure Active Directory. Para um início de sessão bem-sucedido, o administrador do Azure Active Directory tem de ativar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, pode ativar a autenticação em separado para ligações de intranet e extranet, conforme mostrado na imagem seguinte. Erros de início de sessão podem ocorrer se a autenticação de formulários não está ativada para a rede a partir do qual está se conectando.

 ![Política de autenticação global do Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Para obter mais informações, veja [Configuring authentication policies (Configurar políticas de autenticação)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Passos Seguintes

* [Criar um catálogo de dados do Azure](data-catalog-get-started.md)