---
title: Azure AD Connect Health - Diagnosticar erros de sincronização de atributos duplicados / Microsoft Docs
description: Este documento descreve o processo de diagnóstico de erros de sincronização de atributos duplicados e uma possível correção dos cenários de objetos órfãos diretamente do portal Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5942d208fa3859d0a4a80de5f072f2e798fe040f
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028935"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnose and remediate duplicated attribute sync errors (Diagnosticar e resolver erros de sincronização de atributos duplicados)

## <a name="overview"></a>Descrição geral
Dando um passo mais longe para destacar erros de sincronização, o Azure Ative Directory (Azure AD) Connect Health introduz a remediação do autosserviço. Resolve os problemas duplicados atribuem erros de sincronização de atributos e corrige objetos órfãos do Azure AD.
A funcionalidade de diagnóstico tem estes benefícios:
- Fornece um procedimento de diagnóstico que reduz os erros de sincronização de atributos duplicados. E dá correções específicas.
- Aplica uma correção para cenários dedicados da Azure AD para resolver o erro num único passo.
- Não é necessária nenhuma atualização ou configuração para ativar esta funcionalidade.
Para obter mais informações sobre a Azure AD, consulte [a sincronização de identidade e duplica a resiliência do atributo](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problemas
### <a name="a-common-scenario"></a>Um cenário comum
Quando os erros de sincronização **quarantinedAttributeValueMustBeUnique** e **AttributeValueMustBeUnique** acontecem, é comum ver um **UserPrincipalName** ou **Proxy Addresss** conflict in Azure AD. Pode resolver os erros de sincronização atualizando o objeto de origem conflituoso do lado do local. O erro de sincronização será resolvido após a próxima sincronização. Por exemplo, esta imagem indica que dois utilizadores têm um conflito no seu **Nome UserPrincipal**. Ambos são **Joe.J. \@ contoso.com.** Os objetos contraditórios estão em quarentena em Azure AD.

![Diagnosticar cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Cenário de objeto órfão
Ocasionalmente, poderá descobrir que um utilizador existente perde a **Âncora de Origem**. A supressão do objeto de origem aconteceu no Ative Directory. Mas a mudança do sinal de eliminação nunca foi sincronizada para a Azure AD. Esta perda acontece por razões como problemas de sincronização do motor ou migração de domínios. Quando o mesmo objeto for restaurado ou recriado, logicamente, um utilizador existente deve ser o utilizador a sincronizar a partir da **Âncora de Origem**. 

Quando um utilizador existente é um objeto apenas na nuvem, também pode ver o utilizador em conflito sincronizado com a Azure AD. O utilizador não pode ser combinado em sincronização com o objeto existente. Não há forma direta de remaptar a **Âncora de Origem.** Veja mais sobre a [base de conhecimento existente.](https://support.microsoft.com/help/2647098) 

Como exemplo, o objeto existente em Azure AD preserva a licença de Joe. Um objeto recentemente sincronizado com uma **âncora de origem** diferente ocorre em um estado de atributo duplicado em Azure AD. Alterações para Joe em instalações Ative Directy não serão aplicadas ao utilizador original de Joe (objeto existente) em Azure AD.  

![Diagnosticar cenário de objeto órfão de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Etapas de diagnóstico e resolução de problemas na Connect Health 
A função de diagnóstico suporta objetos do utilizador com os seguintes atributos duplicados:

| Nome do atributo | Tipos de erros de sincronização|
| ------------------ | -----------------|
| UserPrincipalName | QuarentenaAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| ProxyAddresses | QuarentenaAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| SipProxyAddress | AtributoValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AtributoValueMustBeUnique |

>[!IMPORTANT]
> Para aceder a esta funcionalidade, é necessária a permissão global de **administração,** ou permissão do **Contribuinte** da Azure RBAC.
>

Siga os passos do portal Azure para reduzir os detalhes do erro de sincronização e fornecer soluções mais específicas:

![Etapas de diagnóstico de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

A partir do portal Azure, tome algumas medidas para identificar cenários fixos específicos:  
1.  Verifique a coluna **de estado do diagnóstico.** O estado mostra se há uma maneira possível de corrigir um erro de sincronização diretamente do Azure Ative Directory. Por outras palavras, existe um fluxo de resolução de problemas que pode reduzir o caso de erro e potencialmente corrigi-lo.

| Estado | O que é que isso significa? |
| ------------------ | -----------------|
| Não começou | Não visitou este processo de diagnóstico. Dependendo do resultado do diagnóstico, há uma forma potencial de corrigir o erro de sincronização diretamente do portal. |
| Correção manual necessária | O erro não se enquadra nos critérios das correções disponíveis a partir do portal. Ou os tipos de objetos conflituosos não são utilizadores, ou já passou pelos passos de diagnóstico, e nenhuma resolução de correção estava disponível a partir do portal. Neste último caso, uma correção do lado do local continua a ser uma das soluções. [Leia mais sobre correções no local.](https://support.microsoft.com/help/2647098) | 
| Sincronização pendente | Foi aplicada uma correção. O portal está à espera do próximo ciclo de sincronização para limpar o erro. |

  >[!IMPORTANT]
  > A coluna de estado de diagnóstico será reiniciada após cada ciclo de sincronização. 
  >

1. Selecione o botão **Diagnosticar** sob os detalhes de erro. Responderá a algumas perguntas e identificará os detalhes do erro de sincronização. As respostas às perguntas ajudam a identificar um caso de objeto órfão.

1. Se um botão **Close** aparecer no final dos diagnósticos, não existe uma correção rápida disponível no portal com base nas suas respostas. Consulte a solução mostrada no último passo. As correções das instalações ainda são as soluções. Selecione o botão **Fechar.** O estado do erro de sincronização atual muda para a **correção manual necessária**. O estado permanece durante o ciclo de sincronização atual.

1. Depois de identificado um caso de objeto órfão, pode corrigir os erros de sincronização de atributos duplicados diretamente do portal. Para ativar o processo, selecione o botão **Aplicar Corrigir.** O estado das atualizações atuais de erro de sincronização para **sincronização pendente**.

1. Após o próximo ciclo de sincronização, o erro deve ser removido da lista.

## <a name="how-to-answer-the-diagnosis-questions"></a>Como responder às questões de diagnóstico 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>O utilizador existe no seu Ative Directory no local?

Esta questão tenta identificar o objeto de origem do utilizador existente a partir do Ative Directory no local.  
1. Verifique se o Azure Ative Directory tem um objeto com o **nome do UtilizadorPrincipal.** Se não, **responda nº.**
2. Se o fizer, verifique se o objeto ainda está no âmbito da sincronização.  
   - Pesse o espaço do conector Azure AD utilizando o DN.
   - Se o objeto for encontrado no estado de **Add Pendente,** **responda nº**. O Azure AD Connect não consegue ligar o objeto ao objeto AZure AD certo.
   - Se o objeto não for encontrado, responda **Sim.**

Nestes exemplos, a questão tenta identificar se **Joe Jackson** ainda existe no Ative Directory.
Para o **cenário comum**, tanto os utilizadores **Joe Johnson** como Joe **Jackson** estão presentes no Ative Directory. Os objetos em quarentena são dois utilizadores diferentes.

![Diagnosticar cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Para o **cenário de objeto órfão,** apenas o único utilizador **Joe Johnson** está presente no Ative Directory:

![Diagnosticar erro de sincronização objeto órfão *does user exist* scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Estas duas contas pertencem ao mesmo utilizador?
Esta questão verifica um utilizador conflituoso e o objeto de utilizador existente em Azure AD para ver se pertencem ao mesmo utilizador.  
1. O objeto conflituoso está recentemente sincronizado com o Azure Ative Directory. Compare os atributos dos objetos:  
   - Nome a Apresentar
   - Nome Principal de Utilizador
   - ID de objeto
2. Se a Azure AD não os comparar, verifique se o Ative Directory tem objetos com os **nomes de UserPrincipalNames** fornecidos . Responda **não** se encontrar os dois.

No exemplo seguinte, os dois objetos pertencem ao mesmo utilizador **Joe Johnson.**

![Diagnosticar erro de sincronização objeto órfão *mesmo utilizador* cenário](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>O que acontece depois da correção é aplicada no cenário do objeto órfão
Com base nas respostas às perguntas anteriores, verá o botão **Apply Fix** quando houver uma correção disponível a partir do Azure AD. Neste caso, o objeto no local está a sincronizar-se com um inesperado objeto AD Azure. Os dois objetos são mapeados utilizando a **âncora de origem**. A alteração **Apply Fix** toma estes ou passos semelhantes:
1. Atualiza a **Âncora de Origem** ao objeto correto em Azure AD.
2. Elimina o objeto conflituoso em Azure AD se estiver presente.

![Diagnosticar erro de sincronização após a correção](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> A alteração **Apply Fix** aplica-se apenas a casos de objetos órfãos.
>

Após os passos anteriores, o utilizador pode aceder ao recurso original, que é uma ligação a um objeto existente. O valor **do estado** do diagnóstico na lista visualiza atualizações para **O Sincronização Pendente**. O erro de sincronização será resolvido após a próxima sincronização. A Connect Health deixará de apresentar o erro de sincronização resolvido na vista da lista.

## <a name="failures-and-error-messages"></a>Falhas e mensagens de erro
**O utilizador com atributo conflituoso é eliminado suavemente no Diretório Ativo Azure. Certifique-se de que o utilizador é duramente eliminado antes de voltar a tentar.**  
O utilizador com atributo conflituoso no Azure AD deve ser limpo antes de poder aplicar a correção. Verifique [como eliminar o utilizador permanentemente no Azure AD](../fundamentals/active-directory-users-restore.md) antes de voltar a tentar a correção. O utilizador também será eliminado automaticamente permanentemente após 30 dias em estado de apagação suave. 

**A atualização da âncora de origem para o utilizador baseado na nuvem no seu inquilino não é suportada.**  
O utilizador baseado em nuvem em Azure AD não deve ter âncora de origem. Neste caso, a atualização da âncora de origem não é suportada. A correção manual é necessária a partir do local. 

**O processo de correção não atualizou os valores.**
As definições específicas, tais como [userWriteback no Azure AD Connect,](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-preview#user-writeback) não são suportadas. Por favor, desative as definições. 

## <a name="faq"></a>FAQ
**Q.** O que acontece se a execução da **Correção de Aplicação** falhar?  
**A.** Se a execução falhar, é possível que o Azure AD Connect esteja a executar um erro de exportação. Refresque a página do portal e redama depois da próxima sincronização. O ciclo de sincronização padrão é de 30 minutos. 


**Q.** E se o **objeto existente** for o objeto a ser eliminado?  
**A.** Se o **objeto existente** for eliminado, o processo não implica uma alteração da Âncora de **Origem**. Normalmente, pode consertá-lo a partir do Ative Directory. 


**Q.** Que permissão precisa um utilizador para aplicar a correção?  
**A.** **A Global Admin**, ou **Colaboradora** da Azure RBAC, tem permissão para aceder ao processo de diagnóstico e resolução de problemas.


**Q.** Tenho de configurar o Azure AD Connect ou atualizar o agente Azure AD Connect Health para esta funcionalidade?  
**A.** Não, o processo de diagnóstico é uma característica completa baseada na nuvem.


**Q.** Se o objeto existente for eliminado suavemente, o processo de diagnóstico tornará o objeto ativo novamente?  
**A.** Não, a correção não atualizará os atributos de objetos que não **o Source Anchor**.