---
title: 'Azure AD Connect: Autenticação em nuvem através de lançamento encenado Microsoft Docs'
description: Este artigo explica como migrar da autenticação federada, para a autenticação em nuvem, utilizando um lançamento encenado.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fbe76fb18e33efaa161d2e2b488b48fa5c8580d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644159"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>Migrar para a autenticação em nuvem usando o rollout encenado (pré-visualização)

Ao utilizar uma abordagem de lançamento encenada, pode migrar da autenticação federada para a autenticação em nuvem. Este artigo discute como fazer a troca. No entanto, antes de iniciar o lançamento encenado, deve considerar as implicações se uma ou mais das seguintes condições forem verdadeiras:
    
-  Está a utilizar um servidor de autenticação multifactor no local. 
-  Estás a usar cartões inteligentes para autenticação. 
-  O seu servidor atual oferece certas funcionalidades apenas da federação.

Antes de experimentar esta funcionalidade, sugerimos que reveja o nosso guia sobre a escolha do método de autenticação certo. Para mais informações, consulte a tabela "Comparar métodos" em [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods).

Para uma visão geral da funcionalidade, veja este "Azure Ative Directory: What is staged rollout?" vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Pré-requisitos

-   Você tem um inquilino azure Ative Directory (Azure AD) com domínios federados.

-   Decidiu mudar-se para qualquer uma das duas opções:
    - **Opção A**  -  *sincronização de hash password (sincronização)*  +  *sem emenda único sign-on (SSO)*
    - **Opção B**  -  autenticação pass-through *pass-through authentication*  +  *SSO sem emenda*
    
    Embora o *SSO sem emenda* seja opcional, recomendamos que lhe permita obter uma experiência de entrada silenciosa para utilizadores que estejam a executar máquinas unidas ao domínio a partir de dentro de uma rede corporativa.

-   Configurou todas as políticas adequadas de marca de inquilinos e de acesso condicional que necessita para os utilizadores que estão a ser migrados para a autenticação na nuvem.

-   Se planeia utilizar a Autenticação Multifactor Azure, recomendamos que utilize [o registo convergente para redefinir a palavra-passe de autosserviço (SSPR) e a Autenticação Multi-Factor](../authentication/concept-registration-mfa-sspr-combined.md) para que os seus utilizadores registem os seus métodos de autenticação uma vez.

-   Para utilizar a funcionalidade de lançamento encenada, precisa de ser administrador global do seu inquilino.

-   Para permitir um *SSO sem emenda* numa floresta específica de Diretório Ativo, você precisa ser um administrador de domínio.


## <a name="supported-scenarios"></a>Cenários suportados

Os seguintes cenários são suportados para o lançamento encenado. A funcionalidade funciona apenas para:

- Utilizadores que são provisionados para AD Azure utilizando o Azure AD Connect. Não se aplica autilizadores apenas à nuvem.

- Tráfego de login do utilizador nos navegadores e clientes de *autenticação moderna.* Aplicações ou serviços na nuvem que utilizem autenticação legado regem-se aos fluxos de autenticação federados. Um exemplo pode ser o Exchange online com a autenticação moderna desligada, ou o Outlook 2010, que não suporta a autenticação moderna.
- O tamanho do grupo está atualmente limitado a 50.000 utilizadores.  Se tiver grupos maiores do que 50.000 utilizadores, recomenda-se dividir este grupo em vários grupos para lançamento encenado.

## <a name="unsupported-scenarios"></a>Cenários não suportados

Os seguintes cenários não são suportados para o lançamento encenado:

- Algumas aplicações enviam o parâmetro de consulta "domain_hint" para a AD Azure durante a autenticação. Estes fluxos continuarão e os utilizadores que estiverem habilitados para o lançamento encenado continuarão a utilizar a federação para autenticação.

<!-- -->

