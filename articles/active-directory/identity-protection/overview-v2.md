---
title: O que é o Azure Active Directory Identity Protection (atualizadas)? | Microsoft Docs
description: O que é o Azure Active Directory Identity Protection (atualizadas)?
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c3543b217339c39ad79c2125afdef8f087a70b3
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336691"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>O que é o Azure Active Directory Identity Protection (atualizadas)?

A experiência de proteção de identidade tiverem sido atualizada para melhor proteger identidades da sua organização. Esta experiência atualizada fornece:

- Experiência de administrador Reprojetado que gira em torno o mais relevante para o risco de utilizador you¬ e o risco de início de sessão de risco

- As investigações poderosas experiência com o suporte para as transferências de filtragem, ordenação e inteligentes

- Cálculo de risco de utilizador para o ajudar a atribuir prioridades aos esforços para os utilizadores que são mais probabilidades de ser comprometido de melhorada

- Nova API de suporte para ativar o acesso programático aos dados de risco

- Processo de comentários do administrador simplificada que permite-lhe imediatamente a proteger os seus utilizadores

- Novo supervisionadas do machine learning para melhorar a precisão de avaliações de risco



A segurança é uma grande preocupação para as organizações de hoje. A maioria dos realizar violações de segurança colocar quando os atacantes obtêm acesso a um ambiente, por roubo de identidade de um utilizador. Ao longo dos anos, os atacantes têm se tornado cada vez mais eficazes em tirar partido das violações de terceiros e utilize ataques de phishing sofisticado. Assim que os atacantes obtêm acesso a contas de utilizador com privilégios baixos, é relativamente fácil para obter acesso aos recursos de importantes da empresa através de movimento lateral. 

Para responder a essas ameaças, o Azure AD Identity Protection fornece para: 

- Proativamente impedir que as identidades comprometidas a ser utilizada 

- Mitigar o risco automaticamente quando é detetada atividade suspeita 

- Investigar os utilizadores de risco e inícios de sessão para potenciais vulnerabilidades de endereço  

- Sejam alertados quando o risco de um utilizador atinge um limiar especificado 

 

O Azure AD Identity Protection é um recurso do Azure Active Directory Premium P2 que permite-lhe configurar políticas para responder automaticamente quando a identidade do utilizador for comprometida ou quando alguém que o proprietário da conta está a tentar iniciar sessão com as suas identidade. Estas políticas, além de outros Controles de acesso condicional fornecido pelo Azure AD, pode seja automaticamente bloquear acesso ou ações de atenuação de iniciar, como a reposição de palavra-passe ou a imposição de multi-factor authentication. Além disso, o Identity Protection fornece capacidades de monitorização e relatórios para obter informações mais aprofundadas sobre risco e potenciais comprometimentos na sua organização. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]


## <a name="risk-events"></a>Eventos de risco

O Azure AD Identity Protection Deteta os seguintes eventos de risco: 

 

| Tipo de evento de risco | Descrição | Tipo de deteção |
| ---             | ---         | ---            |
| Percurso atípico | Iniciar sessão a partir de uma localização atípica, com base em inícios de sessão de recentes do utilizador. | Offline |
| Endereços IP anónimos | Iniciar sessão a partir de um endereço IP anónimo (por exemplo: Tor browser, anonymizer VPNs). | Em tempo real |
| Propriedades de início de sessão não familiar | Inicie sessão com as propriedades que não vimos recentemente para o utilizador especificado. | Em tempo real |
| Endereço IP ligado a software maligno | Iniciar sessão a partir de um endereço IP de software maligno ligado | Offline |
| Fuga de credenciais | Este evento de risco indica que houve uma fuga das credenciais válidas do utilizador | Offline |





## <a name="types-of-risk"></a>Tipos de risco 

Proteção de identidade se baseia em dois tipos de risco:

- Risco de início de sessão

- Risco de utilizador

### <a name="sign-in-risk"></a>Risco de início de sessão

Um risco de início de sessão representa a probabilidade de que um pedido de autenticação especificado não está autorizado pelo proprietário de identidade.

Existem duas avaliações de risco de início de sessão: 

- **Risco de início de sessão (em tempo real)** -o risco de início de sessão (em tempo real) se baseia em todas as detecções em tempo real que acionam durante o processamento de entrada.  

