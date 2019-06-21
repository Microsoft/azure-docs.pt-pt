---
title: Como resolver problemas de catálogo de dados do Azure
description: Este artigo descreve as preocupações de resolução de problemas mais comuns para os recursos do catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203496"
---
# <a name="troubleshooting-azure-data-catalog"></a>Resolução de problemas do catálogo de dados do Azure

Este artigo descreve as preocupações de resolução de problemas mais comuns para os recursos do catálogo de dados do Azure. 

## <a name="functionality-limitations"></a>Limitações de funcionalidade

Ao utilizar o catálogo de dados do Azure, a seguinte funcionalidade é limitada:

- Contas com o tipo **função convidado** não são suportadas. Não é possível adicionar contas de convidado como utilizadores do catálogo de dados do Azure e os utilizadores convidados não é possível utilizar o portal ao www.azuredatacatalog.com.

- Não é suportada a criação de recursos do catálogo de dados do Azure com os comandos de modelos do Azure Resource Manager ou o Azure PowerShell.

- O recurso catálogo de dados do Azure não pode ser movido entre inquilinos do Azure.

## <a name="azure-active-directory-policy-configuration"></a>Configuração de políticas do Azure Active Directory

Pode deparar-se com casos em que consegue iniciar sessão no portal do Catálogo de Dados do Azure, mas quando tenta iniciá-la na ferramenta de registo da origem de dados, recebe uma mensagem de erro que o impede de o fazer. Este erro pode ocorrer quando está na rede da empresa ou quando está a ligar a partir de fora da rede da empresa.

A ferramenta de registo utiliza a *autenticação de formulários* para validar os inícios de sessão dos utilizadores relativamente ao Azure Active Directory. Para um início de sessão bem-sucedido, o administrador do Azure Active Directory tem de ativar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, pode ativar a autenticação em separado para ligações de intranet e extranet, conforme mostrado na imagem seguinte. Erros de início de sessão podem ocorrer se a autenticação de formulários não está ativada para a rede a partir do qual está se conectando.

 ![Política de autenticação global do Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Para obter mais informações, veja [Configuring authentication policies (Configurar políticas de autenticação)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Passos Seguintes

* [Criar um catálogo de dados do Azure](data-catalog-get-started.md)
