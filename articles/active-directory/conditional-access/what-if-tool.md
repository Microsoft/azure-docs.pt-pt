---
title: O Acesso Condicional E se ferramenta - Azure Ative Directory
description: Saiba como pode compreender o impacto das suas políticas de Acesso Condicional no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 119540738ac2703afd79891d9bf8a4a9ff0c7472
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952767"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Resolver problemas com a ferramenta What If no Acesso Condicional

[O Acesso Condicional](./overview.md) é uma capacidade do Azure Ative Directory (Azure AD) que lhe permite controlar a forma como os utilizadores autorizados acedem às suas aplicações na nuvem. Como sabe o que esperar das políticas de Acesso Condicional no seu ambiente? Para responder a esta pergunta, pode utilizar a **ferramenta Acesso Condicional E se for a ferramenta**.

Este artigo explica como pode utilizar esta ferramenta para testar as suas políticas de Acesso Condicional.

> [!VIDEO https://www.youtube.com/embed/M_iQVM-3C3E]

## <a name="what-it-is"></a>O que é

A **ferramenta de acesso condicional E se** a ferramenta de política permitir-lhe compreender o impacto das suas políticas de Acesso Condicional no seu ambiente. Em vez de testar a condução das suas políticas realizando várias entradas manualmente, esta ferramenta permite-lhe avaliar uma inscrição simulada de um utilizador. A simulação estima o impacto deste início de sessão nas suas políticas e gera um relatório de simulação. O relatório não lista apenas as políticas de acesso condicional aplicadas, mas também [as políticas clássicas,](policy-migration.md#classic-policies) se existirem.    

A ferramenta **What If** fornece uma forma de determinar rapidamente as políticas que se aplicam a um utilizador específico. Pode utilizar a informação, por exemplo, se precisar de resolver um problema.    

## <a name="how-it-works"></a>Como funciona

Na **ferramenta Acesso Condicional E se a ferramenta**, primeiro precisa de configurar as definições do cenário de início de sposição que pretende simular. Estas definições incluem:

- O utilizador que pretende testar 
- As aplicações na nuvem que o utilizador tentaria aceder
- As condições em que o acesso às aplicações de nuvem configuradas é realizado
     
Como próximo passo, pode iniciar uma simulação que avalia as suas definições. Apenas as políticas que estão habilitados fazem parte de uma avaliação.

Quando a avaliação tiver terminado, a ferramenta gera um relatório das políticas afetadas. Para obter mais informações sobre uma política de acesso condicional, os [insights de acesso condicional e o livro de relatórios](howto-conditional-access-insights-reporting.md) podem fornecer detalhes adicionais sobre políticas apenas no modo relatório e as políticas atualmente ativadas.

## <a name="running-the-tool"></a>Executando a ferramenta

Pode encontrar a ferramenta **E Se** na página Acesso Condicional **[- Políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** no portal Azure.

Para iniciar a ferramenta, na barra de ferramentas no topo da lista de políticas, clique em **E Se**.

:::image type="content" source="./media/what-if-tool/01.png" alt-text="Screenshot do Acesso Condicional - Página de políticas no portal Azure. Na barra de ferramentas, o E se o item for realçado." border="false":::

Antes de poder fazer uma avaliação, tem de configurar as definições.

## <a name="settings"></a>Definições

Esta secção fornece-lhe informações sobre as definições de simulação executadas.

:::image type="content" source="./media/what-if-tool/02.png" alt-text="Screenshot do portal Azure What If page, com campos para um utilizador, aplicações na nuvem, um endereço I P, uma plataforma de dispositivo, uma aplicação do cliente e um risco de login." border="false":::

### <a name="user"></a>User

Só é possível selecionar um utilizador. Este é o único campo necessário.

### <a name="cloud-apps"></a>Aplicações na cloud

O padrão desta definição é **Todas as aplicações em nuvem**. A definição predefinida realiza uma avaliação de todas as políticas disponíveis no seu ambiente. Pode reduzir o âmbito de aplicações que afetam aplicações específicas na nuvem.

### <a name="ip-address"></a>Endereço IP

O endereço IP é um único endereço IPv4 para imitar a [condição de localização](location-condition.md). O endereço representa o endereço virado para a Internet do dispositivo utilizado pelo utilizador para iniciar sôm. Pode verificar o endereço IP de um dispositivo navegando, por exemplo, para [qual é o meu endereço IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Plataformas de dispositivos

Esta definição imita a condição das plataformas do [dispositivo](concept-conditional-access-conditions.md#device-platforms) e representa o equivalente a todas as **plataformas (incluindo não suportadas)**. 

### <a name="client-apps"></a>Aplicações do cliente

Esta definição imita a condição de aplicações do [cliente.](concept-conditional-access-conditions.md#client-apps)
Por padrão, esta definição causa uma avaliação de todas as políticas com aplicações **do Navegador** ou Mobile e clientes de desktop individualmente ou ambos **selecionados.** Também deteta políticas que impõem **o Exchange ActiveSync (EAS)**. Pode reduzir esta definição selecionando:

- **Navegador** para avaliar todas as políticas tendo pelo menos **o Browser** selecionado. 
- **Aplicativos móveis e clientes de desktop** para avaliar todas as políticas com pelo menos **aplicações móveis e clientes de desktop selecionados.** 

### <a name="sign-in-risk"></a>Risco de início de sessão

Esta definição imita a [condição de risco de inscrição](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Avaliação 

Inicia-se uma avaliação clicando **em E Se**. O resultado da avaliação fornece-lhe um relatório que consiste em: 

:::image type="content" source="./media/what-if-tool/03.png" alt-text="Screenshot de um relatório de avaliação. O texto indica que pelo menos uma política clássica está configurada. Os separadores estão disponíveis para visualização de políticas." border="false":::

- Um indicador se as políticas clássicas existem no seu ambiente
- Políticas aplicáveis ao seu utilizador
- Políticas que não se aplicam ao seu utilizador

Se existem [políticas clássicas](policy-migration.md#classic-policies) para as aplicações de nuvem selecionadas, é apresentado um indicador. Ao clicar no indicador, é redirecionado para a página de políticas clássicas. Na página de políticas clássicas, você pode migrar uma política clássica ou simplesmente desativá-la. Pode voltar ao resultado da sua avaliação fechando esta página.

Na lista de políticas aplicáveis ao utilizador selecionado, também pode encontrar uma lista de controlos de [concessão](concept-conditional-access-grant.md) e controlos de [sessão](concept-conditional-access-session.md) que o seu utilizador deve satisfazer.

Na lista de políticas que não se aplicam ao seu utilizador, pode e também encontrar as razões pelas quais estas políticas não se aplicam. Para cada política enumerada, a razão representa a primeira condição que não foi satisfeita. Uma possível razão para uma política que não é aplicada é uma política de deficientes, porque não são mais avaliadas.   

## <a name="next-steps"></a>Passos seguintes

- Mais informações sobre a aplicação da política de acesso condicional podem ser encontradas usando o modo de relatório de políticas apenas usando [insights de acesso condicional e relatórios](howto-conditional-access-insights-reporting.md).
- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte as políticas comuns de [Acesso Condicional.](concept-conditional-access-policy-common.md)
