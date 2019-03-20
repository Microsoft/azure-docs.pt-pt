---
title: Adicionar a nomenclatura de política de início rápido para grupos do Office 365 - Azure Active Directory | Documentos da Microsoft
description: Explica como adicionar novos utilizadores ou eliminar os utilizadores existentes no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f2219e038d3432807c81246256873a1ecb2cd9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093478"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Início rápido: Política de nomenclatura para os grupos no Azure Active Directory

Neste início rápido, irá configurar a política de nomenclatura no inquilino do Azure Active Directory (Azure AD) para grupos do Office 365 criados pelo utilizador, para o ajudar a ordenar e procurar os grupos do seu inquilino. Por exemplo, pode utilizar a política de nomenclatura para:

* Comunicar a função de um grupo, associação, região geográfica ou quem criou o grupo.
* Ajudar a categorizar grupos no livro de endereços.
* Bloquear a utilização de palavras específicas em nomes de grupos e aliases.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="install-powershell-cmdlets"></a>Instalar cmdlets do PowerShell

Certifique-se de que desinstala qualquer versão anterior do módulo Azure Active Directory PowerShell para Graph para o Windows PowerShell e instala o [Azure Active Directory PowerShell para Graph - Versão de Pré-visualização Pública 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) antes de executar os comandos do PowerShell. 

1. Abra a aplicação Windows PowerShell como administrador.
2. Desinstale qualquer versão anterior do AzureADPreview.
  
   ```
   Uninstall-Module AzureADPreview
   ```
3. Instale a versão mais recente do AzureADPreview.
  
   ```
   Install-Module AzureADPreview
   ```
   Se lhe for pedido para aceder a um repositório não fidedigno, escreva **Y**. Pode demorar alguns minutos a instalar o novo módulo.

## <a name="set-up-naming-policy"></a>Configurar a política de nomenclatura

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>Passo 1: Inicie sessão com cmdlets do PowerShell

1. Abra a aplicação Windows PowerShell. Não precisa de privilégios elevados.

2. Execute os seguintes comandos para preparar a execução dos cmdlets.
  
   ```
   Import-Module AzureADPreview
   Connect-AzureAD
   ```
   No ecrã **Iniciar sessão na sua conta** apresentado, introduza a conta de administrador e a palavra-passe para ligar ao serviço e selecione **Iniciar sessão**.

3. Siga os passos em [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md) para criar definições de grupo para este inquilino.

### <a name="step-2-view-the-current-settings"></a>Passo 2: Ver as definições atuais

1. Veja as definições atuais da política de nomenclatura.
  
   ```
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
2. Apresente as definições atuais do grupo.
  
   ```
   $Setting.Values
   ```
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>Passo 3: Definir a política de nomes e quaisquer palavras bloqueadas personalizadas

1. Defina os prefixos e sufixos de nomes de grupo no Azure AD PowerShell. Para a funcionalidade funcione corretamente, [GroupName] têm de ser incluído na definição.
  
   ```
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
2. Defina as palavras bloqueadas personalizadas que quer restringir. O exemplo seguinte ilustra como pode adicionar as suas próprias palavras personalizadas.
  
   ```
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
3. Guarde as definições da nova política para serem aplicadas, como no exemplo a seguir.
  
   ```
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Já está. Definiu a política de nomenclatura e adicionou palavras bloqueadas personalizadas.

## <a name="clean-up-resources"></a>Limpar recursos

1. Vazio os prefixos de nome de grupo e sufixos no Azure AD PowerShell.
  
   ```
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
2. Vazio as palavras bloqueadas personalizadas.
  
   ```
   $Setting["CustomBlockedWordsList"]=""
   ```
  
3. Guarde as definições.
  
   ```
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a utilizar cmdlets do PowerShell para definir a política de nomenclatura para o inquilino do Azure AD.

Para obter mais informações sobre restrições técnicas, adicionar uma lista de palavras bloqueadas personalizadas ou as experiências de utilizador final nas aplicações do Office 365, avance para o artigo seguinte para obter detalhes sobre a política de nomenclatura.
> [!div class="nextstepaction"]
> [Todos os detalhes da política de nomenclatura](groups-naming-policy.md)
