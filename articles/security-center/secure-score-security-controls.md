---
title: Pontuação de segurança no Centro de Segurança do Azure
description: Descrição da pontuação segura do Azure Security Center e dos seus controlos de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: memildin
ms.openlocfilehash: e881c61d16cd167ace78abb96dca7e714436cfc0
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752213"
---
# <a name="secure-score-in-azure-security-center"></a>Pontuação de segurança no Centro de Segurança do Azure

## <a name="introduction-to-secure-score"></a>Introdução para garantir pontuação

O Centro de Segurança Azure tem dois objetivos principais: 

- para ajudá-lo a entender a sua situação atual de segurança
- para ajudá-lo de forma eficiente e eficaz a melhorar a sua segurança

A característica central no Centro de Segurança que lhe permite atingir esses objetivos é **pontuação segura.**

O Security Center avalia continuamente os seus recursos, subscrições e organização para questões de segurança. Em seguida, agrega todas as conclusões numa única pontuação para que possa dizer, num ápice, a sua situação de segurança atual: quanto maior for a pontuação, menor o nível de risco identificado.

A pontuação segura é mostrada nas páginas do portal Azure como um valor percentual, mas os valores subjacentes também são claramente apresentados:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Pontuação máxima segura, como mostrado no portal":::

Para aumentar a sua segurança, reveja a página de recomendações do Security Center para as ações pendentes necessárias para aumentar a sua pontuação. Cada recomendação inclui instruções para ajudá-lo a remediar a questão específica.

As recomendações são agrupadas nos **controlos de segurança.** Cada controlo é um grupo lógico de recomendações de segurança relacionadas, e reflete as suas superfícies de ataque vulneráveis. A sua pontuação só melhora quando remedia *todas as* recomendações para um único recurso dentro de um controlo. Para ver se a sua organização está a proteger cada superfície de ataque individual, reveja as pontuações para cada controlo de segurança.

