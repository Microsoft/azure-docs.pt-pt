---
title: 'Azure AD Connect: autenticação na nuvem por meio de distribuição em etapas | Microsoft Docs'
description: Este artigo explica como migrar da autenticação federada para a autenticação de nuvem usando uma distribuição em etapas.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915221"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Migrar para a autenticação na nuvem usando a distribuição em etapas (versão prévia)

Usando uma abordagem de distribuição em etapas, você pode migrar da autenticação federada para a autenticação na nuvem. Este artigo discute como fazer a mudança. No entanto, antes de começar a distribuição em etapas, você deve considerar as implicações se uma ou mais das seguintes condições forem verdadeiras:
    
-  No momento, você está usando um servidor de autenticação multifator local. 
-  Você está usando cartões inteligentes para autenticação. 
-  O servidor atual oferece determinados recursos somente de Federação.

Antes de experimentar esse recurso, sugerimos que você examine nosso guia sobre como escolher o método de autenticação certo. Para obter mais informações, consulte a tabela "métodos de comparação" em [escolher o método de autenticação certo para sua solução de identidade híbrida Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods).

Para obter uma visão geral do recurso, veja "Azure Active Directory: o que é distribuição em etapas?" monitor

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Pré-requisitos

-   Você tem um locatário do Azure Active Directory (Azure AD) com domínios federados.

-   Você decidiu mover para uma das duas opções:
    - **Opção A** - *sincronização de hash de senha (sincronização)*  + *logon único contínuo (SSO)*
    - **Opção B** - *autenticação de passagem* + *SSO contínuo*
    
    Embora o *SSO contínuo* seja opcional, é recomendável permitir que ele obtenha uma experiência de entrada silenciosa para usuários que estão executando computadores ingressados no domínio de dentro de uma rede corporativa.

-   Você configurou todas as políticas apropriadas de identidade visual do locatário e de acesso condicional necessárias para os usuários que estão sendo migrados para a autenticação na nuvem.

-   Se você planeja usar a autenticação multifator do Azure, é recomendável usar o [registro convergido para redefinição de senha de autoatendimento (SSPR) e autenticação multifator](../authentication/concept-registration-mfa-sspr-combined.md) para que os usuários registrem seus métodos de autenticação uma vez.

-   Para usar o recurso de distribuição em etapas, você precisa ser um administrador global em seu locatário.

-   Para habilitar o *SSO contínuo* em uma floresta de Active Directory específica, você precisa ser um administrador de domínio.

## <a name="supported-scenarios"></a>Cenários suportados

Os cenários a seguir têm suporte para distribuição em etapas. O recurso funciona apenas para:

- Usuários que são provisionados no Azure AD usando Azure AD Connect. Ele não se aplica a usuários somente de nuvem.

- Tráfego de entrada do usuário em navegadores e clientes de *autenticação modernos* . Aplicativos ou serviços de nuvem que usam autenticação herdada voltarão para fluxos de autenticação federada. Um exemplo pode ser o Exchange Online com autenticação moderna desativada ou o Outlook 2010, que não oferece suporte à autenticação moderna.

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os cenários a seguir não têm suporte para a distribuição em etapas:

- Determinados aplicativos enviam o parâmetro de consulta "domain_hint" para o Azure AD durante a autenticação. Esses fluxos continuarão e os usuários que estiverem habilitados para distribuição em etapas continuarão a usar a Federação para autenticação.

<!-- -->

- Os administradores podem distribuir a autenticação de nuvem usando grupos de segurança. Para evitar a latência de sincronização quando você estiver usando grupos de segurança Active Directory locais, recomendamos que você use grupos de segurança de nuvem. As seguintes condições se aplicam:

    - Você pode usar um máximo de 10 grupos por recurso. Ou seja, você pode usar 10 grupos cada para *sincronização de hash de senha*, *autenticação de passagem*e *SSO contínuo*.
    - *Não há suporte para*grupos aninhados. Esse escopo também se aplica à visualização pública.
    - *Não há suporte* para grupos dinâmicos para distribuição em etapas.
    - Os objetos de contato dentro do grupo bloquearão a adição do grupo.

- Você ainda precisa fazer a transferência final do federado para a autenticação na nuvem usando Azure AD Connect ou o PowerShell. A distribuição em etapas não alterna domínios de federados para gerenciado.

- Ao adicionar um grupo de segurança pela primeira vez para distribuição em etapas, você está limitado a 200 usuários para evitar um tempo limite de UX. Depois de adicionar o grupo, você pode adicionar mais usuários diretamente a ele, conforme necessário.

## <a name="get-started-with-staged-rollout"></a>Introdução à distribuição em etapas

Para testar a entrada de *sincronização de hash de senha* usando a distribuição em etapas, siga as instruções de pré-trabalho na próxima seção.

