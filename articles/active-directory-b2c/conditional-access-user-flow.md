---
title: Adicionar Acesso Condicional a um fluxo de utilizador em Azure AD B2C
description: Saiba como adicionar acesso condicional aos fluxos de utilizador Azure AD B2C. Configure as definições de autenticação multi-factor (MFA) e as políticas de acesso condicional nos fluxos do utilizador para impor políticas e remediar os acessos de risco.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033919"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Adicionar Acesso Condicional aos fluxos de utilizadores no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O Acesso Condicional pode ser adicionado aos fluxos de utilizador do seu Azure Ative Directory B2C (Azure AD B2C) ou políticas personalizadas para gerir as entradas de risco nas suas aplicações. Azure Ative Directy (Azure AD) Conditional Access é a ferramenta utilizada pelo Azure AD B2C para reunir sinais, tomar decisões e impor políticas organizacionais.

![Fluxo de acesso condicional](media/conditional-access-user-flow/conditional-access-flow.png)

Automatizar a avaliação dos riscos com as condições políticas significa que os acessos de risco são identificados imediatamente e, em seguida, remediados ou bloqueados.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Descrição geral do serviço

O Azure AD B2C avalia cada evento de inscrição e garante que todos os requisitos de política são cumpridos antes de conceder o acesso ao utilizador. Durante esta fase **de Avaliação,** o serviço de Acesso Condicional avalia os sinais recolhidos pela Proteção de Identidade durante os eventos de entrada. O resultado deste processo de avaliação é um conjunto de alegações que indicam se a inscrição deve ser concedida ou bloqueada. A política Azure AD B2C utiliza estas alegações para tomar uma ação dentro do fluxo do utilizador, como bloquear o acesso ou desafiar o utilizador com uma remediação específica como a autenticação de vários fatores (MFA). O "bloqueio de acesso" substitui todas as outras definições.

::: zone pivot="b2c-custom-policy"
O exemplo a seguir mostra um perfil técnico de Acesso Condicional que é utilizado para avaliar a ameaça de inscrição.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

Na fase **de Remediação** que se segue, o utilizador é desafiado com MFA. Uma vez concluído, a Azure AD B2C informa a Proteção de Identidade que a ameaça de inscrição identificada foi remediada e por que método. Neste exemplo, o Azure AD B2C indica que o utilizador concluiu com sucesso o desafio de autenticação de vários fatores. 

::: zone pivot="b2c-custom-policy"

O exemplo a seguir mostra um perfil técnico de acesso condicional utilizado para remediar a ameaça identificada:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>Componentes da solução

Estes são os componentes que permitem o acesso condicional em Azure AD B2C:

