---
title: Habilitar a formação em Azure Ative Directory B2C | Microsoft Docs
description: Saiba como identificar menores usando a sua aplicação.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71a3b38da6a63824a42f64052bf16a5fe0e25483
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102522436"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Habilitar a gating de idade em Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A idade em Azure Ative Directory B2C (Azure AD B2C) permite identificar menores que queiram utilizar a sua aplicação, com ou sem o consentimento dos pais. Pode optar por bloquear o menor de iniciar sôms na aplicação. Ou permitir que as utilizações completem a inscrição e forneçam à aplicação o estatuto menor. 

>[!IMPORTANT]
>Esta funcionalidade está em pré-visualização pública. Não utilize recurso para aplicações de produção.
>

Quando a idade é ativada para um fluxo de utilizador, os utilizadores são solicitados para a sua data de nascimento e país de residência. Se um utilizador assinar que não tenha introduzido previamente a informação, terá de a introduzir na próxima vez que iniciar a sua inscrição. As regras são aplicadas sempre que um utilizador assina.

![Screenshot da idade gating informação recolher fluxo](./media/age-gating/age-gating-information-gathering.png)

Azure AD B2C utiliza a informação que o utilizador introduz para identificar se é menor. O campo **AgeGroup** é então atualizado na sua conta. O valor pode `null` ser, `Undefined` , , e `Minor` `Adult` `NotAdult` .  Os **campos ageGroup** e **consentProvidedForMinor** são então utilizados para calcular o valor da **legalAgeGroupClassification**.


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="set-up-your-tenant-for-age-gating"></a>Instale o seu inquilino para a idade de gating

Para utilizar a idade num fluxo de utilizador, você precisa configurar o seu inquilino para ter propriedades extra.

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior. Selecione o diretório que contém o seu inquilino.
1. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Propriedades** para o seu inquilino no menu à esquerda.
1. Under the **Age gating**, selecione **Configure**.
1. Aguarde que a operação esteja concluída e o seu inquilino será preparado para a idade.

::: zone pivot="b2c-user-flow"

## <a name="enable-age-gating-in-your-user-flow"></a>Ativar a gating de idade no fluxo do seu utilizador

Depois de o seu inquilino ser configurado para usar a idade, pode então utilizar esta funcionalidade nos [fluxos](user-flow-versions.md) de utilizador onde está ativada. Permite a formação de idades com os seguintes passos:

1. Crie um fluxo de utilizador que tenha a idade ativada.
1. Depois de criar o fluxo do utilizador, selecione **Propriedades** no menu.
1. Na secção **de gating idade,** selecione **Ativado**.
1. Para **iniciar s seção ou inscrição,** selecione como pretende gerir os utilizadores:
    - Permitir que os menores acedam à sua aplicação.
    - Bloqueie apenas menores com idade inferior à idade de consentimento para aceder à sua aplicação.
    - Bloqueie todos os menores de acederem à sua aplicação.
1. Para **On block**, selecione uma das seguintes opções:
    - **Envie um JSON de volta ao pedido** - esta opção envia uma resposta de volta à aplicação de que um menor foi bloqueado.
    - **Mostrar uma página de erro** - é mostrado ao utilizador uma página informando-os de que não podem aceder à aplicação.

## <a name="test-your-user-flow"></a>Teste o fluxo do seu utilizador

1. Para testar a sua política, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web chamada *testapp1* que registou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **de fluxo do utilizador Executar.**
1. Inscreva-se com uma conta local ou social. Em seguida, selecione o seu país de residência, e data de nascimento que simula um menor. 
1. Repita o teste e selecione uma data de nascimento que simula um adulto.  

Quando se inscreve como menor, deverá ver a seguinte mensagem de erro: *Infelizmente, o seu sinal foi bloqueado. As leis de privacidade e segurança online no seu país impedem o acesso a contas pertencentes a crianças.*

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="enable-age-gating-in-your-custom-policy"></a>Ativar a gating de idade na sua política personalizada

1. Obtenha o exemplo de uma política de gating de idade no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/age-gating).
1. Em cada ficheiro, substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C. Por exemplo, se o nome do seu inquilino B2C for *contosob2c,* todos os casos `yourtenant.onmicrosoft.com` de se tornar `contosob2c.onmicrosoft.com` .
1. Faça o upload dos ficheiros de política.

::: zone-end

## <a name="next-steps"></a>Passos seguintes

- Saiba como gerir o acesso ao [utilizador em Azure AD B2C](manage-user-access.md).

