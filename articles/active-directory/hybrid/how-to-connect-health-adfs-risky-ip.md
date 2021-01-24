---
title: Azure AD Connect Health com relatório IP de risco da AD FS | Microsoft Docs
description: Descreve o relatório IP de risco Azure AD Connect Health AD FS.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0b76d2f943f254eb06208e2c190bae4d4088030
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746075"
---
# <a name="risky-ip-report-public-preview"></a>Relatório IP arriscado (pré-visualização pública)
Os clientes AD FS podem expor pontos finais de autenticação de palavra-passe na internet para fornecer serviços de autenticação para os utilizadores finais acederem a aplicações SaaS como a Microsoft 365. Neste caso, é possível que um ator indevido tente inícios de sessão no seu sistema do AD FS para adivinhar a palavra-passe de um utilizador final e obter acesso aos recursos das aplicações. O AD FS proporciona a funcionalidade de bloqueio de conta de extranet para evitar estes tipos de ataques a partir do AD FS no Windows Server 2012 R2. Se tiver uma versão inferior, recomendamos vivamente que atualize o sistema do AD FS para o Windows Server 2016. <br />

Além disso, é possível que um único endereço IP tente vários inícios de sessão para vários utilizadores. Nesses casos, o número de tentativas por utilizador poderá estar abaixo do limiar da proteção de bloqueio de conta no AD FS. O Azure AD Connect Health oferece agora o “relatório de IP em Risco”, que deteta esta condição e notifica os administradores quando a mesma ocorre. Seguem-se as principais vantagens deste relatório: 
- Deteção de endereços IP que excedem um limiar de inícios de sessão baseados em palavra-passe falhados
- Suporte para inícios de sessão falhados devido a palavra-passe errada ou a estado de bloqueio de extranet
- Notificações por e-mail para alertar os administradores mal essas situações se verifiquem com definições de e-mail personalizáveis
- Definições de limiar personalizáveis que estão em linha com a política de segurança das organizações
- Relatórios transferíveis para análise offline e integração com outros sistemas através de automatização

