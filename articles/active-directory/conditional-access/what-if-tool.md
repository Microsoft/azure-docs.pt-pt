---
title: O que é o que, se a ferramenta no acesso condicional do Azure Active Directory?
description: Saiba como pode compreender o impacto das suas políticas de acesso condicional no seu ambiente.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ec279b9c3333c365e5e516fb61fc10016fefe80
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170127"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>O que é o que, se a ferramenta no acesso condicional do Azure Active Directory?

[Acesso condicional](../active-directory-conditional-access-azure-portal.md) é um recurso do Azure Active Directory (Azure AD) que lhe permite controlar como autorizado aos utilizadores acesso as aplicações na cloud. Como sabe o que esperar formulário as políticas de acesso condicional no seu ambiente? Para responder a essa pergunta, pode utilizar o **acesso condicional e se a ferramenta**.

Este artigo explica como pode utilizar esta ferramenta para testar as suas políticas de acesso condicional.

## <a name="what-it-is"></a>O que é

O **de acesso condicional e se a ferramenta de política** permite-lhe compreender o impacto das suas políticas de acesso condicional no seu ambiente. Em vez de testar as suas políticas ao executar vários inícios de sessão manualmente, esta ferramenta permite avaliar um início de sessão simulado de um utilizador. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação. O relatório não lista apenas política de acesso aplicada políticas de acesso, mas também [políticas clássicas](policy-migration.md#classic-policies) caso existam.    

O que se ferramentas também fornece uma forma de rapidamente determinar as políticas que se aplicam a um utilizador específico. Pode utilizar as informações de, por exemplo, se precisar de resolver um problema.  

## <a name="how-it-works"></a>Como funciona

Na **acesso condicional e se a ferramenta**, tem primeiro de configurar as definições do cenário de início de sessão que pretende simular. Estas definições incluem:

- O utilizador que pretende testar 

- As aplicações na cloud, que o utilizador iria tentar aceder

- As condições sob as qual acesso para o configura cloud aplicações é executada
     
Como passo seguinte, pode iniciar uma simulação de execução que avalia as suas definições. Apenas as políticas ativadas fazem parte de uma edição de avaliação executar.


Quando terminar a avaliação, a ferramenta gera um relatório das políticas afetados.



## <a name="running-the-tool"></a>Executar a ferramenta

Pode encontrar os **e se** ferramenta no **[acesso condicional - políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** página no portal do Azure.

Para iniciar a ferramenta, na barra de ferramentas na parte superior da lista de políticas, clique em **e se**.

![E se](./media/what-if-tool/01.png)

Antes de poder executar uma edição de avaliação, tem de configurar as definições.

## <a name="settings"></a>Definições

Esta secção fornece informações sobre as definições de execução de simulação.

![E se](./media/what-if-tool/02.png)


### <a name="user"></a>Utilizador

Só pode selecionar um utilizador. Este é o único campo obrigatório.

### <a name="cloud-apps"></a>Aplicações na cloud

A predefinição para esta definição é **todas as aplicações na cloud**. A definição predefinida executa uma edição de avaliação de todas as políticas disponíveis no seu ambiente. Pode restringir o âmbito para políticas que afetam a aplicações na cloud específicas.


### <a name="ip-address"></a>Endereço IP

O endereço IP é um único endereço IPv4 para imitar a [condição de localização](location-condition.md). O endereço representa o endereço do dispositivo utilizado pelo seu utilizador para iniciar sessão de acesso à Internet. Pode verificar o endereço IP de um dispositivo por, por exemplo, navegar até [o que é o meu endereço IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Plataformas de dispositivos

Esta definição imita a [condição de plataformas de dispositivos](conditions.md#device-platforms) e representa o equivalente **todas as plataformas (incluindo as não suportadas)**. 
### <a name="client-apps"></a>Aplicações do cliente

Esta definição imita a [condição de aplicações de cliente](conditions.md#client-apps).
Por predefinição, esta definição faz com que uma edição de avaliação de todas as políticas ter **Browser** ou **aplicações móveis e clientes de ambiente de trabalho** seja individualmente ou em ambos selecionado. Também Deteta as políticas que impõem **Exchange ActiveSync (EAS)**. Pode reduzir esta definição ao selecionar:

- **Browser** para avaliar todas as políticas de ter pelo menos **Browser** selecionado. 

- **Aplicações móveis e clientes de ambiente de trabalho** para avaliar todas as políticas de ter pelo menos **aplicações móveis e clientes de ambiente de trabalho** selecionado. 


### <a name="sign-in-risk"></a>Risco de início de sessão

Esta definição imita a [condição de início de sessão de risco](conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Avaliação 

Iniciar uma avaliação clicando **e se**. O resultado da avaliação fornece um relatório que consiste em: 

![E se](./media/what-if-tool/03.png)

- Um indicador se políticas clássicas existem no seu ambiente
- Políticas que se aplicam ao seu utilizador
- Políticas que não se aplicam ao seu utilizador


Se [políticas clássicas](policy-migration.md#classic-policies) existir para as aplicações na cloud selecionada, um indicador é apresentado para. Ao clicar no indicador, será redirecionado para a página de políticas clássicas. Na página de políticas clássicas, pode migrar uma política clássica ou simplesmente desabilitá-lo. Pode retornar o resultado da avaliação ao fechar esta página.

Na lista de políticas que se aplicam ao seu utilizador selecionado, também pode encontrar uma lista de [conceder controlos](controls.md#grant-controls) e [sessão](controls.md#session-controls) controles seu utilizador tem de cumprir.

Na lista de políticas que não se aplicam ao seu usuário, pode e também encontrar os motivos por que não se aplicam estas políticas. Para cada política listada, o motivo pelo qual representa a primeira condição de que não foi cumprida. Uma razão possível para uma política que não se aplica é uma política desativada porque não são avaliadas ainda mais.   



## <a name="next-steps"></a>Passos Seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja [Best practices for conditional access in Azure Active Directory](best-practices.md) (Melhores práticas do acesso condicional no Azure Active Directory). 

- Se pretender migrar políticas clássicas, consulte [migrar políticas clássicas no portal do Azure](policy-migration.md)  
