---
title: 'Azure Ative Directory Connect: Troubleshoot Seamless Single Sign-On [ Microsoft Docs'
description: Este tópico descreve como resolver problemas Azure Ative Directory Seamless Single Sign-On
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
ms.openlocfilehash: 759748124893a8f906a4bc336f835546202b0b62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80049499"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Troubleshoot Azure Ative Directory Seamless Single Sign-On

Este artigo ajuda-o a encontrar informações sobre problemas comuns no que diz respeito ao Diretório Ativo Azure (Azure AD) Single Sign-On (Seamless SSO).

## <a name="known-issues"></a>Problemas conhecidos

- Em alguns casos, permitir o SSO sem emenda pode demorar até 30 minutos.
- Se você desativar e reativar O SSO Sem Emenda no seu inquilino, os utilizadores não terão a única experiência de inscrição até que os seus bilhetes Kerberos em cache, normalmente válidos por 10 horas, tenham expirado.
- Se o SSO Seamless for bem sucedido, o utilizador não tem a oportunidade de selecionar **Mantenha-me inscrito em**. Devido a este comportamento, os cenários de [mapeamento SharePoint e OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) não funcionam.
- Os clientes do Office 365 Win32 (Outlook, Word, Excel, entre outros) com versões 16.0.8730.xxxx e acima são suportados utilizando um fluxo não interativo. Outras versões não são suportadas; nessas versões, os utilizadores introduzirão os seus nomes de utilizador, mas não palavras-passe, para iniciars o seu sessão. Para o OneDrive, terá de ativar a [função de config silencioso OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de inscrição silenciosa.
- O SSO sem emenda não funciona em modo de navegação privada no Firefox.
- O SSO sem emenda não funciona no Internet Explorer quando o modo protected melhorado é ligado.
- O SSO sem emenda não funciona em navegadores móveis no iOS e Android.
- Se um utilizador fizer parte de demasiados grupos no Ative Directory, o bilhete Kerberos do utilizador será provavelmente demasiado grande para ser processado, o que fará com que o SSO sem emenda falhe. Os pedidos Azure AD HTTPS podem ter cabeçalhos com um tamanho máximo de 50 KB; Os bilhetes kerberos precisam de ser menores do que esse limite para acomodar outros artefactos Azure AD (tipicamente, 2 - 5 KB) como cookies. A nossa recomendação é reduzir os membros do grupo dos utilizadores e tentar novamente.
- Se estiver a sincronizar 30 ou mais florestas de DirectórioActivo, não pode ativar o SSO sem emenda através do Azure AD Connect. Como suposição, pode [ativar manualmente](#manual-reset-of-the-feature) a funcionalidade no seu inquilino.
- Adicionar o URL de serviço`https://autologon.microsoftazuread-sso.com`Azure AD () à zona de sites Fidedignos em vez da zona intranet local *impede os utilizadores de iniciar a sessão*.
- O SSO sem emenda suporta os tipos de encriptação AES256_HMAC_SHA1, AES128_HMAC_SHA1 e RC4_HMAC_MD5 para kerberos. Recomenda-se que o tipo de encriptação da conta AzureADSSOAcc$ seja definido para AES256_HMAC_SHA1, ou um dos tipos AES vs RC4 para maior segurança. O tipo de encriptação é armazenado nos Tipos de Encriptação Suportados msDS atributos da conta no seu Diretório Ativo.  Se o tipo de encriptação da conta AzureADSSOAcc$ estiver definido para RC4_HMAC_MD5, e quiser alterá-lo para um dos tipos de encriptação AES, certifique-se de que primeiro passa a chave de desencriptação Kerberos da conta AzureADSSOAcc$ como explicado no [documento faq](how-to-connect-sso-faq.md) sob a questão relevante, caso contrário não acontecerá O SSO Sso Sso.

## <a name="check-status-of-feature"></a>Verifique o estado da funcionalidade

Certifique-se de que a funcionalidade SSO SSO sem emenda ainda está **ativada** no seu inquilino. Pode verificar o estado indo ao painel **Azure AD Connect** no centro de [administração do Azure Ative Directory](https://aad.portal.azure.com/).

![Centro de administração de Diretório Ativo Azure: Painel azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Clique para ver todas as florestas aD que foram ativadas para SSO Sso sem emenda.

![Centro de administração de Diretório Ativo Azure: Painel SSO sem emenda](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Razões de insucesso no centro de administração do Diretório Ativo Azure (precisa de uma licença Premium)

Se o seu inquilino tiver uma licença Azure AD Premium associada a ela, também pode consultar o [relatório de atividades](../reports-monitoring/concept-sign-ins.md) de entrada no centro de [administração do Azure Ative Directory](https://aad.portal.azure.com/).

![Centro de administração do Diretório Ativo Azure: Relatório de inscrições](./media/tshoot-connect-sso/sso9.png)

Navegue até > **aos Sign-ins** de **Diretório Ativo Azure**no centro de administração do [Diretório Ativo Azure](https://aad.portal.azure.com/)e, em seguida, selecione a atividade de entrada de um utilizador específico. Procure o campo CÓDIGO DE **ERRO SIGN-IN.** Mapeie o valor desse campo para uma razão de falha e resolução utilizando a seguinte tabela:

|Código de erro de insessão|Razão de falha de inscrição|Resolução
| --- | --- | ---
| 81001 | A permissão do Kerberos do utilizador é demasiado grande. | Reduza as adesões a grupos do utilizador e tente novamente.
| 81002 | Incapaz de validar o bilhete Kerberos do utilizador. | Consulte a lista de verificação de [problemas.](#troubleshooting-checklist)
| 81003 | Incapaz de validar o bilhete Kerberos do utilizador. | Consulte a lista de verificação de [problemas.](#troubleshooting-checklist)
| 81004 | Falha ao tentar a autenticação do Kerberos. | Consulte a lista de verificação de [problemas.](#troubleshooting-checklist)
| 81008 | Incapaz de validar o bilhete Kerberos do utilizador. | Consulte a lista de verificação de [problemas.](#troubleshooting-checklist)
| 81009 | Incapaz de validar o bilhete Kerberos do utilizador. | Consulte a lista de verificação de [problemas.](#troubleshooting-checklist)
| 81010 | O SSO integrado falhou porque a permissão do Kerberos do utilizador expirou ou é inválida. | O utilizador precisa de iniciar sessão a partir de um dispositivo unido ao domínio dentro da sua rede corporativa.
| 81011 | Incapaz de encontrar o objeto do utilizador com base na informação no bilhete Kerberos do utilizador. | Utilize o Azure AD Connect para sincronizar as informações do utilizador em Azure AD.
| 81012 | O utilizador que tenta iniciar sessão no Azure AD é diferente do utilizador que está inscrito no dispositivo. | O utilizador precisa de iniciar sessão a partir de um dispositivo diferente.
| 81013 | Incapaz de encontrar o objeto do utilizador com base na informação no bilhete Kerberos do utilizador. |Utilize o Azure AD Connect para sincronizar as informações do utilizador em Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista de verificação de resolução de problemas (Troubleshooting checklist)

Utilize a seguinte lista de verificação para resolver problemas sem problemas:

- Certifique-se de que a função SSO sem emenda está ativada no Azure AD Connect. Se não conseguir ativar a funcionalidade (por exemplo, devido a uma porta bloqueada), certifique-se de que tem todos os [pré-requisitos](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) no lugar.
- Se você permitiu a [Azure AD Join](../active-directory-azureadjoin-overview.md) e Seamless SSO no seu inquilino, certifique-se de que o problema não está com a Azure AD Join. O SSO da Azure AD Join tem precedência sobre o SSO SSO sem emenda se o dispositivo estiver registado com AD Azure e junto ao domínio. Com o SSO do Azure AD Junte-se ao utilizador e vê um azulejo de entrada que diz "Conectado ao Windows".
- Certifique-se de que o`https://autologon.microsoftazuread-sso.com`URL Azure AD () faz parte das definições da zona intranet do utilizador.
- Certifique-se de que o dispositivo corporativo está unido ao domínio do Diretório Ativo. O dispositivo _não_ precisa de ser [Azure AD Joined](../active-directory-azureadjoin-overview.md) for Seamless SSO to work.
- Certifique-se de que o utilizador está ligado ao dispositivo através de uma conta de domínio Ative Directory.
- Certifique-se de que a conta do utilizador é de uma floresta de Diretório Ativo onde foi criado o SSO SSO sem emenda.
- Certifique-se de que o dispositivo está ligado à rede corporativa.
- Certifique-se de que o tempo do dispositivo é sincronizado com o tempo tanto no Diretório Ativo como nos controladores de domínio, e que estão dentro de cinco minutos um do outro.
- Certifique-se `AZUREADSSOACC` de que a conta de computador está presente e ativada em cada floresta AD que deseja SSO SSO sem emenda. Se a conta de computador tiver sido eliminada ou estiver em falta, pode utilizar [cmdlets PowerShell](#manual-reset-of-the-feature) para recriá-las.
- Enumere os bilhetes kerberos `klist` existentes no dispositivo utilizando o comando a partir de um pedido de comando. Certifique-se de que `AZUREADSSOACC` os bilhetes emitidos para a conta do computador estão presentes. Os bilhetes kerberos dos utilizadores são normalmente válidos por 10 horas. Pode ter configurações diferentes no Diretório Ativo.
- Se você desativar e reativar O SSO SSO sem emenda no seu inquilino, os utilizadores não terão a única experiência de inscrição até que os seus bilhetes kerberos em cache tenham expirado.
- Purgue os bilhetes kerberos existentes do dispositivo usando o `klist purge` comando, e tente novamente.
- Para determinar se existem problemas relacionados com o JavaScript, reveja os registos de consola do navegador (em **Ferramentas de Desenvolvimento).**
- Reveja os [registos](#domain-controller-logs)do controlador de domínio .

### <a name="domain-controller-logs"></a>Registos de controlador esporásta

Se permitir uma auditoria de sucesso no seu controlador de domínio, então sempre que um utilizador inicia sessão através do SSO Sem emenda, uma entrada de segurança é registada no registo do evento. Você pode encontrar estes eventos de segurança usando a seguinte consulta. (Procure o evento **4769** associado à conta de computador **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Reset manual da funcionalidade

Se a resolução de problemas não ajudou, pode redefinir manualmente a funcionalidade no seu inquilino. Siga estes passos no servidor no local onde está a executar o Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Passo 1: Importar o módulo SSO PowerShell sem emenda

1. Primeiro, descarregue e [instale o Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
2. Navegue na `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
3. Importar o módulo SSO PowerShell sem emenda utilizando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 2: Obtenha a lista de florestas de Diretório Ativo em que o SSO SSO foi ativado

1. Execute o PowerShell como um administrador. Na PowerShell, `New-AzureADSSOAuthenticationContext`ligue. Quando solicitado, insira as credenciais de administrador global do seu inquilino.
2. Ligue. `Get-AzureADSSOStatus` Este comando fornece-lhe a lista de florestas de Diretório Ativo (veja a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Passo 3: Desativar SSO sem emenda para cada floresta de Diretório Ativo onde configurao a funcionalidade

1. Ligue. `$creds = Get-Credential` Quando solicitado, introduza as credenciais do administrador de domínio para a floresta de Diretório Ativo pretendida.

   > [!NOTE]
   >As credenciais de utilizador do administrador de domínio devem ser inseridas no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

2. Ligue. `Disable-AzureADSSOForest -OnPremCredentials $creds` Este comando remove `AZUREADSSOACC` a conta do computador do controlador de domínio no local para esta floresta específica de Diretório Ativo.
3. Repita os passos anteriores para cada floresta de Diretório Ativo onde configura a funcionalidade.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passo 4: Habilitar O SSO sem emenda para cada floresta de diretório ativo

1. Ligue. `Enable-AzureADSSOForest` Quando solicitado, introduza as credenciais do administrador de domínio para a floresta de Diretório Ativo pretendida.

   > [!NOTE]
   >As credenciais de utilizador do administrador de domínio devem ser inseridas no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

2. Repita o passo anterior para cada floresta de Diretório Ativo onde pretende configurar a funcionalidade.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5. Ativar a funcionalidade no seu inquilino

Para ligar o recurso no `Enable-AzureADSSO -Enable $true`seu inquilino, ligue.
