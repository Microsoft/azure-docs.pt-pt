---
title: 'Azure AD Connect: autenticação em nuvem – distribuição em etapas | Microsoft Docs'
description: Explica como migrar da autenticação federada para a autenticação de nuvem usando uma distribuição em etapas.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/28/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24efd9d67e5bcc083ce1a02dcf0850a39189afe8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173103"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Autenticação de nuvem: distribuição em etapas (visualização pública)

Esse recurso permite migrar da autenticação federada para a autenticação de nuvem usando uma abordagem em etapas.

Afastar-se da autenticação federada tem implicações. Por exemplo, se você tiver qualquer um dos seguintes:
    
-  um servidor MFA local 
-  está usando cartões inteligentes para autenticação 
-  outros recursos somente de Federação

Esses recursos devem ser levados em consideração antes de mudar para a autenticação na nuvem.  Antes de experimentar esse recurso, sugerimos que você examine nosso guia sobre como escolher o método de autenticação certo. Consulte [esta tabela](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) para obter mais detalhes.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Pré-requisitos

-   Você tem um locatário do Azure AD com domínios federados.

-   Você decidiu mover para sincronização de hash de senha + SSO contínuo **(opção A)** ou autenticação de passagem + SSO contínuo **(opção B).** Embora o SSO contínuo seja opcional, é recomendável habilitar o SSO contínuo para obter uma experiência de entrada silenciosa para usuários que usam computadores ingressados no domínio de dentro da rede corporativa.

-   Você configurou toda a identidade visual do locatário apropriada e as políticas de acesso condicional necessárias para os usuários que estão sendo migrados para a autenticação na nuvem.

-   Se você planeja usar a autenticação multifator do Azure, recomendamos o uso [do registro convergido para redefinição de senha de autoatendimento (SSPR) e do Azure MFA](../authentication/concept-registration-mfa-sspr-combined.md) para que os usuários registrem seus métodos de autenticação uma vez.

-   Para usar esse recurso, você precisa ser administrador global em seu locatário.

-   Para habilitar o SSO contínuo em uma floresta específica do AD, você precisa ser administrador de domínio.

## <a name="supported-scenarios"></a>Cenários suportados

Esses cenários têm suporte para a distribuição em etapas:

- Esse recurso funciona apenas para usuários provisionados para o Azure AD usando Azure AD Connect e não é aplicável a usuários somente de nuvem.

- Esse recurso funciona apenas para o tráfego de entrada do usuário em navegadores e clientes de autenticação modernos. Aplicativos ou serviços de nuvem usando a autenticação herdada voltarão para os fluxos de autenticação federada. (Exemplo: Exchange Online com autenticação moderna desativada ou o Outlook 2010, que não oferece suporte à autenticação moderna.)

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Esses cenários não têm suporte para a distribuição em etapas:

- Determinados aplicativos enviam o parâmetro de consulta "domínio\_dica" ao Azure AD durante a autenticação. Esses fluxos continuarão e os usuários habilitados para distribuição em etapas continuarão a usar a Federação para autenticação.

<!-- -->

- O administrador pode distribuir a autenticação de nuvem usando grupos de segurança. (Os grupos de segurança de nuvem são recomendados para evitar a latência de sincronização ao usar grupos de segurança do AD local.)

    - Você pode usar um **máximo de 10 grupos por recurso**; ou seja, para cada uma das autenticações de hash de senha/autenticação de passagem/SSO contínuo.

    - **Não há suporte para**grupos aninhados. Esse é o escopo para visualização pública também.

    - **Não há suporte** para grupos dinâmicos para distribuição em etapas.

    - Os objetos de contato dentro do grupo bloquearão o formulário de grupo que está sendo adicionado.

- A transferência final do federado para a autenticação na nuvem ainda precisa ocorrer usando o Azure AD Connect ou o PowerShell. Esse recurso não alterna os domínios de Federação para gerenciado.

- Quando você adiciona um grupo de segurança pela primeira vez para distribuição em etapas, ele é limitado a 200 usuários para evitar que a UX atinja o tempo limite. Depois que o grupo for adicionado na UX, você poderá adicionar mais usuários diretamente ao grupo, conforme necessário.

## <a name="get-started-with-staged-rollout"></a>Introdução à distribuição em etapas

Se você quiser testar a entrada de sincronização de hash de senha (PHS) usando a distribuição em etapas, conclua o trabalho abaixo para habilitar a distribuição em etapas de sincronização de hash de senha.

