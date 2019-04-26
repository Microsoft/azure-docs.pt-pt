---
title: 'Sincronização do Azure AD Connect: Alterar a configuração predefinida | Documentos da Microsoft'
description: Disponibiliza as melhores práticas para alterar a configuração predefinida de sincronização do Azure AD Connect.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60242117"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Sincronização do Azure AD Connect: Melhores práticas para alterar a configuração predefinida
O objetivo deste tópico é descrever as alterações de suportados e não suportadas para a sincronização do Azure AD Connect.

A configuração criada pelo Azure AD Connect funciona "como está" para a maioria dos ambientes que sincronizar o Active Directory no local com o Azure AD. No entanto, em alguns casos, é necessário aplicar algumas alterações para uma configuração para satisfazer uma necessidade específica ou um requisito.

## <a name="changes-to-the-service-account"></a>Alterações para a conta de serviço
Sincronização do Azure AD Connect está a ser executado sob uma conta de serviço criada pelo Assistente de instalação. Esta conta de serviço contém as chaves de encriptação à base de dados utilizado pelo sync. Ele é criado com uma senha longa de 127 carateres e a palavra-passe é definida para não expirar.

* É **não suportado** para alterar ou repor a palavra-passe da conta de serviço. Se o fizer, destrói as chaves de encriptação e o serviço não é capaz de acessar o banco de dados e não é possível iniciar.

## <a name="changes-to-the-scheduler"></a>Alterações ao scheduler
Começando com as versões de compilação 1.1 (Fevereiro de 2016) pode configurar o [agendador](how-to-connect-sync-feature-scheduler.md) ter um ciclo de sincronização diferentes que não o predefinido de 30 minutos.

## <a name="changes-to-synchronization-rules"></a>Alterações às regras de sincronização
O Assistente de instalação fornece uma configuração que tenha suporte para funcionar para os cenários mais comuns. Caso seja necessário fazer alterações à configuração, tem de seguir estas regras ainda ter uma configuração suportada.

> [!WARNING]
> Se fizer alterações às regras de sincronização padrão, em seguida, estas alterações serão substituídas na próxima vez que o Azure AD Connect é atualizado, resultando em resultados da sincronização indesejados inesperado e provavelmente.

* Pode [alterar fluxos de atributos](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) se os fluxos de atributos direta padrão não são adequados para a sua organização.
* Se quiser [fluxo não de um atributo](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) e remover valores de qualquer atributo existente no Azure AD, em seguida, precisa para criar uma regra para este cenário.
* [Desativar uma regra de sincronização indesejados](#disable-an-unwanted-sync-rule) em vez de eliminá-lo. Uma regra eliminada é recriada durante uma atualização.
* Para [alterar uma regra de out-of-box](#change-an-out-of-box-rule), deve fazer uma cópia da regra original e desativar a regra de out-of-box. O Editor de regra de Sincronização pede e ajuda-o.
* Exporte as regras de sincronização personalizados usando o Editor de regras de sincronização. O editor fornece um script do PowerShell que pode utilizar para recriá-las facilmente num cenário de recuperação após desastre.

> [!WARNING]
> As regras de sincronização de out-of-box têm um thumbprint. Se fizer uma alteração para estas regras, o thumbprint já não é correspondentes. Talvez tenha problemas no futuro, quando tenta aplicar uma nova versão do Azure AD Connect. Apenas fazer alterações a forma como ele é descrito neste artigo.

### <a name="disable-an-unwanted-sync-rule"></a>Desativar uma regra de sincronização indesejados
Não elimine uma regra de sincronização de out-of-box. Ele é recriado durante a atualização seguinte.

Em alguns casos, o Assistente de instalação produziu uma configuração que não está a funcionar para a sua topologia. Por exemplo, se tiver uma topologia de floresta de recursos de conta, mas tiver expandido o esquema na floresta de conta com o esquema do Exchange, as regras para o Exchange são criadas para a floresta de conta e a floresta de recursos. Neste caso, terá de desativar a regra de sincronização para o Exchange.

![Regra de sincronização desativado](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na imagem acima, o Assistente de instalação encontrou um esquema do Exchange 2003 antigo na floresta de conta. Foi adicionada esta extensão de esquema antes de floresta de recursos foi introduzida no ambiente da Fabrikam. Para garantir que não existem atributos da implementação do Exchange antigo são sincronizados, a regra de sincronização deve ser desativada conforme apresentado.

### <a name="change-an-out-of-box-rule"></a>Alterar uma regra de out-of-box
O único momento em que deve alterar uma regra de out-of-box é quando precisa alterar a regra de associação. Se precisar alterar um fluxo de atributos, em seguida, deve criar uma regra de sincronização com precedência superior as regras de out-of-box. A regra de é a única regra praticamente terá de clonar **do AD - associação de utilizador**. É possível substituir todas as outras regras com uma regra de precedência superior.

Se precisar de efetuar alterações a uma regra de out-of-box, em seguida, deve fazer uma cópia da regra de out-of-box e desativar a regra original. Em seguida, faça as alterações para a regra clonada. O Editor de regra de sincronização está a ajudar com esses passos. Quando abre uma regra de out-of-box, é apresentada nesta caixa de diálogo:  
![Aviso de regra de caixa](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada, em seguida, é aberta.  
![Regra clonada](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Sobre esta regra clonada, faça as alterações necessárias ao âmbito, associação e transformações.

## <a name="next-steps"></a>Passos Seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
