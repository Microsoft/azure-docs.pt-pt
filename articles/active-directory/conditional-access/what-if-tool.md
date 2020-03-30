---
title: O acesso condicional O que se ferramenta - Diretório Ativo Azure
description: Saiba como pode compreender o impacto das suas políticas de Acesso Condicional no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc87b434664ba12cefeb233972e749f631d8a2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620690"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Resolução de problemas utilizando a ferramenta What If no Acesso Condicional

[O Acesso Condicional](../active-directory-conditional-access-azure-portal.md) é uma capacidade do Azure Ative Directory (Azure AD) que lhe permite controlar a forma como os utilizadores autorizados acedem às suas aplicações na nuvem. Como sabe o que esperar das políticas de Acesso Condicional no seu ambiente? Para responder a esta pergunta, pode utilizar o **Acesso Condicional E Se a ferramenta**.

Este artigo explica como pode usar esta ferramenta para testar as suas políticas de Acesso Condicional.

## <a name="what-it-is"></a>O que é

O **acesso condicional O que se** a ferramenta política lhe permitir compreender o impacto das suas políticas de Acesso Condicional no seu ambiente. Em vez de testar a condução das suas políticas executando múltiplos sign-ins manualmente, esta ferramenta permite-lhe avaliar um sinal de inscrição simulado de um utilizador. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação. O relatório enumera não só as políticas de acesso condicional aplicadas, mas também [as políticas clássicas](policy-migration.md#classic-policies) de acesso, caso existam.    

A ferramenta **What If** fornece uma forma de determinar rapidamente as políticas que se aplicam a um utilizador específico. Pode usar a informação, por exemplo, se precisar de resolver um problema.    

## <a name="how-it-works"></a>Como funciona

Na **ferramenta Acesso Condicional O que se a ferramenta,** é necessário configurar primeiro as definições do cenário de início de sessão que pretende simular. Estas definições incluem:

- O utilizador que pretende testar 
- As aplicações em nuvem que o utilizador tentaria aceder
- As condições em que o acesso às aplicações de nuvem de configures é realizado
     
Como próximo passo, pode iniciar uma simulação que avalia as suas definições. Apenas as políticas que estão habilitadas fazem parte de uma avaliação.

Quando a avaliação terminar, a ferramenta gera um relatório das políticas afetadas.

## <a name="running-the-tool"></a>Executar a ferramenta

Pode encontrar a ferramenta **What If** na página Acesso Condicional **[- Políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** no portal Azure.

Para iniciar a ferramenta, na barra de ferramentas no topo da lista de políticas, clique em **What If**.

![Hipótese](./media/what-if-tool/01.png)

Antes de poder fazer uma avaliação, tem de configurar as definições.

## <a name="settings"></a>Definições

Esta secção fornece-lhe informações sobre as definições de simulação executadas.

![Hipótese](./media/what-if-tool/02.png)

### <a name="user"></a>Utilizador

Só é possível selecionar um utilizador. Este é o único campo necessário.

### <a name="cloud-apps"></a>Aplicativos em nuvem

O padrão para esta definição são **todas as aplicações em nuvem**. A definição padrão realiza uma avaliação de todas as políticas disponíveis no seu ambiente. Pode reduzir o âmbito de aplicações que afetam aplicações específicas da nuvem.

### <a name="ip-address"></a>Endereço IP

O endereço IP é um único endereço IPv4 para imitar a condição de [localização](location-condition.md). O endereço representa o endereço virado para a Internet do dispositivo utilizado pelo utilizador para iniciar sessão. Pode verificar o endereço IP de um dispositivo navegando, por exemplo, para [o que é o meu endereço IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Plataformas de dispositivos

Esta definição imita a condição das plataformas do [dispositivo](concept-conditional-access-conditions.md#device-platforms) e representa o equivalente a todas as **plataformas (incluindo não suportadas)**. 

### <a name="client-apps"></a>Aplicações do cliente

Esta definição imita a condição das [aplicações do cliente.](concept-conditional-access-conditions.md#client-apps-preview)
Por padrão, esta definição causa uma avaliação de todas as políticas que têm aplicações **browser** ou mobile e clientes de desktop individualmente ou ambos **selecionados.** Também deteta políticas que impõem **o Exchange ActiveSync (EAS)**. Pode reduzir esta definição selecionando:

- **Browser** para avaliar todas as políticas tendo pelo menos **o Navegador** selecionado. 
- **Aplicativos móveis e clientes de desktop** para avaliar todas as políticas com pelo menos **aplicações móveis e clientes de desktop selecionados.** 

### <a name="sign-in-risk"></a>Risco de inscrição

Esta definição imita a [condição de risco de inscrição](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Avaliação 

Inicia-se uma avaliação clicando no **What If**. O resultado da avaliação fornece-lhe um relatório que consiste em: 

![Hipótese](./media/what-if-tool/03.png)

- Um indicador se existem políticas clássicas no seu ambiente
- Políticas que se aplicam ao seu utilizador
- Políticas que não se aplicam ao seu utilizador

Se existirem [políticas clássicas](policy-migration.md#classic-policies) para as aplicações em nuvem selecionadas, é-lhe apresentado um indicador. Ao clicar no indicador, é redirecionado para a página de políticas clássicas. Na página de políticas clássicas, pode migrar uma política clássica ou simplesmente desativá-la. Pode voltar ao resultado da sua avaliação fechando esta página.

Na lista de políticas aplicáveis ao utilizador selecionado, também pode encontrar uma lista de controlos de [subvenções](concept-conditional-access-grant.md) e controlos de [sessão](concept-conditional-access-session.md) que o utilizador deve satisfazer.

Na lista de políticas que não se aplicam ao seu utilizador, pode e também encontra as razões pelas quais estas políticas não se aplicam. Para cada política enumerada, a razão representa a primeira condição que não foi satisfeita. Uma possível razão para uma política que não é aplicada é uma política de deficientes, porque não são mais avaliadas.   

## <a name="next-steps"></a>Passos seguintes

- Se quiser saber configurar uma política de Acesso Condicional, consulte [RequireM para aplicações específicas com Acesso Condicional de Diretório Ativo Azure](app-based-mfa.md).
- Se estiver pronto para configurar as políticas de Acesso Condicional para o seu ambiente, consulte as [melhores práticas de Acesso Condicional no Diretório Ativo Azure](best-practices.md). 
- se quiser migrar políticas clássicas, consulte [políticas clássicas de migrar no portal Azure](policy-migration.md)  
