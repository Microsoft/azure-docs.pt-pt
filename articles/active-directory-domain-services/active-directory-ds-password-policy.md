---
title: 'Azure Active Directory Domain Services: Política de palavra-passe | Documentos da Microsoft'
description: Compreender as políticas de palavra-passe nos domínios geridos
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: ergreenl
ms.openlocfilehash: 513cccb8b83eb4a69df1bc6172f1f02485215e35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086008"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Políticas de bloqueio de conta e palavra-passe nos domínios geridos
Este artigo explica as políticas de palavra-passe predefinido num domínio gerido. Também aborda como configurar estas políticas.

## <a name="fine-grained-password-policies-fgpp"></a>Políticas de palavra-passe de refinado bem (FGPP)
Utilize políticas de palavras-passe detalhadas para especificar várias políticas de palavra-passe dentro de um único domínio. FGPP permite-lhe aplicar diferentes restrições de políticas de bloqueio de conta e palavra-passe a diferentes conjuntos de utilizadores num domínio. Por exemplo, pode aplicar definições de palavra-passe strict a contas com privilégios.

Pode configurar as seguintes definições de palavra-passe utilizando FGPP:
* Comprimento mínimo da palavra-passe
* Histórico de palavras-passe
* As palavras-passe tem de cumprir os requisitos de complexidade
* Idade mínima da palavra-passe
* Idade máxima da palavra-passe
* Política de bloqueio de conta
    * Duração do bloqueio de conta
    * Número de tentativas de início de sessão permitido
    * Contagem depois de tentativas de reposição de início de sessão


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Predefinições de política de palavras-passe detalhada num domínio gerido
Captura de ecrã seguinte ilustra a política de palavra-passe de refinado bem predefinida configurada num domínio gerido do Azure AD Domain Services.

![Política de palavras-passe detalhada predefinida](./media/how-to/default-fgpp.png)

> [!NOTE]
> Não é possível modificar ou eliminar a política de palavras-passe detalhada incorporada predefinida. Membros do grupo "Administradores do AAD DC" podem criar FGPP personalizado e configurá-lo para substituir (têm precedência sobre) o padrão FGPP incorporada.
>
>

## <a name="password-policy-settings"></a>Definições de política de palavra-passe
Num domínio gerido, as seguintes políticas de palavra-passe estão configuradas por predefinição:
* Comprimento mínimo da palavra-passe (carateres): 7
* Duração de senha máxima (duração): 90 dias
* As palavras-passe tem de cumprir os requisitos de complexidade

### <a name="account-lockout-settings"></a>Configurações de bloqueio de conta
Num domínio gerido, as seguintes políticas de bloqueio de conta estão configuradas por predefinição:
* Duração do bloqueio de conta: 30
* Número de tentativas de início de sessão permitido: 5
* Reposição de início de sessão tenta contagem após: 30 minutos

Efetivamente, contas de utilizador são bloqueadas durante 30 minutos se as palavras-passe inválidas cinco são utilizadas em dois minutos. As contas são automaticamente desbloqueadas após 30 minutos.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Criar uma política de palavras-passe detalhada personalizado (FGPP) num domínio gerido
Pode criar um FGPP personalizada e aplicá-la a grupos específicos no seu domínio gerido. Esta configuração substitui eficazmente a predefinição que FGPP configurado para o domínio gerido.

> [!TIP]
> Apenas os membros dos **"Administradores do AAD DC"** grupo tem as permissões para criar políticas de palavras-passe detalhada personalizadas.
>
>

Além disso, também pode criar políticas de bem refinado palavras-passe personalizadas e aplicá-las para qualquer UOs personalizadas que criar no domínio gerido.

Pode configurar um FGPP personalizado pelos seguintes motivos:
* Para definir uma política de bloqueio de conta diferente.
* Para configurar uma definição de tempo de vida de palavra-passe predefinida para o domínio gerido.

Para criar um FGPP personalizado no seu domínio gerido:
1. Inicie sessão na VM do Windows utiliza para administrar o seu domínio gerido. Se não tiver um, siga as instruções para [administrar um domínio gerido](active-directory-ds-admin-guide-administer-domain.md)
2. Iniciar o **Centro de administração do Active Directory** na VM.
3. Clique no nome de domínio (por exemplo, ' contoso100.com').
4. Faça duplo clique em **sistema** para abrir o contentor de sistema.
5. Faça duplo clique em **contentor de definições de palavra-passe**.
6. Verá o padrão chamado de FGPP incorporada para o domínio gerido **AADDSSTFPSO**. Não é possível modificar este FGPP incorporada. No entanto, pode criar uma nova substituição FGPP personalizada a predefinição FGPP.
7. Sobre o **tarefas** painel no direito, clique em **New** e clique em **definições de palavra-passe**.
8. Na **criar definições de palavra-passe** caixa de diálogo, especifique as definições de palavra-passe personalizada para aplicar como parte de FGPP personalizado. Não se esqueça de definir a precedência corretamente para substituir a predefinição FGPP.

   ![Criar FGPP personalizado](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Não se esqueça de desmarcar a proteger a partir da opção de eliminação acidental.** Se esta opção estiver selecionada, não é possível guardar a FGPP.
   >
   >

9. Na **diretamente aplica-se a**, clique nas **Add** botão. Na **selecionar utilizadores ou grupos** caixa de diálogo, clique nas **localizações** botão.

   ![Selecione utilizadores e grupos](./media/how-to/fgpp-applies-to.png)

10. Na **localizações** caixa de diálogo, expanda o nome de domínio e clique em **utilizadores do aad DC**. Agora, pode selecionar um grupo dos utilizadores incorporados OU, na qual pretende aplicar a FGPP.

    ![Selecione a UO desse grupo pertence a](./media/how-to/fgpp-container.png)

11. Escreva o nome do grupo e clique nas **verificar nomes** botão para validar o grupo existe.

    ![Selecione o grupo para aplicar FGPP](./media/how-to/fgpp-apply-group.png)

12. O nome do grupo é apresentado em **diretamente aplica-se a** secção. Clique nas **OK** botão para guardar estas alterações.

    ![FGPP aplicada](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Para aplicar políticas de palavra-passe personalizada para contas de utilizador numa UO personalizada:** Políticas de palavra-passe de refinado bem podem ser aplicadas apenas a grupos. Para configurar uma política personalizada de palavra-passe apenas para os utilizadores de uma UO personalizado, crie um grupo que inclui os utilizadores nessa UO.
>
>

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre políticas de palavra-passe de refinado bem do Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurar políticas de palavras-passe detalhada utilizando o Centro de administração do AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
