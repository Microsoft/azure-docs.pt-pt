---
title: Como configurar o Write-back de senha para o Azure AD SSPR-Azure Active Directory
description: Usar o Azure AD e Azure AD Connect para fazer write-back de senhas para um diretório local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e12fe38ba69f6ac8f27130e01baff0c358aa409
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021762"
---
# <a name="how-to-configure-password-writeback"></a>Como: configurar o Write-back de senha

As etapas a seguir pressupõem que você já tenha configurado Azure AD Connect em seu ambiente usando as configurações [expressas](../hybrid/how-to-connect-install-express.md) ou [personalizadas](../hybrid/how-to-connect-install-custom.md) .

1. Para configurar e ativar a repetição de escrita de palavras-passe, inicie sessão no seu servidor do Azure AD Connect e inicie o assistente de configuração **Azure AD Connect**.
2. Na página de **Boas-vindas**, selecione **Configurar**.
3. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
4. Na página **Ligar ao Azure AD**, introduza uma credencial de administrador global e, em seguida, selecione **Seguinte**.
5. Nas páginas de filtragem **Ligar diretórios** e **Domínio/UO**, selecione **Seguinte**.
6. Na página **Funcionalidades opcionais**, selecione a caixa junto a **Repetição de escrita de palavras-passe** e selecione **Seguinte**.
   ![Habilitar write-back de senha no Azure AD Connect][Writeback]
7. Na página **Pronto a configurar**, selecione **Configurar** e aguarde que o processo termine.
8. Quando vir a configuração a concluir, selecione **Sair**.

Para tarefas comuns de solução de problemas relacionadas ao Write-back de senha, consulte a seção [solucionar problemas de write-back de senha](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) em nosso artigo de solução de problemas

> [!WARNING]
> O Write-back de senha deixará de funcionar para clientes que estão usando Azure AD Connect versões 1.0.8641.0 e mais antigas quando o [serviço de controle de acesso do Azure (ACS) for desativado em 7 de novembro de 2018](../develop/active-directory-acs-migration.md). Azure AD Connect versões 1.0.8641.0 e mais antigas não permitirão mais write-back de senha nesse momento porque dependem do ACS para essa funcionalidade.
>
> Para evitar uma interrupção no serviço, atualize de uma versão anterior do Azure AD Connect para uma versão mais recente, consulte o artigo [Azure ad Connect: atualizar de uma versão anterior para a mais recente](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para Write-back de senha

**Redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local é um recurso Premium do Azure ad**. Para obter mais informações sobre licenciamento, consulte o [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Para usar o Write-back de senha, você deve ter uma das seguintes licenças atribuídas em seu locatário:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 ou a3
* Enterprise Mobility + Security E5 ou a5
* Microsoft 365 E3 ou a3
* Microsoft 365 E5 ou a5
* Microsoft 365 F1
* Microsoft 365 Empresas

> [!WARNING]
> Os planos de licenciamento do Office 365 autônomos *não dão suporte a "redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local"* e exigem que você tenha um dos planos anteriores para que essa funcionalidade funcione.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Permissões de Active Directory e políticas de complexidade de senha local 

A conta especificada no utilitário Azure AD Connect deve ter os seguintes itens definidos se você quiser estar no escopo para SSPR:

* **Repor palavra-passe** 
* **Alterar palavra-passe** 
* **Permissões de gravação** em `lockoutTime`
* **Permissões de gravação** em `pwdLastSet`
* **Direitos estendidos** em:
   * O objeto raiz de *cada domínio* nessa floresta
   * As UOs (unidades organizacionais) do usuário que você deseja que estejam no escopo para SSPR

Se você não tiver certeza de qual conta a conta descrita se refere, abra a interface do usuário de configuração do Azure Active Directory Connect e selecione a opção **Exibir configuração atual** . A conta à qual você precisa adicionar permissão está listada em **diretórios sincronizados**.

Se você definir essas permissões, a conta de serviço do MA para cada floresta poderá gerenciar senhas em nome das contas de usuário dentro dessa floresta. 

> [!IMPORTANT]
> Se você não atribuir essas permissões, então, mesmo que o Write-back pareça estar configurado corretamente, os usuários encontrarão erros quando tentarem gerenciar suas senhas locais da nuvem.
>

> [!NOTE]
> Pode levar até uma hora ou mais para que essas permissões sejam replicadas em todos os objetos em seu diretório.
>

Para configurar as permissões apropriadas para que o Write-back de senha ocorra, conclua as seguintes etapas:

1. Abra Active Directory usuários e computadores com uma conta que tenha as permissões de administração de domínio apropriadas.
2. No menu **Exibir** , verifique se **recursos avançados** está ativado.
3. No painel esquerdo, clique com o botão direito do mouse no objeto que representa a raiz do domínio e selecione **propriedades** > **segurança** > **avançado**.
4. Na guia **permissões** , selecione **Adicionar**.
5. Escolha a conta à qual as permissões estão sendo aplicadas (na Azure AD Connect configuração).
6. Na lista suspensa **aplica-se a** , selecione **objetos de usuário descendentes**.
7. Em **permissões**, selecione as caixas para as seguintes opções:
    * **Alterar palavra-passe**
    * **Repor palavra-passe**
8. Em **Propriedades**, selecione as caixas para as seguintes opções:
    * **Gravação de locktime**
    * **PwdLastSet de gravação**
9. Selecione **aplicar/Ok** para aplicar as alterações e sair de qualquer caixa de diálogo aberta.

Como a origem da autoridade está no local, as políticas de complexidade de senha se aplicam da mesma fonte de dados conectada. Verifique se você alterou as políticas de grupo existentes para "duração mínima da senha". A política de grupo não deve ser definida como 1, o que significa que a senha deve ter pelo menos um dia de idade antes de ser atualizada. Você precisa ter certeza de que ele está definido como 0. Essas configurações podem ser encontradas em `gpmc.msc` em **configuração do computador > políticas > configurações do Windows > configurações de segurança > políticas de conta**. Execute `gpupdate /force` para garantir que a alteração entra em vigor. 

## <a name="next-steps"></a>Passos seguintes

[O que é o Write-back de senha?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Habilitar write-back de senha no Azure AD Connect"