Para obter mais informações, consulte [como a sua pontuação segura é calculada](secure-score-security-controls.md#how-your-secure-score-is-calculated) abaixo. 


## <a name="access-your-secure-score"></a>Aceda à sua pontuação segura

Pode encontrar a sua pontuação máxima, bem como a sua pontuação por subscrição, através do portal Azure ou programaticamente como descrito nas seguintes secções:

- [Obtenha a sua pontuação segura a partir do portal](#get-your-secure-score-from-the-portal)
- [Obtenha a sua pontuação segura na API REST](#get-your-secure-score-from-the-rest-api)
- [Obtenha a sua pontuação segura a partir do Azure Resource Graph (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Obtenha a sua pontuação segura a partir do portal

O Centro de Segurança exibe a sua pontuação de forma proeminente no portal: é o primeiro azulejo principal da página geral do Centro de Segurança. Selecionando este azulejo, leva-o à página de pontuação segura dedicada, onde verá a pontuação desabatada por subscrição. Selecione uma única subscrição para ver a lista detalhada de recomendações prioritárias e o impacto potencial que a sua reparação terá na pontuação da subscrição. 

Para recapitular, a sua pontuação segura é mostrada nos seguintes locais nas páginas do portal do Security Center.

- Num azulejo da **visão geral** do Centro de Segurança (painel principal):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A pontuação segura no painel do Centro de Segurança":::

- Na página de **pontuação Secure** dedicada pode ver a pontuação segura para a sua subscrição e os seus grupos de gestão:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="A pontuação segura para subscrições na página de pontuação segura do Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A pontuação segura para grupos de gestão na página de pontuação segura do Security Center":::

    > [!NOTE]
    > Quaisquer grupos de gestão para os quais não tenha permissões suficientes, mostrarão a sua pontuação como "Restrito". 

- No topo da página de **Recomendações:**

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A pontuação segura na página de recomendações do Centro de Segurança":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Obtenha a sua pontuação segura na API REST

Pode aceder à sua pontuação através da pontuação segura API. Os métodos API fornecem a flexibilidade para consultar os dados e construir o seu próprio mecanismo de reporte das suas pontuações seguras ao longo do tempo. Por exemplo, pode utilizar a [API de Pontuações Seguras](/rest/api/securitycenter/securescores) para obter a pontuação de uma subscrição específica. Além disso, pode utilizar a [API de Controlos de Pontuação Segura](/rest/api/securitycenter/securescorecontrols) para listar os controlos de segurança e a pontuação atual das suas subscrições.

![Recuperação de uma única pontuação segura através da API](media/secure-score-security-controls/single-secure-score-via-api.png)

Por exemplo, ferramentas construídas em cima da pontuação segura API, consulte [a área de pontuação segura da nossa comunidade GitHub.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Obtenha a sua pontuação segura a partir do Azure Resource Graph (ARG)

O Azure Resource Graph fornece acesso instantâneo à informação de recursos em todos os seus ambientes em nuvem com capacidades robustas de filtragem, agrupamento e triagem. É uma forma rápida e eficiente de consultar informações através de subscrições Azure programáticas ou dentro do portal Azure. [Saiba mais sobre o Azure Resource Graph](../governance/resource-graph/index.yml).

Para aceder à pontuação segura para várias subscrições com ARG:

1. A partir do portal Azure, abra **o Azure Resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Página de recomendação do Azure Resource Graph Explorer**" :::

1. Insira a sua consulta Kusto (utilizando os exemplos abaixo para obter orientação).

    - Esta consulta devolve o ID de subscrição, a pontuação atual em pontos e em percentagem, e a pontuação máxima para a subscrição. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Esta consulta devolve o estado de todos os controlos de segurança. Para cada controlo, obterá o número de recursos insalubres, a pontuação atual e a pontuação máxima. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecione **Consulta de execução**.




## <a name="tracking-your-secure-score-over-time"></a>Rastreando a sua pontuação segura ao longo do tempo

Se for um utilizador de Power BI com uma conta Pro, pode utilizar o painel **DeSema** para rastrear a sua pontuação segura ao longo do tempo e investigar quaisquer alterações.

> [!TIP]
> Você pode encontrar este dashboard, bem como outras ferramentas para trabalhar programaticamente com pontuação segura, na área dedicada da comunidade do Centro de Segurança Azure no GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

O painel contém os seguintes dois relatórios para ajudá-lo a analisar o seu estado de segurança:

- **Resumo de Recursos** - fornece dados resumidos sobre a saúde dos seus recursos.
- **Secure Score Summary** - fornece dados resumidos sobre o progresso da sua pontuação. Utilize o gráfico "Obter pontuação segura ao longo do tempo por subscrição" para ver as alterações na pontuação. Se notar uma alteração dramática na sua pontuação, verifique a tabela "alterações detetadas que podem afetar a sua pontuação segura" para eventuais alterações que possam ter causado a alteração. Este quadro apresenta recursos eliminados, recursos recém-utilizados ou recursos que o seu estatuto de segurança mudou para uma das recomendações.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="O painel opcional Secure Score Over Time Power BI para rastrear a sua pontuação segura ao longo do tempo e investigar alterações":::





## <a name="how-your-secure-score-is-calculated"></a>Como a sua pontuação segura é calculada 

A contribuição de cada controlo de segurança para a pontuação máxima segura global é claramente indicada na página de recomendações.

[![A pontuação de segurança reforçada introduz controlos de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controlo de segurança, todos os seus recursos devem cumprir todas as recomendações de segurança dentro do controlo de segurança. Por exemplo, o Security Center tem várias recomendações sobre como proteger as suas portas de gestão. Tens de os remediar a todos para fazer a diferença na tua pontuação segura.

Por exemplo, o controlo de segurança chamado "Aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que pode ver na ponta da ferramenta sobre o valor potencial de aumento do controlo:

[![O controlo de segurança "Aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A pontuação máxima para este controlo, Aplicar atualizações do sistema, é sempre 6. Neste exemplo, existem 50 recursos. Assim, dividimos a pontuação máxima por 50, e o resultado é que cada recurso contribui com 0,12 pontos. 

* **Aumento potencial** (0,12 x 8 recursos não saudáveis = 0,96) - Os restantes pontos disponíveis dentro do controlo. Se remediar todas as recomendações neste controlo, a sua pontuação aumentará 2% (neste caso, 0,96 pontos arredondados até 1 ponto). 
* **Pontuação atual** (0,12 x 42 recursos saudáveis = 5,04) - A pontuação atual para este controlo. Cada controlo contribui para a pontuação total. Neste exemplo, o controlo está a contribuir com 5,04 pontos para o total de segurança atual.
* **Pontuação máxima** - O número máximo de pontos que pode ganhar ao completar todas as recomendações dentro de um controlo. A pontuação máxima para um controlo indica o significado relativo desse controlo. Use os valores de pontuação máxima para triagem dos problemas para trabalhar primeiro. 


### <a name="calculations---understanding-your-score"></a>Cálculos - compreender a sua pontuação

|Metric|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controlo de segurança**|<br>![Equação para calcular a pontuação de um controlo de segurança](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Cada controlo de segurança individual contribui para a Pontuação de Segurança. Cada recurso afetado por uma recomendação dentro do controlo, contribui para a pontuação atual do controlo. A pontuação atual para cada controlo é uma medida do estado dos recursos *sob* controlo.<br>![Dicas de ferramentas que mostram os valores utilizados no cálculo da pontuação atual do controlo de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos saudáveis e insalubres.<br>6 / 78 = 0,0769<br>Multiplicar-se pelo número de recursos saudáveis (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Classificação de segurança**<br>Subscrição individual|<br>![Equação para calcular a pontuação segura de uma subscrição](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Pontuação segura de subscrição única com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, existe uma única subscrição com todos os controlos de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os restantes 32 pontos refletem-se nos números de "Potencial aumento de pontuação" dos controlos de segurança.<br>![Lista de controlos e o aumento potencial da pontuação](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Classificação de segurança**<br>Múltiplas subscrições|<br>![Equação para calcular a pontuação segura para várias subscrições](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Ao calcular a pontuação combinada para várias subscrições, o Security Center inclui um *peso* para cada subscrição. Os pesos relativos para as suas subscrições são determinados pelo Security Center com base em fatores como o número de recursos.<br>A pontuação atual para cada subscrição é calculada da mesma forma que para uma única subscrição, mas então o peso é aplicado como mostrado na equação.<br>Ao visualizar várias subscrições, a pontuação segura avalia todos os recursos dentro de todas as políticas ativadas e agrupam o seu impacto combinado na pontuação máxima de cada controlo de segurança.<br>![Pontuação segura para várias subscrições com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do estado de todos os recursos em todas as subscrições.<br>Também aqui, se for à página de recomendações e somar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Que recomendações estão incluídas nos cálculos de pontuação segura?

Só as recomendações incorporadas têm impacto na pontuação segura.

Recomendações assinaladas como **Preview** não estão incluídas nos cálculos da sua pontuação segura. Devem continuar a ser remediados sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação.

Um exemplo de uma recomendação de pré-visualização:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com a bandeira de pré-visualização":::

## <a name="improve-your-secure-score"></a>Melhorar a sua pontuação segura

Para melhorar a sua pontuação segura, remedia as recomendações de segurança da sua lista de recomendações. Pode remediar manualmente cada recomendação para cada recurso, ou utilizando a **Quick Fix!** opção (quando disponível) aplicar uma remediação para uma recomendação a um grupo de recursos rapidamente. Para obter mais informações, consulte [recomendações corretivas.](security-center-remediate-recommendations.md)

Outra forma de melhorar a sua pontuação e garantir que os seus utilizadores não criam recursos que impactem negativamente a sua pontuação é configurar as opções Depor e Negar nas recomendações relevantes. Saiba mais em [Prevenir configurações erradas com recomendações de Cumprir/Negar](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Controlos de segurança e suas recomendações

A tabela abaixo lista os controlos de segurança no Centro de Segurança Azure. Para cada controlo, pode ver o número máximo de pontos que pode adicionar à sua pontuação segura se remediar *todas as* recomendações listadas no controlo, para *todos os* seus recursos. 

O conjunto de recomendações de segurança fornecidas ao Centro de Segurança é adaptado aos recursos disponíveis no ambiente de cada organização. As recomendações podem ser ainda mais [personalizadas, desativando políticas](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) e [isentando recursos específicos de uma recomendação.](exempt-resource.md) 
 
Recomendamos que todas as organizações revejam cuidadosamente as suas iniciativas atribuídas à Política Azure. 

> [!TIP]
> Para mais detalhes sobre a revisão e edição das suas iniciativas, consulte [Trabalhar com políticas de segurança.](tutorial-security-policy.md) 

Embora a iniciativa de segurança padrão do Security Center se baseie nas melhores práticas e padrões da indústria, existem cenários em que as recomendações incorporadas listadas abaixo podem não se adequar completamente à sua organização. Consequentemente, às vezes será necessário ajustar a iniciativa padrão - sem comprometer a segurança - para garantir que está alinhada com as políticas da sua própria organização. padrões da indústria, padrões regulamentares e referências que é obrigado a cumprir.<br><br>
<div class="foo">

<style type="text/css"> .tg {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;tamanho da fonte:14px; transbordamento:escondido;estofamento:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;tamanho da fonte:18px; peso de fonte:normal;transbordar:escondido;estofamento:10px 5px;word-break:normal;} .tg-cly1{text-align:left;vertical-align:middle} .tg-lboi{border-color:herdamento;text-align:left;vertical-align:middle: </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Controlo de segurança, pontuação e descrição</b><br></th>
    <th class="tg-cly1"><b>Recomendações</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ativar mFA (pontuação máxima 10)</p></strong>Se utilizar apenas uma palavra-passe para autenticar um utilizador, deixa um vetor de ataque aberto. Se a palavra-passe é fraca ou foi exposta em outro lugar, será realmente o utilizador a iniciar sessão com o nome de utilizador e a palavra-passe?<br>Com <a href="https://www.microsoft.com/security/business/identity/mfa">o MFA</a> ativado, as suas contas são mais seguras e os utilizadores ainda podem autenticar para quase todas as aplicações com sso único.s.autônea.</td>
    <td class="tg-lboi"; width=55%>- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição<br>- MFA deve ser ativado contas com permissões de escrita na sua subscrição</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Portas de gestão seguras (pontuação máxima 8)</p></strong>Força bruta ataca portas de gestão de alvos para ter acesso a um VM. Uma vez que as portas nem sempre precisam de estar abertas, uma estratégia de mitigação é reduzir a exposição aos portos utilizando controlos de acesso à rede just-in-time, grupos de segurança de rede e gestão de portas de máquinas virtuais.<br>Uma vez que muitas organizações de TI não bloqueiam a saída de comunicações SSH da sua rede, os atacantes podem criar túneis encriptados que permitem que as portas RDP em sistemas infetados comuniquem de volta ao comando do intruso para controlar os servidores. Os atacantes podem usar o subsistema de Gestão Remota do Windows para se deslocarem lateralmente pelo seu ambiente e utilizarem credenciais roubadas para aceder a outros recursos numa rede.</td>
    <td class="tg-lboi"; width=55%>- As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time<br>- As máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>- As portas de gestão devem ser fechadas nas suas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar atualizações do sistema (pontuação máxima 6)</p></strong>As atualizações do sistema proporcionam às organizações a capacidade de manter a eficiência operacional, reduzir as vulnerabilidades de segurança e proporcionar um ambiente mais estável para os utilizadores finais. Não aplicar atualizações deixa vulnerabilidades não remadas e resulta em ambientes que são suscetíveis a ataques. Estas vulnerabilidades podem ser exploradas e levar à perda de dados, exfiltração de dados, ransomware e abuso de recursos. Para implementar atualizações do sistema, pode utilizar a <a href="/azure/automation/update-management/overview">solução 'Gestão de Atualização' para gerir patches e atualizações</a> para as suas máquinas virtuais. A gestão de atualização é o processo de controlo da implementação e manutenção de lançamentos de software.</td>
    <td class="tg-lboi"; width=55%>- Os problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas<br>- O agente de monitorização deve ser instalado em conjuntos de escala de máquina virtual<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- A versão OS deve ser atualizada para as suas funções de serviço na nuvem<br>- As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas<br>- As atualizações do sistema devem ser instaladas nas suas máquinas<br>- As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema<br>- Os Serviços Kubernetes devem ser atualizados para uma versão não vulnerável de Kubernetes<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Pré-visualização)<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas em Linux (Pré-visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Corrigir vulnerabilidades (pontuação máxima 6)</p></strong>Uma vulnerabilidade é uma fraqueza que um ator ameaça pode aproveitar para comprometer a confidencialidade, disponibilidade ou integridade de um recurso. <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">Gerir vulnerabilidades</a> reduz a exposição organizacional, endurece a área de superfície do ponto final, aumenta a resiliência organizacional e reduz a superfície de ataque dos seus recursos. A Gestão de Ameaças e Vulnerabilidades proporciona visibilidade em software e segurança e fornece recomendações para mitigação.</td>
    <td class="tg-lboi"; width=55%>- A segurança avançada dos dados deve ser ativada na Base de Dados SQL<br>- Vulnerabilidades em imagens do Registo de Contentores de Azure devem ser remediadas<br>- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas<br>- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades<br>- A avaliação da vulnerabilidade deve ser ativada em casos geridos pela SQL<br>- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL<br>- Solução de avaliação de vulnerabilidades deve ser instalada nas suas máquinas virtuais<br>- As imagens do contentor devem ser implantadas apenas a partir de registos fidedignos (pré-visualização)<br>- O addon Azure Policy para Kubernetes deve ser instalado e ativado nos seus clusters (pré-visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ativar a encriptação em repouso (pontuação máxima 4)</p></strong><a href="/azure/security/fundamentals/encryption-atrest">A encriptação em repouso</a> fornece proteção de dados para dados armazenados. Os ataques contra dados em repouso incluem tentativas de acesso físico ao hardware em que os dados são armazenados. Os Azures usam encriptação simétrica para encriptar e desencriptar grandes quantidades de dados em repouso. Uma chave de encriptação simétrica é usada para encriptar dados à medida que são escritos para armazenamento. Esta chave de encriptação também é usada para desencriptar esses dados à medida que são reutilizados para serem usados na memória. As chaves devem ser armazenadas num local seguro com políticas de controlo de acesso e auditoria baseadas em identidade. Um desses locais seguros é o Cofre da Chave Azure. Se um intruso obtiver os dados encriptados, mas não as chaves de encriptação, o intruso não pode aceder aos dados sem quebrar a encriptação.</td>
    <td class="tg-lboi"; width=55%>- A encriptação do disco deve ser aplicada em máquinas virtuais<br>- A encriptação de dados transparente na base de dados SQL deve ser ativada<br>- As variáveis de conta de automação devem ser encriptadas<br>- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign<br>- O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Criptografe os dados em trânsito (pontuação máxima 4)</p></strong>Os dados estão "em trânsito" quando são transmitidos entre componentes, locais ou programas. As organizações que não protegem os dados em trânsito são suscetíveis a ataques man-in-the-middle, escutas e sequestro de sessão. Os protocolos SSL/TLS devem ser utilizados para o intercâmbio de dados e recomenda-se uma VPN. Ao enviar dados encriptados entre uma máquina virtual Azure e uma localização no local, através da internet, pode utilizar um gateway de rede virtual, como <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">o Azure VPN Gateway,</a> para enviar tráfego encriptado.</td>
    <td class="tg-lboi"; width=55%>- A API App só deve estar acessível em HTTPS<br>- A App de função só deve estar acessível através do HTTPS<br>- Apenas devem ser ativadas ligações seguras à sua Cache Redis<br>- A transferência segura para contas de armazenamento deve ser ativada<br>- A Aplicação Web só deve ser acessível em HTTPS<br>- O ponto final privado deve ser ativado para servidores PostgreSQL<br>- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL<br>- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL<br>- TLS deve ser atualizado para a versão mais recente para a sua aplicação API<br>- TLS deve ser atualizado para a versão mais recente para a sua aplicação de função<br>- TLS deve ser atualizado para a versão mais recente para a sua aplicação web<br>- FTPS deve ser necessário na sua App API<br>- FTPS deve ser exigido na sua app de função<br>- FTPS deve ser necessário na sua web App</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Gerir acessos e permissões (pontuação máxima 4)</p></strong>Uma parte central de um programa de segurança é garantir que os seus utilizadores tenham o acesso necessário para fazer o seu trabalho, mas não mais do que isso: o <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">modelo de acesso ao privilégio menos privilegiado.</a><br>Controlar o acesso aos seus recursos criando atribuições de funções com <a href="/azure/role-based-access-control/overview">o controlo de acesso baseado em funções Azure (Azure RBAC)</a>. Uma tarefa de função consiste em três elementos:<br>- <strong>Principal de</strong>segurança : o objeto a que o utilizador está a solicitar acesso<br>- <strong>Definição de função:</strong>as suas permissões<br>- <strong>Âmbito</strong>: o conjunto de recursos a que se aplicam as permissões</td>
    <td class="tg-lboi"; width=55%>- As contas precotados devem ser removidas da sua subscrição (Pré-visualização)<br>- As contas pregridas com permissões do proprietário devem ser removidas da sua subscrição (Pré-visualização)<br>- As contas externas com permissões do proprietário devem ser removidas da sua subscrição (Pré-visualização)<br>- As contas externas com permissões de escrita devem ser removidas da sua subscrição (Pré-visualização)<br>- Deve haver mais de um proprietário atribuído à sua subscrição<br>- O controlo de acesso baseado em funções Azure (Azure RBAC) deve ser utilizado nos Serviços Kubernetes (Pré-visualização)<br>- Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente<br>- Os principais serviços devem ser usados para proteger as suas assinaturas em vez de Certificados de Gestão<br>- As capacidades linux menos privilegiadas devem ser aplicadas para contentores (pré-visualização)<br>- O sistema de ficheiros de raiz imutável (apenas para leitura) deve ser aplicado para os contentores (pré-visualização)<br>- O contentor com escalada de privilégio deve ser evitado (pré-visualização)<br>- Devem ser evitados os recipientes de funcionamento como utilizador de raiz (pré-visualização)<br>- Devem ser evitados os recipientes que partilham espaços sensíveis para o nome do hospedeiro (pré-visualização)<br>- A utilização dos suportes de volume do Pod HostPath deve ser restringida a uma lista conhecida (pré-visualização)<br>- Os recipientes privilegiados devem ser evitados (pré-visualização)<br>- O addon Azure Policy para Kubernetes deve ser instalado e ativado nos seus clusters (pré-visualização)<br>- A identidade gerida deve ser usada na sua App API<br>- A identidade gerida deve ser usada na sua função App<br>- A identidade gerida deve ser usada na sua web App</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Remediar configurações de segurança (pontuação máxima 4)</p></strong>Os ativos de TI mal configurados têm um maior risco de serem atacados. As ações básicas de endurecimento são muitas vezes esquecidas quando os ativos estão a ser implantados e os prazos devem ser cumpridos. As configurações erradas de segurança podem estar em qualquer nível na infraestrutura: desde os sistemas operativos e aparelhos de rede, até aos recursos em nuvem.<br>O Azure Security Center compara continuamente a configuração dos seus recursos com requisitos em padrões, regulamentos e referências da indústria. Quando configurar os "pacotes de conformidade" relevantes (padrões e linhas de base) que interessam à sua organização, quaisquer lacunas resultarão em recomendações de segurança que incluam o CCEID e uma explicação do impacto potencial na segurança.<br>Os pacotes comumente utilizados são <a href="/azure/security/benchmarks/introduction">Azure Security Benchmark</a> e <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations Benchmark versão 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas<br>- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas<br>- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Pré-visualização)<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas em Linux (Pré-visualização)<br>- O agente de monitorização deve ser instalado em conjuntos de escala de máquina virtual<br>- Os problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas<br>- Sobrevaor ou desativação de contentores O perfil appArmor deve ser restringido (pré-visualização)<br>- O addon Azure Policy para Kubernetes deve ser instalado e ativado nos seus clusters (pré-visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Restringir o acesso não autorizado à rede (pontuação máxima 4)</p></strong>Os pontos finais dentro de uma organização fornecem uma ligação direta da sua rede virtual aos serviços Azure suportados. Máquinas virtuais numa sub-rede podem comunicar com todos os recursos. Para limitar a comunicação de e para os recursos dentro de uma sub-rede, crie um grupo de segurança de rede e associe-o à sub-rede. As organizações podem limitar e proteger contra o tráfego não autorizado, criando regras de entrada e saída.</td>
    <td class="tg-lboi"; width=55%>- O encaminhamento IP na sua máquina virtual deve ser desativado<br>- As gamas IP autorizadas devem ser definidas nos Serviços Kubernetes (Pré-visualização)<br>- (PRECADO) O acesso aos Serviços de Aplicações deve ser restringido (Pré-visualização)<br>- (PRECADO) As regras para aplicações web em IAAS NSGs devem ser endurecidas<br>- As máquinas virtuais devem ser associadas a um Grupo de Segurança de Rede<br>- O CORS não deve permitir que todos os recursos acedam à sua App API<br>- O CORS não deve permitir que todos os recursos acedam à sua App de Função<br>- O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web<br>- Depuragem remota deve ser desligada para app API<br>- Depuragem remota deve ser desligada para a App de Função<br>- Depuragem remota deve ser desligada para aplicação web<br>- O acesso deve ser restringido para grupos de segurança de rede permissivos com VMs virados para a Internet<br>- Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais<br>- O addon Azure Policy para Kubernetes deve ser instalado e ativado nos seus clusters (pré-visualização)<br>- Os contentores devem ouvir apenas as portas permitidas (pré-visualização)<br>- Os serviços devem ouvir apenas as portas permitidas (pré-visualização)<br>- A utilização da rede de anfitriões e das portas deve ser restringida (pré-visualização)<br>- As redes virtuais devem ser protegidas pelo Azure Firewall (pré-visualização)<br>- O ponto final privado deve ser ativado para servidores MariaDB<br>- O ponto final privado deve ser ativado para servidores MySQL<br>- O ponto final privado deve ser ativado para servidores PostgreSQL</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar controlo de aplicação adaptativo (pontuação máxima 3)</p></strong>O controlo de aplicações adaptativas (AAC) é uma solução inteligente, automatizada e de ponta a ponta, que permite controlar quais aplicações podem funcionar nas suas máquinas Azure e não-Azure. Também ajuda a endurecer as suas máquinas contra malware.<br>O Security Center utiliza machine learning para criar uma lista de aplicações conhecidas e seguras para um grupo de máquinas.<br>Esta abordagem inovadora à listagem de aplicações aprovada proporciona os benefícios de segurança sem a complexidade da gestão.<br>O AAC é particularmente relevante para servidores construídos de propósito que precisam executar um conjunto específico de aplicações.</td>
    <td class="tg-lboi"; width=55%>- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Pré-visualização)<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas em Linux (Pré-visualização)<br>- Os problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar classificação de dados (pontuação máxima 2)</p></strong>Classificar os dados da sua organização pela sensibilidade e impacto do negócio permite-lhe determinar e atribuir valor aos dados, e fornece a estratégia e base para a governação.<br><a href="/azure/information-protection/what-is-information-protection">A Azure Information Protection</a> pode ajudar na classificação de dados. Utiliza políticas de encriptação, identidade e autorização para proteger os dados e restringir o acesso aos dados. Algumas classificações que a Microsoft usa são não-comerciais, públicas, gerais, confidenciais e altamente confidenciais.</td>
    <td class="tg-lboi"; width=55%>- Os dados sensíveis nas suas bases de dados SQL devem ser classificados (Pré-visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Proteja as aplicações contra ataques DDoS (pontuação máxima 2)</p></strong>A negação de serviço distribuída (DDoS) ataca os recursos e torna as aplicações inutilizáveis. Use <a href="/azure/virtual-network/ddos-protection-overview">o Azure DDoS Protection Standard</a> para defender a sua organização dos três principais tipos de ataques DDoS:<br>- <strong>Ataques volumosos</strong> inundam a rede com tráfego legítimo. A Norma de Proteção DDoS atenua estes ataques absorvendo-os ou esfregando-os automaticamente.<br>- <strong>Os ataques de protocolo</strong> tornam um alvo inacessível, explorando fraquezas na pilha de protocolos da camada 3 e da camada 4. A DDoS Protection Standard atenua estes ataques bloqueando o tráfego malicioso.<br>- <strong>A camada de recursos (aplicação) ataca</strong> pacotes de aplicações web alvo. Defenda-se contra este tipo com uma firewall de aplicação web e norma de proteção DDoS.</td>
    <td class="tg-lboi"; width=55%>- A Norma de Proteção DDoS deve ser ativada<br>- O CPU do contentor e os limites de memória devem ser aplicados (pré-visualização)<br>- O addon Azure Policy para Kubernetes deve ser instalado e ativado nos seus clusters (pré-visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ativar a proteção do ponto final (pontuação máxima 2)</p></strong>Para garantir que os seus pontos finais estão protegidos contra malware, os sensores comportamentais recolhem e processam dados dos sistemas operativos dos seus pontos finais e enviam estes dados para a nuvem privada para análise. A análise de segurança aproveita os big data, machine-learning e outras fontes para recomendar respostas a ameaças. Por exemplo, <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">o Microsoft Defender ATP</a> utiliza inteligência de ameaça para identificar métodos de ataque e gerar alertas de segurança.<br>O Security Center suporta as seguintes soluções de proteção de ponto final: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v12.1.1.1100, McAfee v10 para Windows, McAfee v10 para Linux e Sophos v9 para Linux. Se o Centro de Segurança detetar alguma destas soluções, a recomendação de instalação da proteção do ponto final deixará de aparecer.</td>
    <td class="tg-lboi"; width=55%>- Falhas de saúde de proteção de ponto final devem ser remediadas em conjuntos de escala de máquina virtual<br>- Problemas de saúde de proteção de ponto final devem ser resolvidos nas suas máquinas<br>- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual<br>- Instale a solução de proteção do ponto final em máquinas virtuais<br>- Os problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas<br>- O agente de monitorização deve ser instalado em conjuntos de escala de máquina virtual<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Pré-visualização)<br>- O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas em Linux (Pré-visualização)<br>- Instale a solução de proteção do ponto final nas suas máquinas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Permitir a auditoria e o registo (pontuação máxima 1)</p></strong>Os dados de registo fornecem informações sobre problemas passados, previnem potenciais, podem melhorar o desempenho da aplicação e fornecem a capacidade de automatizar ações que de outra forma seriam manuais.<br>- <strong>Os registos de controlo e gestão</strong> fornecem informações sobre as operações <a href="/azure/azure-resource-manager/management/overview">do Azure Resource Manager.</a><br>- <strong>Os registos de planos de dados</strong> fornecem informações sobre os eventos angariados como parte do uso de recursos Azure.<br>- <strong>Eventos processados</strong> fornecem informações sobre eventos/alertas analisados que foram processados.</td>
    <td class="tg-lboi"; width=55%>- A auditoria no servidor SQL deve ser ativada<br>- Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados<br>- Os registos de diagnóstico na Azure Data Lake Store devem ser ativados<br>- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados<br>- Os registos de diagnóstico nas contas do Lote devem ser ativados<br>- Os registos de diagnóstico em Data Lake Analytics devem ser ativados<br>- Os registos de diagnóstico no Centro de Eventos devem ser ativados<br>- Os registos de diagnóstico no IoT Hub devem ser ativados<br>- Os registos de diagnóstico no Cofre de Chaves devem ser ativados<br>- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados<br>- Os registos de diagnóstico no serviço de pesquisa devem ser ativados<br>- Os registos de diagnóstico no Service Bus devem ser ativados<br>- Os registos de diagnóstico em conjuntos de escala de máquina virtual devem ser ativados<br>- As regras de alerta métrico devem ser configuradas nas contas do Lote<br>- As definições de auditoria do SQL devem ter Action-Groups configuradas para capturar atividades críticas<br>- Os servidores SQL devem ser configurados com dias de retenção de auditoria superiores a 90 dias.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Permitir uma proteção avançada de ameaças (pontuação máxima 0)</p></strong>Os planos opcionais de proteção contra ameaças do Azure Security Center proporcionam defesas abrangentes para o seu ambiente. Quando o Centro de Segurança deteta uma ameaça em qualquer área do seu ambiente, gera um alerta. Estes alertas descrevem detalhes dos recursos afetados, sugeriram medidas de reparação e, em alguns casos, uma opção para desencadear uma aplicação lógica em resposta.<br>Cada plano Azure Defender é uma oferta separada e opcional que pode permitir utilizar a recomendação relevante neste controlo de segurança.<br><a href="/azure/security-center/threat-protection">Saiba mais sobre a proteção contra ameaças no Centro de Segurança.</a></td>
    <td class="tg-lboi"; width=55%>- A segurança avançada dos dados deve ser ativada nos servidores da Base de Dados Azure SQL<br>- A segurança avançada dos dados deve ser ativada em servidores SQL em máquinas<br>- A proteção avançada de ameaças deve ser ativada em Máquinas Virtuais<br>- Proteção avançada de ameaças deve ser ativada nos planos do Azure App Service<br>- A proteção avançada de ameaças deve ser ativada nas contas de armazenamento do Azure<br>- A proteção avançada de ameaças deve ser ativada nos clusters de serviços Azure Kubernetes<br>- A proteção avançada de ameaças deve ser ativada nos registos do Registo de Contentores de Azure<br>- Proteção avançada de ameaças deve ser ativada em cofres Azure Key Vault</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementar as melhores práticas de segurança (pontuação máxima 0)</p></strong>As práticas de segurança modernas "assumem a violação" do perímetro da rede. Por essa razão, muitas das melhores práticas neste controlo concentram-se na gestão de identidades.<br>Perder chaves e credenciais é um problema comum. <a href="/azure/key-vault/key-vault-overview">O Azure Key Vault</a> protege chaves e segredos encriptando chaves, ficheiros .pfx e palavras-passe.<br>As redes privadas virtuais (VPNs) são uma forma segura de aceder às suas máquinas virtuais. Se as VPNs não estiverem disponíveis, utilize frases-passe complexas e autenticação de dois fatores, tais como <a href="/azure/active-directory/authentication/concept-mfa-howitworks">autenticação multi-factor AD Azure</a>. A autenticação de dois fatores evita as fraquezas inerentes à dependência apenas de nomes de utilizador e palavras-passe.<br>A utilização de plataformas de autenticação e autorização fortes é outra das melhores práticas. A utilização de identidades federadas permite que as organizações deleguem a gestão de identidades autorizadas. Isto também é importante quando os trabalhadores são despedidos, e o seu acesso precisa de ser revogado.</td>
    <td class="tg-lboi"; width=55%>- Um máximo de 3 proprietários deve ser designado para a sua subscrição<br>- As contas externas com permissões de leitura devem ser removidas da sua subscrição<br>- MFA deve ser ativado em contas com permissões de leitura na sua subscrição<br>- O acesso a contas de armazenamento com firewall e configurações de rede virtuais deve ser restringido<br>- Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nomes do Event Hub<br>- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL<br>- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas<br>- As regras de autorização relativas à instância do Centro de Eventos devem ser definidas<br>- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure<br>- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager<br>- As subnetas devem ser associadas a um Grupo de Segurança de Rede<br>- [Pré-visualização] A proteção de exploração do Windows deve ser ativada <br>- [Pré-visualização] O agente de configuração do hóspede deve ser instalado<br>- Máquinas virtuais não orientadas para a Internet devem ser protegidas com grupos de segurança de rede<br>- A azure Backup deve ser ativado para máquinas virtuais<br>- Backup geo-redundante deve ser ativado para Azure Database for MariaDB<br>- Backup geo-redundante deve ser ativado para Azure Database para MySQL<br>- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL<br>- PHP deve ser atualizado para a versão mais recente para a sua aplicação API<br>- PHP deve ser atualizado para a versão mais recente para a sua aplicação web<br>- Java deve ser atualizado para a versão mais recente para a sua aplicação API<br>- Java deve ser atualizado para a versão mais recente para a sua aplicação de função<br>- Java deve ser atualizado para a versão mais recente para a sua aplicação web<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação API<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação de função<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação web<br>- A retenção de auditoria para servidores SQL deve ser definida para pelo menos 90 dias<br>- As aplicações web devem solicitar um certificado SSL para todos os pedidos de entrada</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Pontuação segura FAQ

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações num controlo de segurança, a minha pontuação segura mudará?
Não. Não mudará até que remediar todas as recomendações para um único recurso. Para obter a pontuação máxima para um controlo, você deve remediar todas as recomendações, para todos os recursos.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim, e eu a desativar na apólice, o meu controlo de segurança será cumprido e a minha pontuação segura atualizada?
Yes. Recomendamos desativar recomendações quando são inaplicáveis no seu ambiente. Para obter instruções sobre como desativar uma recomendação específica, consulte [as políticas de segurança para desativar](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se um controlo de segurança me oferece zero pontos para a minha pontuação segura, devo ignorá-la?
Em alguns casos, verá uma pontuação máxima de controlo superior a zero, mas o impacto é zero. Quando a pontuação incremental para a fixação de recursos é insignificante, é arredondada para zero. Não ignore estas recomendações, pois ainda trazem melhorias de segurança. A única exceção é o controlo "Melhores Práticas Adicionais". Remediar estas recomendações não aumentará a sua pontuação, mas aumentará a sua segurança geral.

## <a name="next-steps"></a>Próximos passos

Este artigo descreveu a pontuação segura e os controlos de segurança que introduz. Para obter material relacionado, consulte os seguintes artigos:

- [Conheça os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como remediar recomendações](security-center-remediate-recommendations.md)
- [Veja as ferramentas baseadas no GitHub para trabalhar programáticamente com pontuação segura](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)