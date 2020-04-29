---
title: Azure AD Connect Health - Diagnosticar erros duplicados de sincronização do atributo Microsoft Docs
description: Este documento descreve o processo de diagnóstico de erros de sincronização de atributos duplicados e uma possível correção dos cenários de objetoórfão diretamente do portal Azure.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48ed9abf3e088e2581a3dd81b7c89e6b99da3ceb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76897187"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnose and remediate duplicated attribute sync errors (Diagnosticar e resolver erros de sincronização de atributos duplicados)

## <a name="overview"></a>Descrição geral
Dando um passo mais longe para destacar erros de sincronização, o Azure Ative Directory (Azure AD) Connect Health introduz a reparação de self-service. Resolve problemas duplicados erros de sincronização de atributos e corrige objetos órfãos de Azure AD.
A funcionalidade de diagnóstico tem estes benefícios:
- Fornece um procedimento de diagnóstico que reduz os erros duplicados de sincronização de atributos. E dá correções específicas.
- Aplica uma correção para cenários dedicados da AD Azure para resolver o erro num único passo.
- Não é necessária nenhuma atualização ou configuração para ativar esta funcionalidade.
Para obter mais informações sobre a AD Azure, consulte a sincronização de identidade e duplicar a resiliência do [atributo.](how-to-connect-syncservice-duplicate-attribute-resiliency.md)

## <a name="problems"></a>Problemas
### <a name="a-common-scenario"></a>Um cenário comum
Quando os erros de sincronização **QuarantinedAttributeValueMustBeUnique** e **AttributeValueMustBeUnique** acontecem, é comum ver um **userprincipalName** ou **proxy Addresss** entrar em conflito em Azure AD. Pode resolver os erros de sincronização atualizando o objeto de origem conflituoso do lado no local. O erro de sincronização será resolvido após a próxima sincronização. Por exemplo, esta imagem indica que dois utilizadores têm um conflito do seu **Nome Principal**de Utilizador . Ambos são **Joe.J\@contoso.com.** Os objetos contraditórios estão em quarentena em Azure D.C.

