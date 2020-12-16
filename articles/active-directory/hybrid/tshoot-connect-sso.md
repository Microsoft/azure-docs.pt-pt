---
title: 'Azure Ative Directory Connect: Troubleshoot Seamless Single Sign-On [ Microsoft Docs'
description: Este tópico descreve como resolver problemas no Azure Ative Directory Seamless Single Sign-On
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bc638eec174a52d501120d5e53bb2dc9e35b688
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591179"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Resolução de problemas Azure Ative Directory Single Sign-On

Este artigo ajuda-o a encontrar informações sobre problemas comuns no que diz respeito ao Azure Ative Directory (Azure AD) Seamless Single Sign-On (Seamless SSO).

## <a name="known-issues"></a>Problemas conhecidos

- Em alguns casos, permitir o SSO sem emenda pode demorar até 30 minutos.
- Se desativar e reativar o SSO sem emenda no seu inquilino, os utilizadores não terão a única experiência de inscrição até que os seus bilhetes Kerberos em cache, normalmente válidos por 10 horas, tenham expirado.
- Se o Seamless SSO tiver sucesso, o utilizador não tem a oportunidade de selecionar **Mantenha-me inscrito.** Devido a este comportamento, [os cenários de mapeamento sharePoint e OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) não funcionam.
- Os clientes Microsoft 365 Win32 (Outlook, Word, Excel e outros) com as versões 16.0.8730.xxxx e acima são suportados com um fluxo não interativo. Outras versões não são suportadas; nessas versões, os utilizadores introduzirão os seus nomes de utilizador, mas não palavras-passe, para iniciar sintrodução. Para o OneDrive, terá de ativar a [funcionalidade de config silencioso OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de sinal-on silenciosa.
- O SSO sem emenda não funciona em modo de navegação privada no Firefox.
- O SSO sem emenda não funciona no Internet Explorer quando o modo Protegido Melhorado é ligado.
- O Seamless SSO não funciona em navegadores móveis em iOS e Android.
- Se um utilizador fizer parte de demasiados grupos no Ative Directory, o bilhete Kerberos do utilizador será provavelmente demasiado grande para ser processado, o que fará com que o SSO sem emenda falhe. Os pedidos Azure AD HTTPS podem ter cabeçalhos com um tamanho máximo de 50 KB; Os bilhetes Kerberos precisam de ser menores do que esse limite para acomodar outros artefactos AD Azure (normalmente, 2 - 5 KB) como cookies. A nossa recomendação é reduzir os membros do grupo de utilizadores e tentar novamente.
- Se estiver a sincronizar 30 ou mais florestas de Ative Directory, não pode ativar o SSO sem emenda através do Azure AD Connect. Como solução alternativa, pode [ativar manualmente](#manual-reset-of-the-feature) a funcionalidade no seu inquilino.
- A adição do URL de serviço AD Azure ( `https://autologon.microsoftazuread-sso.com` ) à zona de sites Fidedignos em vez da zona intranet local bloqueia os *utilizadores de iniciarem a sua inscrição .*
- O Seamless SSO suporta os tipos de encriptação AES256_HMAC_SHA1, AES128_HMAC_SHA1 e RC4_HMAC_MD5 para Kerberos. Recomenda-se que o tipo de encriptação da conta AzureADSSSOAcc$ esteja definido para AES256_HMAC_SHA1, ou um dos tipos AES vs. RC4 para uma maior segurança. O tipo de encriptação é armazenado nos MsDS-SuportadosEncryptionTypes atribuem da conta no seu Diretório Ativo.  Se o tipo de encriptação da conta AzureADSSOAcc$ estiver definido para RC4_HMAC_MD5, e pretender alterá-lo para um dos tipos de encriptação AES, certifique-se de que primeiro rolar sobre a chave de desencriptação Kerberos da conta AzureADSSOAcc$, conforme explicado no [documento faq](how-to-connect-sso-faq.md) sob a questão relevante, caso contrário, o SSO sem emenda não acontecerá.
-  Se tiver mais de uma floresta com confiança florestal, permitindo sSO em uma das florestas, permitirá SSO em todas as florestas de confiança. Se ativar o SSO numa floresta onde o SSO já está ativado, terá um erro ao dizer que o SSO já está ativado na floresta.

## <a name="check-status-of-feature"></a>Verificar o estado da funcionalidade

Certifique-se de que a funcionalidade SSO sem emenda ainda está **ativada** no seu inquilino. Pode verificar o estado indo para o painel **Azure AD Connect** no [centro de administração Azure Ative Directory](https://aad.portal.azure.com/).

![Centro de administração Azure Ative Directory: Painel de ligação Azure AD](./media/tshoot-connect-sso/sso10.png)

Clique para ver todas as florestas de AD que foram ativadas para SSO sem emenda.

![Centro de administração Azure Ative Directory: Painel SSO sem costura](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Razões de falha de inscrição no centro de administração do Azure Ative (precisa de uma licença Premium)

Se o seu inquilino tiver uma licença Azure AD Premium associada a ela, também pode consultar o [relatório de atividades de inscrição](../reports-monitoring/concept-sign-ins.md) no [centro de administração Azure Ative.](https://aad.portal.azure.com/)

![Centro de administração Azure Ative Directory: Relatório de inscrições](./media/tshoot-connect-sso/sso9.png)

Navegue para **Azure Ative Directory**  >  **Sign-ins** no centro de administração do [Azure Ative Directory](https://aad.portal.azure.com/), e, em seguida, selecione a atividade de inscrição de um utilizador específico. Procure o campo **CÓDIGO DE ERRO SIGN-IN.** Mapear o valor desse campo para uma razão de falha e resolução utilizando a seguinte tabela:

|Código de erro de inscrição|Razão de falha de inscrição|Resolução
| --- | --- | ---
| 81001 | A permissão do Kerberos do utilizador é demasiado grande. | Reduza as adesões a grupos do utilizador e tente novamente.
| 81002 | Não é possível validar o bilhete Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81003 | Não é possível validar o bilhete Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81004 | Falha ao tentar a autenticação do Kerberos. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81008 | Não é possível validar o bilhete Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81009 | Não é possível validar o bilhete Kerberos do utilizador. | Consulte a [lista de verificação de resolução de problemas](#troubleshooting-checklist).
| 81010 | O SSO integrado falhou porque a permissão do Kerberos do utilizador expirou ou é inválida. | O utilizador precisa de iniciar sedução a partir de um dispositivo de união de domínios dentro da sua rede corporativa.
| 81011 | Não foi possível encontrar o objeto do utilizador com base nas informações no bilhete Kerberos do utilizador. | Utilize o Azure AD Connect para sincronizar as informações do utilizador em Azure AD.
| 81012 | O utilizador que tenta entrar no Azure AD é diferente do utilizador que está inscrito no dispositivo. | O utilizador precisa de iniciar sôms de um dispositivo diferente.
| 81013 | Não foi possível encontrar o objeto do utilizador com base nas informações no bilhete Kerberos do utilizador. |Utilize o Azure AD Connect para sincronizar as informações do utilizador em Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de resolução de problemas

Utilize a seguinte lista de verificação para resolver problemas de SSO sem problemas:

- Certifique-se de que a função SSO sem emenda está ativada no Azure AD Connect. Se não conseguir ativar a funcionalidade (por exemplo, devido a uma porta bloqueada), certifique-se de que tem todos os [pré-requisitos](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) no lugar.
- Se você ativou tanto [a Azure AD Join](../devices/overview.md) como a Seamless SSO no seu inquilino, certifique-se de que o problema não é com a Azure AD Join. O SSO da Azure AD Join tem precedência sobre o Seamless SSO se o dispositivo estiver registado tanto com AZure AD como com o domínio. Com SSO da Azure AD Junte-se ao utilizador vê um azulejo de inscrição que diz "Ligado ao Windows".
- Certifique-se de que o URL AD Ad do Azure `https://autologon.microsoftazuread-sso.com` faz parte das definições da zona intranet do utilizador.
- Certifique-se de que o dispositivo corporativo está associado ao domínio ative directory. O dispositivo _não_ precisa de ser [Azure AD Unidos](../devices/overview.md) para que o SSO sem emenda funcione.
- Certifique-se de que o utilizador está ligado ao dispositivo através de uma conta de domínio do Ative Directory.
- Certifique-se de que a conta do utilizador é de uma floresta de Diretório Ativo onde foi criada a Seamless SSO.
- Certifique-se de que o dispositivo está ligado à rede corporativa.
- Certifique-se de que o tempo do dispositivo é sincronizado com o tempo tanto no Ative Directory como nos controladores de domínio, e que estão a menos de cinco minutos um do outro.
- Certifique-se de que a `AZUREADSSOACC` conta do computador está presente e ativada em cada floresta AD que pretende ser SSO sem emenda ativada. Se a conta do computador tiver sido eliminada ou faltar, pode utilizar [cmdlets PowerShell](#manual-reset-of-the-feature) para as recriar.
- Listar os bilhetes Kerberos existentes no dispositivo usando o `klist` comando a partir de um aviso de comando. Certifique-se de que os bilhetes emitidos para a `AZUREADSSOACC` conta do computador estão presentes. Os bilhetes Kerberos dos utilizadores são normalmente válidos por 10 horas. Pode ter configurações diferentes no Ative Directory.
- Se você desativou e reativou o SSO sem emenda no seu inquilino, os utilizadores não obterão a única experiência de inscrição até que os seus bilhetes Kerberos em cache tenham expirado.
- Purgue os bilhetes Kerberos existentes do dispositivo usando o `klist purge` comando, e tente novamente.
- Para determinar se existem problemas relacionados com o JavaScript, reveja os registos de consola do navegador (em **Ferramentas de Desenvolvimento).**
- Reveja os [registos do controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Registos de controlador de domínio

Se ativar a auditoria de sucesso no seu controlador de domínio, então sempre que um utilizador assina através do SSO sem emenda, uma entrada de segurança é registada no registo do evento. Pode encontrar estes eventos de segurança utilizando a seguinte consulta. (Procure o evento **4769** associado à conta de computador **AzureADSSSOAcc$.)**

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Reset manual da funcionalidade

Se a resolução de problemas não ajudar, pode redefinir manualmente a funcionalidade no seu inquilino. Siga estes passos no servidor no local onde está a executar O Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passo 1: Importar o módulo SSO PowerShell sem costura

1. Primeiro, faça o download e instale [o Azure AD PowerShell](/powershell/azure/active-directory/overview).
2. Navegue na `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
3. Importe o módulo SSO PowerShell sem costura utilizando este comando: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 2: Obtenha a lista de florestas de Diretório Ativo nas quais o SSO sem emenda foi ativado

1. Execute o PowerShell como um administrador. Em PowerShell, `New-AzureADSSOAuthenticationContext` ligue. Quando solicitado, insira as credenciais globais de administrador do seu inquilino.
2. `Get-AzureADSSOStatus`Ligue. Este comando fornece-lhe a lista de florestas de Diretório Ativo (veja a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Passo 3: Desative o SSO sem emenda para cada floresta de Diretório Ativo onde configurar a funcionalidade

1. `$creds = Get-Credential`Ligue. Quando solicitado, insira as credenciais de administrador de domínio para a floresta de Diretório Ativo pretendida.

   > [!NOTE]
   >O nome de utilizador de credenciais de administrador de domínio deve ser inserido no formato nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo de Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

2. `Disable-AzureADSSOForest -OnPremCredentials $creds`Ligue. Este comando remove a `AZUREADSSOACC` conta do computador do controlador de domínio no local para esta floresta específica do Ative Directory.
3. Repita os passos anteriores para cada floresta ative de diretório onde configurar a funcionalidade.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passo 4: Ativar SSO sem emenda para cada floresta de Diretório Ativo

1. `Enable-AzureADSSOForest`Ligue. Quando solicitado, insira as credenciais de administrador de domínio para a floresta de Diretório Ativo pretendida.

   > [!NOTE]
   >O nome de utilizador de credenciais de administrador de domínio deve ser inserido no formato nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo de Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

2. Repita o passo anterior para cada floresta ative onde pretende configurar a funcionalidade.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5. Ativar a funcionalidade no seu inquilino

Para ligar a funcionalidade no seu inquilino, `Enable-AzureADSSO -Enable $true` ligue.
