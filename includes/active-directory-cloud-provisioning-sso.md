---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504367"
---
## <a name="steps-to-enable-single-sign-on"></a>Passos para permitir o único signo
O fornecimento de nuvem funciona com o Single Sign-on.  Atualmente não existe uma opção para ativar o SSO quando o agente está instalado, no entanto pode utilizar os passos abaixo para ativar o SSO e utilizá-lo. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Passo 1: Transferir e extrair ficheiros Azure AD Connect
1.  Primeiro, descarregue a versão mais recente do [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Abra um pedido de comando usando privilégios administrativos e navegue para o msi que acabou de descarregar.
3.  Executar o seguinte:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Altere o ficheiro e a pasta de extração para combinar com o seu caminho de ficheiro e o nome da sua pasta de extração.  O conteúdo deve agora estar na pasta de extração.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Passo 2: Importar o módulo SSO PowerShell sem emenda

1. Descarregue e [instale o Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
2. Navegue na `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
3. Importar o módulo SSO PowerShell sem emenda utilizando este comando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 3: Obtenha a lista de florestas de Diretório Ativo em que o SSO SSO foi ativado

1. Execute o PowerShell como um administrador. Na PowerShell, `New-AzureADSSOAuthenticationContext`ligue. Quando solicitado, insira as credenciais de administrador global do seu inquilino.
2. Ligue. `Get-AzureADSSOStatus` Este comando fornece-lhe a lista de florestas de Diretório Ativo (veja a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passo 4: Habilitar O SSO sem emenda para cada floresta de diretório ativo

1. Ligue. `Enable-AzureADSSOForest` Quando solicitado, introduza as credenciais do administrador de domínio para a floresta de Diretório Ativo pretendida.

   > [!NOTE]
   >As credenciais de utilizador do administrador de domínio devem ser inseridas no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

2. Repita o passo anterior para cada floresta de Diretório Ativo onde pretende configurar a funcionalidade.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5. Ativar a funcionalidade no seu inquilino

Para ligar o recurso no `Enable-AzureADSSO -Enable $true`seu inquilino, ligue.