- **Início de sessão risco (agregar)** -o risco início de sessão (agregar) é o risco total de um início de sessão. Esta é calculada por um modelo que considera machine learning:

    - Detecções em tempo real (descritas acima)
    
    - Deteções offline (o que são acionados após o início de sessão tem sido efetuada) 
    
    - Todos os outros recursos de entrada


### <a name="user-risk"></a>Risco de utilizador

Um risco de utilizador representa a probabilidade de que uma identidade de determinado é comprometida. 

O risco de utilizador é calculado considerando todos os riscos associados ao utilizador:

- Todos os inícios de sessão de risco
- Todos os eventos de risco não associados a um início de sessão
- O risco de utilizador atual
- Quaisquer ações de remediação ou exoneração de risco realizadas no usuário até a data



## <a name="how-identity-protection-detects-risk"></a>Como o Identity Protection Deteta risco  

Azure AD utiliza machine learning para detetar anomalias e atividade suspeita, usando ambos os sinais detectadas em tempo real durante a inícios de sessão, bem como não - em tempo real sinaliza relacionados com utilizadores e suas atividades de início de sessão. Utilizando estes dados, Identity Protection calcula um risco de início de sessão em tempo real sempre que um utilizador efetua a autenticação, bem como que determina um nível de risco de utilizador geral para o utilizador de alcance. Proteção de identidade permite-lhe automaticamente agir sobre estas deteções de risco ao risco de utilizador de proteção de identidade configuração e políticas de início de sessão de risco.  

 

Para compreender como o Identity Protection Deteta risco, existem dois conceitos importantes: risco de utilizador e o risco de início de sessão. Início de sessão de risco reflete a probabilidade de que um pedido de autenticação especificado não está autorizado pelo proprietário de identidade. Existem dois tipos de riscos de início de sessão: total e em tempo real. Foi detetado o risco de início de sessão em tempo real no momento do determinado tentativa de início de sessão (por exemplo, inícios de sessão de endereços IP anónimos). Risco de início de sessão total é a agregação de detetado em tempo real início de sessão em riscos, bem como qualquer eventos de risco de não-real-time subsequentes associados ao utilizador inícios de sessão (por exemplo, deslocação impossível). Risco de utilizador reflete a probabilidade geral que um ator indevido comprometeu uma identidade específica. Risco de utilizador contém todas as atividades de risco para um determinado usuário, incluindo:

- Risco de início de sessão em tempo real
- Risco de início de sessão subsequente
- Deteções de utilizador de risco.   

 

 
 ![Fluxo](./media/overview-v2/01.png)
 

 

O fluxo de linha de base para a deteção de risco do Identity Protection e a resposta de qualquer dado início de sessão são resumidos no gráfico acima.  

 

 

 

## <a name="common-scenarios"></a>Cenários comuns 

Vamos examinar o exemplo de Sarah, um funcionário da Contoso. 

1. Sarah tenta iniciar sessão no Exchange Online do navegador Tor. No momento de início de sessão, o Azure AD Deteta eventos de risco em tempo real. 

2. Azure AD Deteta que Sara está a iniciar sessão de um endereço IP anónimo, acionar um nível de médio risco de início de sessão. 

3. Sarah é desafiada pela linha de comandos do MFA, porque o administrador de TI da Contoso configurado a política de acesso condicional de risco de início de sessão do Identity Protection. A política requer MFA para um risco de início de sessão de média ou superior. 

4. Sarah passa o MFA linha de comandos e acede ao Exchange Online, e o nível de risco de utilizador de Sarah não é alterado. 

O que aconteceu em segundo plano? A tentativa de início de sessão do navegador Tor acionada por um risco de início de sessão em tempo real no Azure AD para o endereço IP anónimo. Como o Azure AD processou o pedido, esta aplicada a política de início de sessão de risco configurada no Identity Protection, porque o nível de risco de início de sessão de Sarah cumprido o limiar (médio). Uma vez que Sarah tinha anteriormente registado para MFA, ela foi capaz de responder a e passar o desafio MFA. Sua capacidade de passar com êxito o desafio MFA sinalizado com o Azure AD que ela é provável que tenha o proprietário legítimo de identidade, e não aumente o seu nível de risco do utilizador. 