![Diagnosticar cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Cenário de objeto órfão
Ocasionalmente, pode descobrir que um utilizador existente perde a **Âncora Fonte**. A eliminação do objeto de origem aconteceu no local do Diretório Ativo. Mas a mudança do sinal de eliminação nunca foi sincronizada para a AD Azure. Esta perda ocorre por razões como problemas de sincronização do motor ou migração de domínios. Quando o mesmo objeto for restaurado ou recriado, logicamente, um utilizador existente deve ser o utilizador a sincronizar a partir da **Âncora Fonte**. 

Quando um utilizador existente é um objeto apenas na nuvem, também pode ver o utilizador conflituoso sincronizado com a AD Azure. O utilizador não pode ser comparado em sincronização com o objeto existente. Não há forma direta de remapear a **Âncora De Origem.** Veja mais sobre a base de [conhecimento existente.](https://support.microsoft.com/help/2647098) 

Como exemplo, o objeto existente em Azure AD preserva a licença de Joe. Um objeto recém-sincronizado com uma **âncora de origem** diferente ocorre num estado de atributo duplicado em Azure AD. As alterações para Joe no ative directory não serão aplicadas ao utilizador original de Joe (objeto existente) em Azure AD.  

![Diagnosticar erro de sincronização cenário órfão de objeto](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Passos de diagnóstico e resolução de problemas na Connect Health 
A função de diagnóstico suporta objetos do utilizador com os seguintes atributos duplicados:

| Nome do atributo | Tipos de erros de sincronização|
| ------------------ | -----------------|
| UserPrincipalName | QuarentenaAtribuieValueMustBeUnique ou AttributeValueMustBeUnique | 
| ProxyAddresses | QuarentenaAtribuieValueMustBeUnique ou AttributeValueMustBeUnique | 
| SipProxyAddress | AtribuivalueMustBeUnique | 
| Identificador onPremiseSecurityIdentifier |  AtribuivalueMustBeUnique |

>[!IMPORTANT]
> Para aceder a esta funcionalidade, é necessária a permissão global da **Administração,** ou autorização do **Colaborador** a partir das definições RBAC.
>

Siga os passos do portal Azure para reduzir os detalhes do erro de sincronização e fornecer soluções mais específicas:

![Etapas de diagnóstico de erro sincronizado](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

A partir do portal Azure, tome algumas medidas para identificar cenários específicos reparáveis:  
1.  Verifique a coluna de estado do **diagnóstico.** O estado mostra se há uma maneira possível de corrigir um erro de sincronização diretamente do Diretório Ativo Azure. Por outras palavras, existe um fluxo de resolução de problemas que pode reduzir o caso de erro e potencialmente corrigi-lo.

| Estado | O que é que isso significa? |
| ------------------ | -----------------|
| Não começou | Não visitou este processo de diagnóstico. Dependendo do resultado do diagnóstico, há uma forma potencial de corrigir o erro de sincronização diretamente do portal. |
| Correção Manual Necessária | O erro não se enquadra nos critérios das correções disponíveis no portal. Ou os tipos de objetos conflituosos não são utilizadores, ou já passou pelos passos de diagnóstico, e não estava disponível nenhuma resolução de correção a partir do portal. Neste último caso, uma correção do lado no local continua a ser uma das soluções. [Leia mais sobre correções no local](https://support.microsoft.com/help/2647098). | 
| Sincronização pendente | Foi aplicada uma correção. O portal aguarda o próximo ciclo de sincronização para limpar o erro. |

  >[!IMPORTANT]
  > A coluna de estado de diagnóstico será redefinida após cada ciclo de sincronização. 
  >

1. Selecione o botão **Diagnosticar** sob os detalhes do erro. Responderá a algumas perguntas e identificará os detalhes do erro de sincronização. As respostas às perguntas ajudam a identificar um caso de objeto órfão.

1. Se um botão **Close** aparecer no final dos diagnósticos, não há uma correção rápida disponível no portal com base nas suas respostas. Consulte a solução mostrada no último passo. As correções a partir do local ainda são as soluções. Selecione o botão **Fechar.** O estado do erro de sincronização atual muda para a **correção manual necessária**. O estado permanece durante o ciclo de sincronização atual.

1. Após a identificação de um caso de objeto órfão, pode corrigir os erros de sincronização de atributos duplicados diretamente do portal. Para desencadear o processo, selecione o botão **Aplicar Corrigir.** O estado das atualizações de erro de sincronização atual para **sincronização pendente**.

1. Após o próximo ciclo de sincronização, o erro deve ser removido da lista.

## <a name="how-to-answer-the-diagnosis-questions"></a>Como responder às questões de diagnóstico 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>O utilizador existe no seu Diretório Ativo no local?

Esta questão tenta identificar o objeto de origem do utilizador existente a partir do Diretório Ativo no local.  
1. Verifique se o Diretório Ativo azure tem um objeto com o **nome principal**do utilizador fornecido . Se não, responda **Não.**
2. Se o fizer, verifique se o objeto ainda está no âmbito da sincronização.  
   - Procure no espaço do conector Azure AD utilizando o DN.
   - Se o objeto for encontrado no estado **Pendente adicionar,** responda **Nº**. O Azure AD Connect não pode ligar o objeto ao objeto Azure AD direito.
   - Se o objeto não for encontrado, responda **Sim.**

Nestes exemplos, a questão tenta identificar se **Joe Jackson** ainda existe no local Ative Directory.
Para o **cenário comum,** tanto os utilizadores **Joe Johnson** como Joe **Jackson** estão presentes no ative directory no local. Os objetos em quarentena são dois utilizadores diferentes.

![Diagnosticar cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Para o cenário de **objeto órfão,** apenas o único utilizador **Joe Johnson** está presente no local Ative Directory:

![Diagnosticar erro de sincronização objeto órfão *o cenário do utilizador existe*](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Ambas as contas pertencem ao mesmo utilizador?
Esta questão verifica um utilizador em conflito e o objeto de utilizador existente em Azure AD para ver se pertencem ao mesmo utilizador.  
1. O objeto conflituoso está recentemente sincronizado com o Diretório Ativo Azure. Compare os atributos dos objetos:  
   - Nome a Apresentar
   - Nome Principal de Utilizador
   - ID de objeto
2. Se o Azure AD não os comparar, verifique se o Diretório Ativo tem objetos com os **Nomes de Utilizadores**fornecidos . Responda **não** se encontrar os dois.

No exemplo seguinte, os dois objetos pertencem ao mesmo utilizador **Joe Johnson.**

![Diagnosticar erro de sincronização objeto órfão *mesmo cenário de utilizador*](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>O que acontece depois da correção é aplicado no cenário do objeto órfão
Com base nas respostas às perguntas anteriores, verá o botão **Apply Fix** quando houver uma correção disponível a partir de Azure AD. Neste caso, o objeto no local está sincronizando-se com um objeto ad ad azul inesperado. Os dois objetos são mapeados utilizando a **Âncora Fonte**. A alteração **Apply Fix** toma estes ou passos semelhantes:
1. Atualiza a **Âncora Fonte** ao objeto correto em Azure AD.
2. Elimina o objeto conflituoso em Azure AD se estiver presente.

![Diagnosticar erro de sincronização após a correção](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> A alteração **Apply Fix** aplica-se apenas a casos de objetos órfãos.
>

Após os passos anteriores, o utilizador pode aceder ao recurso original, que é uma ligação a um objeto existente. O valor **do estado** do diagnóstico na lista visualiza atualizações para **Sincronização Pendente**. O erro de sincronização será resolvido após a próxima sincronização. A Connect Health deixará de mostrar o erro de sincronização resolvido na vista da lista.

## <a name="failures-and-error-messages"></a>Falhas e mensagens de erro
**O utilizador com atributo conflituoso é eliminado suavemente no Diretório Ativo Azure. Certifique-se de que o utilizador está duramente apagado antes de voltar a tentar.**  
O utilizador com atributo conflituoso em Azure AD deve ser limpo antes de poder aplicar a correção. Verifique [como eliminar o utilizador permanentemente em Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) antes de voltar a tentar a correção. O utilizador também será automaticamente eliminado permanentemente após 30 dias em estado suave apagado. 

**A atualização da âncora de origem para o utilizador baseado em nuvem no seu inquilino não é suportada.**  
O utilizador baseado em nuvem em Azure AD não deve ter âncora de origem. A atualização da âncora de origem não é suportada neste caso. A correção manual é necessária a partir do local. 

## <a name="faq"></a>FAQ
**Q.** O que acontece se a execução da **Correção de Aplicação** falhar?  
**A.** Se a execução falhar, é possível que o Azure AD Connect esteja a cometer um erro de exportação. Refresque a página do portal e tente novamente após a próxima sincronização. O ciclo de sincronização padrão é de 30 minutos. 


**Q.** E se o **objeto existente** for o objeto a ser eliminado?  
**A.** Se o **objeto existente** for eliminado, o processo não implica uma alteração da Âncora **de Origem**. Normalmente, pode consertá-lo a partir do Diretório Ativo no local. 


**Q.** Que permissão precisa um utilizador para aplicar a correção?  
**A.** **A Global Admin**, ou **Colaborador a** partir das definições rBAC, tem permissão para aceder ao processo de diagnóstico e resolução de problemas.


**Q.** Tenho de configurar o Azure AD Connect ou atualizar o agente Azure AD Connect Health para esta funcionalidade?  
**A.** Não, o processo de diagnóstico é uma característica completa baseada na nuvem.


**Q.** Se o objeto existente for apagado suavemente, o processo de diagnóstico tornará o objeto ativo novamente?  
**A.** Não, a correção não atualiza os atributos do objeto para além da **Âncora de Origem**.