- **Fluxo de utilizador** ou **política personalizada** que orienta o utilizador através do processo de inscrição e inscrição.
- **Política de Acesso Condicional** que reúne sinais para tomar decisões e impor políticas organizacionais. Quando um utilizador assina a sua aplicação através de uma política Azure AD B2C, a política de Acesso Condicional utiliza sinais de Proteção de Identidade Azure AD para identificar inserções de risco e apresenta as medidas de reparação adequadas.
- **Aplicação registada** que direciona os utilizadores para o fluxo de utilizador Azure AD B2C apropriado ou para a política personalizada.
- [Tor Browser](https://www.torproject.org/download/) para simular um insusicro de risco.

## <a name="service-limitations-and-considerations"></a>Limitações e considerações de serviço

Ao utilizar o Acesso Condicional Azure AD, considere o seguinte:

- A Proteção de Identidade está disponível para identidades locais e sociais, como google ou Facebook. Para identidades sociais, é necessário ativar manualmente o Acesso Condicional. A deteção é limitada porque as credenciais de conta social são geridas pelo fornecedor de identidade externa.
- Nos inquilinos Azure AD B2C, apenas um subconjunto de políticas de [acesso condicional Azure AD](../active-directory/conditional-access/overview.md) estão disponíveis.


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Escalão de preço

O Azure AD B2C **Premium P2** é necessário para criar políticas de inscrição arriscadas. Os inquilinos **Premium P1** podem criar uma política baseada na localização, aplicação, política baseada no utilizador ou em grupo. Para mais informações, consulte [alterar o seu nível de preços Azure AD B2C](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Prepare o seu inquilino Azure AD B2C

Para adicionar uma política de acesso condicional, desative os predefinições de segurança:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
3. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
4. Selecione **Propriedades** e, em seguida, **selecione Gerir as predefinições de Segurança**.

   ![Desativar os incumprimentos de segurança](media/conditional-access-user-flow/disable-security-defaults.png)

5. Em **Ativar as falhas de Segurança**, selecione **Nº**.

   ![Desafine o alternância de segurança de Ativação para Não](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Adicionar uma política de acesso condicional

Uma política de acesso condicional é uma declaração se-então de atribuições e controlos de acesso. Uma política de acesso condicional reúne sinais para tomar decisões e impor políticas organizacionais. O operador lógico entre as atribuições é *e.* O operador em cada atribuição é *o Or*.

![Atribuições de acesso condicional](media/conditional-access-user-flow/conditional-access-assignments.png)

Para adicionar uma política de acesso condicional:

1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Segurança**, selecione **Acesso Condicional (Pré-visualização)**. Abre a página **Políticas de Acesso Condicional.**
1. Selecione **+ Nova política**.
1. Insira um nome para a apólice, como *o bloqueio de entrada de risco*.
1. Em **Atribuições**, escolha **Utilizadores e grupos**, e, em seguida, selecione uma das seguintes configurações suportadas:

    |Incluir  |Licença | Notas  |
    |---------|---------|---------|
    |**Todos os utilizadores** | P1, P2 |Se optar por incluir **Todos os Utilizadores,** esta política irá afetar todos os seus utilizadores. Para não se trancar, exclua a sua conta administrativa escolhendo **Excluir,** selecionando **funções de Diretório,** e selecionando o **Administrador Global** na lista. Também pode selecionar **Utilizadores e Grupos e,** em seguida, selecionar a sua conta na lista **de utilizadores excluídos Select.**  | 
 
1. Selecione **aplicativos cloud ou ações** e, em seguida, **selecione aplicações**. Navegue pela sua [aplicação de partidos dependentes](tutorial-register-applications.md).

1. Selecione **Condições** e, em seguida, selecione entre as seguintes condições. Por exemplo, selecione **o risco de inscrição** e os níveis de risco **elevados,** **médios** e **baixos.**
    
    |Condição  |Licença  |Notas  |
    |---------|---------|---------|
    |**Risco de utilizador**|P2|O risco do utilizador representa a probabilidade de uma determinada identidade ou conta estar comprometida.|
    |**Risco de início de sessão**|P2|O risco de entrada representa a probabilidade de um dado pedido de autenticação não ser autorizado pelo proprietário da identidade.|
    |**Plataformas de dispositivos**|Não suportado| Caracterizado pelo sistema operativo que funciona num dispositivo. Para obter mais informações, consulte [as plataformas do Dispositivo.](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms)|
    |**Localizações**|P1, P2|Os locais nomeados podem incluir informações públicas da rede IPv4, país ou região, ou áreas desconhecidas que não mapeiam para países ou regiões específicas. Para mais informações, consulte [as localizações.](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations) |
 
1. Em **Controlos de acesso**, selecione **Concessão**. Em seguida, selecione se bloqueia ou concede acesso:
    
    |Opção  |Licença |Nota  |
    |---------|---------|---------|
    |**Bloquear acesso**|P1, P2| Impede o acesso com base nas condições especificadas nesta política de acesso condicional.|
    |**Conceder acesso** com **requere a autenticação de vários fatores**|P1, P2|Com base nas condições especificadas nesta política de acesso condicional, o utilizador é obrigado a passar pela autenticação multi-factor Azure AD B2C.|

1. No âmbito **da política Enable**, selecione um dos seguintes:
    
    |Opção  |Licença |Nota  |
    |---------|---------|---------|
    |**Apenas relatório**|P1, P2| Apenas o relatório permite que os administradores avaliem o impacto das políticas de Acesso Condicional antes de as permitirem no seu ambiente. Recomendamos que verifique a política com este estado e determine o impacto para os utilizadores finais sem necessitar de autenticação de vários fatores ou bloquear os utilizadores. Para obter mais informações, consulte [os resultados do Acesso Condicional de Revisão no relatório de auditoria](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Ativado**| P1, P2| A política de acesso é avaliada e não aplicada. |
    | **Desligado** | P1, P2| A política de acesso não é ativada e não tem qualquer efeito sobre os utilizadores. |

1. Ativar a política de acesso condicional de teste selecionando **Criar**.

## <a name="add-conditional-access-to-a-user-flow"></a>Adicionar Acesso Condicional a um fluxo de utilizador

Depois de ter adicionado a política de acesso condicional Azure AD, permita o acesso condicional no fluxo do utilizador ou na política personalizada. Quando ativa o acesso condicional, não precisa de especificar um nome de política.

As políticas de acesso condicional múltiplas podem aplicar-se a um utilizador individual a qualquer momento. Neste caso, a política de controlo de acessos mais rigorosa tem precedência. Por exemplo, se uma política necessitar de autenticação multi-factor (MFA), enquanto a outra bloqueia o acesso, o utilizador ficará bloqueado.

## <a name="enable-multi-factor-authentication-optional"></a>Ativar a autenticação de vários fatores (opcional)

Ao adicionar acesso condicional a um fluxo de utilizador, considere a utilização de **autenticação multi-factor (MFA)**. Os utilizadores podem utilizar um código único via SMS ou voz, ou uma senha única via e-mail para autenticação de vários fatores. As definições de MFA são independentes das definições de Acesso Condicional. Pode definir MFA para **Always On** de modo a que o MFA seja sempre necessário independentemente da sua configuração de Acesso Condicional. Ou, pode definir O MFA como **Condicional** de modo a que o MFA seja necessário apenas quando uma Política de Acesso Condicional ativa o exija.

> [!IMPORTANT]
> Se a sua política de Acesso Condicional conceder acesso ao MFA mas o utilizador não tiver inscrito um número de telefone, o utilizador poderá estar bloqueado.

::: zone pivot="b2c-user-flow"

Para ativar o acesso condicional a um fluxo de utilizador, certifique-se de que a versão suporta o Acesso Condicional. Estas versões de fluxo do utilizador estão rotuladas **Recomendadas.**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.

1. Em **Políticas**, selecione **fluxos de utilizador**. Em seguida, selecione o fluxo do utilizador.

1. Selecione **Propriedades** e certifique-se de que o fluxo do utilizador suporta o Acesso Condicional procurando a definição de **Acesso Condicional** com a etiqueta .
 
   ![Configure MFA e Acesso Condicional em Imóveis](media/conditional-access-user-flow/add-conditional-access.png)

1. Na secção **de autenticação multi-factor,** selecione o **método MFA** pretendido e, em seguida, sob **a aplicação do MFA,** selecione **Conditional (Recomendado)**.
 
1. Na secção **acesso condicional,** selecione a caixa de verificação **de políticas de acesso condicional.**

1. Selecione **Guardar**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Adicionar Acesso Condicional à sua política

1. Obtenha o exemplo de uma política de acesso condicional no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. Em cada ficheiro, substitua a cadeia `yourtenant` pelo nome do seu inquilino Azure AD B2C. Por exemplo, se o nome do seu inquilino B2C for *contosob2c,* todos os casos `yourtenant.onmicrosoft.com` de se tornar `contosob2c.onmicrosoft.com` .
1. Faça o upload dos ficheiros de política.

## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a `B2C_1A_signup_signin_with_ca` ou a política para abrir a sua página `B2C_1A_signup_signin_with_ca_whatif` geral. Em seguida, **selecione Executar o fluxo do utilizador**. Em **Aplicação**, selecione *webapp1*. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Copie o URL no **ponto final do fluxo do utilizador executar**.

1. Para simular um insusitado de risco, abra o [Tor Browser](https://www.torproject.org/download/) e use o URL que copiou no passo anterior para iniciar seduca na aplicação registada.

1. Insira as informações solicitadas na página de inscrição e, em seguida, tente entrar. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si. No jwt.ms ficha descodificada, devias ver que a inscrição estava bloqueada.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Teste o fluxo do seu utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**. Em **Aplicação**, selecione *webapp1*. A **URL de resposta** deve mostrar `https://jwt.ms` .

1. Copie o URL no **ponto final do fluxo do utilizador executar**.

1. Para simular um insusitado de risco, abra o [Tor Browser](https://www.torproject.org/download/) e use o URL que copiou no passo anterior para iniciar seduca na aplicação registada.

1. Insira as informações solicitadas na página de inscrição e, em seguida, tente entrar. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si. No jwt.ms ficha descodificada, devias ver que a inscrição estava bloqueada.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Revisão dos resultados do acesso condicional no relatório de auditoria

Para rever o resultado de um evento de Acesso Condicional:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

3. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.

4. No âmbito **de Atividades**, selecione **registos de auditoria.**

5. Filtrar o registo de auditoria definindo **a categoria** para **B2C** e definindo o **Tipo de Recurso de Atividade** para a **Proteção de Identidade**. Em seguida, selecione **Aplicar**.

6. Reveja a atividade de auditoria até aos últimos sete dias. Os seguintes tipos de atividade estão incluídos:

   - **Avaliar políticas de acesso condicional**: Esta entrada de registo de auditoria indica que foi realizada uma avaliação de Acesso Condicional durante uma autenticação.
   - **Utilizador remediado**: Esta entrada indica que a concessão ou requisitos de uma política de acesso condicional foram cumpridos pelo utilizador final, tendo esta atividade sido comunicada ao motor de risco para mitigar (reduzir o risco de) o utilizador.

7. Selecione uma entrada **de registo de política de acesso condicional** na lista para abrir a página De **Dados de Atividade: Audit log,** que mostra os identificadores de registo de auditoria, juntamente com esta informação na secção **Detalhes Adicionais:**

   - **CondicionalAccessResult**: A subvenção exigida pela avaliação da política condicional.
   - **Políticas Aplicadas**: Uma lista de todas as políticas de Acesso Condicional onde as condições foram satisfeitas e as políticas estão ON.
   - **ReportingPolicies**: Uma lista das políticas de acesso condicional que foram definidas para o modo apenas de reporte e onde as condições foram satisfeitas.

## <a name="next-steps"></a>Passos seguintes

[Personalize a interface do utilizador num fluxo de utilizador Azure AD B2C](customize-ui-with-html.md)
