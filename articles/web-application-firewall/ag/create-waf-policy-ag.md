---
title: Criar políticas de firewall de aplicação web (WAF) para gateway de aplicação
description: Saiba como criar políticas de firewall de aplicação web para gateway de aplicação.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 7ab4b60747509dfe56ec2e89b38986de747dab69
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014541"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Criar políticas de firewall de aplicação web para gateway de aplicação

Associar uma política de WAF aos ouvintes permite que vários sites por trás de uma única WAF sejam protegidos por diferentes políticas. Por exemplo, se houver cinco sites por trás do seu WAF, pode ter cinco políticas waf separadas (uma para cada ouvinte) para personalizar as exclusões, regras personalizadas e conjuntos de regras geridos para um site sem efetuar os outros quatro. Se quiser que uma única política se aplique a todos os sites, pode associar a política ao Gateway de Aplicação, em vez dos ouvintes individuais, para que se aplique globalmente. As políticas também podem ser aplicadas a uma regra de encaminhamento baseada em caminhos. 

Pode fazer as políticas que quiser. Uma vez que você cria uma política, deve ser associado a um Gateway de aplicação para entrar em vigor, mas pode ser associado a qualquer combinação de Gateways de Aplicação e ouvintes. 

Se o seu Gateway de Aplicação tiver uma política aplicada, e depois aplicar uma política diferente a um ouvinte nesse Gateway de Aplicação, a política do ouvinte entrará em vigor, mas apenas para o ou ouvinte a que estão atribuídos. A política de Gateway de Aplicação ainda se aplica a todos os outros ouvintes que não têm uma política específica atribuída a eles. 

   > [!NOTE]
   > Por site e por URI AS políticas waf estão em pré-visualização pública. Isto significa que esta funcionalidade está sujeita aos Termos de Utilização Suplementares da Microsoft. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
   > [!NOTE]
   > Uma vez que uma Política de Firewall esteja associada a um WAF, deve haver sempre uma política associada a essa WAF. Podes substituir essa política, mas desassociar uma política da WAF não é totalmente apoiada. 

