---
title: Como resolver problemas no Catálogo de Dados do Azure
description: Este artigo descreve preocupações comuns de resolução de problemas para os recursos do Catálogo de Dados Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68879542"
---
# <a name="troubleshooting-azure-data-catalog"></a>Resolução de Problemas com o Catálogo de Dados do Azure

Este artigo descreve preocupações comuns de resolução de problemas para os recursos do Catálogo de Dados Azure. 

## <a name="functionality-limitations"></a>Limitações de funcionalidade

Ao utilizar o Catálogo de Dados do Azure, a seguinte funcionalidade é limitada:

- As contas com o tipo **de Função de Hóspede** não são suportadas. Não é possível adicionar contas de hóspedes como utilizadores do Azure Data Catalog, e os utilizadores convidados não podem utilizar o portal em [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) .

- A criação de recursos do Catálogo de Dados Azure utilizando modelos de gestores de recursos Azure ou comandos Azure PowerShell não é suportada.

- O recurso Azure Data Catalog não pode ser movido entre inquilinos da Azure.

## <a name="azure-active-directory-policy-configuration"></a>Configuração de políticas do Azure Active Directory

Pode deparar-se com casos em que consegue iniciar sessão no portal do Catálogo de Dados do Azure, mas quando tenta iniciá-la na ferramenta de registo da origem de dados, recebe uma mensagem de erro que o impede de o fazer. Este erro pode ocorrer quando estiver na rede da empresa ou quando estiver a ligar-se de fora da rede da empresa.

A ferramenta de registo utiliza a *autenticação de formulários* para validar os inícios de sessão dos utilizadores relativamente ao Azure Active Directory. Para um início de sessão bem-sucedido, o administrador do Azure Active Directory tem de ativar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, pode ativar a autenticação em separado para ligações de intranet e extranet, conforme mostrado na imagem seguinte. Podem ocorrer erros de inscrição se a autenticação dos formulários não estiver ativada para a rede a partir da qual está a ligar.

 ![Política de autenticação global do Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Para obter mais informações, veja [Configuring authentication policies (Configurar políticas de autenticação)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Passos seguintes

* [Criar um Catálogo de Dados do Azure](data-catalog-get-started.md)
