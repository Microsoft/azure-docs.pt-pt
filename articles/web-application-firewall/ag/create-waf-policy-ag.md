---
title: Criar políticas de Firewall de Aplicação Web (WAF) para Gateway de Aplicações
description: Saiba como criar políticas de Firewall de Aplicação Web para Gateway de aplicações.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 26078c3757e42c3e290a5f4122461b287582fb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96518826"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Criar políticas de Firewall de Aplicação Web para Gateway de Aplicações

Associar uma política da WAF com os ouvintes permite que vários sites por trás de um único WAF sejam protegidos por diferentes políticas. Por exemplo, se houver cinco sites por trás do seu WAF, pode ter cinco políticas WAF separadas (uma para cada ouvinte) para personalizar as exclusões, regras personalizadas e regras geridas para um site sem efetuar os outros quatro. Se quiser que uma única política se aplique a todos os sites, pode apenas associar a política com o Gateway de Aplicação, em vez dos ouvintes individuais, para que seja aplicada globalmente. As políticas também podem ser aplicadas a uma regra de encaminhamento baseada em caminhos. 

Pode fazer quantas políticas quiser. Uma vez que crie uma política, deve ser associada a um Gateway de aplicação para entrar em vigor, mas pode ser associada a qualquer combinação de Gateways de Aplicação e ouvintes. 

Se o seu Application Gateway tiver uma política aplicada, e depois aplicar uma política diferente a um ouvinte no Gateway de Aplicação, a política do ouvinte entrará em vigor, mas apenas para os ouvintes a que estão designados. A política de Gateway de Aplicação ainda se aplica a todos os outros ouvintes que não têm uma política específica que lhes seja atribuída. 

   > [!NOTE]
   > Uma vez que uma Política de Firewall está associada a um WAF, deve haver sempre uma política associada a essa WAF. Podes sobrepor essa política, mas desassociar uma política da WAF não é inteiramente apoiado. 

