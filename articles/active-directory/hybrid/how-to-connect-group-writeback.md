---
title: 'Azure AD Connect: Writeback de grupo'
description: Este artigo descreve a gravação de grupo no Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246915"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect group writeback

A writeback de grupos permite que os clientes aproveitem os grupos de nuvem para as suas necessidades híbridas. Se utilizar a funcionalidade Grupos Microsoft 365, pode ter estes grupos representados no seu Ative Directory no local. Esta opção **só** está disponível se tiver o Exchange presente no seu Ative Directory no local.

## <a name="pre-requisites"></a>Pré-requisitos
Devem ser cumpridos os seguintes requisitos para permitir a inscrição em grupo.
- Licenças Azure Ative Directory Premium para o seu inquilino.
- Uma implementação híbrida configurada entre a sua organização Exchange on-in e Microsoft 365 e verificou que está a funcionar corretamente.
- Instalou uma versão suportada do Exchange no local
- Inscrição única configurada usando Azure Ative Directory Connect 

## <a name="enable-group-writeback"></a>Ativar repetição de escrita do grupo
Para ativar a gravação do grupo, utilize os seguintes passos:

1. Abra o assistente Azure AD Connect, selecione **Configurar** e, em seguida, clique em **Seguinte**.
2. **Selecione Personalizar opções de sincronização** e, em seguida, clique em **Seguinte**.
3. Na página **'Ligar a AD' Azure,** insira as suas credenciais. Clique em **Seguinte**.
4. Na página **de funcionalidades Opcionais,** verifique se as opções que configuraste anteriormente ainda estão selecionadas.
5. Selecione **a gravação do Grupo** e, em seguida, clique em **Seguinte**.
6. Na **página Writeback,** selecione uma unidade organizacional ative directory (OU) para armazenar objetos sincronizados da Microsoft 365 para a sua organização no local e, em seguida, clique em **Seguinte**.
7. Na página **Pronto** para configurar, clique em **Configurar**.
8. Quando o assistente estiver completo, clique em **Sair** na página completa da Configuração.
9. Abra o Windows PowerShell como administrador no servidor Azure Ative Directory Connect e execute os seguintes comandos.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

Para obter informações adicionais sobre a configuração dos grupos Microsoft 365 consulte [Configure Grupos Microsoft 365 com híbrido exchange no local](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Desativar a gravação do grupo
Para desativar o Writeback do Grupo, utilize os seguintes passos: 


1. Lance o assistente de Ligação Azure Ative e navegue para a página Tarefas Adicionais. Selecione a tarefa **de personalização de opções de sincronização** e clique **em seguida**.
2. Na página **Funcionalidades Opcionais,** desmarque a gravação do grupo.  Receberá um aviso informando-o de que os grupos serão eliminados.  Clique em **Sim**.
   >[!IMPORTANT]
   > Desativar o Writeback do Grupo fará com que quaisquer grupos que foram previamente criados por esta funcionalidade sejam eliminados do seu Diretório Ativo local no próximo ciclo de sincronização. 

   ![Caixa de desmarcação](media/how-to-connect-group-writeback/group2.png)
  
3. Clique em **Seguinte**.
4. Clique em **Configurar**.

 >[!NOTE]
 > Desativará as bandeiras de Repartição total e Sincronização Completas para "verdadeiro" no Conector de Diretório Ativo Azure, fazendo com que as alterações de regras se propaguem no próximo ciclo de sincronização, eliminando os grupos previamente recímos para o seu Diretório Ativo.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
