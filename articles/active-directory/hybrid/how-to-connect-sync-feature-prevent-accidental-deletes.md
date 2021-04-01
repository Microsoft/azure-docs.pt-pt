---
title: 'Sincronização Azure AD Connect: Prevenir eliminações acidentais | Microsoft Docs'
description: Este tópico descreve a função prevent acidental de eliminações (prevenção de supressões acidentais) no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16d48cda87b8226ebc3bbab179c1034abf0a486f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90084614"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: impedir eliminações acidentais
Este tópico descreve a função prevent acidental de eliminações (prevenção de supressões acidentais) no Azure AD Connect.

Ao instalar o Azure AD Connect, evitar que as eliminações acidentais sejam ativadas por defeito e configuradas para não permitir uma exportação com mais de 500 eliminações. Esta funcionalidade foi concebida para proteger contra alterações acidentais de configuração e alterações ao seu diretório no local, que poderiam afetar muitos utilizadores e outros objetos.

## <a name="what-is-prevent-accidental-deletes"></a>O que é prevenir eliminações acidentais
Cenários comuns quando vê muitas eliminações incluem:

* Alterações na [filtragem](how-to-connect-sync-configure-filtering.md) onde um [ou](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) ou ou inteiro ou [domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) não é selecionado.
* Todos os objetos numa UO são eliminados.
* O nome de uma UO foi alterado para que todos os objetos na mesma sejam considerados como estando fora do âmbito para a sincronização.

O valor predefinido de 500 objetos pode ser alterado com a utilização do PowerShell `Enable-ADSyncExportDeletionThreshold` , que faz parte do módulo AD Sync instalado com o Azure Ative Directory Connect. Deve configurar este valor para se adaptar ao tamanho da sua organização. Uma vez que o programador de sincronização funciona a cada 30 minutos, o valor é o número de eliminações vistas dentro de 30 minutos.

Se houver demasiadas eliminações encenadas para serem exportadas para a Azure AD, então a exportação para e recebe um e-mail como este:

![Evitar que acidentalmente elimine o e-mail](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Olá (contacto técnico). No (momento) o serviço de sincronização de identidade detetou que o número de supressões excedia o limiar de eliminação configurado para (nome da organização). Um total de (número) objetos foram enviados para eliminação nesta execução de sincronização de identidade. Isto cumpriu ou excedeu o valor do limiar de eliminação configurado de (número) de objetos. Precisamos que confirme que estas supressões devem ser processadas antes de prosseguirmos. Consulte as supressões acidentais de prevenção para obter mais informações sobre o erro listado nesta mensagem de correio eletrónico.*
>
> 

Também pode ver o estado `stopped-deletion-threshold-exceeded` quando olha para o UI do Gestor de **Serviços de Sincronização** para o perfil de Exportação.
![Evitar elimina acidentalmente o Sync Service Manager UI](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se isto foi inesperado, então investigue e tome medidas corretivas. Para ver quais objetos estão prestes a ser eliminados, faça o seguinte:

1. Inicie **o Serviço de Sincronização** a partir do Menu Iniciar.
2. Vá a **Connectors.**
3. Selecione o Conector com o diretório ativo do tipo **Azure**.
4. Em **Ações** à direita, **selecione Search Connector Space**.
5. No pop-up em **Scope**, selecione **Disconnected Since** e escolha uma hora no passado. Clique em **Procurar**. Esta página fornece uma visão de todos os objetos prestes a serem eliminados. Ao clicar em cada item, pode obter informações adicionais sobre o objeto. Também pode clicar na **Definição de Coluna** para adicionar atributos adicionais para serem visíveis na grelha.

![Procurar espaço no conector](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Se não tem a certeza de que todos os apagantes são desejados e deseja seguir um caminho mais seguro. Pode utilizar o cmdlet PowerShell: `Enable-ADSyncExportDeletionThreshold` para definir um novo limiar em vez de desativar o limiar que poderia permitir supressões não desejadas. 

## <a name="if-all-deletes-are-desired"></a>Se todos os apagantes forem desejados
Se todas as eliminações forem desejadas, então faça o seguinte:

1. Para recuperar o limiar de eliminação atual, executar o cmdlet PowerShell `Get-ADSyncExportDeletionThreshold` . Forneça uma conta e senha do Administrador Global Azure AD. O valor predefinido é 500.
2. Para desativar temporariamente esta proteção e deixar passar as eliminações, passe o cmdlet PowerShell: `Disable-ADSyncExportDeletionThreshold` . Forneça uma conta e senha do Administrador Global Azure AD.
   ![A screenshot mostra uma caixa de diálogo para introduzir o nome de utilizador e palavra-passe do Administrador Global Azure AD.](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Com o Azure Ative Directory Connector ainda selecionado, selecione o Action **Run** e selecione **Export**.
4. Para voltar a ativar a proteção, executar o cmdlet PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500` . Substitua 500 pelo valor que notou ao recuperar o limiar de eliminação atual. Forneça uma conta e senha do Administrador Global Azure AD.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
