---
title: 'Azure AD Connect agente de provisionamento em nuvem: Gerir opções de registo | Microsoft Docs'
description: Este artigo descreve como gerir as opções de registo no agente de provisão de nuvem Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678226"
---
# <a name="manage-agent-registry-options"></a>Gerir opções de registo de agentes

Esta secção descreve as opções de registo que pode definir para controlar o comportamento de processamento de tempo de execução do agente de provisionamento Azure AD Connect. 

## <a name="configure-ldap-connection-timeout"></a>Configurar o tempo limite de ligação LDAP
Ao efetuar operações LDAP em controladores de domínio ative configurados, por defeito, o agente de provisionamento utiliza o valor de tempo limite de ligação padrão de 30 segundos. Se o seu controlador de domínio demorar mais tempo a responder, poderá ver a seguinte mensagem de erro no ficheiro de registo do agente: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

As operações de pesquisa LDAP podem demorar mais tempo se o atributo de pesquisa não estiver indexado. Como primeiro passo, se obter o erro acima, verifique primeiro se o atributo de pesquisa/procura está [indexado](/windows/win32/ad/indexed-attributes). Se os atributos de pesquisa estiverem indexados e o erro persistir, pode aumentar o tempo limite de ligação LDAP utilizando os seguintes passos: 

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
Por predefinição, o agente de provisionamento Azure AD Connect não persegue [referências](/windows/win32/ad/referrals). Pode querer permitir a perseguição de encaminhamento, para apoiar certos cenários de provisionamento de entrada de RH, tais como: 
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

## <a name="skip-gmsa-configuration"></a>Ignore a configuração da GMSA
Com a versão 1.1.281.0+, por padrão, quando executa o assistente de configuração do agente, é solicitado que se configura a Conta de Serviço Gerido do [Grupo (GMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). A configuração GMSA pelo assistente é utilizada em tempo de execução para todas as operações de sincronização e provisionamento. 

Se estiver a atualizar a partir de uma versão anterior do agente e tiver configurado uma conta de serviço personalizada com permissões de nível OU delegadas específicas para a sua topologia do Ative Directory, poderá querer saltar/adiar a configuração da GMSA e planear esta alteração. 

> [!NOTE]
> Esta orientação aplica-se especificamente aos clientes que tenham configurado o fornecimento de RH (Workday/SuccessFactors) com versões de agente antes do 1.1.281.0 e tenham configurado uma conta de serviço personalizado para operações de agente. A longo prazo, recomendamos a mudança para GMSA como uma boa prática.  

Neste cenário, ainda pode atualizar os binários do agente e saltar a configuração GMSA utilizando os seguintes passos: 

1. Inicie sessão como Administrador no servidor do Windows que executa o Agente de Provisionamento AZure AD Connect.
1. Executar o instalador do agente para instalar os novos binários de agente. Feche o assistente de configuração do agente que se abre automaticamente após o sucesso da instalação. 
1. Utilize o item do menu *Executar* para abrir o editor de registo (regedit.exe) 
1. Localize a pasta chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique à direita e selecione "Novo valor DWORD de >"
1. Fornecer o nome: `UseCredentials`
1. Clique duas vezes no **Nome do Valor** e introduza os dados de valor como `1` .  
    > [!div class="mx-imgBorder"]
    > ![Usar credenciais](media/how-to-manage-registry-options/use-credentials.png)
1. Reinicie o serviço de provisionamento Azure AD Connect da consola *De Serviços.*
1. Se tiver implantado vários agentes de provisionamento, aplique esta alteração de registo a todos os agentes para obter consistência.
1. A partir do atalho do ambiente de trabalho, executar o assistente de configuração do agente. O assistente saltará a configuração GMSA. 


> [!NOTE]
> Pode confirmar que as opções de registo foram definidas permitindo a [registo de verbose](how-to-troubleshoot.md#log-files). Os registos emitidos durante a arranque do agente apresentarão os valores config escolhidos no registo. 

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)

