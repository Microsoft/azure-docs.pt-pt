---
title: 'Sincronização de Azure AD Connect: impedir exclusões acidentais | Microsoft Docs'
description: Este tópico descreve o recurso impedir exclusões acidentais (evitando exclusões acidentais) no Azure AD Connect.
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
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827133"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: impedir eliminações acidentais
Este tópico descreve o recurso impedir exclusões acidentais (evitando exclusões acidentais) no Azure AD Connect.

Ao instalar Azure AD Connect, impedir exclusões acidentais é habilitada por padrão e configurada para não permitir uma exportação com mais de 500 exclusões. Esta funcionalidade foi concebida para proteger contra alterações acidentais de configuração e alterações ao seu diretório no local, que poderiam afetar muitos utilizadores e outros objetos.

## <a name="what-is-prevent-accidental-deletes"></a>O que é evitar exclusões acidentais
Os cenários comuns quando você vê muitas exclusões incluem:

* Alterações na [filtragem](how-to-connect-sync-configure-filtering.md) em que uma [UO](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) ou [domínio](how-to-connect-sync-configure-filtering.md#domain-based-filtering) inteiro é desmarcado.
* Todos os objetos numa UO são eliminados.
* O nome de uma UO foi alterado para que todos os objetos na mesma sejam considerados como estando fora do âmbito para a sincronização.

O valor padrão de 500 objetos pode ser alterado com o PowerShell usando `Enable-ADSyncExportDeletionThreshold`, que faz parte do módulo AD Sync instalado com Azure Active Directory Connect. Você deve configurar esse valor para se ajustar ao tamanho da sua organização. Como o Agendador de sincronização é executado a cada 30 minutos, o valor é o número de exclusões vistas em 30 minutos.

Se houver muitas exclusões em etapas para serem exportadas para o Azure AD, a exportação será interrompida e você receberá um email como este:

![Impedir exclusão acidental de email](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Olá (contato técnico). Em (hora) o serviço de Sincronização de Identidades detectou que o número de exclusões excedeu o limite de exclusão configurado para o (nome da organização). Um total de objetos (número) foram enviados para exclusão nesta Sincronização de Identidades execução. Isso atingiu ou excedeu o valor de limite de exclusão configurado de objetos (número). Precisamos que você forneça a confirmação de que essas exclusões devem ser processadas antes de continuarmos. Consulte impedindo exclusões acidentais para obter mais informações sobre o erro listado nesta mensagem de email.*
>
> 

Você também pode ver o status `stopped-deletion-threshold-exceeded` ao examinar a interface do usuário do **Synchronization Service Manager** para o perfil de exportação.
![impedir que as exclusões acidentais sincronizem a interface do usuário Service Manager](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se isso era inesperado, investigue e tome as medidas corretivas. Para ver quais objetos estão prestes a serem excluídos, faça o seguinte:

1. Inicie o **serviço de sincronização** no menu iniciar.
2. Vá para **conectores**.
3. Selecione o conector com o tipo **Azure Active Directory**.
4. Em **ações** à direita, selecione **Pesquisar espaço do conector**.
5. No pop-up em **escopo**, selecione **desconectado desde** e escolha um horário no passado. Clique em **Procurar**. Esta página fornece uma exibição de todos os objetos a serem excluídos. Ao clicar em cada item, você pode obter informações adicionais sobre o objeto. Você também pode clicar em **configuração de coluna** para adicionar atributos adicionais a serem visíveis na grade.

![Pesquisar espaço do conector](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Se você não tiver certeza de que todas as exclusões são desejadas e deseja descer uma rota mais segura. Você pode usar o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold` para definir um novo limite em vez de desabilitar o limite que pode permitir exclusões indesejadas. 

## <a name="if-all-deletes-are-desired"></a>Se todas as exclusões forem desejadas
Se todas as exclusões forem desejadas, faça o seguinte:

1. Para recuperar o limite de exclusão atual, execute o cmdlet do PowerShell `Get-ADSyncExportDeletionThreshold`. Forneça uma conta de administrador global do Azure AD e uma senha. O valor padrão é 500.
2. Para desabilitar temporariamente essa proteção e permitir que essas exclusões passem, execute o cmdlet do PowerShell: `Disable-ADSyncExportDeletionThreshold`. Forneça uma conta de administrador global do Azure AD e uma senha.
   ![Credenciais](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Com o conector de Azure Active Directory ainda selecionado, selecione a ação **executar** e selecione **Exportar**.
4. Para reabilitar a proteção, execute o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Substitua 500 pelo valor que você observou ao recuperar o limite de exclusão atual. Forneça uma conta de administrador global do Azure AD e uma senha.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Sincronização de Azure AD Connect: entender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
