---
title: Os dados de auditoria de resolução de problemas da alteração verificada do domínio | Microsoft Docs
description: Fornece-lhe informações que aparecerão nos registos de atividade do Azure Ative Directory quando alterar um domínio verificado pelos utilizadores.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87117437"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Resolução de problemas: Dados de auditoria sobre a alteração verificada do domínio 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Tenho muitas alterações nos meus utilizadores e não sei qual é a causa.

### <a name="symptoms"></a>Sintomas

Verifico os registos de auditoria da AZure AD e vejo várias atualizações de utilizadores a ocorrerem no meu inquilino AZure AD. Estes eventos **do Utilizador de Atualização** não exibem informações **do Ator,** o que causa incerteza quanto ao que/quem desencadeou as alterações em massa para os utilizadores. 

### <a name="cause"></a>Causa

 Uma razão comum por trás das mudanças de objetos de massa é uma operação de backend não sincronizada chamada **ProxyCalc**.  **ProxyCalc** é a lógica que determina os endereços de Nome e Proxy **do UserPrincipal** **adequados,** que são atualizados em utilizadores, grupos ou contactos Azure AD. O design por trás **do ProxyCalc** é garantir que todos os **endereços** **UserPrincipalName** e Proxy são consistentes em Azure AD a qualquer momento. **O ProxyCalc** deve ser desencadeado por uma mudança explícita como uma mudança de domínio verificada e não é executado perpetuamente em segundo plano como uma tarefa. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>O que significa consistência do Nome Do Utilizador? 

Para os utilizadores apenas na nuvem, a consistência significa que o **Nome Do UtilizadorPrincipal** está definido para um sufixo de domínio verificado. Quando um **Nome Usáccipo** inconsistente é processado, o **ProxyCalc** converte-o no sufixo de onmicrosoft.com predefinido, por exemplo: username@Contoso.onmicrosoft.com 

Para utilizadores sincronizados, a consistência significa que o **Nome Do UtilizadorPrincipal** está definido para um sufixo de domínio verificado e corresponde ao valor do **Nome Do Utilizador** nas instalações (ShadowUserPrincipalName). Quando um **Nome De UtilizadorPrincipal** inconsistente for processado, o **ProxyCalc** reverterá para o mesmo valor que o **ShadowUserPrincipalName** ou, caso o sufixo de domínio tenha sido removido do inquilino, convertê-lo-á ao sufixo de domínio predefinido *.onmicrosoft.com. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>O que significa consistência do Endereço Proxy? 

Para os utilizadores apenas na nuvem, a consistência significa que os Endereços Proxy correspondem a um sufixo de domínio verificado. Quando um endereço proxy inconsistente é processado, **ProxyCalc** irá convertê-lo para o sufixo de domínio padrão *.onmicrosoft.com, por exemplo: SMTP:username@Contoso.onmicrosoft.com 

Para os utilizadores sincronizados, a consistência significa que os endereços proxy correspondem ao valor(es) proxy address(es) no local (es) (i.e) ShadowProxyAddresses). Espera-se que os **ProxyAddresses** estejam sincronizados com **os ShadowProxyAddresses**. Se o utilizador sincronizado tiver uma licença de Troca atribuída, os Endereços Proxy devem corresponder ao valor (es) Proxy Address(es) no local e também devem corresponder a um sufixo de domínio verificado. Neste cenário, **o ProxyCalc** irá sanitizar o inconsistente Endereço Proxy com um sufixo de domínio não verificado e será removido do objeto em Azure AD. Se esse domínio não verificado for verificado mais tarde, **o ProxyCalc** recomputa e adiciona o Endereço Proxy de **ShadowProxyAddresses** de volta ao objeto em Azure AD.  

> [!NOTE]
> Para objetos sincronizados, para evitar que a lógica **ProxyCalc** calcule resultados inesperados, o melhor é definir endereços de procuração para um domínio verificado Azure AD no objeto On-Premis.  

  
Uma das tarefas de administração que pode desencadear **o ProxyCalc** é sempre que há uma mudança de domínio verificada. Esta tarefa ocorre sempre que um domínio verificado é adicionado/removido de um inquilino AD Azure, que ativa internamente **ProxyCalc**.  

Por exemplo, se adicionar um Fabrikam.com de domínio verificado ao seu inquilino Contoso.onmicrosoft.com, esta ação irá desencadear uma operação ProxyCalc em todos os objetos do arrendatário. Este evento será capturado nos registos de Auditoria AZure AD como eventos **do Utilizador de atualização** precedidos por um evento **de domínio verificado add.** Por outro lado, se Fabrikam.com foi removido do inquilino Contoso.onmicrosoft.com, então todos os eventos do **Utilizador de Atualização** serão precedidos por um evento **de domínio verificado por Remove.**   

#### <a name="additional-notes"></a>Notas adicionais:

A ProxyCalc não provoca alterações em certos objetos que: 

- não têm uma licença de Troca ativa 
- tem **MSExchRemoteRecipientType** definido para Nulo 
- não são considerados um recurso partilhado. Recurso partilhado é quando **CloudMSExchRecipientDisplayType** contém um dos **seguintes valores: MailboxUser (partilhado)**, **PublicFolder,** **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **Caixa de correio de grupo,** Caixa de **Correio,** **ACLableMailboxUser,** **ACLableTeamMailbox** 
  
 A fim de construir uma maior correlação entre estes dois eventos díspares, a Microsoft está a trabalhar na atualização da informação do **Ator** nos registos de auditoria para identificar estas alterações como desencadeadas por uma mudança de domínio verificada. Esta ação ajudará a verificar quando ocorreu o evento de mudança de domínio verificado e começou a atualizar em massa os objetos no seu inquilino. 

Além disso, na maioria dos casos, não existem alterações nos utilizadores, uma vez que os seus Endereços **De Nome** e Procuração são **consistentes,** pelo que estamos a trabalhar para exibir em Registos de Auditoria apenas as atualizações que causaram uma alteração real no objeto. Esta ação evitará o ruído nos registos de auditoria e ajudará os administradores a correlacionar as alterações restantes do utilizador ao evento de alteração de domínio verificado, conforme explicado acima. 

## <a name="next-steps"></a>Passos Seguintes

[Atributos de sombra de sincronização Azure AD Connect](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