Todas as novas definições waf da Firewall de aplicação web (regras personalizadas, configurações de rulset geridas, exclusões, etc.) vivem dentro de uma Política WAF. Se tiver um WAF existente, estas configurações podem ainda existir no seu config WAF. Para obter passos sobre como avançar para a nova Política waf, consulte [Migrate o seu WAF Config para uma Política WAF](#migrate) mais tarde neste artigo. 

## <a name="create-a-policy"></a>Criar uma política

Em primeiro lugar, crie uma política básica de WAF com um conjunto de regras de padrão gerido (DRS) utilizando o portal Azure.

1. No lado superior esquerdo do portal, selecione **Criar um recurso**. Procure **WAF,** selecione Firewall de **aplicação web**e, em seguida, selecione **Criar**.
2. Em Criar uma página de **política WAF,** separador **Basics,** introduzir ou selecionar as seguintes informações, aceitar as predefinições para as definições restantes e, em seguida, selecionar **Rever + criar**:

   |Definição  |Valor  |
   |---------|---------|
   |Política para     |WAF Regional (Gateway de Aplicação)|
   |Subscrição     |Selecione o seu nome de subscrição|
   |Grupo de recursos     |Selecione o seu grupo de recursos|
   |Nome da política     |Digite um nome único para a sua política waf.|
3. No separador **Associação,** introduza uma das seguintes definições e, em seguida, **selecione Adicionar:**

   |Definição  |Valor  |
   |---------|---------|
   |Gateway de aplicação associada     |Selecione o nome do perfil do Gateway da aplicação.|
   |Ouvintes Associados     |Selecione o nome do seu Ouvinte gateway de aplicação e, em seguida, selecione **Adicionar**.|

   > [!NOTE]
   > Se atribuir uma apólice ao seu Gateway de Aplicação (ou ouvinte) que já tem uma política em vigor, a política original é substituída e substituída pela nova política.
4. Selecione **Rever + criar**e, em seguida, selecione **Criar**.

   ![Fundamentos da política da WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Configure as regras waf (opcional)

Quando cria uma política WAF, por defeito está no modo *deteção.* No modo deteção, a WAF não bloqueia quaisquer pedidos. Em vez disso, as regras waf correspondentes são registadas nos registos waf. Para ver o WAF em ação, pode alterar as definições de modo para *Prevenção*. No modo de Prevenção, as regras de correspondência definidas no conjunto de regras de CRS selecionada seleção estão bloqueadas e/ou registadas nos registos WAF.

## <a name="managed-rules"></a>Regras geridas

As regras OWASP geridas pelo Azure são ativadas por defeito. Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de verificação em frente ao número da regra e selecione **Desativar** no separador acima.

[![Regras geridas ](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Regras personalizadas

Para criar uma regra personalizada, selecione **Adicionar regra personalizada** sob o separador de regras **Personalizadas.** Isto abre a página de configuração de regras personalizadas. A imagem seguinte mostra uma regra personalizada de exemplo configurada para bloquear um pedido se a cadeia de consulta contiver o *bloqueio*de texto .

[![Editar regra ](../media/create-waf-policy-ag/edit-custom-rule.png) personalizada](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrar o seu WAF Config para uma política waf

Se tiver um WAF existente, pode ter notado algumas alterações no portal. Primeiro, tens de identificar que tipo de Política permitiste na tua WAF. Há três estados potenciais:

- Sem política waf
- Regras personalizadas apenas Política
- Política waf

Pode dizer em que estado está o seu WAF olhando para ele no portal. Se as definições waf estiverem visíveis e puderem ser alteradas a partir da vista Gateway da aplicação, o seu WAF encontra-se no estado 1.

[![Configuração ](../media/create-waf-policy-ag/waf-configure.png) WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Se selecionar firewall de **aplicação web** e mostrar-lhe uma política associada, o WAF está no estado 2 ou no estado 3. Depois de navegar para a apólice, se mostrar **apenas** regras personalizadas, e Gateways de aplicação associados, então é uma política apenas de Regras Personalizadas.

![Regras personalizadas de WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Se também mostrar Definições de Política e Regras Geridas, então é uma política completa de Firewall de aplicações web. 

![Definições de política waf](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrar para a política da WAF

Se tiver uma política de regras personalizadas apenas WAF, então talvez queira mudar-se para a nova Política WAF. Em frente, a política de firewall apoiará as definições de política do WAF, conjuntos de regras geridos, exclusões e grupos de regras desativados. Essencialmente, todas as configurações waf que foram feitas anteriormente dentro do Gateway de Aplicação são agora feitas através da Política WAF. 

Edita a regra personalizada, apenas a política waf é desativada. Para editar quaisquer definições de WAF, tais como regras incapacitantes, adicionando exclusões, etc. você tem que migrar para um novo recurso de política de firewall de alto nível.

Para tal, crie uma Política de Firewall de *Aplicação Web* e associe-a ao seu Gateway de aplicação e ao(s) ouvinte(s) de eleição. Esta nova Política deve ser exatamente a mesma que a atual config waf, o que significa que todas as regras personalizadas, exclusão, regra dos deficientes, etc. devem ser copiadas para a nova Política que está a criar. Uma vez que tenha uma Política associada ao seu Gateway de Aplicação, então pode continuar a fazer alterações às suas regras e definições waf. Também pode fazê-lo com a Azure PowerShell. Para mais informações, consulte [Associate uma política waf com um Gateway](associate-waf-policy-existing-gateway.md)de Aplicação existente .

Opcionalmente, você pode usar um roteiro de migração para migrar para uma política waf. Para mais informações, consulte as políticas de firewall de [aplicação web migrate utilizando o Azure PowerShell](migrate-policy.md).

## <a name="force-mode"></a>Modo de força

Se não quiser copiar tudo numa política exatamente igual à sua config atual, pode colocar o WAF no modo de "força". Execute o seguinte código Azure PowerShell e o seu WAF estará em modo de vigor. Em seguida, pode associar qualquer Política WAF ao seu WAF, mesmo que não tenha as mesmas definições que o seu config. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

Em seguida, procees com os passos para associar uma Política WAF ao seu gateway de aplicação. Para mais informações, consulte Associate a WAF Policy com um Gateway de [Aplicação existente.](associate-waf-policy-existing-gateway.md)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre grupos e regras de [regras CRS firewall de aplicação web](application-gateway-crs-rulegroups-rules.md).
