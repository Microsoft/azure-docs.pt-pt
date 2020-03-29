---
title: 'Sincronização Azure AD Connect: Alterar a configuração predefinida / Microsoft Docs'
description: Fornece as melhores práticas para alterar a configuração padrão do sincronizado Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940a35d89996b1eb9600fe4214863d2b5304750e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242117"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Sincronização Azure AD Connect: As melhores práticas para alterar a configuração predefinida
O objetivo deste tópico é descrever alterações suportadas e não suportadas à sincronização Azure AD Connect.

A configuração criada pelo Azure AD Connect funciona "como está" para a maioria dos ambientes que sincronizam no local O Diretório Ativo com a AD Azure. No entanto, em alguns casos, é necessário aplicar algumas alterações a uma configuração para satisfazer uma necessidade ou exigência particular.

## <a name="changes-to-the-service-account"></a>Alterações na conta de serviço
O sincronizado Azure AD Connect está a funcionar sob uma conta de serviço criada pelo assistente de instalação. Esta conta de serviço contém as chaves de encriptação da base de dados utilizadas por sincronização. É criado com uma senha de 127 caracteres e a palavra-passe está definida para não expirar.

* Não é **suportado** para alterar ou redefinir a palavra-passe da conta de serviço. Ao fazê-lo destrói as chaves de encriptação e o serviço não consegue aceder à base de dados e não é capaz de iniciar.

## <a name="changes-to-the-scheduler"></a>Alterações ao programador
Começando com os lançamentos da build 1.1 (fevereiro de 2016) pode configurar o [programador](how-to-connect-sync-feature-scheduler.md) para ter um ciclo de sincronização diferente dos 30 minutos predefinidos.

## <a name="changes-to-synchronization-rules"></a>Alterações às Regras de Sincronização
O assistente de instalação fornece uma configuração que é suposto funcionar para os cenários mais comuns. Caso necessite de fazer alterações na configuração, então deve seguir estas regras para ainda ter uma configuração suportada.

> [!WARNING]
> Se fizer alterações às regras de sincronização predefinidas, estas alterações serão substituídas da próxima vez que o Azure AD Connect for atualizado, resultando em resultados inesperados e prováveis de sincronização indesejados.

* Pode [alterar os fluxos de atributos](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) se os fluxos de atributos diretos padrão não forem adequados para a sua organização.
* Se quiser [não fluir um atributo](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) e remover quaisquer valores de atributo existentes em Azure AD, então precisa criar uma regra para este cenário.
* [Desative uma Regra de Sincronização indesejada](#disable-an-unwanted-sync-rule) em vez de a apagar. Uma regra eliminada é recriada durante uma atualização.
* Para [alterar uma regra fora da caixa,](#change-an-out-of-box-rule)deve fazer uma cópia da regra original e desativar a regra fora da caixa. O Editor de Regras de Sincronização pede e ajuda.
* Exporte as suas regras de sincronização personalizada utilizando o Editor de Regras de Sincronização. O editor fornece-lhe um script PowerShell que pode usar para recriá-los facilmente num cenário de recuperação de desastres.

> [!WARNING]
> As regras de sincronização fora da caixa têm uma impressão digital. Se fizeres uma alteração a estas regras, a impressão digital já não está a condizer. Poderá ter problemas no futuro quando tentar aplicar um novo lançamento do Azure AD Connect. Só faça alterações da forma como é descrito neste artigo.

### <a name="disable-an-unwanted-sync-rule"></a>Desativar uma regra de sincronização indesejada
Não elimine uma regra de sincronização fora da caixa. É recriado durante a próxima atualização.

Em alguns casos, o assistente de instalação produziu uma configuração que não está a funcionar para a sua topologia. Por exemplo, se tiver uma topologia florestal de recursos de conta, mas tiver alargado o esquema na floresta de contas com o esquema de intercâmbio, então são criadas regras para a Troca para a floresta de conta e para a floresta de recursos. Neste caso, precisa de desativar a Regra de Sincronização para Troca.

![Regra de sincronização para deficientes](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na imagem acima, o assistente de instalação encontrou um antigo esquema de Intercâmbio 2003 na floresta de contas. Esta extensão de esquema foi adicionada antes da floresta de recursos ser introduzida no ambiente de Fabrikam. Para garantir que nenhum atributo da antiga implementação do Exchange seja sincronizado, a regra de sincronização deve ser desativada como mostrado.

### <a name="change-an-out-of-box-rule"></a>Alterar uma regra fora da caixa
A única altura em que deves mudar uma regra fora da caixa é quando precisas de mudar a regra de adesão. Se precisar de alterar um fluxo de atributos, então deve criar uma regra de sincronização com maior precedência do que as regras fora da caixa. A única regra que praticamente precisa de clonar é a regra **in from AD - User Join**. Pode anular todas as outras regras com uma regra de precedência mais elevada.

Se precisar de alterar uma regra fora da caixa, então deve fazer uma cópia da regra fora da caixa e desativar a regra original. Em seguida, faça as alterações à regra clonada. O Editor de Regras de Sincronização está a ajudá-lo com esses passos. Quando abreuma regra fora da caixa, é-lhe apresentada esta caixa de diálogo:  
![Aviso fora da regra da caixa](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada é então aberta.  
![Regra clonada](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Nesta regra clonada, faça as alterações necessárias ao âmbito, à adesão e às transformações.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
