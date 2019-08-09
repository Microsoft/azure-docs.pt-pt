---
title: Como solucionar problemas do catálogo de dados do Azure
description: Este artigo descreve as preocupações comuns de solução de problemas para recursos do catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879542"
---
# <a name="troubleshooting-azure-data-catalog"></a>Solucionando problemas do catálogo de dados do Azure

Este artigo descreve as preocupações comuns de solução de problemas para recursos do catálogo de dados do Azure. 

## <a name="functionality-limitations"></a>Limitações de funcionalidade

Ao usar o catálogo de dados do Azure, a seguinte funcionalidade é limitada:

- Não há suporte para contas com **função de convidado** de tipo. Você não pode adicionar contas de convidado como usuários do catálogo de dados do Azure e os usuários convidados não [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)podem usar o portal em.

- Não há suporte para a criação de recursos do catálogo de dados do Azure usando modelos de Azure Resource Manager ou comandos Azure PowerShell.

- O recurso do catálogo de dados do Azure não pode ser movido entre locatários do Azure.

## <a name="azure-active-directory-policy-configuration"></a>Configuração de políticas do Azure Active Directory

Pode deparar-se com casos em que consegue iniciar sessão no portal do Catálogo de Dados do Azure, mas quando tenta iniciá-la na ferramenta de registo da origem de dados, recebe uma mensagem de erro que o impede de o fazer. Esse erro pode ocorrer quando você está na rede da empresa ou quando você está se conectando de fora da rede da empresa.

A ferramenta de registo utiliza a *autenticação de formulários* para validar os inícios de sessão dos utilizadores relativamente ao Azure Active Directory. Para um início de sessão bem-sucedido, o administrador do Azure Active Directory tem de ativar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, pode ativar a autenticação em separado para ligações de intranet e extranet, conforme mostrado na imagem seguinte. Erros de entrada poderão ocorrer se a autenticação de formulários não estiver habilitada para a rede da qual você está se conectando.

 ![Política de autenticação global do Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Para obter mais informações, veja [Configuring authentication policies (Configurar políticas de autenticação)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Passos Seguintes

* [Criar um catálogo de dados do Azure](data-catalog-get-started.md)
