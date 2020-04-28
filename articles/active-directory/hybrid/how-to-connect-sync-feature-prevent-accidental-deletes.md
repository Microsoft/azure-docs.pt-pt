---
title: 'Sincronização Azure AD Connect: Prevenir eliminações acidentais / Microsoft Docs'
description: Este tópico descreve a função de eliminação acidental (prevenção de eliminações acidentais) no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f3109b4c87e25444629ca25411894eab8a9d56
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "71827133"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: impedir eliminações acidentais
Este tópico descreve a função de eliminação acidental (prevenção de eliminações acidentais) no Azure AD Connect.

Ao instalar o Azure AD Connect, evitar que as eliminações acidentais sejam ativadas por predefinição e configuradas para não permitir uma exportação com mais de 500 exclusões. Esta funcionalidade foi concebida para proteger contra alterações acidentais de configuração e alterações ao seu diretório no local, que poderiam afetar muitos utilizadores e outros objetos.

## <a name="what-is-prevent-accidental-deletes"></a>O que é prevenir apagações acidentais
Os cenários comuns quando se vê muitos exclusores incluem:

* Alterações à [filtragem](how-to-connect-sync-configure-filtering.md) quando um [ou](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) [domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) inteiro não é selecionado.
* Todos os objetos numa UO são eliminados.
* O nome de uma UO foi alterado para que todos os objetos na mesma sejam considerados como estando fora do âmbito para a sincronização.

O valor padrão de 500 objetos `Enable-ADSyncExportDeletionThreshold`pode ser alterado com a utilização da PowerShell, que faz parte do módulo AD Sync instalado com o Azure Ative Directory Connect. Deve configurar este valor para se adaptar ao tamanho da sua organização. Uma vez que o programador de sincronização funciona a cada 30 minutos, o valor é o número de exclusões vistas dentro de 30 minutos.

Se houver demasiados aeliminar para serem exportados para a AD Azure, então a exportação para e recebe um e-mail como este:

![Prevenir a eliminação acidental de e-mail](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Olá (contacto técnico). Na (altura) o serviço de sincronização de identidade detetou que o número de supressões excedia o limiar de eliminação configurado para (nome da organização). Um total de (número) objetos foram enviados para eliminação nesta execução de sincronização de identidade. Isto cumpriu ou excedeu o valor limiar de eliminação configurado de objetos (número). Precisamos que forneça a confirmação de que estas supressões devem ser processadas antes de prosseguirmos. Consulte as eliminações acidentais para obter mais informações sobre o erro listado nesta mensagem de e-mail.*
>
> 

Você também pode `stopped-deletion-threshold-exceeded` ver o estado quando você olhar no Gestor de **Serviços de Sincronização** UI para o perfil de Exportação.
![Prevenir a eliminação acidental do Gestor de Serviçosincronizado UI](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se isto foi inesperado, então investigue e tome medidas corretivas. Para ver quais os objetos que estão prestes a ser eliminados, faça o seguinte:

1. Inicie o Serviço de **Sincronização** a partir do Menu Iniciar.
2. Vá aos **Conectores.**
3. Selecione o Conector com o tipo **Azure Ative Directory**.
4. Em **ações** à direita, selecione **Search Connector Space**.
5. No pop-up sob **Scope,** selecione **Disconnected Since** e escolha uma vez no passado. Clique em **Procurar**. Esta página fornece uma visão de todos os objetos prestes a serem eliminados. Ao clicar em cada item, pode obter informações adicionais sobre o objeto. Também pode clicar na **Definição de Colunas** para adicionar atributos adicionais para serem visíveis na grelha.

![Pesquisar Espaço conector](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Se não tem a certeza se todos os apagues são desejados e deseja seguir um caminho mais seguro. Pode utilizar o cmdlet `Enable-ADSyncExportDeletionThreshold` PowerShell: para definir um novo limiar em vez de desativar o limiar que poderia permitir supressões indesejadas. 

## <a name="if-all-deletes-are-desired"></a>Se todos os exclusões forem desejados
Se todos os exclusões forem desejados, faça o seguinte:

1. Para recuperar o limiar de eliminação atual, `Get-ADSyncExportDeletionThreshold`executar o cmdlet PowerShell . Forneça uma conta de Administrador Global Da AD Azure e senha. O valor predefinido é 500.
2. Para desativar temporariamente esta proteção e deixar passar esses `Disable-ADSyncExportDeletionThreshold`apague, passe o cmdlet PowerShell: . Forneça uma conta de Administrador Global Da AD Azure e senha.
   ![Credenciais](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Com o Conector de Diretório Ativo Azure ainda selecionado, selecione a **ação Run** e selecione **Export**.
4. Para voltar a ativar a proteção, `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`execute o cmdlet PowerShell: . Substitua 500 pelo valor que notou ao recuperar o limiar de eliminação atual. Forneça uma conta de Administrador Global Da AD Azure e senha.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
