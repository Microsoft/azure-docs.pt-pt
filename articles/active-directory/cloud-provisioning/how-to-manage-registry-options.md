---
title: 'Azure AD Connect agente de provisionamento em nuvem: Gerir opções de registo / Microsoft Docs'
description: Este artigo descreve como gerir as opções de registo no agente de provisão de nuvem Azure AD Connect.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371138"
---
# <a name="manage-agent-registry-options"></a>Gerir opções de registo de agentes

Esta secção descreve as opções de registo que pode definir para controlar o comportamento de processamento de tempo de execução do agente de provisionamento Azure AD Connect. 

## <a name="configure-ldap-connection-timeout"></a>Configurar o tempo limite de ligação LDAP
Ao efetuar operações LDAP em controladores de domínio ative configurados, por defeito, o agente de provisionamento utiliza o valor de tempo limite de ligação padrão de 30 segundos. Se o seu controlador de domínio demorar mais tempo a responder, poderá ver a seguinte mensagem de erro no ficheiro de registo do agente: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

As operações de pesquisa LDAP podem demorar mais tempo se o atributo de pesquisa não estiver indexado. Como primeiro passo, se obter o erro acima, verifique primeiro se o atributo de pesquisa/procura está [indexado](https://docs.microsoft.com/windows/win32/ad/indexed-attributes). Se os atributos de pesquisa estiverem indexados e o erro persistir, pode aumentar o tempo limite de ligação LDAP utilizando os seguintes passos: 

1. Inicie sessão como Administrador no servidor do Windows que executa o Agente de Provisionamento AZure AD Connect.
1. Utilize o item do menu *Executar* para abrir o editor de registo (regedit.exe) 
1. Localize a pasta chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique à direita e selecione "Novo valor de série de >"
1. Fornecer o nome: `LdapConnectionTimeoutInMilliseconds`
1. Clique duas vezes no **Nome do Valor** e introduza os dados de valor como `60000` milissegundos.
    > [!div class="mx-imgBorder"]
    > ![Tempo de ligação LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Reinicie o serviço de provisionamento Azure AD Connect da consola *De Serviços.*
1. Se tiver implantado vários agentes de provisionamento, aplique esta alteração de registo a todos os agentes para obter consistência. 

## <a name="configure-referral-chasing"></a>Configurar a perseguição de encaminhamento
Por predefinição, o agente de provisionamento Azure AD Connect não persegue [referências](https://docs.microsoft.com/windows/win32/ad/referrals). Pode querer permitir a perseguição de encaminhamento, para apoiar certos cenários de provisionamento de entrada de RH, tais como: 
* Verificando a singularidade da UPN em vários domínios
* Resolução de referências de gestores de domínios cruzados

Utilize os seguintes passos para ligar a perseguição de encaminhamento:

1. Inicie sessão como Administrador no servidor do Windows que executa o Agente de Provisionamento AZure AD Connect.
1. Utilize o item do menu *Executar* para abrir o editor de registo (regedit.exe) 
1. Localize a pasta chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique à direita e selecione "Novo valor de série de >"
1. Fornecer o nome: `ReferralChasingOptions`
1. Clique duas vezes no **Nome do Valor** e introduza os dados de valor como `96` . Este valor corresponde ao valor constante `ReferralChasingOptions.All` e especifica que as referências subárvores e de nível base serão seguidas pelo agente. 
    > [!div class="mx-imgBorder"]
    > ![Perseguição de referência](media/how-to-manage-registry-options/referral-chasing.png)
1. Reinicie o serviço de provisionamento Azure AD Connect da consola *De Serviços.*
1. Se tiver implantado vários agentes de provisionamento, aplique esta alteração de registo a todos os agentes para obter consistência.

> [!NOTE]
> Pode confirmar que as opções de registo foram definidas permitindo a [registo de verbose](how-to-troubleshoot.md#log-files). Os registos emitidos durante a arranque do agente apresentarão os valores config escolhidos no registo. 

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)