- Os administradores podem lançar a autenticação em nuvem usando grupos de segurança. Para evitar a latência sincronizada quando estiver a utilizar grupos de segurança ative diretórios, recomendamos que utilize grupos de segurança na nuvem. Aplicam-se as seguintes condições:

    - Pode utilizar um máximo de 10 grupos por recurso. Ou seja, pode utilizar 10 grupos cada um para sincronização de *hash password,* *autenticação pass-through*e *SSO sem emenda.*
    - Os grupos aninhados não são *apoiados.* Este âmbito aplica-se também à pré-visualização pública.
    - Os grupos dinâmicos não são apoiados para o lançamento *encenado.*
    - Os objetos de contacto dentro do grupo impedirão que o grupo seja adicionado.

- Ainda precisa de fazer o corte final da autenticação federada para a nuvem utilizando o Azure AD Connect ou o PowerShell. O lançamento encenado não muda os domínios de federados para geridos.

- Quando adiciona um grupo de segurança para lançamento encenado, está limitado a 200 utilizadores para evitar um intervalo de ux. Depois de adicionar o grupo, pode adicionar mais utilizadores diretamente ao mesmo, conforme necessário.

>[!NOTE]
> Como os pontos finais inquilinos não enviam pistas de login, não são suportados para lançamento encenado.  As aplicações SAML utilizam os pontos finais arrendados e também não são suporte para lançamento sondados.

## <a name="get-started-with-staged-rollout"></a>Começar com o lançamento encenado

Para testar o *sincronia* de hash de palavra-passe utilizando o rollout encenado, siga as instruções de pré-trabalho na secção seguinte.

