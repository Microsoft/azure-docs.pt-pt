---
title: Boas práticas para acesso condicional no Diretório Ativo Azure Microsoft Docs
description: Saiba mais sobre as coisas que deve saber e o que deve evitar fazer ao configurar as políticas de Acesso Condicional.
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
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295347"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Boas práticas para acesso condicional em Diretório Ativo Azure

Com o Acesso Condicional do [Diretório Ativo Azure (Azure AD),](../active-directory-conditional-access-azure-portal.md)pode controlar a forma como os utilizadores autorizados acedem às suas aplicações na nuvem. Este artigo fornece-lhe informações sobre:

- Coisas que deve saber 
- O que deve evitar fazer ao configurar as políticas de Acesso Condicional. 

Este artigo assume que está familiarizado com os conceitos e a terminologia delineada no [What is Conditional Access in Azure Ative Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>O que é necessário para fazer uma apólice funcionar?

Quando cria uma nova política, não existem utilizadores, grupos, apps ou controlos de acesso selecionados.

![Aplicativos em nuvem](./media/best-practices/02.png)

Para que a sua política funcione, tem de configurar:

| O quê           | Como                                  | Por que |
| :--            | :--                                  | :-- |
| **Aplicativos em nuvem** |Selecione uma ou mais aplicações.  | O objetivo de uma política de Acesso Condicional é permitir-lhe controlar como os utilizadores autorizados podem aceder a aplicações na nuvem.|
| **Utilizadores e grupos** | Selecione pelo menos um utilizador ou grupo autorizado a aceder às suas aplicações na nuvem selecionadas. | Uma política de Acesso Condicional que não tenha utilizadores e grupos atribuídos, nunca é desencadeada. |
| **Controlos de acesso** | Selecione pelo menos um controlo de acesso. | Se as suas condições estiverem satisfeitas, o seu processador de política precisa de saber o que fazer. |

## <a name="what-you-should-know"></a>O que deve saber

### <a name="how-are-conditional-access-policies-applied"></a>Como são aplicadas as políticas de Acesso Condicional?

Mais de uma política de Acesso Condicional pode ser aplicada quando aceder a uma aplicação na nuvem. Neste caso, todas as políticas que se aplicam devem ser satisfeitas. Por exemplo, se uma das políticas necessitar de autenticação multifactor (MFA) e outra necessitar de um dispositivo compatível, deve completar o MFA e utilizar um dispositivo conforme. 

Todas as políticas são aplicadas em duas fases:

- Fase 1: 
   - Recolha de detalhes: Recolha de detalhes para identificar políticas que já estariam satisfeitas.
   - Durante esta fase, os utilizadores podem ver um pedido de certificado se a conformidade do dispositivo fizer parte das suas políticas de Acesso Condicional. Esta solicitação pode ocorrer para aplicações de navegador quando o sistema operativo do dispositivo não é o Windows 10.
   - A fase 1 da avaliação das políticas ocorre para todas as políticas e políticas ativadas no [modo apenas para relatórios](concept-conditional-access-report-only.md).
- Fase 2:
   - Execução: Tendo em conta os dados recolhidos na fase 1, solicite ao utilizador que satisfaça quaisquer requisitos adicionais que não tenham sido cumpridos.
   - Aplique os resultados na sessão. 
   - A fase 2 da avaliação das políticas ocorre para todas as políticas ativadas.

### <a name="how-are-assignments-evaluated"></a>Como são avaliadas as atribuições?

Todas as atribuições são logicamente **ANDed**. Se tiver mais de uma tarefa configurada, todas as atribuições devem estar satisfeitas para desencadear uma política.  

Se precisar configurar uma condição de localização que se aplique a todas as ligações feitas fora da rede da sua organização:

- Incluir **todos os locais**
- Excluir **Todos os IPs fidedignos**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>O que fazer se estiver trancado fora do portal de administração da AD Azure?

Se estiver bloqueado fora do portal Azure AD devido a uma definição incorreta numa política de acesso condicional:

- Verifique se há outros administradores na sua organização que ainda não estão bloqueados. Um administrador com acesso ao portal Azure pode desativar a política que está a afetar o seu registo. 
- Se nenhum dos administradores da sua organização conseguir atualizar a apólice, terá de submeter um pedido de apoio. O suporte da Microsoft pode rever e atualizar as políticas de Acesso Condicional que estão a impedir o acesso.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>O que acontece se tiver políticas no portal clássico azure e no portal Azure configurados?  

Ambas as políticas são aplicadas pelo Azure Ative Directory e o utilizador só tem acesso quando todos os requisitos são cumpridos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>O que acontece se tiver políticas no portal Intune Silverlight e no portal Azure?

Ambas as políticas são aplicadas pelo Azure Ative Directory e o utilizador só tem acesso quando todos os requisitos são cumpridos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>O que acontece se eu tiver múltiplas políticas para o mesmo utilizador configuradas?  

Para cada entrada, o Azure Ative Diretório avalia todas as políticas e garante que todos os requisitos são cumpridos antes de ter acesso ao utilizador. O acesso ao bloco supera todas as outras definições de configuração. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>O Acesso Condicional funciona com o Exchange ActiveSync?

Sim, pode utilizar o Exchange ActiveSync numa política de acesso condicional.

Algumas aplicações na nuvem como o SharePoint Online e o Exchange Online também suportam protocolos de autenticação legado. Quando uma aplicação de cliente pode usar um protocolo de autenticação legado para aceder a uma aplicação na nuvem, a Azure AD não pode impor uma política de Acesso Condicional nesta tentativa de acesso. Para evitar que uma aplicação de clientes convertasse a aplicação das políticas, deve verificar se é possível apenas ativar a autenticação moderna nas aplicações de nuvem afetadas.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Como configurar o Acesso Condicional com aplicações do Office 365?

Como as aplicações do Office 365 estão interligadas, recomendamos a atribuição de aplicações comumente usadas em conjunto na criação de políticas.

As aplicações interligadas comuns incluem Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online e Office 365 Yammer.

É importante para políticas que requerem interações do utilizador, como a autenticação de vários fatores, quando o acesso é controlado no início de uma sessão ou tarefa. Caso não o faça, os utilizadores não poderão completar algumas tarefas dentro de uma aplicação. Por exemplo, se necessitar de autenticação multifactor em dispositivos não geridos para aceder ao SharePoint mas não para e-mail, os utilizadores que trabalham no seu e-mail não poderão anexar ficheiros SharePoint a uma mensagem. Mais informações podem ser encontradas no artigo, Quais são as dependências de [serviço no Acesso Condicional do Diretório Ativo Do Azure?](service-dependencies.md)

## <a name="what-you-should-avoid-doing"></a>O que deve evitar fazer

O quadro de Acesso Condicional proporciona-lhe uma grande flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que deve rever cuidadosamente cada política de configuração antes de a lançar para evitar resultados indesejáveis. Neste contexto, deve prestar especial atenção a atribuições que afetam conjuntos completos, tais como **todos os utilizadores/grupos/aplicações em nuvem.**

No seu ambiente, deve evitar as seguintes configurações:

**Para todos os utilizadores, todas as aplicações na nuvem:**

- **Block access** - Esta configuração bloqueia toda a sua organização, o que definitivamente não é uma boa ideia.
- **Requerer um dispositivo conforme** - Para utilizadores que ainda não tenham matriculado os seus dispositivos, esta política bloqueia todos os acessos, incluindo o acesso ao portal Intune. Se é um administrador sem um dispositivo matriculado, esta política impede-o de voltar ao portal Azure para alterar a política.
- **Requer a adesão ao domínio** - Este acesso ao bloco de políticas também tem o potencial de bloquear o acesso a todos os utilizadores da sua organização se ainda não tiver um dispositivo de domínio.
- **Requerer a política de proteção de aplicações** - Este acesso ao bloco de políticas também tem o potencial de bloquear o acesso a todos os utilizadores da sua organização se não tiver uma política intune. Se é um administrador sem aplicação de cliente que tenha uma política de proteção de aplicações Intune, esta política impede-o de voltar a entrar em portais como intune e Azure.

**Para todos os utilizadores, todas as aplicações na nuvem, todas as plataformas do dispositivo:**

- **Block access** - Esta configuração bloqueia toda a sua organização, o que definitivamente não é uma boa ideia.

## <a name="how-should-you-deploy-a-new-policy"></a>Como deve implementar uma nova política?

Como primeiro passo, deve avaliar a sua política utilizando o [e se ferramenta](what-if-tool.md).

Quando as novas políticas estiverem prontas para o seu ambiente, implemente-as por fases:

1. Aplique uma política a um pequeno conjunto de utilizadores e verifique se se comporta como esperado. 
1. Quando expande uma política para incluir mais utilizadores. Continue a excluir todos os administradores da política para garantir que ainda têm acesso e podem atualizar uma política se for necessária uma mudança.
1. Aplicar uma política a todos os utilizadores apenas se necessário. 

Como uma boa prática, crie uma conta de utilizador que seja:

- Dedicado à administração de políticas 
- Excluído de todas as suas políticas

## <a name="policy-migration"></a>Migração política

Considere migrar as políticas que não criou no portal Azure porque:

- Agora pode abordar cenários que não conseguiu resolver antes.
- Pode reduzir o número de políticas que tem de gerir consolidando-as.   
- Você pode gerir todas as suas políticas de Acesso Condicional em um local central.
- O portal clássico azure foi retirado.   

Para obter mais informações, veja [Migrate classic policies in the Azure portal](policy-migration.md) (Migrar políticas clássicas no portal do Azure).

## <a name="next-steps"></a>Passos seguintes

Se quiser saber:

- Como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicações específicas com acesso condicional de diretório ativo Azure](app-based-mfa.md).
- Como planear as suas políticas de Acesso Condicional, consulte como planear a sua implementação de [Acesso Condicional no Diretório Ativo Azure](plan-conditional-access.md).
