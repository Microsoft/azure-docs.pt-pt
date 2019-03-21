---
title: 'Azure Active Directory Connect: Resolver problemas de sessão único totalmente integrado-| Documentos da Microsoft'
description: Este tópico descreve como resolver problemas do Azure Active Directory totalmente integrada início de sessão único
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: abfdad1db655c102dbfb300434eac952fe2154dc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095906"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Resolver problemas relacionados com o Azure Active Directory totalmente integrada início de sessão único

Este artigo ajuda-o a localizar informações sobre problemas comuns sobre o Azure Active Directory (Azure AD) totalmente integrada logon único (SSO totalmente integrado) de resolução de problemas.

## <a name="known-issues"></a>Problemas conhecidos

- Em alguns casos, a ativar o SSO totalmente integrado pode demorar até 30 minutos.
- Se desativa e reativar o SSO totalmente integrado no seu inquilino, os utilizadores não receberão a experiência de início de sessão única até que as suas permissões de Kerberos em cache, normalmente, válidas durante 10 horas, tem expirado.
- Suporte do browser Microsoft Edge não está disponível.
- Se tiver êxito SSO totalmente integrado, o utilizador não tem a oportunidade de selecionar **manter sessão iniciada**. Devido a esse comportamento [cenários de mapeamento do SharePoint e OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) não funcionam.
- Clientes do Office 365 Win32 (Outlook, Word, Excel e outras pessoas) com as versões 16.0.8730.xxxx e superior são suportados com um fluxo não interativo. Outras versões não são suportadas; por essas versões, os usuários passarão a seus nomes de utilizador, mas não as palavras-passe, início de sessão. Para o OneDrive, terá de ativar a [funcionalidade de configuração automática do OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de início de sessão silenciosa.
- SSO totalmente integrado não funciona no modo de navegação particular, no Firefox.
- SSO totalmente integrado não funciona no Internet Explorer, quando o modo protegido avançado está ativado.
- SSO totalmente integrado não funciona em browsers para dispositivos móveis no iOS e Android.
- Se um utilizador faz parte de demasiados grupos no Active Directory, permissão Kerberos do utilizador deverá ser demasiado grande para processar e isso fará com que SSO totalmente integrado a falhar. Pedidos de HTTPS de AD do Azure podem ter cabeçalhos com um tamanho máximo de 50 KB; Os tíquetes Kerberos tem de ser menor do que esse limite para acomodar outros artefatos do Azure AD (normalmente, 2 a 5 KB), como cookies. A nossa recomendação é reduzir as associações de grupo do utilizador e tente novamente.
- Se estiver sincronizando 30 ou mais florestas do Active Directory, não é possível ativar o SSO totalmente integrado através do Azure AD Connect. Como solução, pode [ative manualmente](#manual-reset-of-the-feature) a funcionalidade no seu inquilino.
- Adicionar o URL do serviço do Azure AD (https://autologon.microsoftazuread-sso.com) para a zona de sites fidedignos, em vez de zona da Local intranet *bloqueia os utilizadores se inscrevam*.
- Utiliza o SSO totalmente integrado a **RC4_HMAC_MD5** tipo de encriptação para Kerberos. Desativar a utilização do **RC4_HMAC_MD5** tipo de encriptação nas definições do Active Directory irá interromper o SSO totalmente integrado. Na sua ferramenta de Editor de gerenciamento de diretiva de grupo Certifique-se de que o valor da política para **RC4_HMAC_MD5** sob **configuração do computador -> definições do Windows -> definições de segurança -> Políticas locais -> Opções de segurança - > "Segurança de rede: Configurar tipos de encriptação permitidos para Kerberos"** é **ativado**. Além disso, o SSO totalmente integrado não é possível utilizar outros tipos de encriptação, por isso, certifique-se de que estão **desativada**.

## <a name="check-status-of-feature"></a>Verificar o estado do recurso

Certifique-se de que a funcionalidade de SSO totalmente integrado é ainda **ativado** no seu inquilino. Pode verificar o estado ao aceder a **do Azure AD Connect** painel na [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: Painel do Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Clicar para ver todas as florestas do AD que foram ativadas para SSO totalmente integrado.

![Centro de administração do Azure Active Directory: Painel SSO totalmente integrado](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivos das falhas de início de sessão no Centro de administração do Active Directory do Azure (precisa de uma licença Premium)

Se o seu inquilino tem uma licença do Azure AD Premium associada a ele, também pode ver o [relatório de atividade de início de sessão](../reports-monitoring/concept-sign-ins.md) no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: Relatório de inícios de sessão](./media/tshoot-connect-sso/sso9.png)

Navegue até **do Azure Active Directory** > **inícios de sessão** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/)e, em seguida, selecione a atividade de início de sessão de um utilizador específico. Procure o **código de erro de início de sessão** campo. Mapear o valor deste campo a um motivo da falha e a resolução com a tabela seguinte:

|Código de erro de início de sessão|Motivo da falha de início de sessão|Resolução
| --- | --- | ---
| 81001 | A permissão do Kerberos do utilizador é demasiado grande. | Reduza as adesões a grupos do utilizador e tente novamente.
| 81002 | Não é possível validar a permissão Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81003 | Não é possível validar a permissão Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81004 | Falha ao tentar a autenticação do Kerberos. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81008 | Não é possível validar a permissão Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81009 | Não é possível validar a permissão Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81010 | O SSO integrado falhou porque a permissão do Kerberos do utilizador expirou ou é inválida. | O utilizador tem de iniciar sessão a partir de um dispositivo associado a um domínio no interior da rede empresarial.
| 81011 | Não é possível encontrar o objeto de utilizador com base nas informações na permissão Kerberos do utilizador. | Utilize o Azure AD Connect para sincronizar informações do utilizador no Azure AD.
| 81012 | O utilizador tentar iniciar sessão com o Azure AD é diferente do utilizador que tem sessão iniciado no dispositivo. | O utilizador tem de iniciar sessão a partir de um dispositivo diferente.
| 81013 | Não é possível encontrar o objeto de utilizador com base nas informações na permissão Kerberos do utilizador. |Utilize o Azure AD Connect para sincronizar informações do utilizador no Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de resolução de problemas

Utilize a lista de verificação seguinte para resolver problemas de SSO totalmente integrado:

- Certifique-se de que a funcionalidade de SSO totalmente integrado está ativada no Azure AD Connect. Se não é possível ativar a funcionalidade (por exemplo, devido a uma porta bloqueada), certifique-se de que tem todos os [pré-requisitos](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) no local.
- Se tiver ativado o ambos [associação do Azure AD](../active-directory-azureadjoin-overview.md) e SSO totalmente integrado no seu inquilino, certifique-se de que o problema não está com a associação do Azure AD. SSO da associação do Azure AD têm precedência sobre o SSO totalmente integrado se o dispositivo está registado com o Azure AD e associados a um domínio. Com o SSO da associação do Azure AD, o utilizador verá um mosaico de início de sessão que diz "Conectados para Windows".
- Certifique-se de que o URL do Azure AD (https://autologon.microsoftazuread-sso.com) é parte integrante das definições de zona de Intranet do utilizador.
- Certifique-se de que o dispositivo da empresa está associado ao domínio do Active Directory. O dispositivo _não_ têm de ser [do Azure AD associado](../active-directory-azureadjoin-overview.md) para SSO totalmente integrado trabalhar.
- Certifique-se de que o usuário está conectado ao dispositivo por meio de uma conta de domínio do Active Directory.
- Certifique-se de que a conta de utilizador está numa floresta do Active Directory onde o SSO totalmente integrado tiver sido configurada.
- Certifique-se de que o dispositivo está ligado à rede empresarial.
- Certifique-se de que a hora do dispositivo está sincronizada com a hora no Active Directory e os controladores de domínio e que estão dentro de cinco minutos entre si.
- Certifique-se de que o `AZUREADSSOACC` conta de computador está presente e ativado em cada floresta do AD que pretende que o SSO totalmente integrado ativada. Se a conta de computador foi eliminada ou está em falta, pode usar [cmdlets do PowerShell](#manual-reset-of-the-feature) para voltar a criá-los.
- Listar os tíquetes Kerberos existentes no dispositivo utilizando o `klist` comando numa linha de comandos. Certifique-se de que as permissões emitidas para o `AZUREADSSOACC` conta de computador estão presentes. Permissões de Kerberos dos utilizadores são normalmente válidos durante 10 horas. Pode ter diferentes configurações no Active Directory.
- Se desativado e reativada SSO totalmente integrado no seu inquilino, os utilizadores não receberão a experiência de início de sessão única até que os respetivos bilhetes Kerberos em cache expiraram.
- Remover permissões de Kerberos existentes do dispositivo utilizando o `klist purge` de comandos e tente novamente.
- Para determinar se existem problemas relacionados com o JavaScript, reveja os registos de consola do browser (sob **ferramentas de programação**).
- Reveja os [registos de controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Registos de controlador de domínio

Se ativar a auditoria de êxito no seu controlador de domínio, em seguida, sempre que um utilizador inicia sessão através do SSO totalmente integrado, uma entrada de segurança é registrada no log de eventos. Pode localizar esses eventos de segurança utilizando a seguinte consulta. (Procure o evento **4769** associadas com a conta de computador **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Reposição manual do recurso

Se a resolução de problemas não o ajudaram, pode repor manualmente a funcionalidade no seu inquilino. Siga estes passos no servidor no local em que estiver a executar o Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passo 1: Importe o módulo PowerShell do SSO totalmente integrado

1. Em primeiro lugar, transfira e instale [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navegue para o `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
3. Importar o módulo do PowerShell do SSO totalmente integrado ao utilizar este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 2: Obter a lista de florestas do Active Directory no qual foi ativado o SSO totalmente integrado

1. Execute o PowerShell como administrador. No PowerShell, chamar `New-AzureADSSOAuthenticationContext`. Quando lhe for pedido, introduza as credenciais de administrador global do seu inquilino.
2. Chamar `Get-AzureADSSOStatus`. Este comando fornece a lista de florestas do Active Directory (consulte a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Passo 3: Desativar o SSO totalmente integrado para cada floresta do Active Directory onde configurou o recurso

1. Chamar `$creds = Get-Credential`. Quando lhe for pedido, introduza as credenciais de administrador de domínio para a floresta do Active Directory pretendida.

   > [!NOTE]
   > Utilizamos o nome de utilizador do administrador de domínio, fornecido em nomes de Principal utilizador (UPN) (johndoe@contoso.com) formato ou o domínio sam conta qualificado (contoso\diogoandrade ou com\johndoe) formato de nome, para localizar a floresta de AD pretendida. Se utilizar o nome qualificado de sam conta de domínio, usamos a parte do nome de utilizador de domínio [localizar o controlador de domínio, o administrador de domínio através de DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se utilizar o UPN em vez disso, podemos [traduzi-la para um nome de sam conta qualificado do domínio](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) antes de localizar o controlador de domínio apropriadas.

2. Chamar `Disable-AzureADSSOForest -OnPremCredentials $creds`. Este comando remove o `AZUREADSSOACC` conta de computador do controlador de domínio no local para esta floresta do Active Directory específico.
3. Repita os passos anteriores para cada floresta do Active Directory onde configurou a funcionalidade.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passo 4: Ativar o SSO totalmente integrado para cada floresta do Active Directory

1. Chamar `Enable-AzureADSSOForest`. Quando lhe for pedido, introduza as credenciais de administrador de domínio para a floresta do Active Directory pretendida.

   > [!NOTE]
   > Utilizamos o nome de utilizador do administrador de domínio, fornecido em nomes de Principal utilizador (UPN) (johndoe@contoso.com) formato ou o domínio sam conta qualificado (contoso\diogoandrade ou com\johndoe) formato de nome, para localizar a floresta de AD pretendida. Se utilizar o nome qualificado de sam conta de domínio, usamos a parte do nome de utilizador de domínio [localizar o controlador de domínio, o administrador de domínio através de DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se utilizar o UPN em vez disso, podemos [traduzi-la para um nome de sam conta qualificado do domínio](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) antes de localizar o controlador de domínio apropriadas.

2. Repita o passo anterior para cada floresta do Active Directory onde pretende configurar a funcionalidade.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5. Ativar a funcionalidade no seu inquilino

Para ativar a funcionalidade no seu inquilino, chame `Enable-AzureADSSO -Enable $true`.
