---
title: 'Azure AD Connect: O que é o Módulo ADConnectivityTool PowerShell / Microsoft Docs'
description: Este documento introduz o novo módulo ADConnectivity PowerShell e como pode ser usado para ajudar a resolver problemas.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85360185"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Resolução de problemas A ligação AD AD com o módulo ADConnectivityTool PowerShell

A ferramenta ADConnectivity é um módulo PowerShell que é utilizado num dos seguintes:

- Durante a instalação, quando um problema de conectividade de rede impede a validação bem sucedida das credenciais do Ative Directory, o utilizador fornecido no Assistente.
- Post instalação por um utilizador que ligue para as funções a partir de uma sessão PowerShell.

A ferramenta está localizada em: **C:\Program Files\Microsoft Azure Ative Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool durante a instalação

Na página 'Ligar os **directórios',** no Assistente de Ligação AD Azure, se ocorrer um problema de rede, o ADConnectivityTool utilizará automaticamente uma das suas funções para determinar o que se passa.  Qualquer um dos seguintes pode ser considerado problemas de rede:

- O nome da Floresta que o utilizador forneceu foi digitado erradamente, ou disse floresta não existe 
- A porta UDP 389 está fechada nos Controladores de Domínio associados à Floresta que o utilizador forneceu
- As credenciais fornecidas na janela "conta de floresta AD" não têm privilégios para recuperar os Controladores de Domínio associados à Floresta Alvo
- Qualquer uma das portas TCP 53, 88 ou 389 estão fechadas nos Controladores de Domínio associados à Floresta que o utilizador forneceu 
- Tanto o UDP 389 como uma porta TCP (ou portas) estão fechados
- O DNS não pôde ser resolvido para a floresta fornecida e\ou os seus controladores de domínio associados

Sempre que algum destes problemas for encontrado, uma mensagem de erro relacionada é exibida no Assistente AADConnect:


![Erro](media/how-to-connect-adconnectivitytools/error1.png)

Por exemplo, quando estamos a tentar adicionar um diretório no ecrã De ligar os **seus diretórios,** o Azure AD Connect precisa de verificar isso e espera ser capaz de comunicar com um controlador de domínio sobre a porta 389.  Se não puder, veremos o erro que é mostrado na imagem acima.  

O que está realmente a acontecer nos bastidores é que o Azure AD Connect está a chamar a `Start-NetworkConnectivityDiagnosisTools` função.  Esta função é chamada quando a validação de credenciais falha devido a um problema de conectividade de rede.

Finalmente, é gerado um ficheiro de registo detalhado sempre que a ferramenta é chamada do assistente. O registo está localizado em **C:\ProgramData\AADConnect\ADConnectivityTool- \<date> - \<time> .log**

## <a name="adconnectivitytools-post-installation"></a>Instalação pós-ADConnectivityTools
Depois de o Azure AD Connect ter sido instalado, qualquer uma das funções do módulo PowerShell ADConnectivityTools pode ser utilizada.  

Pode encontrar informações de referência sobre as funções na [Referência ADConnectivityTools](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Vamos chamar esta função porque **só** pode ser chamada manualmente uma vez que o ADConnectivityTool.psm1 tenha sido importado para o PowerShell. 

Esta função executa a mesma lógica que o Assistente de Ligação AD Azure executa para validar as credenciais de AD fornecidas.  No entanto, fornece uma explicação muito mais verbosa sobre o problema e uma solução sugerida. 

A validação da conectividade consiste nos seguintes passos:
-   Obtenha objeto FQDN de domínio (nome de domínio totalmente qualificado)
-   Valide que, se o utilizador selecionado 'Criar nova conta AD', estas credenciais pertencem ao grupo de Administradores empresariais
-   Obtenha objeto FQDN florestal
-   Confirme que pelo menos um domínio associado ao objeto FQDN florestal previamente obtido é alcançável
-   Verifique se o nível funcional da floresta é o Windows Server 2003 ou superior.

O utilizador poderá adicionar um Diretório se todas estas ações forem executadas com sucesso.

Se o utilizador executa esta função depois de resolver um problema (ou se não houver qualquer problema), a saída indicará para o utilizador voltar ao Assistente de Ligação AD Azure e tentar introduzir novamente as credenciais.



## <a name="next-steps"></a>Passos Seguintes
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- [Instalação Express](how-to-connect-install-express.md)
- [Instalação personalizada](how-to-connect-install-custom.md)
- [Referência de ADConnectivityTools](reference-connect-adconnectivitytools.md)

