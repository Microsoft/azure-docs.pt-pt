---
title: 'Azure AD Connect: O que é o módulo do PowerShell ADConnectivityTool | Documentos da Microsoft'
description: Este documento apresenta o novo módulo do PowerShell de ADConnectivity
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b2471b28132b26c41844b620f6dcf49e3802a6e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436715"
---
# <a name="what-is-the-adconnectivitytool-powershell-module"></a>O que é o módulo do PowerShell ADConnectivityTool?

A ferramenta de ADConnectivity é um módulo do PowerShell que é utilizado em um dos seguintes:

- Durante a instalação quando um problema de conectividade de rede é impede que a validação bem-sucedida das credenciais do Active Directory fornecida no assistente pelo utilizador.
- Após a instalação por um usuário que chama as funções a partir de uma sessão do PowerShell.

A ferramenta está localizada em: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool durante a instalação

Sobre o **ligar os seus diretórios** página, o Assistente do Azure AD Connect, se um problema de rede ocorre, o will ADConnectivityTool automaticamente utilizar uma das suas funções para determinar o que está acontecendo.  Qualquer um dos seguintes podem ser considerados problemas de rede:

- O nome da floresta fornecida pelo utilizador foi escrito de rostos, ou floresta disse não existe 
- A porta UDP 389 está fechada em controladores de domínio associados com a floresta que o usuário forneceu
- As credenciais fornecidas na janela de 'Conta AD de floresta' não tem privilégios para obter os controladores de domínio associados com a floresta de destino
- Qualquer uma das portas TCP 53, 88 ou 389 estão fechadas em controladores de domínio associados com a floresta que o usuário forneceu 
- Tanto UDP 389 e uma porta TCP (ou portas) estão fechadas
- DNS não pode ser resolvido para a e/ou de floresta fornecido seus controladores de domínio associado

Sempre que algum desses problemas seja localizado, é apresentada uma mensagem de erro relacionadas no Assistente do AADConnect:


![Erro](media/how-to-connect-adconnectivitytools/error1.png)

Por exemplo, quando estamos está a tentar adicionar um diretório no **ligar os seus diretórios** ecrã, o Azure AD Connect precisa verificar se isso e deve ser capaz de comunicar com um controlador de domínio através da porta 389.  Se não for possível, veremos o erro que é mostrado na captura de ecrã acima.  

O que realmente está acontecendo nos bastidores, é que o Azure AD Connect está chamando o `Start-NetworkConnectivityDiagnosisTools` função.  Esta função é chamada quando a validação de credenciais falhar devido a um problema de conectividade de rede.

Por fim, um arquivo de log detalhados é gerado sempre que a ferramenta é chamada a partir do assistente. O registo está localizado em **C:\ProgramData\AADConnect\ADConnectivityTool-\<data >-\<tempo >. log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools pós-instalação
Depois de ter sido instalado o Azure AD Connect, pode ser utilizada qualquer uma das funções no módulo do ADConnectivityTools PowerShell.  

Pode encontrar informações de referência sobre as funções no [ADConnectivityTools referência](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Vamos chamar esta função porque ele pode **apenas** chamado manualmente depois do ADConnectivityTool.psm1 tem sido importados para o PowerShell. 

Esta função executa a mesma lógica que executa o Azure AD ligar Assistente para validar as credenciais fornecidas do AD.  No entanto, ele fornece uma explicação muito mais detalhada sobre o problema e uma solução de sugestão. 

A validação de conectividade inclui os seguintes passos:
-   Obter o objeto de domínio FQDN (nome de domínio completamente qualificado)
-   Validar a que, se o usuário selecionar "Create AD nova conta", estas credenciais de pertencer ao grupo Administradores da empresa
-   Objeto FQDN da floresta de GET
-   Confirme que pelo menos um domínio associado ao objeto obtido anteriormente do FQDN da floresta está acessível
-   Certifique-se de que o nível funcional da floresta for Windows Server 2003 ou superior.

O utilizador será possível adicionar um diretório se todas estas ações foram executadas com êxito.

Se o usuário executa essa função, após um problema é resolvido (ou se não há problema existe em todos os) indica a saída para o usuário voltar para o Assistente do Azure para ligar AD e experimente inserir as credenciais novamente.



## <a name="next-steps"></a>Próximos Passos
- [Azure AD Connect: contas e permissões](reference-connect-accounts-permissions.md)
- [Instalação rápida](how-to-connect-install-express.md)
- [Instalação personalizada](how-to-connect-install-custom.md)
- [Referência de ADConnectivityTools](reference-connect-adconnectivitytools.md)

