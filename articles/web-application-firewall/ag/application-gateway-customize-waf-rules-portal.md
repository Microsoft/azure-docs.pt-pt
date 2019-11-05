---
title: Personalizar regras de firewall do aplicativo Web no gateway Aplicativo Azure-portal do Azure
description: Este artigo fornece informações sobre como personalizar as regras de firewall do aplicativo Web no gateway de aplicativo com o portal do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/24/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: ab2722ed4a4aa4fe95be15cd536bfd5d959f9139
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516932"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalizar regras de firewall do aplicativo Web usando o portal do Azure

O WAF (firewall do aplicativo Web) Aplicativo Azure Gateway fornece proteção para aplicativos Web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (projeto de segurança de aplicativo Web aberto). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o gateway de aplicativo fornece a capacidade de personalizar grupos de regras e regras. Para obter mais informações sobre grupos de regras e regras específicas, consulte [lista de regras e grupos de regras CRS de firewall do aplicativo Web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se o seu gateway de aplicativo não estiver usando a camada WAF, a opção de atualizar o gateway de aplicativo para a camada WAF aparecerá no painel direito. 

![Habilitar WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Exibir regras e grupos de regras

**Para exibir regras e grupos de regras**
1. Navegue até o gateway de aplicativo e selecione **Firewall do aplicativo Web**.  
2. Selecione sua **política do WAF**.
2. Selecione **regras gerenciadas**.

   Esta exibição mostra uma tabela na página de todos os grupos de regras fornecidos com o conjunto de regras escolhido. Todas as caixas de seleção da regra estão marcadas.

## <a name="disable-rule-groups-and-rules"></a>Desabilitar grupos de regras e regras

> [!IMPORTANT]
> Tome cuidado ao desabilitar quaisquer regras ou grupos de regras. Isso pode expô-lo a maiores riscos de segurança.

Quando estiver desabilitando regras, você poderá desabilitar um grupo de regras inteiro ou regras específicas em um ou mais grupos de regras. 

**Para desabilitar grupos de regras ou regras específicas**

   1. Pesquise as regras ou os grupos de regras que você deseja desabilitar.
   2. Marque as caixas de seleção das regras que você deseja desabilitar. 
   3. Selecione a ação na parte superior da página (habilitar/desabilitar) para as regras selecionadas.
   2. Selecione **Guardar**. 

![Salvar alterações][3]

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que o WAF bloqueie a solicitação no modo de prevenção. No modo de detecção, eles são registrados como exceções.

Eles não podem ser configurados ou desabilitados:

* Falha ao analisar os resultados do corpo da solicitação na solicitação sendo bloqueada, a menos que a inspeção do corpo seja desativada (XML, JSON, dados de formulário)
* O comprimento de dados do corpo da solicitação (sem arquivos) é maior que o limite configurado
* O corpo da solicitação (incluindo arquivos) é maior que o limite
* Ocorreu um erro interno no mecanismo de WAF

Específico do CRS 3. x:

* Limite de Pontuação de anomalias de entrada excedido

## <a name="next-steps"></a>Passos seguintes

Depois de configurar suas regras desabilitadas, você pode aprender a exibir os logs do WAF. Para obter mais informações, consulte [diagnóstico do gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
