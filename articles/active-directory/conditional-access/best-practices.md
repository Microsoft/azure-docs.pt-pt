---
title: Boas práticas para acesso condicional no Diretório Ativo Azure Microsoft Docs
description: Saiba sobre as coisas que deve saber e o que deve evitar fazer ao configurar políticas de Acesso Condicional.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 161b02ec962df5c5e1c8dee7e124ef78b3ca4db3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601984"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Melhores práticas para acesso condicional no Diretório Ativo Azure

Com [o Azure Ative Directory (Azure AD) Acesso Condicional,](./overview.md)pode controlar como os utilizadores autorizados acedem às suas aplicações na nuvem. Este artigo fornece-lhe informações sobre:

- Coisas que deve saber 
- O que deve evitar fazer ao configurar políticas de acesso condicional. 

Este artigo assume que está familiarizado com os conceitos e a terminologia delineada no [Que é o Acesso Condicional no Diretório Ativo Azure?](./overview.md)

## <a name="whats-required-to-make-a-policy-work"></a>O que é necessário para fazer uma política funcionar?

Quando cria uma nova política, não existem utilizadores, grupos, apps ou controlos de acesso selecionados.

![Aplicações na cloud](./media/best-practices/02.png)

Para que a política funcione, tem de configurar:

| O quê           | Como                                  | Porquê |
| :--            | :--                                  | :-- |
| **Aplicações na cloud** |Selecione uma ou mais aplicações.  | O objetivo de uma política de Acesso Condicional é permitir-lhe controlar como os utilizadores autorizados podem aceder a aplicações na nuvem.|
| **Utilizadores e grupos** | Selecione, pelo menos, um utilizador ou um grupo com autorização de acesso às aplicações na cloud selecionadas. | Uma política de Acesso Condicional que não tenha utilizadores e grupos atribuídos, nunca é ativada. |
| **Controlos de acesso** | Selecione, pelo menos, um controlo de acesso. | Se as suas condições forem satisfeitas, o seu processador de políticas precisa de saber o que fazer. |

## <a name="what-you-should-know"></a>O que deve saber

### <a name="how-are-conditional-access-policies-applied"></a>Como são aplicadas as políticas de acesso condicional?

Mais de uma política de Acesso Condicional pode aplicar-se quando aceder a uma aplicação na nuvem. Neste caso, todas as políticas que se aplicam devem ser satisfeitas. Por exemplo, se uma política requer a autenticação de vários fatores (MFA) e outra requer um dispositivo conforme, deve completar o MFA e utilizar um dispositivo em conformidade. 

Todas as políticas são aplicadas em duas fases:

- Fase 1: Recolher detalhes da sessão 
   - Recolha detalhes da sessão, como a localização do utilizador e a identidade do dispositivo que serão necessários para a avaliação de políticas. 
   - Durante esta fase, os utilizadores poderão ver um certificado a solicitar se a conformidade do dispositivo fizer parte das suas políticas de Acesso Condicional. Esta solicitação pode ocorrer para aplicações de navegador quando o sistema operativo do dispositivo não é o Windows 10. 
   - A fase 1 da avaliação das políticas ocorre para políticas e políticas ativadas [apenas](concept-conditional-access-report-only.md)no modo de relatório .
- Fase 2: Execução 
   - Utilize os detalhes da sessão recolhidos na fase 1 para identificar quaisquer requisitos que não tenham sido cumpridos. 
   - Se houver uma política que esteja configurada para bloquear o acesso, com o controlo da subvenção por bloco, a aplicação da lei parará aqui e o utilizador será bloqueado. 
   - O utilizador será então solicitado a completar requisitos adicionais de controlo de subvenções que não tenham sido satisfeitos durante a fase 1 na seguinte ordem, até que a política esteja satisfeita:  
      - Autenticação multifator 
      - Política de proteção de aplicativos/aplicativos de cliente aprovado 
      - Dispositivo gerido (junção AD AD compatível ou híbrido) 
      - Termos de utilização 
      - Controlos personalizados  
      - Uma vez satisfeitos os controlos de subvenção, aplique controlos de sessão (App Enforcado, Microsoft Cloud App Security e Token Lifetime) 
   - A fase 2 da avaliação das políticas ocorre para todas as políticas ativadas. 

### <a name="how-are-assignments-evaluated"></a>Como são avaliadas as atribuições?

Todas as atribuições são logicamente **ANDed.** Se tiver mais do que uma missão configurada, todas as atribuições devem estar satisfeitas para desencadear uma apólice.  

Se precisar de configurar uma condição de localização que se aplique a todas as ligações feitas fora da rede da sua organização:

- Incluir **todos os locais**
- Excluir **todos os IPs fidedignos**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>O que fazer se estiver trancado fora do portal de administração Azure AD?

Se estiver bloqueado fora do portal AD Azure devido a uma definição incorreta numa política de acesso condicional:

- Verifique se há outros administradores na sua organização que ainda não estão bloqueados. Um administrador com acesso ao portal Azure pode desativar a política que está a afetar a sua inscrição. 
- Se nenhum dos administradores da sua organização conseguir atualizar a política, tem de apresentar um pedido de apoio. O suporte da Microsoft pode rever e atualizar políticas de Acesso Condicional que estão a impedir o acesso.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>O que acontece se tiver políticas no portal clássico do Azure e no portal Azure configurados?  

Ambas as políticas são aplicadas pelo Azure Ative Directory e o utilizador só tem acesso quando todos os requisitos são cumpridos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>O que acontece se tiver políticas no portal Intune Silverlight e no portal Azure?