Mas e se Sarah não era um a tentar iniciar sessão? 

1. Um ator malicioso com credenciais de Sarah tenta iniciar sessão na conta do Exchange Online de Sarah do navegador Tor, uma vez que está a tentar ocultar o respetivo endereço de IP. 

2. Azure AD Deteta que a tentativa de início de sessão é de um endereço IP anónimo, acionar um risco de início de sessão em tempo real. 

3. O ator malicioso é desafiado pela linha de comandos do MFA, porque o administrador de TI da Contoso configurado a Identity Protection início de sessão risco política de acesso condicional para exigir a MFA quando o risco de início de sessão é médio ou superior. 

4. O ator malicioso falha na submissão da MFA e não consegue aceder à conta do Exchange Online de Sarah. 

5. A MFA com falha prompt acionado um evento de risco de serem registadas, gerando o risco de utilizador de Sarah para inícios de sessão futuros. 

Agora que um ator malicioso tentou aceder à conta de Sarah, vejamos o que acontece na próxima vez que Sarah tenta iniciar sessão. 

1. Sarah tenta iniciar sessão no Exchange Online do Outlook. No momento de início de sessão, o Azure AD Deteta eventos de risco em tempo real, bem como qualquer risco de utilizador anterior. 

2. O Azure AD não Deteta qualquer risco de início de sessão em tempo real, mas detecta o risco de utilizador elevada devido à atividade de risco nos últimos nos cenários anteriores.  

3. Sarah é contestada por uma linha de comandos para a reposição de palavra-passe, porque Contoso do administrador de TI configurado a política de risco de utilizador do Identity Protection para exigir a alteração de palavra-passe quando um utilizador com alto risco inicia sessão. 

4. Uma vez que a Sara está registada para SSPR e o MFA, ela com êxito repõe sua senha. 

5. Ao repor a sua senha, credenciais de Sarah já não sejam comprometidas e sua identidade devolve um estado seguro. 

6. Eventos de risco de Sarah anteriores são resolvidos e seu nível de risco do utilizador é automaticamente reiniciado como uma resposta para mitigar o comprometimento de credenciais. 

## <a name="how-do-i-configure-identity-protection"></a>Como posso configurar a proteção de identidade? 

Para começar a utilizar com o Identity Protection, primeiro de configurar uma política de risco do utilizador e uma política de risco de início de sessão. Assim que estas políticas são configuradas e aplicadas a um grupo de teste, pode simular eventos de risco para compreender como o Identity Protection irá responder no seu ambiente. A seguir o guia de introdução guias fornecem instruções sobre como configurar as políticas mencionadas anteriormente e testar no seu ambiente. 

 

Identity Protection suporta 3 funções no Azure AD para balancear as atividades de gestão em torno de sua implantação: 

| Função | Pode fazer | Não é possível fazer |
| --- | --- | --- |
| Administrador global | Acesso total ao Identity Protection, carregar Identity Protection | |
| Administrador de segurança | Acesso total ao Identity Protection | Carregar Identity Protection, repor palavras-passe para um utilizador |
| Leitor de segurança | Acesso só de leitura ao Identity Protection | Carregar Identity Protection, remediar utilizadores, configurar políticas, repor palavras-passe| 

Para obter mais detalhes, consulte [atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

 
## <a name="licensing"></a>Licenciamento

>[!NOTE]
> Durante a pré-visualização pública de proteção de identidade (atualizar), apenas os clientes do Azure AD Premium P2 têm acesso para o relatório de utilizadores de risco e o relatório de inícios de sessão de risco.



| Capacidade | Azure AD Premium P2 | Azure AD Premium P1 | Basic/gratuita do Azure AD |
| --- | --- | --- | --- |
| Política de risco de utilizador | Sim | Não | Não |
| Política de risco de início de sessão | Sim | Não | Não |
| Relatório dos utilizadores de risco | Acesso total | Informações limitadas | Informações limitadas |
| Relatório de inícios de sessão de risco | Acesso total | Informações limitadas | Informações limitadas |
| Política de registo MFA | Sim | Não | Não |







## <a name="next-steps"></a>Passos Seguintes 

Para começar a utilizar com o Identity Protection, veja [política de risco de início de sessão de configurar](quickstart-sign-in-risk-policy.md). 






