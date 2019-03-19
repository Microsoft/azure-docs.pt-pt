---
title: O Azure AD Connect Health - diagnosticar duplicados erros de sincronização de atributo | Documentos da Microsoft
description: Este documento descreve o processo de diagnóstico de erros de sincronização de atributo duplicado e uma correção potencial dos cenários de objeto órfão diretamente a partir do portal do Azure.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbdeef7c591221756ad206bf2f3dd78ac3d26c4f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57885322"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnosticar e resolver erros de sincronização de atributo duplicado

## <a name="overview"></a>Descrição geral
Para realçar os erros de sincronização a demorar mais uma etapa, o Azure Active Directory (Azure AD) Connect Health apresenta a remediação de gestão personalizada. Ele resolve os erros de sincronização de atributo duplicado e correções de objetos que ficarão órfãos do Azure AD.
A funcionalidade de diagnóstico tem estes benefícios:
- Ele fornece um procedimento de diagnóstico que restringe os erros de sincronização de atributo duplicado. Além de oferecer correções específicas.
- Ele aplica-se uma correção para cenários dedicadas do Azure AD para resolver o erro numa única etapa.
- Nenhuma atualização ou a configuração é necessária para ativar esta funcionalidade.
Para obter mais informações sobre o Azure AD, consulte [resiliência de atributos de sincronização e duplicação de identidade](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problemas
### <a name="a-common-scenario"></a>Um cenário comum
Quando **QuarantinedAttributeValueMustBeUnique** e **AttributeValueMustBeUnique** ocorrem erros de sincronização, é comum ver uma **UserPrincipalName** ou **Endereços proxy** conflito no Azure AD. Poderá ajudar a resolver os erros de sincronização ao atualizar o objeto de origem em conflito do lado no local. O erro de sincronização será resolvido após a próxima sincronização. Por exemplo, esta imagem indica que dois usuários têm um conflito de seus **UserPrincipalName**. Ambos são **Joe.J\@contoso.com**. Os objetos em conflito são colocados em quarentena no Azure AD.

![Diagnosticar o cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Cenário de objeto órfão
Ocasionalmente, pode achar que um utilizador existente perde a **âncora de origem**. A eliminação de objeto de origem aconteceu no Active Directory no local. Mas a alteração do sinal de eliminação nunca foi sincronizada com o Azure AD. Esta perda acontece por motivos como os problemas de motor de sincronização ou migração de domínio. Quando o mesmo objeto é restaurado ou recriado, logicamente, um utilizador existente deve ser o utilizador para sincronizar a partir da **âncora de origem**. 

Quando um utilizador existente é um objeto apenas na cloud, também pode ver o utilizador em conflito sincronizada com o Azure AD. O utilizador não pode ser correspondido sincronizadas para o objeto existente. Não é possível direto para remapear os **âncora de origem**. Veja mais informações sobre o [base de dados de conhecimento existente](https://support.microsoft.com/help/2647098). 

Por exemplo, o objeto existente no Azure AD preserva a licença de Joe. Um objeto recentemente sincronizado com outro **âncora de origem** ocorre num Estado de atributo duplicado no Azure AD. As alterações de Joe no Active Directory no local não serão aplicadas ao utilizador original do João (objeto existente) no Azure AD.  

![Diagnosticar o cenário de sincronização de objeto órfão de erro](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnóstico e resolução de problemas de passos no Connect Health 
A funcionalidade de diagnóstico suporta objetos de utilizador com os seguintes atributos duplicados:

| Nome de atributo | Tipos de erros de sincronização|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique ou AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Para aceder a esta funcionalidade, **Administrador Global** permissão, ou **contribuinte** permissão a partir das definições de RBAC, é necessário.
>

Siga os passos a partir do portal do Azure para restringir os detalhes do erro de sincronização e a fornecer soluções mais específicas:

![Passos de diagnóstico de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

No portal do Azure, tome algumas providências para identificar cenários de fixable específicos:  
1.  Verifique os **diagnosticar estado** coluna. O estado é apresentado se existe uma forma possível para corrigir um erro de sincronização diretamente a partir do Azure Active Directory. Em outras palavras, um fluxo de resolução de problemas existe que pode restringir o caso de erro e potencialmente corrigi-lo.

| Estado | O que significa? |
| ------------------ | -----------------|
| Não Iniciado | Ainda não visitou este processo de diagnóstico. Consoante o resultado de diagnóstico, há uma possível maneira de corrigir o erro de sincronização diretamente no portal. |
| Correção Manual Obrigatória | O erro não se ajusta os critérios de correções disponíveis no portal. Qualquer um dos tipos de objeto em conflito não são usuários, ou que já utilizou os passos de diagnóstico, e nenhuma resolução de correção estava disponível no portal. No último caso, uma correção do lado no local ainda é uma das soluções. [Leia mais sobre locais correções](https://support.microsoft.com/help/2647098). | 
| Sincronização Pendente | Foi aplicada uma correção. O portal está a aguardar o próximo ciclo de sincronização limpar o erro. |

  >[!IMPORTANT]
  > A coluna de estado de diagnóstico irá repor após cada ciclo de sincronização. 
  >

1. Selecione o **diagnosticar** botão sob os detalhes do erro. Irá responder a algumas perguntas e identificar os detalhes do erro de sincronização. Respostas às perguntas ajudam a identificar um caso de objeto órfão.

1. Se um **fechar** botão não aparece no final de diagnóstico, há nenhuma correção rápida disponível no portal com base em suas respostas. Consulte a solução mostrada no último passo. Correções no local ainda são as soluções. Selecione o **fechar** botão. O estado de erro de sincronização atual muda para **correção Manual obrigatória**. O estado permanece durante o ciclo de sincronização atual.

1. Depois de um caso de objeto órfão for identificado, pode corrigir os atributos duplicados erros de sincronização diretamente no portal. Para acionar o processo, selecione o **aplicar correção** botão. O estado das atualizações de erro de sincronização atual para **pendentes sincronização**.

1. Após o próximo ciclo de sincronização, o erro deve ser removido da lista.

## <a name="how-to-answer-the-diagnosis-questions"></a>Como responder às perguntas de diagnóstico 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>O utilizador existir no Active Directory no local?

Essa pergunta tenta identificar o objeto de origem do utilizador existente do Active Directory no local.  
1. Verifique se o Azure Active Directory tem um objeto com o fornecido **UserPrincipalName**. Se não estiver, responder **não**.
2. Se assim for, verifique se o objeto ainda está no âmbito para sincronização.  
   - Pesquisar no espaço conector do Azure AD utilizando o DN.
   - Se o objeto se encontra no **adicionar pendente** de estado, responder **não**. O Azure AD Connect não é possível ligar o objeto para o objeto à direita do Azure AD.
   - Se o objeto não for encontrado, responder **Sim**.

Nestes exemplos, a pergunta tenta identificar se **Joe Jackson** ainda existe no Active Directory no local.
Para o **cenário comum**, ambos os utilizadores **Joe Johnson** e **Joe Jackson** estão presentes no Active Directory no local. Os objetos em quarentena são dois utilizadores diferentes.

![Diagnosticar o cenário comum de erro de sincronização](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Para o **cenário de objeto órfão**, apenas o utilizador único **Joe Johnson** está presente no Active Directory no local:

![Diagnosticar o objeto órfão de erro de sincronização * utilizador existe * cenário](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Ambas estas contas pertencem ao mesmo utilizador?
Essa pergunta verifica um utilizador em conflito de entrada e o objeto de utilizador existente no Azure AD para ver se eles pertencem ao mesmo utilizador.  
1. O objeto em conflito recentemente está sincronizado com o Azure Active Directory. Compare os atributos de objectos:  
   - Nome a Apresentar
   - Nome Principal de Utilizador
   - ID de objeto
2. Se não for compará-los do Azure AD, verifique se o Active Directory tem objetos com o fornecido **UserPrincipalNames**. Resposta **não** se encontrar ambos.

No exemplo a seguir, os dois objetos pertencem ao mesmo utilizador **Joe Johnson**.

![Diagnosticar o objeto órfão de erro de sincronização * mesmo utilizador * cenário](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>O que acontece depois da correção é aplicada no cenário de objeto órfão
Com base nas respostas às perguntas anteriores, verá a **aplicar correção** botão quando existe uma correção do Azure AD. Neste caso, o objeto no local está a sincronizar com um Azure inesperado objeto do AD. Os dois objetos são mapeados, utilizando o **âncora de origem**. O **aplicar correção** alteração tem estes ou passos semelhantes:
1. Atualizações a **âncora de origem** para o objeto correto no Azure AD.
2. Elimina o objeto em conflito no Azure AD, se estiver presente.

![Diagnosticar o erro de sincronização após a correção](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> O **aplicar correção** alteração aplica-se apenas a casos de objeto órfão.
>

Depois dos passos anteriores, o usuário pode acessar o recurso original, o que é uma ligação para um objeto existente. O **diagnosticar o estado** atualizações do valor na vista de lista **sincronização pendente**. O erro de sincronização será resolvido após a próxima sincronização. Connect irá de estado de funcionamento não mostrar mais o erro de sincronização resolvido na vista de lista.

## <a name="failures-and-error-messages"></a>Falhas e mensagens de erro
**Utilizador com o atributo em conflito é programável eliminada no Azure Active Directory. Certifique-se de que o utilizador é difícil eliminado antes da repetição.**  
O utilizador com o atributo em conflito no Azure AD deve ser limpos antes de poder aplicar a correção. Confira [como eliminar o utilizador permanentemente no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) antes de repetir a correção. O utilizador também serão automaticamente eliminado permanentemente após 30 dias no estado de eliminado de forma recuperável. 

**Não é suportada a âncora de origem de atualização para o utilizador com base na cloud no seu inquilino.**  
Utilizador com base na cloud no Azure AD não deve ter a âncora de origem. Atualizar a âncora de origem não é suportada neste caso. Correção manual não é necessária no local. 

## <a name="faq"></a>FAQ
**P.** O que acontece durante a execução do **aplicar correção** falhar?  
**R.** Se falhar a execução, é possível que o Azure AD Connect está em execução um erro de exportação. Atualize a página de portal e tente novamente após a próxima sincronização. Ciclo de sincronização a predefinição é 30 minutos. 


**P.** E se o **objeto existente** deve ser o objeto a eliminar?  
**R.** Se o **objeto existente** deve ser eliminado, o processo não envolve uma alteração de **âncora de origem**. Normalmente, pode corrigi-lo do Active Directory no local. 


**P.** Permissão um utilizador ter de aplicar a correção?  
**R.** **Administrador global**, ou **contribuinte** partir das definições de RBAC, tem permissão para acessar o diagnóstico e resolução de problemas do processo.


**P.** É necessário que configurar o Azure AD Connect ou atualizar o agente do Azure AD Connect Health para esta funcionalidade?  
**R.** Não, o processo de diagnóstico é um recurso completo com base na cloud.


**P.** Se o objeto existente for programável eliminada, o processo de diagnóstico fará o objeto de Active Directory novamente?  
**R.** Não, a correção não atualizar atributos de objetos que **âncora de origem**.
