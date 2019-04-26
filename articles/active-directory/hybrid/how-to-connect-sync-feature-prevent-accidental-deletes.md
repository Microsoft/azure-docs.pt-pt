---
title: 'Sincronização do Azure AD Connect: Impedir eliminações acidentais | Documentos da Microsoft'
description: Este tópico descreve a funcionalidade de impedir eliminações acidentais (impedir eliminações acidentais) no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 07/12/2017
ms.date: 11/09/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: b1244dd460196e5882caab0d4b526850da48d084
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383394"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: Impedir eliminações acidentais
Este tópico descreve a funcionalidade de impedir eliminações acidentais (impedir eliminações acidentais) no Azure AD Connect.

Quando instalar o Azure AD Connect, impedir acidental eliminações é ativada por predefinição e configurado para não permitir uma exportação com mais de 500 eliminações. Esta funcionalidade foi concebida para proteger contra alterações acidentais de configuração e alterações ao seu diretório no local, que poderiam afetar muitos utilizadores e outros objetos.

## <a name="what-is-prevent-accidental-deletes"></a>O que está a impedir eliminações acidentais
Cenários comuns quando vê que elimina muitos incluem:

- Alterado para [filtragem](how-to-connect-sync-configure-filtering.md) sempre que um toda [UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) ou [domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) é não selecionada.
- Todos os objetos numa UO são eliminados.
- O nome de uma UO foi alterado para que todos os objetos na mesma sejam considerados como estando fora do âmbito para a sincronização.

O valor predefinido de 500 objetos pode ser alterado com o PowerShell usando `Enable-ADSyncExportDeletionThreshold`, que faz parte do módulo de sincronização do AD instalado com o Azure Active Directory Connect. Deve configurar esse valor para se ajustar ao tamanho da sua organização. Uma vez que o agendador de sincronização é executada a cada 30 minutos, o valor é o número de eliminações visto dentro de 30 minutos.

Se existirem demasiados eliminações de teste para ser exportada para o Azure AD, em seguida, pára a exportação e receber um e-mail assim:

![Impedir eliminações acidentais e-mail](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Olá (contacto técnico). A (hora), o serviço de sincronização de identidade detetou que o número de eliminações excedeu o limiar de eliminação configurado para (nome da organização). Um total de (número) objetos foram enviadas para eliminação nesta execução de sincronização de identidade. Isso atingido ou excedido o limiar configurado de eliminação de objetos (número). Tem de fornecer a confirmação de que estes eliminações devem ser processadas antes de irá prosseguir. Veja as impedir eliminações acidentais para obter mais informações sobre o erro listado nesta mensagem de e-mail.*
>
> 

Também pode ver o estado `stopped-deletion-threshold-exceeded` quando analisar o **Synchronization Service Manager** interface do Usuário para o perfil de exportação.
![Impedir eliminações acidentais da IU do Gestor do serviço de sincronização](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se esta foi inesperada, em seguida, investigue e tome medidas corretivas. Para ver quais os objetos que estão prestes a ser eliminado, efetue o seguinte:

1. Inicie **serviço de sincronização** do Menu Iniciar.
2. Aceda a **conectores**.
3. Seleccione o conector com o tipo **do Azure Active Directory**.
4. Sob **ações** à direita, selecione **procurar espaço conector**.
5. No pop-up sob **âmbito**, selecione **desligado, uma vez que** e escolha uma hora no passado. Clique em **Procurar**. Esta página fornece uma vista de todos os objetos prestes a ser eliminado. Ao clicar em cada item, pode obter informações adicionais sobre o objeto. Também pode clicar **definição de coluna** para adicionar atributos adicionais para ser visíveis na grelha.

![Procurar espaço conector](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

Se todas as exclusões são pretendidos, em seguida, faça o seguinte:

1. Para obter o limiar de eliminação atual, execute o cmdlet do PowerShell `Get-ADSyncExportDeletionThreshold`. Forneça uma conta de Administrador Global do AD do Azure e a palavra-passe. O valor predefinido é 500.
2. Temporariamente desativar esta proteção e permitir que as eliminações percorrer, execute o cmdlet do PowerShell: `Disable-ADSyncExportDeletionThreshold`. Forneça uma conta de Administrador Global do AD do Azure e a palavra-passe.
   ![Credenciais](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Com o conector Azure Active Directory ainda selecionado, selecione a ação **execute** e selecione **exportar**.
4. Para voltar a ativar a proteção, execute o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Substitua o valor percebido ao obter o limiar de eliminação atual 500. Forneça uma conta de Administrador Global do AD do Azure e a palavra-passe.

## <a name="next-steps"></a>Passos Seguintes
**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
- [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

