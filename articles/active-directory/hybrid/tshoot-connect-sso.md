---
title: 'Azure Active Directory Connect: Solucionar problemas de logon único contínuo | Microsoft Docs'
description: Este tópico descreve como solucionar problemas Azure Active Directory logon único contínuo
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1293bbf6d2966caf7e6e095c1721e29890a57b76
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025799"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Solucionar problemas Azure Active Directory logon único contínuo

Este artigo ajuda você a encontrar informações de solução de problemas comuns sobre o logon único contínuo (SSO contínuo) do Azure Active Directory (Azure AD).

## <a name="known-issues"></a>Problemas conhecidos

- Em alguns casos, habilitar o SSO contínuo pode levar até 30 minutos.
- Se você desabilitar e reabilitar o SSO contínuo em seu locatário, os usuários não terão a experiência de logon único até seus tíquetes Kerberos em cache, normalmente válidos por 10 horas, expiraram.
- Se o SSO contínuo for bem sucedido, o usuário não terá a oportunidade de selecionar **manter-me conectado**. Devido a esse comportamento, os [cenários de mapeamento do SharePoint e do onedrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) não funcionam.
- Os clientes do Office 365 Win32 (Outlook, Word, Excel e outros) com versões 16.0.8730. xxxx e superior têm suporte usando um fluxo não interativo. Não há suporte para outras versões; nessas versões, os usuários irão inserir seus nomes de usuário, mas não senhas, para entrar. Para o OneDrive, você precisará ativar o [recurso de configuração silenciosa do onedrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de logon silencioso.
- O SSO contínuo não funciona no modo de navegação particular no Firefox.
- O SSO contínuo não funciona no Internet Explorer quando o modo protegido aprimorado está ativado.
- O SSO contínuo não funciona em navegadores móveis no iOS e no Android.
- Se um usuário fizer parte de um número excessivo de grupos no Active Directory, o tíquete Kerberos do usuário provavelmente será muito grande para ser processado, e isso fará com que o SSO contínuo falhe. Solicitações de HTTPS do Azure AD podem ter cabeçalhos com um tamanho máximo de 50 KB; Os tíquetes Kerberos precisam ser menores do que esse limite para acomodar outros artefatos do Azure AD (normalmente, 2-5 KB), como cookies. Nossa recomendação é reduzir as associações de grupo do usuário e tentar novamente.
- Se você estiver sincronizando 30 ou mais florestas Active Directory, não poderá habilitar o SSO contínuo por meio de Azure AD Connect. Como alternativa, você pode [habilitar manualmente](#manual-reset-of-the-feature) o recurso em seu locatário.
- Adicionar a URL de serviço do Azure AD (https://autologon.microsoftazuread-sso.com) à zona de sites confiáveis em vez da zona de intranet local *impede que os usuários entrem*.
- O SSO contínuo dá suporte aos tipos de criptografia AES256_HMAC_SHA1, AES128_HMAC_SHA1 e RC4_HMAC_MD5 para Kerberos. É recomendável que o tipo de criptografia para a conta AzureADSSOAcc $ seja definido como AES256_HMAC_SHA1 ou um dos tipos AES vs. RC4 para maior segurança. O tipo de criptografia é armazenado no atributo msDS-SupportedEncryptionTypes da conta no seu Active Directory.  Se o tipo de criptografia da conta AzureADSSOAcc $ for definido como RC4_HMAC_MD5 e você quiser alterá-lo para um dos tipos de criptografia AES, certifique-se de que você primeiro se sobreponha à chave de descriptografia do Kerberos da conta AzureADSSOAcc $, conforme explicado no [documento de perguntas frequentes ](how-to-connect-sso-faq.md)sob a pergunta relevante, caso contrário, o SSO contínuo não ocorrerá.

## <a name="check-status-of-feature"></a>Verificar o status do recurso

Verifique se o recurso SSO contínuo ainda está **habilitado** no seu locatário. Você pode verificar o status acessando o painel de **Azure ad Connect** no [centro de administração de Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: Painel de Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Clique para ver todas as florestas do AD que foram habilitadas para o SSO contínuo.

![Centro de administração do Azure Active Directory: Painel de SSO contínuo](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Motivos de falha de conexão no centro de administração de Azure Active Directory (precisa de uma licença Premium)

Se o seu locatário tiver uma licença de Azure AD Premium associada a ela, você também poderá examinar o [relatório de atividade de entrada](../reports-monitoring/concept-sign-ins.md) no [centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Azure Active Directory: Relatório de entradas](./media/tshoot-connect-sso/sso9.png)

Navegue até **Azure Active Directory** **entradas**  >  no [centro de administração do Azure Active Directory](https://aad.portal.azure.com/)e, em seguida, selecione uma atividade de entrada do usuário específico. Procure o campo **código de erro de entrada** . Mapeie o valor desse campo para um motivo de falha e resolução usando a seguinte tabela:

|Código de erro de entrada|Motivo da falha de conexão|Resolução
| --- | --- | ---
| 81001 | A permissão do Kerberos do utilizador é demasiado grande. | Reduza as adesões a grupos do utilizador e tente novamente.
| 81002 | Não é possível validar o tíquete Kerberos do usuário. | Consulte a [lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81003 | Não é possível validar o tíquete Kerberos do usuário. | Consulte a [lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81004 | Falha ao tentar a autenticação do Kerberos. | Consulte a [lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81008 | Não é possível validar o tíquete Kerberos do usuário. | Consulte a [lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81009 | Não é possível validar o tíquete Kerberos do usuário. | Consulte a [lista de verificação de solução de problemas](#troubleshooting-checklist).
| 81010 | O SSO integrado falhou porque a permissão do Kerberos do utilizador expirou ou é inválida. | O usuário precisa entrar de um dispositivo ingressado no domínio dentro de sua rede corporativa.
| 81011 | Não é possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. | Use Azure AD Connect para sincronizar as informações do usuário no Azure AD.
| 81012 | O usuário que está tentando entrar no Azure AD é diferente do usuário que está conectado ao dispositivo. | O usuário precisa entrar de um dispositivo diferente.
| 81013 | Não é possível localizar o objeto de usuário com base nas informações no tíquete Kerberos do usuário. |Use Azure AD Connect para sincronizar as informações do usuário no Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de solução de problemas

Use a seguinte lista de verificação para solucionar problemas de SSO contínuo:

- Verifique se o recurso SSO contínuo está habilitado no Azure AD Connect. Se você não puder habilitar o recurso (por exemplo, devido a uma porta bloqueada), verifique se você tem todos os [pré-requisitos](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) em vigor.
- Se você habilitou o [ingresso no Azure ad](../active-directory-azureadjoin-overview.md) e o SSO contínuo em seu locatário, verifique se o problema não é com o ingresso no Azure AD. O SSO do ingresso no Azure AD terá precedência sobre o SSO contínuo se o dispositivo estiver registrado no Azure AD e ingressado no domínio. Com o SSO do ingresso no Azure AD, o usuário vê um bloco de entrada que diz "conectado ao Windows".
- Verifique se a URL do Azure AD (https://autologon.microsoftazuread-sso.com) faz parte das configurações de zona da intranet do usuário.
- Verifique se o dispositivo corporativo está ingressado no domínio Active Directory. O dispositivo _não_ precisa ser [ingressado no Azure ad](../active-directory-azureadjoin-overview.md) para que o SSO contínuo funcione.
- Verifique se o usuário está conectado ao dispositivo por meio de uma conta de domínio Active Directory.
- Verifique se a conta do usuário é de uma floresta Active Directory em que o SSO contínuo foi configurado.
- Verifique se o dispositivo está conectado à rede corporativa.
- Verifique se a hora do dispositivo está sincronizada com o tempo no Active Directory e nos controladores de domínio, e se eles estão dentro de cinco minutos.
- Verifique se a conta de computador `AZUREADSSOACC` está presente e habilitada em cada floresta do AD que você deseja que o SSO contínuo esteja habilitado. Se a conta de computador tiver sido excluída ou estiver ausente, você poderá usar os [cmdlets do PowerShell](#manual-reset-of-the-feature) para recriá-las.
- Liste os tíquetes Kerberos existentes no dispositivo usando o comando `klist` de um prompt de comando. Verifique se os tíquetes emitidos para a conta de computador `AZUREADSSOACC` estão presentes. Os tíquetes Kerberos dos usuários normalmente são válidos por 10 horas. Você pode ter configurações diferentes em Active Directory.
- Se você tiver desabilitado e reabilitado o SSO contínuo em seu locatário, os usuários não terão a experiência de logon único até que seus tíquetes Kerberos em cache tenham expirado.
- Limpe os tíquetes Kerberos existentes do dispositivo usando o comando `klist purge` e tente novamente.
- Para determinar se há problemas relacionados ao JavaScript, examine os logs do console do navegador (em **ferramentas para desenvolvedores**).
- Examine os [logs do controlador de domínio](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Logs do controlador de domínio

Se você habilitar a auditoria de êxito em seu controlador de domínio, sempre que um usuário entrar por meio do SSO contínuo, uma entrada de segurança será registrada no log de eventos. Você pode encontrar esses eventos de segurança usando a consulta a seguir. (Procure o evento **4769** associado à conta de computador **AzureADSSOAcc $** .)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Redefinição manual do recurso

Se a solução de problemas não ajudar, você poderá redefinir manualmente o recurso em seu locatário. Siga estas etapas no servidor local em que você está executando Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passo 1: Importar o módulo do PowerShell do SSO contínuo

1. Primeiro, baixe e instale o [PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importe o módulo do PowerShell do SSO contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 2: Obter a lista de florestas Active Directory nas quais o SSO contínuo foi habilitado

1. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Quando solicitado, insira as credenciais de administrador global do seu locatário.
2. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas de Active Directory (consulte a lista "domínios") na qual esse recurso foi habilitado.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Passo 3: Desabilitar o SSO contínuo para cada floresta Active Directory em que você configurou o recurso

1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de administrador de domínio para a floresta Active Directory pretendida.

   > [!NOTE]
   >O nome de usuário das credenciais do administrador de domínio deve ser inserido no formato de nome da conta SAM (contoso\johndoe ou contoso. com\johndoe). Usamos a parte de domínio do nome de usuário para localizar o controlador de domínio do administrador de domínio usando DNS.

   >[!NOTE]
   >A conta de administrador de domínio usada não deve ser um membro do grupo usuários protegidos. Nesse caso, a operação falhará.

2. Chame `Disable-AzureADSSOForest -OnPremCredentials $creds`. Esse comando Remove a conta de computador `AZUREADSSOACC` do controlador de domínio local para essa floresta de Active Directory específica.
3. Repita as etapas anteriores para cada floresta Active Directory em que você configurou o recurso.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passo 4: Habilitar o SSO contínuo para cada floresta de Active Directory

1. Chame `Enable-AzureADSSOForest`. Quando solicitado, insira as credenciais de administrador de domínio para a floresta Active Directory pretendida.

   > [!NOTE]
   >O nome de usuário das credenciais do administrador de domínio deve ser inserido no formato de nome da conta SAM (contoso\johndoe ou contoso. com\johndoe). Usamos a parte de domínio do nome de usuário para localizar o controlador de domínio do administrador de domínio usando DNS.

   >[!NOTE]
   >A conta de administrador de domínio usada não deve ser um membro do grupo usuários protegidos. Nesse caso, a operação falhará.

2. Repita a etapa anterior para cada floresta Active Directory em que você deseja configurar o recurso.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5: Habilitar o recurso em seu locatário

Para ativar o recurso em seu locatário, chame `Enable-AzureADSSO -Enable $true`.
