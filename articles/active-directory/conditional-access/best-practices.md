---
title: Práticas recomendadas para acesso condicional no Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as coisas que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b7265f8d5ec4b7336253787e9cb881900a52b79
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963450"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Práticas recomendadas para acesso condicional no Azure Active Directory

Com o [acesso condicional do Azure Active Directory (AD do Azure)](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados acessam seus aplicativos de nuvem. Este artigo fornece informações sobre:

- Coisas que você deve saber 
- O que você deve evitar fazer ao configurar políticas de acesso condicional. 

Este artigo pressupõe que você esteja familiarizado com os conceitos e a terminologia descrita em [o que é o acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>O que é necessário para fazer uma política funcionar?

Quando você cria uma nova política, não há usuários, grupos, aplicativos ou controles de acesso selecionados.

![Aplicações na cloud](./media/best-practices/02.png)

Para fazer sua política funcionar, você deve configurar:

| O quê           | Qual                                  | Por |
| :--            | :--                                  | :-- |
| **Aplicativos de nuvem** |Selecione um ou mais aplicativos.  | O objetivo de uma política de acesso condicional é permitir que você controle como os usuários autorizados podem acessar os aplicativos de nuvem.|
| **Usuários e grupos** | Selecione pelo menos um usuário ou grupo que esteja autorizado a acessar seus aplicativos de nuvem selecionados. | Uma política de acesso condicional que não tem usuários e grupos atribuídos nunca é disparada. |
| **Controles de acesso** | Selecione pelo menos um controle de acesso. | Se suas condições forem satisfeitas, o processador de política precisará saber o que fazer. |

## <a name="what-you-should-know"></a>O que deve saber

### <a name="how-are-conditional-access-policies-applied"></a>Como as políticas de acesso condicional são aplicadas?

Mais de uma política de acesso condicional pode ser aplicada quando você acessa um aplicativo de nuvem. Nesse caso, todas as políticas que se aplicam devem ser satisfeitas. Por exemplo, se uma política exigir MFA e a segunda exigir um dispositivo compatível, você deverá passar pelo MFA e usar um dispositivo compatível. 

Todas as políticas são impostas em duas fases:

- Na **primeira** fase, todas as políticas são avaliadas e todos os controles de acesso que não são satisfeitos são coletados. 

- Na **segunda** fase, você receberá uma solicitação para atender aos requisitos que não atendeu. Se qualquer uma das políticas bloquear o acesso, você será bloqueado e não será solicitado a atender a outros controles de política. Se nenhuma das políticas bloquear você, você será solicitado a atender a outros controles de política na seguinte ordem:

   ![Encomenda](./media/best-practices/06.png)
    
   Os provedores de MFA externos e os termos de uso vêm em seguida.

### <a name="how-are-assignments-evaluated"></a>Como as atribuições são avaliadas?

Todas as atribuiçõessão logicamente ANDeddas. Se você tiver mais de uma atribuição configurada, todas as atribuições deverão ser satisfeitas para disparar uma política.  

Se você precisar configurar uma condição de local que se aplica a todas as conexões feitas de fora da rede da sua organização:

- Incluir **todos os locais**
- Excluir **todos os IPs confiáveis**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>O que fazer se você estiver bloqueado no portal de administração do Azure AD?

Se você estiver bloqueado no portal do AD do Azure devido a uma configuração incorreta em uma política de acesso condicional:

- Verifique se há outros administradores em sua organização que ainda não foram bloqueados. Um administrador com acesso ao portal do Azure pode desabilitar a política que está afetando sua entrada. 
- Se nenhum dos administradores da sua organização puder atualizar a política, você precisará enviar uma solicitação de suporte. O suporte da Microsoft pode revisar e atualizar políticas de acesso condicional que estão impedindo o acesso.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>O que acontece se você tiver políticas no portal clássico do Azure e portal do Azure configuradas?  

Ambas as políticas são impostas por Azure Active Directory e o usuário obtém acesso somente quando todos os requisitos são atendidos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>O que acontece se você tiver políticas no portal do Intune Silverlight e o portal do Azure?

Ambas as políticas são impostas por Azure Active Directory e o usuário obtém acesso somente quando todos os requisitos são atendidos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>O que acontece se eu tiver várias políticas para o mesmo usuário configurado?  

Para cada entrada, Azure Active Directory avalia todas as políticas e garante que todos os requisitos sejam atendidos antes de conceder acesso ao usuário. Bloquear o acesso supera todas as outras definições de configuração. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>O acesso condicional funciona com o Exchange ActiveSync?

Sim, você pode usar o Exchange ActiveSync em uma política de acesso condicional com algumas [limitações](block-legacy-authentication.md). 

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Como você deve configurar o acesso condicional com os aplicativos do Office 365?

Como os aplicativos do Office 365 são interconectados, é recomendável atribuir aplicativos comumente usados ao criar políticas.

Aplicativos comuns interconectados incluem Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online e Office 365 Yammer.

É importante para as políticas que exigem interações com o usuário, como a autenticação multifator, quando o acesso é controlado no início de uma sessão ou tarefa. Caso contrário, os usuários não poderão concluir algumas tarefas em um aplicativo. Por exemplo, se você precisar de autenticação multifator em dispositivos não gerenciados para acessar o SharePoint, mas não para email, os usuários que trabalham em seu email não poderão anexar arquivos do SharePoint a uma mensagem. Mais informações podem ser encontradas no artigo [quais são as dependências de serviço em Azure Active Directory acesso condicional?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>O que você deve evitar fazer

A estrutura de acesso condicional fornece uma ótima flexibilidade de configuração. No entanto, grande flexibilidade também significa que você deve examinar atentamente cada política de configuração antes de liberá-la para evitar resultados indesejáveis. Nesse contexto, você deve prestar atenção especial às atribuições que afetam os conjuntos completos, como **todos os usuários/grupos/aplicativos de nuvem**.

Em seu ambiente, você deve evitar as seguintes configurações:

**Para todos os usuários, todos os aplicativos de nuvem:**

- **Bloquear o acesso** -essa configuração bloqueia toda a organização, o que definitivamente não é uma boa ideia.
- **Exigir dispositivo compatível** -para usuários que ainda não registraram seus dispositivos, essa política bloqueia todo o acesso, incluindo acesso ao portal do Intune. Se você for um administrador sem um dispositivo registrado, essa política o impedirá de voltar para o portal do Azure para alterar a política.
- **Exigir ingresso no domínio** – esse acesso ao bloqueio de política também tem o potencial de bloquear o acesso para todos os usuários em sua organização se você ainda não tiver um dispositivo ingressado no domínio.
- **Exigir política de proteção de aplicativo** -este acesso ao bloqueio de política também tem o potencial de bloquear o acesso para todos os usuários em sua organização se você não tiver uma política do Intune. Se você for um administrador sem um aplicativo cliente que tenha uma política de proteção de aplicativo do Intune, essa política o bloqueará de voltar para portais como o Intune e o Azure.

**Para todos os usuários, todos os aplicativos de nuvem, todas as plataformas de dispositivo:**

- **Bloquear o acesso** -essa configuração bloqueia toda a organização, o que definitivamente não é uma boa ideia.

## <a name="how-should-you-deploy-a-new-policy"></a>Como você deve implantar uma nova política?

Como primeira etapa, você deve avaliar sua política usando a [ferramenta What If](what-if-tool.md).

Quando novas políticas estiverem prontas para seu ambiente, implante-as em fases:

1. Aplique uma política a um pequeno conjunto de usuários e verifique se ele se comporta conforme o esperado. 
1. Quando você expande uma política para incluir mais usuários. Continue a excluir todos os administradores da política para garantir que eles ainda tenham acesso e possam atualizar uma política se uma alteração for necessária.
1. Aplique uma política a todos os usuários somente se necessário. 

Como prática recomendada, crie uma conta de usuário que seja:

- Dedicado à administração de política 
- Excluído de todas as suas políticas

## <a name="policy-migration"></a>Migração de política

Considere migrar as políticas que você não criou no portal do Azure porque:

- Agora você pode abordar os cenários que não podia manipular antes.
- Você pode reduzir o número de políticas a serem gerenciadas, consolidando-as.   
- Você pode gerenciar todas as políticas de acesso condicional em um local central.
- O portal clássico do Azure foi desativado.   

Para obter mais informações, consulte [migrar políticas clássicas no portal do Azure](policy-migration.md).

## <a name="next-steps"></a>Passos Seguintes

Se você quiser saber:

- Como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md).
- Como planejar suas políticas de acesso condicional, consulte [como planejar sua implantação de acesso condicional no Azure Active Directory](plan-conditional-access.md).
