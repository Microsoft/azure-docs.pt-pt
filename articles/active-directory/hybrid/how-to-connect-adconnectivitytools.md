---
title: 'Azure AD Connect: O que é o Módulo PowerShell ADConnectivityTool / Microsoft Docs'
description: Este documento introduz o novo módulo ADConnectivity PowerShell e como pode ser usado para ajudar a resolver problemas.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "64571127"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Conectividade AD De Saque de Problemas com o módulo ADConnectivityTool PowerShell

A ferramenta ADConnectivity é um módulo PowerShell que é usado num dos seguintes:

- Durante a instalação quando um problema de conectividade de rede impede a validação bem sucedida das credenciais de Diretório Ativo que o utilizador forneceu no Assistente.
- Instalação de correio por um utilizador que ligue para as funções a partir de uma sessão powerShell.

A ferramenta está localizada em: **C:\Program Files\Microsoft Azure Ative Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>Ferramenta ADConnectivity durante a instalação

Na página **Connect your diretórios,** no Assistente de Ligação AD Azure, se ocorrer um problema de rede, o ADConnectivityTool utilizará automaticamente uma das suas funções para determinar o que se passa.  Qualquer uma das seguintes questões de rede pode ser considerada:

- O nome da Floresta que o utilizador forneceu foi dactilografado erradamente, ou dito Floresta não existe 
- A porta UDP 389 está fechada nos Controladores de Domínio associados à Floresta que o utilizador forneceu
- As credenciais fornecidas na janela "Conta Florestal AD" não têm privilégios para recuperar os Controladores de Domínio associados à Floresta Alvo
- Qualquer uma das portas TCP 53, 88 ou 389 estão fechadas nos Controladores de Domínio associados à Floresta que o utilizador forneceu 
- Tanto a UDP 389 como uma porta TCP (ou portas) estão fechadas
- O DNS não pôde ser resolvido para a floresta fornecida e\ou para os seus controladores de domínio associados

Sempre que algum destes problemas for encontrado, uma mensagem de erro relacionada é exibida no Assistente AADConnect:


![Erro](media/how-to-connect-adconnectivitytools/error1.png)

Por exemplo, quando estamos a tentar adicionar um diretório no ecrã **Connect dos seus diretórios,** o Azure AD Connect precisa de verificar isso e espera poder comunicar com um controlador de domínio sobre a porta 389.  Se não puder, veremos o erro que é mostrado na imagem acima.  

O que está realmente a acontecer nos bastidores, `Start-NetworkConnectivityDiagnosisTools` é que o Azure AD Connect está a chamar a função.  Esta função é chamada quando a validação de credenciais falha devido a um problema de conectividade da rede.

Finalmente, um ficheiro de registo detalhado é gerado sempre que a ferramenta é chamada do assistente. O registo está localizado em **C:\ProgramData\AADConnect\ADConnectivityTool-date\<>-time\<>.log**

## <a name="adconnectivitytools-post-installation"></a>Instalação de posta ADConnectivityTools
Depois de instalado o Azure AD Connect, qualquer uma das funções do módulo ADConnectivityTools PowerShell pode ser utilizada.  

Pode encontrar informações de referência sobre as funções na [Referência ADConnectivityTools](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Validação de início de conectividade

Vamos chamar esta função porque **só** pode ser chamada manualmente depois de o ADConnectivityTool.psm1 ter sido importado para o PowerShell. 

Esta função executa a mesma lógica que o Assistente de Ligação AD Azure corre para validar as credenciais AD fornecidas.  No entanto, fornece uma explicação muito mais verbosa sobre o problema e uma solução sugerida. 

A validação da conectividade consiste nos seguintes passos:
-   Obtenha objeto De Domínio FQDN (nome de domínio totalmente qualificado)
-   Valide que, se o utilizador selecionado 'Criar nova conta AD', estas credenciais pertencem ao grupo de Administradores empresariais
-   Obtenha objeto Forest FQDN
-   Confirme que pelo menos um domínio associado ao objeto FQDN florestal previamente obtido é alcançável
-   Verifique se o nível funcional da floresta é o Windows Server 2003 ou superior.

O utilizador poderá adicionar um Diretório se todas estas ações forem executadas com sucesso.

Se o utilizador executar esta função depois de um problema ser resolvido (ou se não houver nenhum problema) a saída indicará que o utilizador volte ao Assistente de Ligação AD Azure e tente inserir novamente as credenciais.



## <a name="next-steps"></a>Passos Seguintes
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- [Instalação Express](how-to-connect-install-express.md)
- [Instalação Personalizada](how-to-connect-install-custom.md)
- [Referência de ADConnectivityTools](reference-connect-adconnectivitytools.md)

