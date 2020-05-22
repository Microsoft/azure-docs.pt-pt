---
title: Pontuação segura no Centro de Segurança Azure
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
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 3b740fca47b233fe38915280a1f53458c139293d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83778850"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Pontuação segura melhorada (pré-visualização) no Centro de Segurança Azure

## <a name="introduction-to-secure-score"></a>Introdução para garantir pontuação

O Azure Security Center tem dois objetivos principais: ajudá-lo a compreender a sua situação de segurança atual e ajudá-lo a melhorar de forma eficiente e eficaz a sua segurança. O aspeto central do Centro de Segurança que lhe permite atingir esses objetivos é a pontuação segura.

O Security Center avalia continuamente os seus recursos, subscrições e organização para questões de segurança. Em seguida, agrega todas as descobertas numa única pontuação para que possa dizer, num ápice, a sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado. Use a pontuação para acompanhar os esforços de segurança e projetos na sua organização. 

A página de pontuação segura do Centro de Segurança inclui:

- **A pontuação** - A pontuação segura é mostrada como um valor percentual, mas os valores subjacentes também são claros:

    [![Pontuação segura mostrada como um valor percentual com os números subjacentes claros também](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Controlos** de segurança - Cada controlo é um grupo lógico de recomendações de segurança relacionadas, e reflete as suas superfícies vulneráveis de ataque. Um controlo é um conjunto de recomendações de segurança, com instruções que o ajudam a implementar essas recomendações. A sua pontuação só melhora quando remedia *todas as* recomendações para um único recurso dentro de um controlo.

    Para ver imediatamente como a sua organização está a garantir cada superfície de ataque individual, reveja as pontuações de cada controlo de segurança.

    Para mais informações, consulte [como a pontuação segura é calculada](secure-score-security-controls.md#how-the-secure-score-is-calculated) abaixo. 


>[!TIP]
> Versões anteriores do Security Center premiaram pontos ao nível da recomendação: quando remediaste uma recomendação para um único recurso, a tua pontuação segura melhorou. Hoje, a sua pontuação só melhora se remediar *todas as* recomendações para um único recurso dentro de um controlo. Assim, a sua pontuação só melhora quando melhora a segurança de um recurso.
> Embora esta versão melhorada ainda esteja em pré-visualização, a experiência de pontuação segura anterior está disponível como opção do Portal Azure. 


## <a name="locating-your-secure-score"></a>Localizar a sua pontuação segura

O Centro de Segurança exibe a sua pontuação de forma proeminente: é a primeira coisa mostrada na página overview. Se clicar na página de pontuação de segurança dedicada, verá a pontuação discriminada por subscrição. Clique numa única subscrição para ver a lista detalhada de recomendações prioritárias e o impacto potencial que a sua remediação terá na pontuação da subscrição.

## <a name="how-the-secure-score-is-calculated"></a>Como a pontuação segura é calculada 

A contribuição de cada controlo de segurança para a pontuação global segura é mostrada claramente na página de recomendações.

[![A pontuação segura melhorada introduz controlos de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controlo de segurança, todos os seus recursos devem cumprir todas as recomendações de segurança dentro do controlo de segurança. Por exemplo, o Security Center tem múltiplas recomendações sobre como proteger as suas portas de gestão. No passado, poderia remediar algumas dessas recomendações relacionadas e interdependentes, deixando outras por resolver, e a sua pontuação segura melhoraria. Quando olhado objectivamente, é fácil argumentar que a sua segurança não tinha melhorado até que os tivesse resolvido a todos. Agora, tens de os remediar a todos para fazer a diferença na tua pontuação segura.

Por exemplo, o controlo de segurança chamado "Aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que pode ver na dica de ferramentas sobre o valor potencial de aumento do controlo:

[![O controlo de segurança "Aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A pontuação máxima para este controlo, Aplicar atualizações do sistema, é sempre 6. Neste exemplo, existem 50 recursos. Assim, dividimos a pontuação máxima por 50, e o resultado é que cada recurso contribui com 0,12 pontos. 

* **Aumento potencial** (0,12 x 8 recursos não saudáveis = 0,96) - Os restantes pontos disponíveis dentro do controlo. Se remediar todas as recomendações neste controlo, a sua pontuação aumentará 2% (neste caso, 0,96 pontos arredondados até 1 ponto). 
* **Pontuação atual** (0,12 x 42 recursos saudáveis = 5,04) - A pontuação atual para este controlo. Cada controlo contribui para a pontuação total. Neste exemplo, o controlo está a contribuir com 5,04 pontos para o total atual seguro.
* **Pontuação máxima** - O número máximo de pontos que pode ganhar ao completar todas as recomendações dentro de um controlo. A pontuação máxima para um controlo indica o significado relativo desse controlo. Use os valores de pontuação máxima para triagem das questões para trabalhar primeiro. 


### <a name="calculations---understanding-your-score"></a>Cálculos - compreender a sua pontuação

|Metric|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controlo de segurança**|<br>![Equação para calcular a pontuação atual de um controlo de segurança](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Cada controlo de segurança individual contribui para a Pontuação de Segurança. Cada recurso afetado por uma recomendação dentro do controlo contribui para a pontuação atual do controlo. A pontuação atual para cada controlo é uma medida do estado dos recursos *no controlo.*<br>![Dicas de ferramentas mostrando os valores utilizados no cálculo da pontuação atual do controlo de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos saudáveis e pouco saudáveis.<br>6 / 78 = 0,0769<br>Multiplicar isso pelo número de recursos saudáveis (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Classificação de segurança**<br>Subscrição individual|<br>![Equação para calcular a pontuação atual de segurança](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Pontuação segura de subscrição única com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, existe uma única subscrição com todos os controlos de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os restantes 32 pontos refletem-se nos números de "Potencial aumento de pontuação" dos controlos de segurança.<br>![Lista de controlos e aumento potencial da pontuação](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Classificação de segurança**<br>Múltiplas subscrições|<br>As pontuações atuais para todos os recursos em todas as subscrições são adicionadas e o cálculo é então o mesmo que para uma única subscrição<br><br>Ao visualizar várias subscrições, a pontuação segura avalia todos os recursos dentro de todas as políticas ativadas e agrupa o seu impacto combinado na pontuação máxima de cada controlo de segurança.<br>![Pontuação segura para várias subscrições com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do estado de todos os recursos em todas as subscrições.<br>Também aqui, se for à página de recomendações e somar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

## <a name="improving-your-secure-score"></a>Melhorar a sua pontuação segura

Para melhorar a sua pontuação segura, remediar as recomendações de segurança da sua lista de recomendações. Pode remediar cada recomendação manualmente para cada recurso, ou utilizando o **Quick Fix!** opção (quando disponível) de aplicar rapidamente uma reparação de uma recomendação a um grupo de recursos. Para mais informações, consulte [recomendações de Remediação](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Apenas recomendações incorporadas têm impacto na pontuação segura.

## <a name="security-controls-and-their-recommendations"></a>Controlos de segurança e respetivas recomendações

A tabela abaixo lista os controlos de segurança no Centro de Segurança Azure. Para cada controlo, pode ver o número máximo de pontos que pode adicionar à sua pontuação segura se remediar *todas as* recomendações listadas no controlo, para *todos os* seus recursos. 

<div class="foo">

<style type="text/css">.tg {border-collapse:collapse;border-espaing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;tamanho de fonte:14px; transbordamento:escondido;enchimento:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;tamanho de fonte:18px; fonte-peso:normal;transbordamento:escondido;enchimento:10px 5px;word-break:normal;} .tg.cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:her;text-align:left;vertical-align:middle}</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Controlo de segurança, pontuação e descrição</b><br></th>
    <th class="tg-cly1"><b>Recomendações</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ativar o MFA (pontuação máxima 10)</p></strong>Se usar apenas uma palavra-passe para autenticar um utilizador, deixa um vetor de ataque aberto. Se a palavra-passe for fraca ou tiver sido exposta noutro local, será realmente o utilizador a iniciar sessão com o nome de utilizador e a palavra-passe?<br>Com o <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> ativado, as suas contas são mais seguras e os utilizadores ainda podem autenticar quase qualquer aplicação com um único sinal (SSO).</td>
    <td class="tg-lboi"; width=55%>-MFA deve ser ativado em contas com permissões do proprietário na sua subscrição<br>-MFA deve ser ativado contas com permissões de escrita na sua subscrição</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Portas de gestão seguras (pontuação máxima 8)</p></strong>Força bruta ataca portas de gestão alvo para ter acesso a um VM. Uma vez que os portos nem sempre precisam de ser abertos, uma estratégia de mitigação é reduzir a exposição aos portos utilizando controlos de acesso à rede just-in-time, grupos de segurança de rede e gestão de portas de máquinas virtuais.<br>Uma vez que muitas organizações de TI não bloqueiam as comunicações SSH saindo da sua rede, os atacantes podem criar túneis encriptados que permitem que portas RDP em sistemas infetados se comuniquem de volta ao comando do intruso para controlar servidores. Os atacantes podem usar o subsistema de Gestão Remota do Windows para se moverem lateralmente pelo seu ambiente e utilizarem credenciais roubadas para aceder a outros recursos numa rede.</td>
    <td class="tg-lboi"; width=55%>-O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais<br>-As máquinas virtuais devem ser associadas a um Grupo de Segurança da Rede<br>-As portas de gestão devem ser fechadas nas suas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar atualizações do sistema (pontuação máxima 6)</p></strong>As atualizações do sistema proporcionam às organizações a capacidade de manter a eficiência operacional, reduzir vulnerabilidades de segurança e proporcionar um ambiente mais estável para os utilizadores finais. Não aplicar atualizações deixa vulnerabilidades não corrigidas e resulta em ambientes suscetíveis a ataques. Estas vulnerabilidades podem ser exploradas e levar à perda de dados, exfiltração de dados, ransomware e abuso de recursos. Para implementar atualizações do sistema, pode utilizar a <a href="https://docs.microsoft.com/azure/automation/automation-update-management">solução 'Gestão de Actualização' para gerir patches e atualizações</a> para as suas máquinas virtuais. A gestão da atualização é o processo de controlo da implementação e manutenção de lançamentos de software.</td>
    <td class="tg-lboi"; width=55%>-Problemas de saúde do agente de monitorização devem ser resolvidos nas suas máquinas<br>-O agente de monitorização deve ser instalado em conjuntos de escala de máquinas virtuais<br>-O agente de monitorização deve ser instalado nas suas máquinas<br>-A versão OS deve ser atualizada para as suas funções de serviço na nuvem<br>-As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas<br>-As atualizações do sistema devem ser instaladas nas suas máquinas<br>-As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema<br>-Os serviços kubernetes devem ser atualizados para uma versão kubernetes não vulnerável<br>-O agente de monitorização deve ser instalado nas suas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Remediar vulnerabilidades (pontuação máxima 6)</p></strong>Uma vulnerabilidade é uma fraqueza que um ator ameaçador poderia alavancar, para comprometer a confidencialidade, disponibilidade ou integridade de um recurso. <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">Gerir vulnerabilidades</a> reduz a exposição organizacional, endurece a área de superfície do ponto final, aumenta a resiliência organizacional e reduz a superfície de ataque dos seus recursos. A Gestão de Ameaças e Vulnerabilidades proporciona visibilidade em configurações de software e segurança e fornece recomendações para mitigações.</td>
    <td class="tg-lboi"; width=55%>-A segurança avançada de dados deve ser ativada nos seus servidores SQL<br>-Vulnerabilidades nas imagens do Registo de Contentores de Azure devem ser remediadas<br>-As vulnerabilidades nas suas bases de dados SQL devem ser remediadas<br>-Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades<br>-A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL<br>-A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL<br>-A solução de avaliação de vulnerabilidade deve ser instalada nas suas máquinas virtuais</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ativar encriptação em repouso (pontuação máxima 4)</p></strong><a href="https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest">A encriptação em repouso</a> fornece proteção de dados para dados armazenados. Os ataques contra dados em repouso incluem tentativas de acesso físico ao hardware no qual os dados são armazenados. Os azures usam encriptação simétrica para encriptar e desencriptar grandes quantidades de dados em repouso. Uma chave de encriptação simétrica é usada para encriptar dados tal como está escrito para armazenamento. Esta chave de encriptação também é usada para desencriptar esses dados à medida que são corrigidos para serem usados na memória. As chaves devem ser armazenadas num local seguro com políticas de controlo de acesso e auditoria baseadas na identidade. Um desses locais seguros é o Cofre de Chaves Azure. Se um intruso obtiver os dados encriptados, mas não as chaves de encriptação, o intruso não pode aceder aos dados sem quebrar a encriptação.</td>
    <td class="tg-lboi"; width=55%>-A encriptação do disco deve ser aplicada em máquinas virtuais<br>-Encriptação transparente de dados nas bases de dados SQL deve ser ativada<br>-Variáveis da conta de automação devem ser encriptadas<br>-Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign<br>-O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Criptografe dados em trânsito (pontuação máxima 4)</p></strong>Os dados estão "em trânsito" quando são transmitidos entre componentes, locais ou programas. As organizações que não protegem os dados em trânsito são suscetíveis a ataques man-in-the-middle, escutas e sequestro de sessões. Os protocolos SSL/TLS devem ser utilizados para a troca de dados e recomenda-se uma VPN. Ao enviar dados encriptados entre uma máquina virtual Azure e uma localização no local, através da internet, pode utilizar um portal de rede virtual, como <a href="https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways">o Azure VPN Gateway,</a> para enviar tráfego encriptado.</td>
    <td class="tg-lboi"; width=55%>-A Aplicação API só deve ser acessível em HTTPS<br>-App de funções só deve ser acessível através de HTTPS<br>-Apenas ligações seguras ao seu Redis Cache devem ser ativadas<br>-Transferência segura para contas de armazenamento deve ser ativada<br>-Aplicação Web só deve ser acessível em HTTPS</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Gerir acessos e permissões (pontuação máxima 4)</p></strong>Uma parte central de um programa de segurança é garantir que os seus utilizadores tenham o acesso necessário para fazer o seu trabalho, mas não mais do que isso: o modelo de <a href="https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">acesso menos privilegiado.</a><br>Controle o acesso aos seus recursos criando atribuições de funções com <a href="https://docs.microsoft.com/azure/role-based-access-control/overview">controlo de acesso baseado em funções (RBAC)</a>. Uma atribuição de funções é constituída por três elementos:<br>- <strong>Chefe de segurança</strong>: o objeto a que o utilizador está a pedir acesso<br>- <strong>Definição de papéis</strong>: as suas permissões<br>- <strong>Âmbito</strong>: o conjunto de recursos a que se aplicam as permissões</td>
    <td class="tg-lboi"; width=55%>-As contas depreciadas devem ser removidas da sua subscrição (Pré-visualização)<br>-As contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição (Pré-visualização)<br>-As contas externas com permissões do proprietário devem ser removidas da sua subscrição (Pré-visualização)<br>-As contas externas com permissões de escrita devem ser removidas da sua subscrição (Pré-visualização)<br>-Deve haver mais de um proprietário atribuído à sua subscrição<br>-O Controlo de Acesso baseado em funções (RBAC) deve ser utilizado nos serviços kubernetes (pré-visualização)<br>-Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Remediar as configurações de segurança (pontuação máxima 4)</p></strong>Os ativos de TI mal configurados têm um maior risco de serem atacados. As ações básicas de endurecimento são muitas vezes esquecidas quando os ativos estão a ser implantados e os prazos devem ser cumpridos. As configurações de segurança podem estar em qualquer nível da infraestrutura: desde os sistemas operativos e aparelhos de rede, até aos recursos em nuvem.<br>O Azure Security Center compara continuamente a configuração dos seus recursos com requisitos nos padrões, regulamentos e referências da indústria. Quando configurar os "pacotes de conformidade" relevantes (padrões e linhas de base) que importam para a sua organização, quaisquer lacunas resultarão em recomendações de segurança que incluam o CCEID e uma explicação do potencial impacto de segurança.<br>Os pacotes comumente utilizados são <a href="https://docs.microsoft.com/azure/security/benchmarks/introduction">o Azure Security Benchmark</a> e o <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations Benchmark versão 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>-Políticas de Segurança do Pod devem ser definidas nos Serviços Kubernetes<br>-As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas<br>-As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas<br>-As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas<br>-O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>-O agente de monitorização deve ser instalado nas suas máquinas<br>-O agente de monitorização deve ser instalado em conjuntos de escala de máquinas virtuais<br>-Problemas de saúde do agente de monitorização devem ser resolvidos nas suas máquinas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Restringir o acesso não autorizado à rede (pontuação máxima 4)</p></strong>Os pontos finais dentro de uma organização fornecem uma ligação direta da sua rede virtual aos serviços Azure suportados. Máquinas virtuais numa subnet podem comunicar com todos os recursos. Para limitar a comunicação de e para os recursos dentro de uma subnet, crie um grupo de segurança de rede e associe-o à subrede. As organizações podem limitar e proteger contra o tráfego não autorizado, criando regras de entrada e saída.</td>
    <td class="tg-lboi"; width=55%>-O reencaminhamento ip na sua máquina virtual deve ser desativado<br>-As gamas IP autorizadas devem ser definidas nos Serviços Kubernetes (Pré-visualização)<br>-(DEPRECIADO) O acesso aos Serviços de Aplicações deve ser restringido (Pré-visualização)<br>-(DEPRECIADO) As regras aplicáveis às aplicações web em NsGs IaaS devem ser endurecidas<br>-As máquinas virtuais devem ser associadas a um Grupo de Segurança da Rede<br>-O CORS não deve permitir que todos os recursos acedam à sua App API<br>-O CORS não deve permitir que todos os recursos acedam à sua App de Funções<br>-O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web<br>-Depuração remota deve ser desligada para App API<br>-Depuração remota deve ser desligada para app de função<br>-Depuração remota deve ser desligada para aplicação web<br>-O acesso deve ser restringido para grupos permissivos de segurança da rede com VMs virados para a Internet<br>-As regras do Grupo de Segurança da Rede para as máquinas virtuais viradas para a Internet devem ser endurecidas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar o controlo de aplicação adaptável (pontuação máxima 3)</p></strong>O controlo adaptativo de aplicações (AAC) é uma solução inteligente, automatizada e de ponta a ponta, que permite controlar quais as aplicações que podem ser executadas nas suas máquinas Azure e não-Azure. Também ajuda a endurecer as suas máquinas contra malware.<br>O Security Center usa machine learning para criar uma lista branca de aplicações conhecidas para um grupo de máquinas.<br>Esta abordagem inovadora à aplicação whitelisting proporciona os benefícios de segurança sem a complexidade da gestão.<br>O AAC é particularmente relevante para servidores construídos de propósito que precisam executar um conjunto específico de aplicações.</td>
    <td class="tg-lboi"; width=55%>-Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais<br>-O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>-O agente de monitorização deve ser instalado nas suas máquinas<br>-Problemas de saúde do agente de monitorização devem ser resolvidos nas suas máquinas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Aplicar classificação de dados (pontuação máxima 2)</p></strong>Classificar os dados da sua organização por sensibilidade e impacto empresarial permite-lhe determinar e atribuir valor aos dados, e fornece a estratégia e base para a governação.<br><a href="https://docs.microsoft.com/azure/information-protection/what-is-information-protection">A Proteção de Informação Azure</a> pode ajudar na classificação de dados. Utiliza políticas de encriptação, identidade e autorização para proteger dados e restringir o acesso aos dados. Algumas classificações que a Microsoft usa são não-comerciais, públicas, gerais, confidenciais e altamente confidenciais.</td>
    <td class="tg-lboi"; width=55%>-Os dados sensíveis nas suas bases de dados SQL devem ser classificados (Pré-visualização)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Proteja aplicações contra ataques DDoS (pontuação máxima 2)</p></strong>Os ataques de negação de serviço distribuídos (DDoS) sobrecarregam os recursos e tornam as aplicações inutilizáveis. Utilize a Norma de <a href="https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview">Proteção DDoS Azure</a> para defender a sua organização dos três principais tipos de ataques DDoS:<br>- <strong>Ataques volutrricos</strong> inundam a rede com tráfego legítimo. A Norma de Proteção DDoS atenua estes ataques absorvendo-os ou esfregando-os automaticamente.<br>- <strong>Os ataques protocolares</strong> tornam um alvo inacessível, explorando fraquezas na camada 3 e camada 4 do protocolo. A Norma de Proteção DDoS atenua estes ataques bloqueando o tráfego malicioso.<br>- <strong>A camada de recursos (aplicação) ataca</strong> pacotes de aplicação web alvo. Defenda-se contra este tipo com uma firewall de aplicação web e Padrão de Proteção DDoS.</td>
    <td class="tg-lboi"; width=55%>-Norma de proteção DDoS deve ser ativada</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ativar a proteção do ponto final (pontuação máxima 2)</p></strong>Para garantir que os seus pontos finais estão protegidos contra malware, os sensores comportamentais recolhem e processam dados dos sistemas operativos dos seus pontos finais e enviam estes dados para a nuvem privada para análise. A análise de segurança aproveita os big data, a aprendizagem automática e outras fontes para recomendar respostas a ameaças. Por exemplo, o <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> usa inteligência de ameaça para identificar métodos de ataque e gerar alertas de segurança.<br>O Security Center suporta as seguintes soluções de proteção de pontofinal: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v12.1.1.1.1100, McAfee v10 para Windows, McAfee v10 para Linux e Sophos v9 para o Linux. Se o Security Center detetar alguma destas soluções, a recomendação de instalação da proteção do ponto final deixará de aparecer.</td>
    <td class="tg-lboi"; width=55%>-Falhas de saúde de proteção de pontos finais devem ser remediadas em conjuntos de escala de máquinas virtuais<br>-Questões de saúde de proteção de pontofinal devem ser resolvidas nas suas máquinas<br>-A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual<br>-Instale solução de proteção de pontofinal em máquinas virtuais<br>-Problemas de saúde do agente de monitorização devem ser resolvidos nas suas máquinas<br>-O agente de monitorização deve ser instalado em conjuntos de escala de máquinas virtuais<br>-O agente de monitorização deve ser instalado nas suas máquinas<br>-O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>-Instale a solução de proteção do ponto final nas suas máquinas</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ativar auditoria e exploração madeireira (pontuação máxima 1)</p></strong>O registo de dados fornece informações sobre problemas passados, previne potenciais, pode melhorar o desempenho da aplicação e fornece a capacidade de automatizar ações que de outra forma seriam manuais.<br>- <strong>Os registos</strong> de controlo e gestão fornecem informações sobre as operações <a href="https://docs.microsoft.com/azure/azure-resource-manager/management/overview">do Gestor de Recursos azure.</a><br>- <strong>Os registos</strong> de planos de dados fornecem informações sobre eventos levantados como parte do uso de recursos do Azure.<br>- <strong>Eventos processados</strong> fornecem informações sobre eventos/alertas analisados que foram processados.</td>
    <td class="tg-lboi"; width=55%>-A auditoria no servidor SQL deve ser ativada<br>-Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados<br>-Os registos de diagnóstico na Azure Data Lake Store devem ser ativados<br>-Os registos de diagnóstico no Azure Stream Analytics devem ser ativados<br>-Os registos de diagnóstico nas contas do Lote devem ser ativados<br>-Os registos de diagnóstico no Data Lake Analytics devem ser ativados<br>-Os registos de diagnóstico no Event Hub devem ser ativados<br>-Os registos de diagnóstico no IoT Hub devem ser ativados<br>-Os registos de diagnóstico no Cofre-Chave devem ser ativados<br>-Os registos de diagnóstico em Aplicações Lógicas devem ser ativados<br>-Os registos de diagnóstico no serviço de pesquisa devem ser ativados<br>-Os registos de diagnóstico no Ônibus de Serviço devem ser ativados<br>-Os registos de diagnóstico em conjuntos de escala de máquinas virtuais devem ser ativados<br>-As regras de alerta métrico devem ser configuradas nas contas do Lote<br>-As definições de auditoria sQL devem ter grupos de ação configurados para capturar atividades críticas<br>-Os servidores SQL devem ser configurados com dias de retenção de auditoria superiores a 90 dias.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementar as melhores práticas de segurança (pontuação máxima 0)</p></strong>As práticas de segurança modernas "assumem a violação" do perímetro da rede. Por essa razão, muitas das melhores práticas neste controlo focam-se na gestão de identidades.<br>Perder chaves e credenciais é um problema comum. <a href="https://docs.microsoft.com/azure/key-vault/key-vault-overview">O Azure Key Vault</a> protege chaves e segredos encriptando chaves, ficheiros .pfx e senhas.<br>As redes privadas virtuais (VPNs) são uma forma segura de aceder às suas máquinas virtuais. Se as VPNs não estiverem disponíveis, utilize frases-passe complexas e autenticação de dois fatores, como a <a href="https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks">autenticação de multi-factors Azure</a>. A autenticação de dois fatores evita as fraquezas inerentes a confiar apenas nos nomes de utilizador e palavras-passe.<br>A utilização de plataformas de autenticação e autorização fortes é outra das melhores práticas. O uso de identidades federadas permite que as organizações delegem a gestão de identidades autorizadas. Isto também é importante quando os trabalhadores são despedidos e o seu acesso tem de ser revogado.</td>
    <td class="tg-lboi"; width=55%>-Um máximo de 3 proprietários deve ser designado para a sua subscrição<br>-As contas externas com permissões de leitura devem ser removidas da sua subscrição<br>-MFA deve ser ativado em contas com permissões de leitura na sua subscrição<br>-O acesso a contas de armazenamento com configurações de firewall e rede virtual deve ser restringido<br>-Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do espaço de nome do Event Hub<br>-Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL<br>-As regras de autorização sobre a instância do Event Hub devem ser definidas<br>-As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos do Azure<br>-Máquinas virtuais devem ser migradas para novos recursos do Gestor de Recursos Do Azure<br>-As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança<br>-A segurança avançada dos dados deve ser ativada nas suas instâncias geridas<br>-Todos os tipos avançados de proteção contra ameaças devem ser ativados em definições avançadas de segurança de dados geridas pela SQL<br>-As notificações de e-mail para administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados do servidor SQL<br>-Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL<br>-As subredes devem ser associadas a um Grupo de Segurança da Rede<br>-Todos os tipos avançados de proteção contra ameaças devem ser ativados em definições avançadas de segurança de dados do servidor SQL<br>-[Pré-visualização] O guarda de exploração do Windows deve ser ativado <br>-[Pré-visualização] O agente de configuração do hóspede deve ser instalado</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Pontuação segura FAQ

### <a name="why-has-my-secure-score-gone-down"></a>Porque é que a minha pontuação segura baixou?
O Security Center mudou para uma pontuação segura melhorada (atualmente em estado de pré-visualização) que inclui alterações na forma como a pontuação é calculada. Agora, você deve resolver todas as recomendações para um recurso para receber pontos. Os resultados também mudaram para uma escala de 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações num controlo de segurança, a minha pontuação segura mudará?
Não. Não mudará até que reamediar todas as recomendações para um único recurso. Para obter a pontuação máxima para um controlo, você deve remediar todas as recomendações, para todos os recursos.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>A experiência anterior da pontuação segura ainda está disponível? 
Sim. Por um tempo, estarão correndo lado a lado para facilitar a transição. Espere que o modelo anterior seja eliminado gradualmente a tempo. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim, e eu desativá-la na apólice, o meu controlo de segurança será cumprido e a minha pontuação segura atualizada?
Sim. Recomendamos que sejam recomendações incapacitantes quando são inaplicáveis no seu ambiente. Para obter instruções sobre como desativar uma recomendação específica, consulte as políticas de [segurança de sactivação](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se um controlo de segurança me oferecer zero pontos para a minha pontuação segura, devo ignorá-lo?
Em alguns casos, verá um controlo máximo de pontuação superior a zero, mas o impacto é zero. Quando a pontuação incremental para a fixação de recursos é insignificante, é arredondada para zero. Não ignore estas recomendações, pois ainda trazem melhorias de segurança. A única exceção é o controlo "Práticas Adicionais". Remediar estas recomendações não aumentará a sua pontuação, mas aumentará a sua segurança global.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu a pontuação segura e os controlos de segurança que introduz. Para material relacionado, consulte os seguintes artigos:

- [Conheça os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como remediar recomendações](security-center-remediate-recommendations.md)