---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 6d95e40623f17a39145778a2fc067dccc68fd872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95553898"
---
## <a name="steps-to-enable-single-sign-on"></a>Passos para permitir o único sinal de inscrição
O fornecimento de nuvens funciona com o Single Sign-on.  Atualmente não existe uma opção para ativar sSO quando o agente está instalado, no entanto pode usar os passos abaixo para ativar sSO e usá-lo. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Passo 1: Descarregar e extrair ficheiros Azure AD Connect
1.  Primeiro, baixe a versão mais recente do [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Abra um pedido de comando usando privilégios administrativos e navegue para o MSI que acabou de descarregar.
3.  Executar o seguinte:  `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Altere o filepa e a dobradinha para combinar com o seu percurso de ficheiro e o nome da sua pasta de extração.  O conteúdo deve estar agora na pasta de extração.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Passo 2: Importar o módulo SSO PowerShell sem costura

1. Faça o download e [instale o Azure AD PowerShell](/powershell/azure/active-directory/overview).
2. Navegue na `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
3. Importe o módulo SSO PowerShell sem costura utilizando este comando: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Passo 3: Obtenha a lista de florestas de Diretório Ativo nas quais o SSO sem emenda foi ativado

1. Execute o PowerShell como um administrador. Em PowerShell, `New-AzureADSSOAuthenticationContext` ligue. Quando solicitado, insira as credenciais globais de administrador do seu inquilino.
2. `Get-AzureADSSOStatus`Ligue. Este comando fornece-lhe a lista de florestas de Diretório Ativo (veja a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Passo 4: Ativar SSO sem emenda para cada floresta de Diretório Ativo

1. `Enable-AzureADSSOForest`Ligue. Quando solicitado, insira as credenciais de administrador de domínio para a floresta de Diretório Ativo pretendida.

   > [!NOTE]
   >O nome de utilizador de credenciais de administrador de domínio deve ser inserido no formato nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo de Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

2. Repita o passo anterior para cada floresta ative onde pretende configurar a funcionalidade.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Passo 5. Ativar a funcionalidade no seu inquilino

Para ligar a funcionalidade no seu inquilino, `Enable-AzureADSSO -Enable $true` ligue.