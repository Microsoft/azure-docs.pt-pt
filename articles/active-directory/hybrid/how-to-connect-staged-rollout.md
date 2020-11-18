---
title: 'Azure AD Connect: Autenticação em nuvem através do lançamento encenado Microsoft Docs'
description: Este artigo explica como migrar da autenticação federada, para a autenticação em nuvem, utilizando um lançamento encenado.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a79ec9a54ae597bc7e9795029dc4292c2c82345
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836398"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>Migrar para a autenticação em nuvem usando o lançamento encenado (pré-visualização)

O lançamento encenado permite testar seletivamente grupos de utilizadores com capacidades de autenticação em nuvem como Azure AD Multi-Factor Authentication (MFA), Acesso Condicional, Proteção de Identidade para credenciais vazadas, Governação de Identidade, entre outros, antes de cortar os seus domínios.  Este artigo discute como fazer a troca. Antes de iniciar o lançamento encenado, no entanto, deve considerar as implicações se uma ou mais das seguintes condições forem verdadeiras:
    
-  Está atualmente a utilizar um servidor de autenticação multi-factor no local. 
-  Estás a usar cartões inteligentes para autenticação. 
-  O seu servidor atual oferece certas funcionalidades apenas da federação.

Antes de experimentar esta funcionalidade, sugerimos que reveja o nosso guia sobre a escolha do método de autenticação certo. Para obter mais informações, consulte a tabela "Comparar métodos" na [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](./choose-ad-authn.md#comparing-methods).

Para uma visão geral da funcionalidade, veja este "Azure Ative Directory: What is staged rollout?" vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Pré-requisitos

-   Você tem um inquilino Azure Ative (Azure AD) com domínios federados.

-   Decidiu mudar-se para uma das duas opções:
    - **Opção A**  -  *sincronização de hash de palavra-palavra (sincronização)*  +  *único sinal (SSO)* sem emenda .  Para mais informações, consulte [o que é sincronização de haxixe de palavra-passe](whatis-phs.md) e O que é [SSO sem costura](how-to-connect-sso.md)
    - **Opção B**  -  autenticação pass-through *pass-through authentication*  +  *SSO sem emenda.*  Para mais informações, consulte [o que é a autenticação pass-through](how-to-connect-pta.md)  
    
    Embora *o SSO sem emenda* seja opcional, recomendamos que ele obtenha uma experiência de entrada silenciosa para utilizadores que estão executando máquinas de domínio a partir de dentro de uma rede corporativa.

-   Configurar todas as políticas adequadas de marca de inquilino e acesso condicional que necessita para os utilizadores que estão a ser migrados para a autenticação em nuvem.

-   Se pretender utilizar a autenticação multi-factor Azure AD, recomendamos que utilize [o registo combinado para reiniciar a palavra-passe de autosserviço (SSPR) e a Autenticação Multi-Factor](../authentication/concept-registration-mfa-sspr-combined.md) para que os seus utilizadores registem os seus métodos de autenticação uma vez. Nota: ao utilizar o SSPR para redefinir a palavra-passe ou alterar a palavra-passe utilizando a página MyProfile durante o lançamento encenado, o Azure AD Connect precisa de sincronizar o novo hash de palavra-passe que pode demorar até 2 minutos após o reset.

-   Para utilizar a funcionalidade de lançamento encenada, tem de ser um administrador global no seu inquilino.

-   Para ativar *sSO sem costura* numa floresta de Diretório Ativo específica, você precisa ser um administrador de domínio.

-  Se estiver a implementar a ad AD Híbrida ou AZure AD, tem de fazer o upgrade para a atualização do Windows 10 1903.


## <a name="supported-scenarios"></a>Cenários suportados

Os seguintes cenários são suportados para o lançamento encenado. A funcionalidade funciona apenas para:

- Utilizadores que são a provisionados ao Azure AD utilizando o Azure AD Connect. Não se aplica a utilizadores apenas na nuvem.

- Tráfego de login do utilizador nos navegadores e clientes *de autenticação moderna.* As aplicações ou serviços em nuvem que utilizem a autenticação antiga vão recair sobre os fluxos de autenticação federados. Um exemplo pode ser Exchange online com a autenticação moderna desligada, ou Outlook 2010, que não suporta a autenticação moderna.
- O tamanho do grupo está atualmente limitado a 50.000 utilizadores.  Se tiver grupos maiores, então 50.000 utilizadores, recomenda-se dividir este grupo em vários grupos para o lançamento encenado.

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os seguintes cenários não são suportados para o lançamento encenado:

- Algumas aplicações enviam o parâmetro de consulta "domain_hint" para Azure AD durante a autenticação. Estes fluxos continuarão, e os utilizadores que estejam habilitados para o lançamento encenado continuarão a utilizar a federação para a autenticação.

<!-- -->

- Os administradores podem lançar a autenticação em nuvem utilizando grupos de segurança. Para evitar a latência sincronizada quando estiver a utilizar grupos de segurança do Ative Directory no local, recomendamos que utilize grupos de segurança na nuvem. Aplicam-se as seguintes condições:

    - Pode utilizar um máximo de 10 grupos por recurso. Ou seja, pode utilizar 10 grupos cada para *sincronização de haxixe de palavra-passe,* *autenticação de passagem* e *SSO sem costura.*
    - Os grupos aninhados não são *apoiados.* Este âmbito aplica-se também à visualização pública.
    - Os grupos dinâmicos não são *apoiados* para o lançamento encenado.
    - Os objetos de contacto no interior do grupo impedirão a adição do grupo.

- Ainda precisa de fazer o corte final de federado para autenticação em nuvem utilizando O AD Connect ou PowerShell. O lançamento encenado não muda os domínios de federados para geridos.  Para obter mais informações sobre o corte de domínio, consulte [Migrar da federação para a sincronização de hash de palavra-passe](plan-migrate-adfs-password-hash-sync.md) e [migrar da federação para a autenticação pass-through](plan-migrate-adfs-pass-through-authentication.md)



- Quando adicionas um grupo de segurança para lançamento encenado, estás limitado a 200 utilizadores para evitar uma saída sem acordo com o UX. Depois de ter adicionado o grupo, pode adicionar mais utilizadores diretamente ao mesmo, conforme necessário.

- Enquanto os utilizadores estão em Lançamento Encenado, quando o EnforceCloudPasswordPolicyForPasswordSyncedUsers está ativado, a política de validade da palavra-passe está definida para 90 dias sem opção de personalizar. 


## <a name="get-started-with-staged-rollout"></a>Começar com lançamento encenado

Para testar o *sômin de sincronização de hash da palavra-passe* utilizando o lançamento encenado, siga as instruções de pré-trabalho na secção seguinte.

Para obter informações sobre quais os cmdlets PowerShell a utilizar, consulte [a pré-visualização Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Pré-trabalhar para sincronização de haxixe de palavra-passe

1. Ativar a sincronização de hash de *palavra-passe* a partir da página [de funcionalidades opcionais](how-to-connect-install-custom.md#optional-features) no Azure AD Connect. 

   ![Screenshot da página "Funcionalidades Opcionais" no Azure Ative Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Certifique-se de que um ciclo completo *de sincronização de haxixe de palavra-passe* foi executado de modo a que todas as hashes de palavra-passe dos utilizadores tenham sido sincronizadas com a Azure AD. Para verificar o estado do *hash de palavra-passe,* pode utilizar os diagnósticos PowerShell na [sincronização de hash de palavra-passe de resolução de problemas com a sincronização Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Screenshot do registo de resolução de problemas AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se pretender testar a entrada *de autenticação* transitada através da utilização de uma implementação encenada, ative-a seguindo as instruções de pré-trabalho na secção seguinte.

## <a name="pre-work-for-pass-through-authentication"></a>Pré-trabalho para autenticação pass-through

1. Identifique um servidor que esteja a executar o Windows Server 2012 R2 ou mais tarde onde pretende que o agente *de autenticação pass-through* seja executado. 

   *Não* escolha o servidor Azure AD Connect. Certifique-se de que o servidor está ligado ao domínio, pode autenticar utilizadores selecionados com Ative Directory e comunicar com a Azure AD em portas e URLs de saída. Para obter mais informações, consulte a secção "Passo 1: Verifique os pré-requisitos" do [Quickstart: Azure AD sem costura única.](how-to-connect-sso-quick-start.md)

1. [Descarregue o agente de autenticação AZure AD Connect](https://aka.ms/getauthagent)e instale-o no servidor. 

1. Para permitir [uma elevada disponibilidade,](how-to-connect-sso-quick-start.md)instale agentes de autenticação adicionais noutros servidores.

1. Certifique-se de que configura as [definições de Bloqueio Inteligente](../authentication/howto-password-smart-lockout.md) adequadamente. Ao fazê-lo, ajuda a garantir que as contas de Ative Directory dos seus utilizadores não são bloqueadas por maus atores.

Recomendamos que se permita um *SSO sem emenda,* independentemente do método de entrada de *dados (sincronização de haxixe* *de palavra-passe ou autenticação pass-through)* que selecione para o lançamento encenado. Para *ativar sSO sem costura,* siga as instruções de pré-trabalho na secção seguinte.

## <a name="pre-work-for-seamless-sso"></a>Pré-trabalho para SSO sem costura

Ativar *sSO sem costura* nas florestas do Ative Directory utilizando o PowerShell. Se tiver mais do que uma floresta de Diretório Ativo, ative-a para cada floresta individualmente. *O SSO sem emenda* é acionado apenas para utilizadores selecionados para o lançamento encenado. Não afeta a configuração da federação.

Permitir *um SSO sem costura* fazendo o seguinte:

1. Inscreva-se no Azure AD Connect Server.

2. Vá para a pasta *%programfiles% \\ Microsoft Azure Ative Directory Connect.*

3. Importar o módulo *SSO* PowerShell sem costura executando o seguinte comando: 

   `Import-Module .\AzureADSSO.psd1`

4. Execute o PowerShell como um administrador. Em PowerShell, `New-AzureADSSOAuthenticationContext` ligue. Este comando abre um painel onde pode introduzir as credenciais globais de administrador do seu inquilino.

5. `Get-AzureADSSOStatus | ConvertFrom-Json`Ligue. Este comando apresenta uma lista de florestas de Diretório Ativo (ver a lista de "Domínios") nas quais esta funcionalidade foi ativada. Por defeito, é definido como falso ao nível do inquilino.

   ![Exemplo da saída Do Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. `$creds = Get-Credential`Ligue. A pedido, insira as credenciais de administrador de domínio para a floresta de Diretório Ativo pretendida.

7. `Enable-AzureADSSOForest -OnPremCredentials $creds`Ligue. Este comando cria a conta de computador AZUREADSSOACC a partir do controlador de domínio no local para a floresta de Diretório Ativo que é necessária para *sSO sem costura*.

8. *O SSO sem emenda* requer que os URLs estejam na zona intranet. Para implementar esses URLs utilizando políticas de grupo, consulte [Quickstart: Azure AD sem costura única sign-on](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Para uma passagem completa, você também pode baixar [nossos planos de implementação](https://aka.ms/SeamlessSSODPDownload) para *SSO sem costura.*

## <a name="enable-staged-rollout"></a>Ativar o lançamento encenado

Para lançar uma função específica (*autenticação pass-through*, *sincronização de haxixe de palavras-passe*, ou *SSO sem costura*) para um conjunto selecionado de utilizadores em grupo, siga as instruções nas secções seguintes.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Ativar um lançamento encenado de uma característica específica no seu inquilino

Pode lançar uma destas opções:

- **Opção A**  -  *sincronização de haxixe*  +  de palavra-passe *SSO sem costura*
- **Opção B**  -  autenticação pass-through *pass-through authentication*  +  *SSO sem costura*
- **Não suportado**  -  *sincronização de haxixe*  +  de palavra-passe autenticação pass-through *pass-through authentication*  +  *SSO sem costura*

Faça o seguinte:

1. Para aceder ao UX de pré-visualização, inscreva-se no [portal AD AZure](https://aka.ms/stagedrolloutux).

2. Selecione o lançamento encenado de Enable para o link **de entrada de utilizador gerido (Pré-visualização).**

   Por exemplo, se pretender ativar a *Opção A*, deslize o **Sync de Hash De palavra-passe** e os controlos **de entrada única sem emenda** para **On**, como mostram as seguintes imagens.

   ![A página Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![A página "Ativar funcionalidades de lançamento encenadas (Pré-visualização)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Adicione os grupos à funcionalidade para permitir a *autenticação pass-through* e *sSO sem costura*. Para evitar uma rização ux, certifique-se de que os grupos de segurança não contêm mais de 200 membros inicialmente.

   ![A página "Gerir grupos para password Hash Sync (Pré-visualização)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Os membros de um grupo estão automaticamente habilitados para o lançamento encenado. Grupos aninhados e dinâmicos não são apoiados para o lançamento encenado.
   >Ao adicionar um novo grupo, os utilizadores do grupo (até 200 utilizadores para um novo grupo) serão atualizados para utilizar em imidiamente gerido. Editar um grupo (adicionar ou remover utilizadores), pode levar até 24 horas para que as alterações entrem em vigor.
   >O SSO sem emenda só se aplicará se os utilizadores estiverem no grupo SSO sem emenda e também num grupo de PTA ou PHS.

## <a name="auditing"></a>Auditoria

Permitimos eventos de auditoria para as várias ações que executamos para lançamento encenado:

- Evento de auditoria quando ativar um lançamento encenado para *sincronização de hash de palavra-passe,* *autenticação de passagem* ou *SSO sem costura*.

  >[!NOTE]
  >Um evento de auditoria é registado quando *o SSO sem costura* é ligado através do lançamento encenado.

  ![O painel "Criar política de implementação para funcionalidades" - Separador de atividade](./media/how-to-connect-staged-rollout/sr7.png)

  ![O painel "Criar política de implementação para funcionalidades" - Separador de Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr8.png)

- Evento de auditoria quando um grupo é adicionado ao *sincronização de haxixe de palavra-passe,* *autenticação pass-through,* ou *SSO sem costura*.

  >[!NOTE]
  >Um evento de auditoria é registado quando um grupo é adicionado ao *sincronização de haxixe de palavra-passe* para o lançamento encenado.

  ![O painel "Adicionar um grupo para apresentar rollout" - Separador de atividade](./media/how-to-connect-staged-rollout/sr9.png)

  ![O painel "Adicionar um grupo para apresentar rollout" - Separador de Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr10.png)

- Evento de auditoria quando um utilizador que foi adicionado ao grupo está habilitado para o lançamento encenado.

  ![O painel "Adicionar utilizador ao lançamento de funcionalidades" - Separador de atividade](media/how-to-connect-staged-rollout/sr11.png)

  ![O painel "Adicionar utilizador ao lançamento de funcionalidades" - Separador alvo(s)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validação

Para testar o sômin com sincronização de *haxixe* de *palavra-passe* ou autenticação de passagem (nome de utilizador e acesso à palavra-passe), faça o seguinte:

1. Na extranet, aceda à [página apps](https://myapps.microsoft.com) numa sessão de navegador privada e, em seguida, insira o Nome Do UtilizadorPrincipalName (UPN) da conta de utilizador selecionada para o lançamento encenado.

   Os utilizadores que foram alvo de lançamento encenado não são redirecionados para a sua página de login federada. Em vez disso, é-lhes pedido que assinem a página de inscrição da marca Azure.

1. Certifique-se de que o sinal de inscrição aparece com sucesso no [relatório de atividade de inscrição AD AZure,](../reports-monitoring/concept-sign-ins.md) filtrando com o Nome Do UtilizadorPrincipal.

Para testar o *sso sem costura:*

1. Na intranet, aceda à [página apps](https://myapps.microsoft.com) numa sessão de navegador privada e, em seguida, insira o Nome Do UtilizadorPrincipalName (UPN) da conta de utilizador selecionada para o lançamento encenado.

   Os utilizadores que foram visados para o lançamento encenado de *SSO sem emenda* são apresentados com um "Tentar contratá-lo em..." mensagem antes de serem silenciosamente assinados.

1. Certifique-se de que o sinal de inscrição aparece com sucesso no [relatório de atividade de inscrição AD AZure,](../reports-monitoring/concept-sign-ins.md) filtrando com o Nome Do UtilizadorPrincipal.

   Para acompanhar os logins do utilizador que ainda ocorrem nos Serviços da Federação de Diretórios Ativos (AD FS) para utilizadores de lançamentos encenados selecionados, siga as instruções na [resolução de problemas da AD FS: Eventos e registos](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Consulte a documentação do fornecedor sobre como verificar isto em fornecedores de federação de terceiros.

## <a name="remove-a-user-from-staged-rollout"></a>Remova um utilizador do lançamento encenado

A remoção de um utilizador do grupo desativa o lançamento encenado para esse utilizador. Para desativar a função de lançamento encenada, deslize o controlo de volta para **Off**.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Posso usar esta capacidade na produção?**

R: Sim, você pode usar esta funcionalidade no seu inquilino de produção, mas recomendamos que você experimente primeiro no seu inquilino de teste.

**P: Esta funcionalidade pode ser usada para manter uma "coexistência" permanente, onde alguns utilizadores usam a autenticação federada e outros utilizam a autenticação em nuvem?**

R: Não, esta função foi concebida para testar a autenticação em nuvem. Depois de ter sido bem sucedido a testar alguns grupos de utilizadores, deverá cortar para a autenticação na nuvem. Não recomendamos a utilização de um estado misto permanente, pois esta abordagem pode levar a fluxos de autenticação inesperados.

**P: Posso usar o PowerShell para executar o lançamento encenado?**

R: Sim. Para aprender a usar o PowerShell para executar o lançamento encenado, consulte [a pré-visualização AD Azure](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Passos seguintes
- [Pré-visualização Azure AD 2.0](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