Ambas as políticas são aplicadas pelo Azure Ative Directory e o utilizador só tem acesso quando todos os requisitos são cumpridos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>O que acontece se eu tiver várias políticas para o mesmo utilizador configuradas?  

Para cada súldista, o Azure Ative Directory avalia todas as políticas e garante que todos os requisitos são cumpridos antes de ter acesso ao utilizador. O acesso ao bloco supera todas as outras definições de configuração. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>O Acesso Condicional funciona com o Exchange ActiveSync?

Sim, pode utilizar o Exchange ActiveSync numa política de Acesso Condicional.

Algumas aplicações em nuvem como o SharePoint e o Exchange Online também suportam protocolos de autenticação legado. Quando uma aplicação de cliente pode usar um protocolo de autenticação legado para aceder a uma aplicação na nuvem, a Azure AD não pode impor uma política de Acesso Condicional nesta tentativa de acesso. Para evitar que uma aplicação do cliente ignore a aplicação das políticas, deve verificar se é possível apenas ativar a autenticação moderna nas aplicações de nuvem afetadas.

### <a name="how-should-you-configure-conditional-access-with-microsoft-365-apps"></a>Como deve configurar o Acesso Condicional com aplicações microsoft 365?

Como as aplicações da Microsoft 365 estão interligadas, recomendamos atribuir aplicações comumente usadas em conjunto ao criar políticas.

Aplicações interligadas comuns incluem Microsoft Flow, Microsoft Planner, Microsoft Teams, Exchange Online, SharePoint e Yammer.

É importante para políticas que exijam interações do utilizador, como a autenticação de vários fatores, quando o acesso é controlado no início de uma sessão ou tarefa. Caso não o faça, os utilizadores não poderão completar algumas tarefas dentro de uma aplicação. Por exemplo, se necessitar de autenticação multi-factor em dispositivos não geridos para aceder ao SharePoint mas não para enviar e-mail, os utilizadores que trabalham no seu e-mail não poderão anexar ficheiros SharePoint a uma mensagem. Mais informações podem ser encontradas no artigo, Quais são as [dependências de serviços no Azure Ative Directory Conditional Access?](service-dependencies.md)

## <a name="what-you-should-avoid-doing"></a>O que deve evitar fazer

A estrutura de Acesso Condicional proporciona-lhe uma grande flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que deve rever cuidadosamente cada política de configuração antes de a lançar para evitar resultados indesejáveis. Neste contexto, deve prestar especial atenção às atribuições que afetam conjuntos completos, tais como **todos os utilizadores /grupos /apps cloud**.

No seu ambiente, deve evitar as seguintes configurações:

**Para todos os utilizadores, todas as aplicações na nuvem:**

- **Bloquear o acesso** - Esta configuração bloqueia toda a sua organização, o que definitivamente não é uma boa ideia.
- **Requerem dispositivo compatível** - Para utilizadores que ainda não tenham inscrito os seus dispositivos, esta política bloqueia todo o acesso, incluindo o acesso ao portal Intune. Se é um administrador sem um dispositivo matriculado, esta política impede-o de voltar ao portal Azure para alterar a política.
- **Requer adesão de domínio** - Este acesso ao bloco de políticas também tem o potencial de bloquear o acesso a todos os utilizadores da sua organização se ainda não tiver um dispositivo de união de domínios.
- **Requer uma política de proteção de aplicações** - Este acesso por bloqueio de políticas também tem o potencial de bloquear o acesso a todos os utilizadores da sua organização se não tiver uma política Intune. Se é um administrador sem uma aplicação de cliente que tenha uma política de proteção de aplicações Intune, esta política impede-o de voltar a portais como o Intune e o Azure.

**Para todos os utilizadores, todas as aplicações em nuvem, todas as plataformas do dispositivo:**

- **Bloquear o acesso** - Esta configuração bloqueia toda a sua organização, o que definitivamente não é uma boa ideia.

## <a name="how-should-you-deploy-a-new-policy"></a>Como deve implementar uma nova política?

Como primeiro passo, deve avaliar a sua política utilizando o ["e se" ferramenta](what-if-tool.md).

Quando as novas políticas estiverem prontas para o seu ambiente, implemente-as por fases:

1. Aplique uma política a um pequeno conjunto de utilizadores e verifique se se comporta como esperado. 
1. Quando expande uma política para incluir mais utilizadores. Continue a excluir todos os administradores da apólice para garantir que ainda têm acesso e que podem atualizar uma política se for necessária uma alteração.
1. Aplicar uma política a todos os utilizadores apenas se necessário. 

Como uma boa prática, crie uma conta de utilizador que seja:

- Dedicado à administração de políticas 
- Excluído de todas as suas políticas

## <a name="policy-migration"></a>Migração política

Considere migrar as políticas que não criou no portal Azure porque:

- Agora pode abordar cenários que não conseguiu lidar antes.
- Pode reduzir o número de políticas que tem de gerir consolidando-as.   
- Pode gerir todas as suas políticas de Acesso Condicional num local central.
- O portal clássico do Azure foi reformado.   

Para obter mais informações, veja [Migrate classic policies in the Azure portal](policy-migration.md) (Migrar políticas clássicas no portal do Azure).

## <a name="next-steps"></a>Passos seguintes

Se quiser saber:

- Como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicações específicas com Acesso Condicional do Diretório Ativo Azure](../authentication/tutorial-enable-azure-mfa.md).
- Como planear as suas políticas de Acesso Condicional, ver [como planear a sua implementação de Acesso Condicional no Diretório Ativo Azure.](plan-conditional-access.md)