Para obter informações sobre quais os cmdlets PowerShell a utilizar, consulte a [pré-visualização Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Pré-trabalho para sincronização de hash de senha

1. Ative o sincronização de hash de *palavra-passe*   na página de [funcionalidades Opcional](how-to-connect-install-custom.md#optional-features)no   Azure AD Connect. 

   ![Screenshot da página "Funcionalidades Opcionais" no Azure Ative Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Certifique-se de que um ciclo completo de sincronização de *hash* foi executado de modo a que todas as hashes de senha dos utilizadores tenham sido sincronizadas para a AD Azure. Para verificar o estado da sincronização de hash de *palavra-passe,* pode utilizar os diagnósticos powerShell em [sync de hash de palavra-passe de Troubleshoot com sincronização azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Screenshot do registo de resolução de problemas DaADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Se pretender testar o sinal de *autenticação pass-through* utilizando o rollout encenado, ative-o seguindo as instruções de pré-trabalho na secção seguinte.

## <a name="pre-work-for-pass-through-authentication"></a>Pré-trabalho para autenticação pass-through

1. Identifique um servidor que esteja a executar o Windows Server 2012 R2 ou mais tarde onde pretende que o agente de *autenticação pass-through* seja executado. 

   *Não* escolha o servidor Azure AD Connect.Certifique-se de que o servidor está unido ao domínio, pode autenticar utilizadores selecionados com Diretório Ativo e comunicar com a Azure AD em portas de saída e URLs. Para mais informações, consulte a secção "Passo 1: Verifique os pré-requisitos" de [Quickstart: Azure AD seamless single sign-on](how-to-connect-sso-quick-start.md).

1. [Descarregue o agente de autenticação Azure AD Connect](https://aka.ms/getauthagent)e instale-o no servidor. 

1. Para permitir [uma alta disponibilidade,](how-to-connect-sso-quick-start.md)instale agentes de autenticação adicionais noutros servidores.

1. Certifique-se de que configura as [definições de Smart Lockout](../authentication/howto-password-smart-lockout.md) de forma adequada. Fazê-lo ajuda a garantir que as contas de Diretório Ativo dos seus utilizadores não são bloqueadas por maus atores.

Recomendamos que permita o *SSO sem emenda,* independentemente do método de entrada *(sincronização* de hash password ou *autenticação pass-through*) selecione para o lançamento encenado. Para ativar *o SSO sem emenda,* siga as instruções de pré-trabalho na secção seguinte.

## <a name="pre-work-for-seamless-sso"></a>Pré-trabalho para SSO sem emenda

Ative *sSO sem emenda*nas florestas de   Diretório Ativo utilizando powerShell. Se tiver mais do que uma floresta de Diretório Ativo, ative-a individualmente para cada floresta.  *O SSO sem emenda* é acionado apenas para utilizadores selecionados para lançamento sem emenda. Não afeta a sua atual instalação da federação.

Habilitar *o SSO sem emenda* fazendo o seguinte:

1. Inscreva-se no Azure AD Connect Server.

2. Vá à pasta *%programfiles% \\ Microsoft Azure Ative Directory Connect.*  

3. Importar o módulo *SSO* PowerShell sem costura executando o seguinte comando: 

   `Import-Module .\AzureADSSO.psd1`

4. Execute o PowerShell como um administrador. Na PowerShell,  `New-AzureADSSOAuthenticationContext` ligue. Este comando abre um painel onde pode introduzir as credenciais de administrador global do seu inquilino.

5.  `Get-AzureADSSOStatus | ConvertFrom-Json`Ligue. Este comando apresenta uma lista de florestas de Diretório Ativo (ver a lista "Domínios") na qual esta funcionalidade foi ativada. Por padrão, está definido para falso ao nível do inquilino.

   ![Exemplo da saída do Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6.  `$creds = Get-Credential`Ligue. No momento, introduza as credenciais do administrador de domínio para a floresta de Diretório Ativo pretendida.

7. `Enable-AzureADSSOForest -OnPremCredentials $creds`Ligue. Este comando cria a conta de computador AZUREADSSOACC a partir do controlador de domínio no local para a floresta de Diretório Ativo que é necessária para *sso sem emenda*.

8. *SSO sem emenda* requer URLs para estar na zona intranet. Para implementar esses URLs utilizando políticas de grupo, consulte [Quickstart: Azure AD seamless single sign-on](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Para uma passagem completa, também pode descarregar os nossos planos de [implementação](https://aka.ms/SeamlessSSODPDownload) para *SSO sem emenda.*

## <a name="enable-staged-rollout"></a>Ativar o lançamento encenado

Para lançar uma funcionalidade específica *(autenticação pass-through,* sincronização de *hash password*, ou *SSO sem emenda*) para um conjunto selecionado de utilizadores em um grupo, siga as instruções nas secções seguintes.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Ativar um lançamento encenado de uma característica específica no seu inquilino

Pode lançar uma destas opções:

- **Opção A**  -  sincronização de *hash password*  +  *SSO sem emenda*
- **Opção B**  -  autenticação pass-through *pass-through authentication*  +  *SSO sem emenda*
- **Não apoiado**  -  sincronização de *hash password*  +  autenticação pass-through *pass-through authentication*  +  *SSO sem emenda*

Faça o seguinte:

1. Para aceder à pré-visualização UX, inscreva-se no [portal Azure AD](https://aka.ms/stagedrolloutux).

2. Selecione o **'''''Preview' do link de receção (Pré-visualização)** do utilizador.

   Por exemplo, se pretender ativar a *Opção A,* deslize os controlos de inscrição single password **Hash Sync** e **Seamless single-on** para **On**, como mostra as seguintes imagens.

   ![A página Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![A página "Ativar funcionalidades de lançamento encenado (Pré-visualização)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Adicione os grupos à funcionalidade para permitir a *autenticação pass-through* e *o SSO sem emenda*. Para evitar uma saída de UX, certifique-se de que os grupos de segurança não contêm mais de 200 membros inicialmente.

   ![A página "Gerir grupos para Password Hash Sync (Pré-visualização)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Os membros de um grupo estão automaticamente habilitados para o lançamento encenado. Grupos aninhados e dinâmicos não são apoiados para o lançamento encenado.

## <a name="auditing"></a>Auditoria

Permitimos eventos de auditoria para as várias ações que executamos para o lançamento encenado:

- Evento de auditoria quando ativa um lançamento encenado para sincronização de *hash de palavra-passe,* *autenticação pass-through*, ou *SSO sem emenda*.

  >[!NOTE]
  >Um evento de auditoria é registado quando *o SSO sem emenda* é ligado utilizando o lançamento encenado.

  ![O painel "Criar rollout para recurso" - Separador de atividade](./media/how-to-connect-staged-rollout/sr7.png)

  ![O painel "Criar a política de rollout para recurso" - Separador Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr8.png)

- Evento de auditoria quando um grupo é adicionado à sincronização de *hash password,* *autenticação pass-through*, ou *SSO sem emenda*.

  >[!NOTE]
  >Um evento de auditoria é registado quando um grupo é adicionado à sincronização de *hash de senha* para lançamento encenado.

  ![O painel "Adicionar um grupo para apresentar rollout" - Separador de atividade](./media/how-to-connect-staged-rollout/sr9.png)

  ![O painel "Adicionar um grupo para apresentar o rollout" - separador Propriedades Modificadas](./media/how-to-connect-staged-rollout/sr10.png)

- Evento de auditoria quando um utilizador que foi adicionado ao grupo está habilitado para o lançamento encenado.

  ![O painel "Adicionar utilizador a função rollout" - Separador de atividade](media/how-to-connect-staged-rollout/sr11.png)

  ![O painel "Adicionar o utilizador ao lançamento da função" - separador target(s)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validação

Para testar o sessão com sincronização de hash de *palavra-passe* ou *autenticação pass-through* (nome de utilizador e entrada de password), faça o seguinte:

1. Na extranet, vá à página de [Apps](https://myapps.microsoft.com) numa sessão de navegador privado e, em seguida, introduza o UserPrincipalName (UPN) da conta de utilizador que é selecionada para lançamento encenado.

   Os utilizadores que foram visados para o lançamento encenado não são redirecionados para a sua página de login federado. Em vez disso, é-lhes pedido que inscrevam-se na página de inscrição da marca Azure AD.

1. Certifique-se de que o início de sessão aparece com sucesso no relatório de atividade de entrada da [AD Azure](../reports-monitoring/concept-sign-ins.md) filtrando com o UserPrincipalName.

Para testar o sign-in com *SSO sem emenda:*

1. Na intranet, vá à página de [Apps](https://myapps.microsoft.com) numa sessão de navegador privado e, em seguida, introduza o UserPrincipalName (UPN) da conta de utilizador que é selecionada para lançamento encenado.

   Os utilizadores que foram alvo de lançamento encenado de *SSO sem costura* são apresentados com um "Tentando inscrevê-lo em ..." mensagem antes de serem silenciosamente assinados.

1. Certifique-se de que o início de sessão aparece com sucesso no relatório de atividade de entrada da [AD Azure](../reports-monitoring/concept-sign-ins.md) filtrando com o UserPrincipalName.

   Para acompanhar os registos dos utilizadores que ainda ocorram nos Serviços da Federação de Diretórios Ativos (AD FS) para utilizadores de lançamento seletos selecionados, siga as instruções em [AD FS troubleshooting: Eventos e registos .](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events) Verifique a documentação do fornecedor sobre como verificar isso em fornecedores de federações de terceiros.

## <a name="remove-a-user-from-staged-rollout"></a>Remova um utilizador do lançamento encenado

A remoção de um utilizador do grupo desativa o lançamento faseado para esse utilizador. Para desativar a função de lançamento encenada, deslize o controlo de volta para **Off**.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Posso usar esta capacidade na produção?**

R: Sim, você pode usar esta funcionalidade no seu inquilino de produção, mas recomendamos que experimente primeiro no seu inquilino de teste.

**P: Esta funcionalidade pode ser utilizada para manter uma "coexistência" permanente, onde alguns utilizadores usam autenticação federada e outros utilizam a autenticação em nuvem?**

R: Não, esta funcionalidade foi concebida para migrar de autenticação federada para cloud por etapas e depois para eventualmente cortar para a autenticação em nuvem. Não recomendamos a utilização de um estado misto permanente, porque esta abordagem pode levar a fluxos de autenticação inesperados.

**P: Posso usar a PowerShell para realizar o lançamento encenado?**

R: Sim. Para aprender a usar o PowerShell para realizar o lançamento encenado, consulte [a Pré-visualização do AD Azure](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Passos seguintes
- [Pré-visualização Azure AD 2.0](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
