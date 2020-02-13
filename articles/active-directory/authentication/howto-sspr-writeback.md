---
title: Configure a reescrita da palavra-passe para SSPR - Diretório Ativo Azure
description: Utilize a Azure AD e a Azure AD Connect para reescrever palavras-passe para um diretório no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1fa447312ad6a1f92eaed1164020cb6ee95606e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161601"
---
# <a name="how-to-configure-password-writeback"></a>Como: Configurar a reescrita da palavra-passe

Os seguintes passos assumem que já configuraram o Azure AD Connect no seu ambiente utilizando as definições [Express](../hybrid/how-to-connect-install-express.md) ou [Custom.](../hybrid/how-to-connect-install-custom.md)

1. Para configurar e ativar a repetição de escrita de palavras-passe, inicie sessão no seu servidor do Azure AD Connect e inicie o assistente de configuração **Azure AD Connect**.
2. Na página de **Boas-vindas**, selecione **Configurar**.
3. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
4. Na página **Ligar ao Azure AD**, introduza uma credencial de administrador global e, em seguida, selecione **Seguinte**.
5. Nas páginas de filtragem **Ligar diretórios** e **Domínio/UO**, selecione **Seguinte**.
6. Na página **Funcionalidades opcionais**, selecione a caixa junto a **Repetição de escrita de palavras-passe** e selecione **Seguinte**.
   ![Ativar a reescrita da palavra-passe no Azure AD Connect][Writeback]
7. Na página **Pronto a configurar**, selecione **Configurar** e aguarde que o processo termine.
8. Quando vir a configuração a concluir, selecione **Sair**.

Para tarefas comuns de resolução de problemas relacionadas com a reescrita de palavra-passe, consulte a secção [Troubleshoot password writeback](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) no nosso artigo de resolução de problemas.

> [!WARNING]
> A reversão da palavra-passe deixará de funcionar para os clientes que estão a utilizar as versões Azure AD Connect 1.0.8641.0 e mais antigas quando o serviço de Controlo de [Acesso Azure (ACS) for retirado a 7 de novembro de 2018.](../azuread-dev/active-directory-acs-migration.md) Azure AD Connect versões 1.0.8641.0 e mais antigas não permitirão mais write-back de senha nesse momento porque dependem do ACS para essa funcionalidade.
>
> Para evitar uma perturbação no serviço, atualize de uma versão anterior do Azure AD Connect para uma versão mais recente, consulte o artigo [Azure AD Connect: Upgrade de uma versão anterior para a mais recente](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para Write-back de senha

**Reset/Change/Unlock com a reescrita de palavras-passe de self-service é uma característica premium do Azure AD**. Para mais informações sobre o licenciamento, consulte o site de [preços do Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

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
> Os planos de licenciamento autónomos do Office 365 *não suportam "Reset/Change/Unlock with on-premis"* e exigem que tenha um dos planos anteriores para que esta funcionalidade funcione.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Permissões de Diretório Ativo e políticas de complexidade de senhas no local 

A conta especificada no utilitário Azure AD Connect deve ter os seguintes itens definidos se quiser estar à margem do SSPR:

* **Repor palavra-passe** 
* **Alterar palavra-passe** 
* **Escreva permissões** na `lockoutTime`
* **Escreva permissões** na `pwdLastSet`
* **Direitos alargados** sobre ambos:
   * O objeto raiz de *cada domínio* naquela floresta
   * As unidades organizacionais do utilizador (OUs) que pretende estar ao seu alcance para o SSPR

Se não tiver a certeza a que conta a conta descrita se refere, abra o UI de configuração de Diretório Ativo Do Azure e selecione a opção de **configuração atual do View.** A conta a que precisa de adicionar permissão está listada em **Diretórios Sincronizados**.

Se definir estas permissões, a conta de serviço mA para cada floresta pode gerir senhas em nome das contas de utilizador dentro dessa floresta. 

> [!IMPORTANT]
> Se não atribuir estas permissões, então, embora o writeback pareça estar configurado corretamente, os utilizadores encontrarão erros quando tentam gerir as suas palavras-passe no local a partir da nuvem.
>

> [!NOTE]
> Pode levar até uma hora ou mais para estas permissões replicarem todos os objetos do seu diretório.
>

Para configurar as permissões adequadas para a reescrita de palavra-passe, complete os seguintes passos:

1. Open Ative Directory Users and Computers com uma conta que tem as permissões de administração de domínio apropriadas.
2. A partir do menu **'Ver',** certifique-se de que **as funcionalidades Avançadas** estão ligadas.
3. No painel esquerdo, clique à direita no objeto que representa a raiz do domínio e selecione **Propriedades** > **Segurança** > **Avançado**.
4. A partir do separador **Permissões,** selecione **Adicionar**.
5. Escolha a conta a que estão a ser aplicadas permissões (a partir da configuração Azure AD Connect).
6. Na lista **Aplica-se à** lista de abandono, selecione **objetos de utilizador descendentes**.
7. Em **Permissões,** selecione as caixas para as seguintes opções:
    * **Alterar palavra-passe**
    * **Repor palavra-passe**
8. Em **Propriedades**, selecione as caixas para as seguintes opções:
    * **Escreva lockoutTime**
    * **Escreva pwdLastSet**
9. Selecione **Apply/OK** para aplicar as alterações e sair de quaisquer caixas de diálogo abertas.

Uma vez que a fonte de autoridade está no local, as políticas de complexidade da palavra-passe aplicam-se a partir da mesma fonte de dados conectada. Certifique-se de que alterou as políticas de grupo existentes para a "idade mínima da senha". A política do grupo não deve ser definida para 1, o que significa que a palavra-passe deve ter pelo menos um dia antes de ser atualizada. Tens de ter a certeza que está marcado para 0. Estas definições podem ser encontradas em `gpmc.msc` em configuração de **computador > Políticas > Definições do Windows > Definições de Segurança > Políticas de Conta**. Faça `gpupdate /force` para garantir que a mudança tenha efeito. 

## <a name="next-steps"></a>Passos seguintes

[O que é a redação da palavra-passe?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Ativar a reescrita da palavra-passe no Azure AD Connect"
