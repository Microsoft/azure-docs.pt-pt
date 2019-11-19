---
title: Criar políticas de WAF (firewall do aplicativo Web) para o gateway de aplicativo
description: Saiba como criar políticas de firewall do aplicativo Web para o gateway de aplicativo.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 3f7d213aed82d1cb94bb96b9e212d3b255851afd
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171227"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Criar políticas de firewall do aplicativo Web para o gateway de aplicativo

A associação de uma política WAF com ouvintes permite que vários sites por trás de um único WAF sejam protegidos por políticas diferentes. Por exemplo, se houver cinco sites por trás de seu WAF, você poderá ter cinco políticas de WAF separadas (uma para cada ouvinte) para personalizar as exclusões, regras personalizadas e conjuntos de regras gerenciados para um site sem afetar os outros quatro. Se você quiser que uma única política se aplique a todos os sites, basta associar a política ao gateway de aplicativo, em vez de aos ouvintes individuais, para que ele se aplique globalmente. As políticas também podem ser aplicadas a uma regra de roteamento com base em caminho. 

   > [!NOTE]
   > A política de WAF por site está disponível no EUA Central do Sul e Europa Setentrional. Para acessá-los no portal, use [este link](https://aka.ms/AppgwwafWithAllFeatureFlags) até que ele fique ativo para todos.  

Você pode fazer quantas políticas desejar. Depois de criar uma política, ela deve estar associada a um gateway de aplicativo para entrar em vigor, mas pode ser associada a qualquer combinação de gateways de aplicativo e ouvintes. 

Se o gateway de aplicativo tiver uma política aplicada e você aplicar uma política diferente a um ouvinte nesse gateway de aplicativo, a política do ouvinte entrará em vigor, mas apenas para os ouvintes aos quais eles estão atribuídos. A política de gateway de aplicativo ainda se aplica a todos os outros ouvintes que não têm uma política específica atribuída a eles. 

   > [!NOTE]
   > As políticas de WAF por site e por URI estão em visualização pública. Isso significa que esse recurso está sujeito aos termos de uso suplementares da Microsoft. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Todas as novas configurações de WAF do firewall do aplicativo Web (regras personalizadas, configurações de rulset gerenciadas, exclusões, etc.) residem dentro de uma política de WAF. Se você tiver um WAF existente, essas configurações ainda poderão existir em sua configuração do WAF. Para obter as etapas sobre como mover para a nova política WAF, consulte [migrar sua configuração do WAF para uma política do WAF](#migrate) mais adiante neste artigo. 

## <a name="create-a-policy"></a>Criar uma política

Primeiro, crie uma política de WAF básica com um conjunto de regras padrão gerenciado (DRS) usando o portal do Azure.

1. No lado superior esquerdo do portal, selecione **criar um recurso**. Procure **WAF**, selecione **Firewall do aplicativo Web**e, em seguida, selecione **criar**.
2. Na página **criar uma política de WAF** , na guia **noções básicas** , insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e, em seguida, selecione **revisar + criar**:

   |Definição  |Valor  |
   |---------|---------|
   |Política para     |WAF regionais (gateway de aplicativo)|
   |Subscrição     |Selecione o nome da sua assinatura|
   |Grupo de recursos     |Selecione seu grupo de recursos|
   |Nome da política     |Digite um nome exclusivo para sua política de WAF.|
3. Na guia **Associação** , insira uma das seguintes configurações e, em seguida, selecione **Adicionar**:

   |Definição  |Valor  |
   |---------|---------|
   |Associar o gateway de aplicativo     |Selecione o nome do perfil do gateway de aplicativo.|
   |Associar ouvintes     |Selecione o nome do ouvinte do gateway de aplicativo e, em seguida, selecione **Adicionar**.|

   > [!NOTE]
   > Se você atribuir uma política ao seu gateway de aplicativo (ou ouvinte) que já tenha uma política em vigor, a política original será substituída e substituído pela nova política.
4. Selecione **revisar + criar**e, em seguida, selecionar **criar**.

   ![Noções básicas da política de WAF](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>Configurar regras do WAF (opcional)

Quando você cria uma política de WAF, por padrão ele está no modo de *detecção* . No modo de detecção, WAF não bloqueia nenhuma solicitação. Em vez disso, as regras de WAF correspondentes são registradas nos logs do WAF. Para ver WAF em ação, você pode alterar as configurações de modo para *prevenção*. No modo de prevenção, as regras de correspondência definidas no conjunto de regras do CRS que você selecionou são bloqueadas e/ou registradas nos logs do WAF.

## <a name="managed-rules"></a>Regras gerenciadas

As regras de OWASP gerenciadas pelo Azure são habilitadas por padrão. Para desabilitar uma regra individual dentro de um grupo de regras, expanda as regras dentro desse grupo de regras, marque a caixa de seleção na frente do número da regra e selecione **desabilitar** na guia acima.

[![regras gerenciadas](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Regras personalizadas

Para criar uma regra personalizada, selecione **Adicionar regra personalizada** na guia **regras personalizadas** . Isso abre a página configuração de regra personalizada. A captura de tela a seguir mostra um exemplo de regra personalizada configurada para bloquear uma solicitação se a cadeia de caracteres de consulta contiver o texto *blockme*.

[![editar regra personalizada](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate"></a>Migrar sua configuração do WAF para uma política do WAF

Se você tiver um WAF existente, talvez tenha notado algumas alterações no Portal. Primeiro, você precisa identificar o tipo de política que você habilitou em seu WAF. Há três Estados potenciais:

- Nenhuma política de WAF
- Política somente regras personalizadas
- Política de WAF

Você pode saber em qual estado seu WAF está examinando-o no Portal. Se as configurações de WAF estiverem visíveis e puderem ser alteradas de dentro da exibição do gateway de aplicativo, seu WAF estará no estado 1.

[![](../media/create-waf-policy-ag/waf-configure.png) de configuração do WAF](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Se você selecionar **Firewall do aplicativo Web** e ele mostrar uma política associada, o WAF estará no estado 2 ou no estado 3. Depois de navegar até a política, se ela mostrar **apenas** regras personalizadas e gateways de aplicativo associados, será uma política personalizada apenas para regras.

![Regras personalizadas de WAF](../media/create-waf-policy-ag/waf-custom-rules.png)

Se ele também mostra configurações de política e regras gerenciadas, é uma política de firewall de aplicativo Web completa. 

![Configurações de política de WAF](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrar para a política WAF

Se você tiver apenas uma política personalizada WAF, convém mover para a nova política WAF. No futuro, a política de firewall dará suporte a configurações de política de WAF, conjuntos de regras gerenciados, exclusões e grupos de regras desabilitados. Essencialmente, todas as configurações de WAF que foram feitas anteriormente dentro do gateway de aplicativo agora são feitas por meio da política de WAF. 

As edições para a regra personalizada somente WAF política estão desabilitadas. Para editar as configurações de WAF, como desabilitar regras, adicionar exclusões, etc., você precisa migrar para um novo recurso de política de firewall de nível superior.

Para fazer isso, crie uma *política de firewall do aplicativo Web* e associe-a aos gateways de aplicativo e aos ouvintes de sua escolha. Essa nova política **deve** ser exatamente a mesma que a configuração atual do WAF, o que significa que cada regra personalizada, exclusão, regra desabilitada etc. deve ser copiada para a nova política que você está criando. Depois de ter uma política associada ao seu gateway de aplicativo, você poderá continuar a fazer alterações nas regras e configurações do WAF. Você também pode fazer isso com Azure PowerShell. Para obter mais informações, consulte [associar uma política de WAF a um gateway de aplicativo existente](associate-waf-policy-existing-gateway.md).

Opcionalmente, você pode usar um script de migração para migrar para uma política do WAF. Para obter mais informações, consulte [migrar políticas de firewall do aplicativo Web usando Azure PowerShell](migrate-policy.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [regras e grupos de regras CRS do firewall do aplicativo Web](application-gateway-crs-rulegroups-rules.md).