Para obter mais informações sobre os cmdlets do PowerShell usados, consulte [visualização do AzureAD 2,0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>Pré-trabalho para sincronização de hash de senha

1. Habilite a sincronização de hash de senha da página [recursos opcionais](how-to-connect-install-custom.md#optional-features) no Azure ad Connect. 

   ![Captura de tela da página recursos opcionais no Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Certifique-se de que um ciclo de sincronização de hash de senha completo tenha sido executado para que todos os hashes de senha dos usuários tenham sido sincronizados com o Azure AD. Você pode usar o diagnóstico do PowerShell [aqui](tshoot-connect-password-hash-synchronization.md) para verificar o status da sincronização de hash de senha.

   ![Captura de tela do log de solução de problemas do AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se você quiser testar a entrada de PTA (passagem de autenticação) usando a distribuição em etapas, conclua o trabalho anterior para habilitar o PTA para distribuição em etapas.

## <a name="pre-work-for-pass-through-authentication"></a>Pré-trabalho para autenticação de passagem

1. Identifique um servidor executando o Windows Server 2012 R2 ou posterior no qual você deseja que o agente de autenticação de passagem seja executado (não**escolha o servidor de Azure ad Connect**). Certifique-se de que o servidor está ingressado no domínio, pode autenticar usuários selecionados com Active Directory e pode se comunicar com o Azure AD nas portas/URLs de saída (consulte [pré-requisitos](how-to-connect-sso-quick-start.md)detalhados).

1. [Baixe](https://aka.ms/getauthagent) & instalar o agente de autenticação do Microsoft Azure ad Connect no servidor. 

1. Para habilitar a [alta disponibilidade](how-to-connect-sso-quick-start.md), instale agentes de autenticação adicionais em outros servidores.

1. Verifique se você definiu [as configurações de bloqueio inteligente](../authentication/howto-password-smart-lockout.md) adequadamente. Isso é para garantir que as contas de Active Directory locais dos usuários não sejam bloqueadas por atores inválidos.

É recomendável habilitar o SSO contínuo, independentemente do método de entrada (PHS ou PTA) selecionado para distribuição em etapas. Conclua o trabalho anterior para habilitar o SSO contínuo para distribuição em etapas.

## <a name="pre-work-for-seamless-sso"></a>Pré-trabalho para SSO contínuo

Habilite o SSO contínuo nas florestas do AD usando o PowerShell. Se você tiver mais de uma floresta do AD, habilite o mesmo para cada floresta individualmente. O SSO contínuo só será disparado para usuários selecionados para distribuição em etapas e não afetará sua configuração de Federação existente.

**Resumo das etapas**

1. Primeiro, faça logon no Azure AD Connect Server.

2. Navegue até a pasta% ProgramFiles%\\Microsoft Azure Active Directory Connect.

3. Importe o módulo do PowerShell do SSO contínuo usando este comando: `Import-Module .\\AzureADSSO.psd1`.

4. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer uma caixa de diálogo na qual você pode inserir as credenciais de administrador global do seu locatário.

5. Chame `Get-AzureADSSOStatus \| ConvertFrom-Json`. Esse comando fornece a lista de florestas do AD (consulte a lista \"domínios\") em que esse recurso foi habilitado. Por padrão, ele é definido como false no nível do locatário.

   > **Exemplo:** 
   > ![exemplo da saída do Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Chame `\$creds = Get-Credential`. Quando solicitado, insira as credenciais de administrador de domínio para a floresta do AD pretendida.

7. Chame `Enable-AzureADSSOForest -OnPremCredentials \$creds`. Esse comando cria a conta de computador AZUREADSSOACC do controlador de domínio local para essa floresta de Active Directory específica que é necessária para o SSO contínuo.

8. O SSO contínuo exige que as URLs estejam na zona da intranet. Consulte o guia de [início rápido de logon único contínuo](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) para implantar essas URLs usando as políticas de grupo.

9.  Você também pode baixar nossos [planos de implantação](https://aka.ms/SeamlessSSODPDownload) para o SSO contínuo para obter uma explicação completa.

## <a name="enable-staged-rollout"></a>Habilitar distribuição em etapas

Use as etapas a seguir para distribuir um recurso específico (autenticação de passagem/sincronização de hash de senha/SSO contínuo) para um conjunto selecionado de usuários em um grupo:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Habilitar a distribuição em etapas de um recurso específico em seu locatário

Você pode distribuir uma dessas opções

-   Sincronização de hash de senha + SSO contínuo **(opção A)**

-   Autenticação de passagem + SSO contínuo **(opção B)**

-   Sincronização de hash de senha + autenticação de passagem +-de SSO contínuo **\>** ***sem suporte***

Conclua estas etapas:

1. Faça logon no portal do AD do Azure usando a URL abaixo para acessar a UX de visualização.

   > <https://aka.ms/stagedrolloutux>

2. Clique em habilitar a distribuição em etapas para entrada do usuário gerenciado (versão prévia)

   *Por exemplo:* (**opção B**) se você quiser habilitar a sincronização de hash de senha e o SSO contínuo, deslize os recursos sincronização de hash de senha e logon único contínuo para **' ativado '** , conforme mostrado abaixo.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Adicione os respectivos grupos ao recurso para habilitar a autenticação de passagem e o logon único contínuo. Verifique se os grupos de segurança têm, no máximo, 200 membros inicialmente para evitar o tempo limite de UX.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Os membros de um grupo são habilitados automaticamente para distribuição em etapas. Não há suporte para grupos aninhados e dinâmicos para distribuição em etapas.

## <a name="auditing"></a>Auditoria

Habilitamos eventos de auditoria para as diferentes ações que executamos para distribuição em etapas.

- Evento de auditoria quando você habilita a distribuição de preparo para sincronização de hash de senha/autenticação de passagem/SSO contínuo.

  >[!NOTE]
  >Evento de auditoria que é registrado quando o SeamlessSSO é **ativado usando StagedRollout** .

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Evento de auditoria quando um grupo é adicionado à sincronização de hash de senha/autenticação de passagem/SSO contínuo.

  >[!NOTE]
  >Evento de auditoria registrado quando um grupo é adicionado à sincronização de hash de senha para distribuição em etapas

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Evento de auditoria quando um usuário que foi adicionado ao grupo é habilitado para distribuição em etapas

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validação

Para testar a entrada com a sincronização de hash de senha ou a autenticação de passagem (entrada de nome de usuário/senha):

1. Navegue até <https://myapps.microsoft.com> em uma sessão privada do navegador da extranet e insira o UserPrincipalName (UPN) da conta de usuário selecionada para distribuição em etapas.

1. Se o usuário tiver sido direcionado para distribuição em etapas, o usuário não será redirecionado para sua página de logon federado e, em vez disso, será solicitado a entrar na página de entrada com a marca do locatário do Azure AD.

1. Verifique se a entrada é exibida com êxito no relatório de [atividade de entrada do Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando com o userPrincipalName.

Para testar a entrada com o SSO contínuo:

1. Navegue até <https://myapps.microsoft.com>/em uma sessão privada do navegador da intranet e insira o UserPrincipalName (UPN) da conta de usuário selecionada para distribuição em etapas.

1. Se o usuário tiver sido direcionado para a distribuição em etapas de SSO contínuo, o usuário verá uma "tentando conectá-lo..." mensagem antes de ser silenciosamente conectado.

1. Verifique se a entrada é exibida com êxito no relatório de [atividade de entrada do Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando com o userPrincipalName.

Para verificar se os logons do usuário ainda estão ocorrendo em provedores de Federação:

Veja como você pode acompanhar as entradas do usuário que ainda estão ocorrendo em AD FS para os usuários de distribuição em etapas selecionadas usando [essas instruções](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consulte a documentação do fornecedor sobre como verificar isso em provedores de Federação de terceiros.

## <a name="roll-back"></a>Reverter

### <a name="remove-a-user-from-staged-rollout"></a>Remover um usuário da distribuição em etapas

1.  A remoção do usuário do grupo desabilita a distribuição em etapas para o usuário.

2.  Se você quiser desabilitar o recurso de distribuição em etapas, deslize o recurso de volta para o estado **"desativado"** para desligar a distribuição em etapas.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

-   **P: um cliente pode usar esse recurso em produção?**

-   R: Sim, esse recurso pode ser usado em seu locatário de produção, mas é recomendável que você primeiro Experimente esse recurso em seu locatário de teste.

-   **P: esse recurso pode ser usado para manter uma "coexistência" permanente, em que alguns usuários usam a autenticação federada e outras autenticações de nuvem?**

-   R: não, esse recurso foi projetado para migrar da autenticação federada para a nuvem em estágios e, em seguida, para, eventualmente, para a autenticação na nuvem. Não recomendamos um estado misto permanente, pois isso poderia levar a fluxos de autenticação inesperados.

-   **P: podemos usar o PowerShell para executar a distribuição em etapas?**

-   R: Sim, encontre a documentação para usar o PowerShell para executar a distribuição em etapas aqui.

## <a name="next-steps"></a>Próximos Passos
- [Versão prévia do AzureAD 2,0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