Para obter informações sobre quais cmdlets do PowerShell usar, consulte [visualização do Azure AD 2,0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Pré-trabalho para sincronização de hash de senha

1. Habilite a *sincronização de hash de senha* da página [recursos opcionais](how-to-connect-install-custom.md#optional-features) no Azure ad Connect. 

   ![Captura de tela da página "recursos opcionais" no Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Verifique se um ciclo de *sincronização de hash de senha* completo foi executado para que todos os hashes de senha dos usuários tenham sido sincronizados com o Azure AD. Para verificar o status da *sincronização de hash de senha*, você pode usar o diagnóstico do PowerShell em [solucionar problemas de sincronização de hash de senha com Azure ad Connect sincronização](tshoot-connect-password-hash-synchronization.md).

   ![Captura de tela do log de solução de problemas do AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se você quiser testar a entrada de *autenticação de passagem* usando a distribuição em etapas, habilite-a seguindo as instruções de trabalho na próxima seção.

## <a name="pre-work-for-pass-through-authentication"></a>Pré-trabalho para autenticação de passagem

1. Identifique um servidor que esteja executando o Windows Server 2012 R2 ou posterior no qual você deseja que o agente de *autenticação de passagem* seja executado. 

   *Não* escolha o servidor de Azure ad Connect. Certifique-se de que o servidor está ingressado no domínio, pode autenticar usuários selecionados com Active Directory e pode se comunicar com o Azure AD em URLs e portas de saída. Para obter mais informações, consulte a seção "etapa 1: verificar os pré-requisitos" do [início rápido: logon único contínuo do Azure ad](how-to-connect-sso-quick-start.md).

1. [Baixe o agente de autenticação Azure ad Connect](https://aka.ms/getauthagent)e instale-o no servidor. 

1. Para habilitar a [alta disponibilidade](how-to-connect-sso-quick-start.md), instale agentes de autenticação adicionais em outros servidores.

1. Verifique se você definiu [as configurações de bloqueio inteligente](../authentication/howto-password-smart-lockout.md) adequadamente. Isso ajuda a garantir que as contas de Active Directory locais dos usuários não sejam bloqueadas por atores ruins.

É recomendável habilitar o *SSO contínuo* , independentemente do método de entrada (*sincronização de hash de senha* ou autenticação de *passagem*) selecionado para distribuição em etapas. Para habilitar o *SSO contínuo*, siga as instruções de pré-trabalho na próxima seção.

## <a name="pre-work-for-seamless-sso"></a>Pré-trabalho para SSO contínuo

Habilite o *SSO contínuo* nas florestas de Active Directory usando o PowerShell. Se você tiver mais de uma floresta Active Directory, habilite-a para cada floresta individualmente. O  *SSO contínuo* é disparado somente para usuários selecionados para distribuição em etapas. Ele não afeta sua configuração de Federação existente.

Habilite o *SSO contínuo* fazendo o seguinte:

1. Entre no Azure AD Connect Server.

2. Vá para a pasta *% ProgramFiles%\\Microsoft Azure Active Directory Connect* .

3. Importe o módulo do PowerShell do *SSO contínuo* executando o seguinte comando: 

   `Import-Module .\AzureADSSO.psd1`

4. Execute o PowerShell como um administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando abre um painel onde você pode inserir as credenciais de administrador global do seu locatário.

5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando exibe uma lista de florestas de Active Directory (consulte a lista "domínios") na qual esse recurso foi habilitado. Por padrão, ele é definido como false no nível do locatário.

   ![Exemplo de saída do Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Chame `$creds = Get-Credential`. No prompt, insira as credenciais de administrador de domínio para a floresta Active Directory pretendida.

7. Chame `Enable-AzureADSSOForest -OnPremCredentials $creds`. Esse comando cria a conta de computador AZUREADSSOACC do controlador de domínio local para a floresta Active Directory necessária para o *SSO contínuo*.

8. O *SSO contínuo* exige que as URLs estejam na zona da intranet. Para implantar essas URLs usando políticas de grupo, consulte [início rápido: logon único contínuo do Azure ad](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Para obter uma explicação completa, você também pode baixar nossos [planos de implantação](https://aka.ms/SeamlessSSODPDownload) para o *SSO contínuo*.

## <a name="enable-staged-rollout"></a>Habilitar distribuição em etapas

Para distribuir um recurso específico (*autenticação de passagem*, sincronização de *hash de senha*ou *SSO contínuo*) para um conjunto selecionado de usuários em um grupo, siga as instruções nas próximas seções.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Habilitar uma distribuição em etapas de um recurso específico em seu locatário

Você pode distribuir uma destas opções:

- **Opção A** - *sincronização de hash de senha* + *SSO contínuo*
- **Opção B** - *autenticação de passagem* + *SSO contínuo*
- **Sem suporte** - *sincronização de hash de senha* + *autenticação de passagem* + *SSO contínuo*

Faça o seguinte:

1. Para acessar o UX de visualização, entre no [portal do AD do Azure](https://aka.ms/stagedrolloutux).

2. Selecione o link **habilitar a distribuição em etapas para entrada do usuário gerenciado (versão prévia)** .

   Por exemplo, se você quiser habilitar a *opção a*, deslize os controles **sincronização de hash de senha** e **logon único contínuo** para **ativado**, conforme mostrado nas imagens a seguir.

   ![A página Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![A página "habilitar recursos de distribuição em etapas (visualização)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Adicione os grupos ao recurso para habilitar a *autenticação de passagem* e o *SSO contínuo*. Para evitar um tempo limite de UX, verifique se os grupos de segurança contêm no máximo 200 membros inicialmente.

   ![A página "gerenciar grupos para sincronização de hash de senha (visualização)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Os membros de um grupo são habilitados automaticamente para distribuição em etapas. Não há suporte para grupos aninhados e dinâmicos para distribuição em etapas.

## <a name="auditing"></a>Auditoria

Habilitamos eventos de auditoria para as várias ações que executamos para a distribuição em etapas:

- Evento de auditoria quando você habilita uma distribuição em etapas para *sincronização de hash de senha*, autenticação de *passagem*ou *SSO contínuo*.

  >[!NOTE]
  >Um evento de auditoria é registrado quando o *SSO contínuo* é ativado usando a distribuição em etapas.

  ![O painel "criar política de distribuição para recurso" – guia atividade](./media/how-to-connect-staged-rollout/sr7.png)

  ![O painel "criar política de distribuição para recurso" – guia Propriedades modificadas](./media/how-to-connect-staged-rollout/sr8.png)

- Evento de auditoria quando um grupo é adicionado à *sincronização de hash de senha*, à autenticação de *passagem*ou ao *SSO contínuo*.

  >[!NOTE]
  >Um evento de auditoria é registrado quando um grupo é adicionado à *sincronização de hash de senha* para distribuição em etapas.

  ![O painel "adicionar um grupo ao recurso de distribuição" – guia atividade](./media/how-to-connect-staged-rollout/sr9.png)

  ![O painel "adicionar um grupo ao desenvolvimento de recursos" – guia Propriedades modificadas](./media/how-to-connect-staged-rollout/sr10.png)

- Evento de auditoria quando um usuário que foi adicionado ao grupo é habilitado para distribuição em etapas.

  ![O painel "Adicionar usuário ao recurso de distribuição" – guia atividade](media/how-to-connect-staged-rollout/sr11.png)

  ![O painel "Adicionar usuário ao recurso de distribuição de recursos" – guia destino (s)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validação

Para testar a entrada com a *sincronização de hash de senha* ou *a autenticação de passagem* (logon de usuário e senha), faça o seguinte:

1. Na extranet, vá para a [página de aplicativos](https://myapps.microsoft.com) em uma sessão de navegador privada e, em seguida, insira o USERPRINCIPALNAME (UPN) da conta de usuário selecionada para distribuição em etapas.

   Os usuários que foram direcionados para a distribuição em etapas não são redirecionados para sua página de logon federada. Em vez disso, eles são solicitados a entrar na página de entrada com identidade visual do locatário do Azure AD.

1. Verifique se a entrada é exibida com êxito no relatório de [atividade de entrada do Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando com o userPrincipalName.

Para testar a entrada com o *SSO contínuo*:

1. Na intranet, vá para a [página aplicativos](https://myapps.microsoft.com) em uma sessão privada do navegador e insira o USERPRINCIPALNAME (UPN) da conta de usuário selecionada para distribuição em etapas.

   Os usuários que foram direcionados para a distribuição em etapas de *SSO contínuo* são apresentados com uma "tentativa de conectá-lo..." antes que eles sejam conectados silenciosamente.

1. Verifique se a entrada é exibida com êxito no relatório de [atividade de entrada do Azure ad](../reports-monitoring/concept-sign-ins.md) filtrando com o userPrincipalName.

   Para acompanhar as entradas de usuário que ainda ocorrem em Serviços de Federação do Active Directory (AD FS) (AD FS) para usuários de distribuição em etapas selecionadas, siga as instruções em [AD FS solução de problemas: eventos e log](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consulte a documentação do fornecedor sobre como verificar isso em provedores de Federação de terceiros.

## <a name="remove-a-user-from-staged-rollout"></a>Remover um usuário da distribuição em etapas

A remoção de um usuário do grupo desabilita a distribuição em etapas para esse usuário. Para desabilitar o recurso de distribuição em etapas, deslize o controle de volta para **desativado**.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: posso usar esse recurso em produção?**

R: Sim, você pode usar esse recurso em seu locatário de produção, mas é recomendável primeiro experimentá-lo em seu locatário de teste.

**P: esse recurso pode ser usado para manter uma "coexistência" permanente, em que alguns usuários usam a autenticação federada e outros usam a autenticação de nuvem?**

R: não, esse recurso foi projetado para migrar da autenticação federada para a nuvem em estágios e, em seguida, para, eventualmente, para a autenticação na nuvem. Não recomendamos o uso de um estado misto permanente, pois essa abordagem pode levar a fluxos de autenticação inesperados.

**P: posso usar o PowerShell para executar a distribuição em etapas?**

R: Sim. Para saber como usar o PowerShell para executar a distribuição em etapas, consulte [visualização do Azure ad](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Passos seguintes
- [Versão prévia do Azure AD 2,0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