> [!NOTE]
> Para utilizar este relatório, tem de se certificar de que a auditoria do AD FS está ativada. Para obter mais informações, consulte [Ativar Auditoria do AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Para aceder à pré-visualização, precisa da permissão do Administrador Global ou do [Leitor de Segurança](../../role-based-access-control/built-in-roles.md#security-reader).  
>

## <a name="what-is-in-the-report"></a>O que está no relatório?
O sinal falhado nos endereços IP do cliente da atividade é agregado através de servidores Proxy de aplicação web. Cada item no relatório de IP em Risco mostra informações agregadas sobre as atividades de início de sessão do AD FS falhadas que excedam o limiar designado. Fornece as seguintes informações: ![ Screenshot que mostra um relatório IP arriscado com cabeçalhos de coluna realçados.](./media/how-to-connect-health-adfs/report4a.png)

| Item de Relatório | Descrição |
| ------- | ----------- |
| Time Stamp (Carimbo de Data/Hora) | Mostra o carimbo de data/hora com base na hora local do portal do Azure quando a janela de tempo da deteção é iniciada.<br /> Todos os eventos diários são gerados à meia-noite, no fuso horário UTC. <br />O carimbo de data/hora dos eventos de hora é arredondado para o início da hora. Pode ver a hora de início da primeira atividade em “firstAuditTimestamp” no ficheiro exportado. |
| Trigger Type (Tipo de Acionador) | Mostra o tipo da janela de tempo da deteção. Os tipos de acionadores de agregação são horários ou diários. É útil para detetar um ataque de força bruta de frequência elevada por oposição a um ataque lento, em que o número de tentativas é distribuído ao longo do dia. |
| Endereço IP | O endereço IP em risco individual que recebeu atividades de início de sessão com palavra-passe errada ou bloqueio de extranet. Isto pode ser um IPv4 ou um endereço IPv6. |
| Bad Password Error Count (Contagem de Erros de Palavra-passe Errada) | A contagem de erros de Palavra-Passe Errada que ocorreram no endereço IP durante a janela de tempo da deteção. Estes erros podem acontecer várias vezes a determinados utilizadores Tenha em atenção que esta contagem não inclui tentativas falhadas devido a palavras-passe expiradas. |
| Extranet Lock Out Error Count (Contagem de Erros de Bloqueio de Extranet) | A contagem de erros de Bloqueio de Extranet que ocorreram no endereço IP durante a janela de tempo da deteção. Os erros de Bloqueio de Extranet podem acontecer várias vezes a determinados utilizadores. Só serão vistos de o Bloqueio de Extranet estiver configurado no AD FS (versões 2012R2 ou superiores). <b>Tenha em atenção</b> que recomendamos vivamente ativar esta funcionalidade caso permita inícios de sessão de extranet através da utilização de palavras-passe. |
| Unique Users Attempted (Utilizadores Individuais Tentados) | A contagem de contas de utilizador individuais tentadas no endereço IP durante a janela de tempo da deteção. Esta contagem proporciona um mecanismo para diferenciar um padrão de ataque a um único utilizador por oposição a um padrão de ataque a vários utilizadores.  |

Por exemplo, o item de relatório abaixo indica que, na janela entre as 18:00 e as 19:00 do dia 28/02/2018, o endereço IP <i>104.2XX.2XX.9</i> não teve erros de palavra-passe errada e teve 284 erros de bloqueio de extranet. Foram afetados 14 utilizadores individuais dentro dos critérios. O evento de atividade excedeu o limiar designado de hora do relatório. 

![Screenshot que mostra um exemplo de uma entrada de relatório IP arriscado.](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Só aparecerão na lista do relatório as atividades que excedam o limiar designado. 
> - Este relatório pode remontar, no máximo, aos últimos 30 dias.
> - Este relatório de alerta não mostra endereços IP do Exchange nem endereços IP privados. Permanecem incluídos na lista de exportação. 
>

![Screenshot que mostra o relatório IP risky com o "Download", "Definições de notificação" e "Definições de limiar" realçados.](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Endereços IP do balançador de carga na lista
O balanceador de carga agregou as atividades de início de sessão falhadas e atingiu o limiar de alerta. Se vir endereços IP do balanceador de carga, é altamente provável que o seu balanceador de carga externo não esteja a enviar o endereço IP cliente quando transmite o pedido para o servidor Proxy de Aplicação Web. Configure o seu balanceador de carga corretamente para o reencaminhamento do endereço IP do cliente. 

## <a name="download-risky-ip-report"></a>Baixar relatório IP de risco 
Ao utilizar a funcionalidade **Exportar**, é possível exportar toda a lista de endereços IP em risco dos últimos 30 dias a partir do portal do Connect Health. O resultado da exportação incluíra todas as atividades de início de sessão do AD FS falhadas em cada janela de tempo de deteção, pelo que pode personalizar a filtragem após a exportação. Para além das agregações realçadas no portal, o resultado da exportação também mostrará mais detalhes sobre as atividades de início de sessão falhadas por endereço IP:

|  Item de Relatório  |  Descrição  | 
| ------- | ----------- | 
| firstAuditTimestamp | Mostra o primeiro carimbo de data/hora de quando as atividades falhadas se iniciaram durante a janela de tempo de deteção.  | 
| lastAuditTimestamp | Mostra o último carimbo de data/hora de quando as atividades falhadas terminaram durante a janela de tempo de deteção.  | 
| attemptCountThresholdIsExceeded | O sinalizador se as atividades atuais estiverem a exceder o limiar de alerta.  | 
| isWhitelistedIpAddress | O sinalizador se o endereço IP é filtrado em alertas e relatórios. Os endereços IP privados (<i>10.x.x.x, 172.x.x.x e 192.168.x.x</i>) e os endereços IP do Exchange são filtrados e marcados como Verdadeiros. Se vir intervalos de endereços IP privados, é altamente provável que o seu balanceador de carga externo não esteja a enviar o endereço IP cliente quando transmite o pedido para o servidor Proxy de Aplicação Web.  | 

## <a name="configure-notification-settings"></a>Configurar as definições de notificação
Os contactos dos administradores do relatório podem ser atualizados através das **Definições de Notificação**. Por predefinição, a notificação de alerta de e-mail de IP em risco está no estado desativado. Para ativar a notificação, utilize o botão em “Obter notificações por e-mail para endereços IP que excedam o relatório de limiar de atividades falhadas”. Tal como as definições de notificação de alertas genéricas no Connect Health, permite-lhe personalizar a lista de destinatários designados da notificação quanto ao relatório de IP em risco a partir daí. Também pode notificar todos os administradores globais quando fizer a alteração. 

## <a name="configure-threshold-settings"></a>Configurações de limiar de configuração
O limiar de alerta pode ser atualizado a partir das Definições do Limiar. Para começar, o sistema tem um limiar predefinido. Os valores predefinidos são dados abaixo. Existem quatro categorias nas definições de limiar do relatório de IP em risco:

![Portal do Azure AD Connect Health](./media/how-to-connect-health-adfs/report4d.png)

| Item do Limiar | Descrição |
| --- | --- |
| (Bad U/P + Extranet Lockout) / Day [(U/P incorreta + Bloqueio de Extranet) / Dia]  | Definição do limiar para reportar a atividade e acionar a notificação de alerta quando a contagem de Palavras-passe Erradas mais a contagem de Bloqueios de Extranet o excede por **dia**. O valor predefinido é de 100.|
| (Bad U/P + Extranet Lockout) / Hour [U/P incorreta + Bloqueio de Extranet / Hora] | Definição do limiar para reportar a atividade e acionar a notificação de alerta quando a contagem de Palavras-passe Erradas mais a contagem de Bloqueios de Extranet o excede por **hora**. O valor predefinido é de 50.|
| Extranet Lockout / Day [Bloqueio de Extranet / Dia] | Definição do limiar para reportar a atividade e acionar a notificação de alerta quando a contagem de Bloqueios de Extranet o excede por **dia**. O valor predefinido é 50.|
| Extranet Lockout / Hour [Bloqueio de Extranet / Hora]| Definição do limiar para reportar a atividade e acionar a notificação de alerta quando a contagem de Bloqueios de Extranet o excede por **hora**. O valor predefinido é de 25|

> [!NOTE]
> - A alteração do limiar de relatório será aplicada passado uma hora da alteração da definição. 
> - Os itens reportados existentes não serão afetados pela alteração ao limiar. 
> - Recomendamos que analise o número de eventos vistos no seu ambiente e ajuste o limiar em conformidade. 
>
>

## <a name="faq"></a>FAQ
**Por que razão estou a ver intervalos de endereços IP privados no relatório?**  <br />
Os endereços IP privados<i>(10.x.x.x, 172.x.x.x & 192.168.x.x)</i>e os endereços IP de troca são filtrados e marcados como Verdadeiros na lista aprovada pelo IP. Se vir intervalos de endereços IP privados, é altamente provável que o seu balanceador de carga externo não esteja a enviar o endereço IP cliente quando transmite o pedido para o servidor Proxy de Aplicação Web.

**Porque estou a ver endereços IP do balanceador de carga no relatório?**  <br />
Se vir endereços IP do balanceador de carga, é altamente provável que o seu balanceador de carga externo não esteja a enviar o endereço IP cliente quando transmite o pedido para o servidor Proxy de Aplicação Web. Configure o seu balanceador de carga corretamente para o reencaminhamento do endereço IP do cliente. 

**Como posso bloquear o endereço IP?**  <br />
Deve adicionar um endereço IP malicioso identificado à firewall ou bloqueá-lo no Exchange.   <br />

**Por que razão não vejo nenhum item neste relatório?** <br />
- As atividades de início de sessão falhadas não estão a exceder as definições de limiar.
- Confirme que não tem nenhum alerta “O serviço de estado de funcionamento não está atualizado” ativo na sua lista de servidores do AD FS.  Leia mais sobre [como resolver problemas com este alerta](how-to-connect-health-data-freshness.md).
- As auditorias não estão ativadas nos farms do AD FS.

**Por que razão não estou a ver o acesso ao relatório?**  <br />
É necessária a permissão Administrador Global ou [Leitor de Segurança](../../role-based-access-control/built-in-roles.md#security-reader). Contacte o administrador global para obter acesso.


## <a name="next-steps"></a>Próximos passos
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