Todas as novas definições de WAF do Web Application Firewall (regras personalizadas, configurações de rulset geridas, exclusões, etc.) vivem dentro de uma Política WAF. Se tiver um WAF existente, estas definições ainda podem existir no seu config WAF. Para obter medidas sobre como passar para a nova Política da WAF, consulte [Migrar o seu WAF Config para uma Política WAF](#migrate) mais tarde neste artigo. 

## <a name="create-a-policy"></a>Criar uma política

Em primeiro lugar, crie uma política de WAF básica com um Conjunto de Regras Padrão (DRS) gerido utilizando o portal Azure.

1. No lado superior esquerdo do portal, selecione **Criar um recurso**. Procure por **WAF**, selecione **Web Application Firewall** e, em seguida, selecione **Criar**.
2. Na Configuração de uma página **de política da WAF,** **separador Basics,** insira ou selecione as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **Rever + criar**:

   |Definição  |Valor  |
   |---------|---------|
   |Política para     |WAF Regional (Gateway de Aplicação)|
   |Subscrição     |Selecione o nome da sua subscrição|
   |Grupo de recursos     |Selecione o seu grupo de recursos|
   |Nome da política     |Digite um nome único para a sua política WAF.|
3. No separador **Associação,** introduza uma das seguintes definições e, em seguida, **selecione Adicionar**:

   |Definição  |Valor  |
   |---------|---------|
   |Gateway de aplicação associada     |Selecione o nome do perfil do Gateway de Aplicação.|
   |Ouvintes Associados     |Selecione o nome do seu Ouvinte de Gateway de Aplicação e, em seguida, **selecione Adicionar**.|

   > [!NOTE]
   > Se atribuir uma política ao seu Application Gateway (ou ouvinte) que já tenha uma política em vigor, a política original é substituída e substituída pela nova política.
4. Selecione **Rever + criar** e, em seguida, selecione **Criar**.

   ![Fundamentos básicos da política da WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Configure as regras da WAF (opcional)

Quando cria uma política WAF, por padrão está no modo *deteção.* No modo deteção, a WAF não bloqueia quaisquer pedidos. Em vez disso, as regras de WAF correspondentes são registadas nos registos waf. Para ver o WAF em ação, pode alterar as definições de modo para *Prevenção*. No modo Prevenção, as regras de correspondência definidas no Regulamento DE SIR selecionado estão bloqueadas e/ou registadas nos registos WAF.

## <a name="managed-rules"></a>Regras geridas

As regras OWASP geridas pelo Azure são ativadas por padrão. Para desativar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, selecione a caixa de verificação à frente do número de regras e selecione **Desativar** o separador acima.

[![Regras geridas ](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Regras personalizadas

Para criar uma regra personalizada, **selecione Adicionar regra personalizada** no separador **regras personalizadas.** Isto abre a página de configuração de regras personalizadas. A imagem que se segue mostra uma regra personalizada de exemplo configurada para bloquear um pedido se a cadeia de consulta contiver o *bloqueio de* texto .

[![Editar regra ](../media/create-waf-policy-ag/edit-custom-rule.png) personalizada](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>Migrar o seu WAF Config para uma Política da WAF

Se tiver um WAF existente, pode ter notado algumas alterações no portal. Primeiro, tens de identificar o tipo de política que ativaste na tua WAF. Existem três estados potenciais:

- Sem política da WAF
- Regras personalizadas apenas Política
- Política da WAF

Pode dizer em que estado está o seu WAF olhando para ele no portal. Se as definições de WAF forem visíveis e puderem ser alteradas a partir da vista 'Gateway de aplicação', o seu WAF encontra-se no estado 1.

[![Configuração ](../media/create-waf-policy-ag/waf-configure.png) WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Se selecionar **firewall de aplicação web** e mostrar uma política associada, o WAF está no estado 2 ou estado 3. Depois de navegar para a apólice, se mostrar **apenas** regras personalizadas, e Gateways de aplicações associados, então é uma Política de Regras Personalizadas.

![Regras personalizadas de WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Se também mostrar Definições de Política e Regras Geridas, então é uma política completa de Firewall de aplicações web. 

![Definições de política da WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrar para a Política da WAF

Se tiver uma Política waf apenas de regras personalizadas, então é melhor passar para a nova Política da WAF. Para a frente, a política de firewall apoiará as definições de política da WAF, regras geridas, exclusões e grupos de regras desativados. Essencialmente, todas as configurações WAF que foram previamente feitas dentro do Gateway de aplicação são agora feitas através da Política WAF. 

As edições à regra personalizada apenas a política WAF são desativadas. Para editar quaisquer definições de WAF, tais como desativar regras, adicionar exclusões, etc. você tem que migrar para um novo recurso de política de firewall de alto nível.

Para tal, crie uma *Política de Firewall de Aplicação Web* e associe-a ao seu(s) Gateway(s) de aplicação e ao(s) ouvinte(s) de eleição. Esta nova Política deve ser exatamente a mesma que a atual config da WAF, o que significa que todas as regras personalizadas, exclusão, regras desativadas, etc. devem ser copiadas para a nova Política que está a criar. Uma vez que tenha uma Política associada ao seu Gateway de Aplicação, pode continuar a errar nas suas regras e configurações WAF. Também pode fazê-lo com a Azure PowerShell. Para obter mais informações, consulte [uma política da WAF com um Gateway de aplicação existente.](associate-waf-policy-existing-gateway.md)

Opcionalmente, você pode usar um script de migração para migrar para uma política waf. Para obter mais informações, consulte [as políticas de Firewall de Aplicação Web Migrar utilizando a Azure PowerShell](migrate-policy.md).

## <a name="force-mode"></a>Modo de força

Se não quiser copiar tudo numa política exatamente igual à sua config atual, pode colocar o WAF no modo "force". Executar o seguinte código Azure PowerShell e o seu WAF estará em modo de força. Em seguida, pode associar qualquer Política WAF à sua WAF, mesmo que não tenha exatamente as mesmas definições que o seu config. 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

Em seguida, procees com os passos para associar uma Política WAF ao seu gateway de aplicação. Para obter mais informações, consulte [uma Política WAF com um Gateway de aplicação existente.](associate-waf-policy-existing-gateway.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [grupos e regras de regras do CRS do Firewall de Aplicação Web.](application-gateway-crs-rulegroups-rules.md)
