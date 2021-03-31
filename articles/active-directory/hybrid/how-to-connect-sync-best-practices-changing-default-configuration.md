---
title: 'Sincronização Azure AD Connect: Alterar a configuração padrão | Microsoft Docs'
description: Fornece as melhores práticas para alterar a configuração padrão da sincronização Azure AD Connect.
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
ms.topic: how-to
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e91ff8fa3666a2dfc5aaad07be7927852b08bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357703"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect Sync: As melhores práticas para alterar a configuração padrão
O objetivo deste tópico é descrever alterações suportadas e não suportadas na sincronização Azure AD Connect.

A configuração criada por Azure AD Connect funciona "como está" para a maioria dos ambientes que sincronizam o Ative Directory com Azure AD. No entanto, em alguns casos, é necessário aplicar algumas alterações a uma configuração para satisfazer uma necessidade ou requisito particular.

## <a name="changes-to-the-service-account"></a>Alterações na conta de serviço
A azure AD Connect está a ser executada sob uma conta de serviço criada pelo assistente de instalação. Esta conta de serviço contém as chaves de encriptação da base de dados utilizadas por sincronização. É criado com uma senha de 127 caracteres e a palavra-passe está definida para não expirar.

* Não é **suportado** para alterar ou redefinir a palavra-passe da conta de serviço. Ao fazê-lo, destrói as chaves de encriptação e o serviço não consegue aceder à base de dados e não é capaz de iniciar.

## <a name="changes-to-the-scheduler"></a>Alterações ao programador
Começando pelos lançamentos da build 1.1 (fevereiro de 2016) pode configurar o [programador](how-to-connect-sync-feature-scheduler.md) para ter um ciclo de sincronização diferente dos 30 minutos predefinidos.

## <a name="changes-to-synchronization-rules"></a>Alterações às Regras de Sincronização
O assistente de instalação fornece uma configuração que supostamente funciona para os cenários mais comuns. No caso de necessitar de fazer alterações na configuração, deve seguir estas regras para ainda ter uma configuração suportada.

> [!WARNING]
> Se efecer as regras de sincronização padrão, estas alterações serão substituídas da próxima vez que o Azure AD Connect for atualizado, resultando em resultados inesperados e prováveis de sincronização indesejados.

* Pode [alterar fluxos de atributos](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) se os fluxos de atributos diretos predefinidos não forem adequados para a sua organização.
* Se não [pretender fazer um atributo](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) e remover quaisquer valores de atributos existentes no AD Azure, então precisa de criar uma regra para este cenário.
* [Desative uma regra de sincronização indesejada](#disable-an-unwanted-sync-rule) em vez de a eliminar. Uma regra eliminada é recriada durante uma atualização.
* Para [alterar uma regra fora da caixa,](#change-an-out-of-box-rule)deve fazer uma cópia da regra original e desativar a regra fora da caixa. O Sync Rule Editor solicita e ajuda.
* Exporte as suas regras de sincronização personalizadas utilizando o Editor de Regras de Sincronização. O editor fornece-lhe um script PowerShell que pode usar para recriá-los facilmente num cenário de recuperação de desastres.

> [!WARNING]
> As regras de sincronização fora da caixa têm uma impressão digital. Se fizer uma alteração a estas regras, a impressão digital já não corresponde. Poderá ter problemas no futuro quando tentar aplicar um novo lançamento do Azure AD Connect. Só faça alterações da forma como é descrito neste artigo.

### <a name="disable-an-unwanted-sync-rule"></a>Desative uma regra de sincronização indesejada
Não elimine uma regra de sincronização fora de caixa. É recriado durante a próxima atualização.

Em alguns casos, o assistente de instalação produziu uma configuração que não está a funcionar para a sua topologia. Por exemplo, se você tem uma topologia florestal de recursos de conta mas você estendeu o esquema na floresta de conta com o esquema de troca, então as regras de Troca são criadas para a floresta de conta e a floresta de recursos. Neste caso, é necessário desativar a Regra de Sincronização para Troca.

![Regra de sincronização desativada](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na imagem acima, o assistente de instalação encontrou um esquema antigo de Exchange 2003 na floresta de contas. Esta extensão de esquema foi adicionada antes da introdução da floresta de recursos no ambiente de Fabrikam. Para garantir que nenhum atributo da antiga implementação do Exchange seja sincronizado, a regra de sincronização deve ser desativada como mostrado.

### <a name="change-an-out-of-box-rule"></a>Alterar uma regra fora da caixa
A única altura em que deves alterar uma regra fora da caixa é quando precisas de alterar a regra de junção. Se precisar de alterar um fluxo de atributos, então deve criar uma regra de sincronização com maior precedência do que as regras fora da caixa. A única regra que praticamente precisa de clonar é a regra **In from AD - User Join**. Pode anular todas as outras regras com uma regra de precedência mais elevada.

Se precisar de fazer alterações a uma regra fora de caixa, então deve fazer uma cópia da regra fora da caixa e desativar a regra original. Em seguida, faça as alterações à regra clonada. O Editor de Regras de Sincronização está a ajudá-lo com esses passos. Quando abre uma regra fora da caixa, é-lhe apresentada esta caixa de diálogo:  
![Aviso fora da regra da caixa](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada é então aberta.  
![Regra clonada](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Nesta regra clonada, faça todas as alterações necessárias ao âmbito, à união e às transformações.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
