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
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: 1476e00386f95e1c86630a0aa0ea7e4bb4180307
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684689"
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

O potencial para o controlo de segurança "Aplicar atualizações do sistema" na imagem acima mostra "2% (1 Ponto)". Isto significa que se remediar todas as recomendações neste controlo, a sua pontuação aumentará 2% (neste caso, um ponto). Para a simplicidade, os valores na coluna "Potencial aumento" da lista de recomendações são arredondados para números inteiros. As pontas de ferramentas mostram os valores precisos:

* **Pontuação máxima** - O número máximo de pontos que pode ganhar ao completar todas as recomendações dentro de um controlo. A pontuação máxima para um controlo indica o significado relativo desse controlo. Use os valores de pontuação máxima para triagem quais as questões para trabalhar primeiro. 
* **Potencial aumento** - Os restantes pontos disponíveis dentro do controlo. Para obter estes pontos adicionados à sua pontuação segura, reamediar todas as recomendações do controlo. No exemplo acima, o único ponto indicado para o controlo é, na verdade, 0,96 pontos.
* **Pontuação atual** - A pontuação atual para este controlo. Cada controlo contribui para a pontuação total. Neste exemplo, o controlo está a contribuir com 5,04 pontos para o total. 

### <a name="calculations---understanding-your-score"></a>Cálculos - compreender a sua pontuação

|Metric|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controlo de segurança**|<br>![Equação para calcular a pontuação atual de um controlo de segurança](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Cada controlo de segurança individual contribui para a Pontuação de Segurança. Cada recurso afetado por uma recomendação dentro do controlo contribui para a pontuação atual do controlo. A pontuação atual para cada controlo é uma medida do estado dos recursos *no controlo.*<br>![Dicas de ferramentas mostrando os valores utilizados no cálculo da pontuação atual do controlo de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos saudáveis e pouco saudáveis.<br>6 / 78 = 0,0769<br>Multiplicar isso pelo número de recursos saudáveis (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Classificação de segurança**<br>Subscrição individual|<br>![Equação para calcular a pontuação atual de segurança](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Pontuação segura de subscrição única com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, existe uma única subscrição com todos os controlos de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os restantes 32 pontos refletem-se nos números de "Potencial aumento de pontuação" dos controlos de segurança.<br>![Lista de controlos e aumento potencial da pontuação](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Classificação de segurança**<br>Múltiplas subscrições|<br>A pontuação atual para todos os recursos em todas as subscrições é adicionada e o cálculo é então o mesmo que para uma única subscrição<br><br>Ao visualizar várias subscrições, a pontuação segura avalia todos os recursos dentro de todas as políticas ativadas e agrupa o seu impacto combinado na pontuação máxima de cada controlo de segurança.<br>![Pontuação segura para várias subscrições com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do estado de todos os recursos em todas as subscrições.<br>Também aqui, se for à página de recomendações e somar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

## <a name="improving-your-secure-score"></a>Melhorar a sua pontuação segura

Para melhorar a sua pontuação segura, remediar as recomendações de segurança da sua lista de recomendações. Pode remediar cada recomendação manualmente para cada recurso, ou utilizando o **Quick Fix!** opção (quando disponível) de aplicar rapidamente uma reparação de uma recomendação a um grupo de recursos. Para mais informações, consulte [recomendações de Remediação](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Apenas recomendações incorporadas têm impacto na pontuação segura.

## <a name="security-controls-and-their-recommendations"></a>Controlos de segurança e respetivas recomendações

A tabela abaixo lista os controlos de segurança no Centro de Segurança Azure. Para cada controlo, pode ver o número máximo de pontos que pode adicionar à sua pontuação segura se remediar *todas as* recomendações listadas no controlo, para *todos os* seus recursos. 

> [!TIP]
> Se quiser filtrar ou classificar esta lista de forma diferente, copie e cole no Excel.

|Controlo de segurança|Pontos máximos seguros|Recomendações|
|----------------|:-------------------:|---------------|
|**Ativar MFA**|10|- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição<br>- O MFA deve ser ativado contas com permissões de escrita na sua subscrição|
|**Portas de gestão segura**|8|- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais<br>- As máquinas virtuais devem ser associadas a um Grupo de Segurança da Rede<br>- As portas de gestão devem ser fechadas nas suas máquinas virtuais|
|**Aplicar atualizações do Sistema**|6|- Problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas<br>- O agente de monitorização deve ser instalado em conjuntos de escala de máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- A versão OS deve ser atualizada para as suas funções de serviço na nuvem<br>- Devem ser instaladas atualizações do sistema em conjuntos de escala de máquinas virtuais<br>- As atualizações do sistema devem ser instaladas nas suas máquinas<br>- As suas máquinas devem ser reiniciadas para aplicar atualizações do sistema<br>- Os serviços kubernetes devem ser atualizados para uma versão kubernetes não vulnerável<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais|
|**Remediar vulnerabilidades**|6|- A segurança avançada dos dados deve ser ativada nos seus servidores SQL<br>- As vulnerabilidades nas imagens do Registo de Contentores de Azure devem ser remediadas<br>- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas<br>- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades<br>- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL<br>- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL<br>- A solução de avaliação de vulnerabilidade deve ser instalada nas suas máquinas virtuais|
|**Ativar encriptação em repouso**|4|- A encriptação do disco deve ser aplicada em máquinas virtuais<br>- Encriptação transparente de dados nas bases de dados SQL deve ser ativada<br>- Variáveis da conta de automação devem ser encriptadas<br>- Os clusters de tecidos de serviço devem ter a propriedade ClusterProtectionLevel definida para Encriptar AndSign<br>- O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave|
|**Encriptar dados em trânsito**|4|- A App API só deve ser acessível em HTTPS<br>- App de Funções só deve ser acessível através de HTTPS<br>- Apenas devem ser ativadas ligações seguras ao seu Redis Cache<br>- Transferência segura para contas de armazenamento deve ser ativada<br>- Aplicação Web só deve ser acessível em HTTPS|
|**Gerir acessos e permissões**|4|- As contas depreciadas devem ser removidas da sua subscrição (Pré-visualização)<br>- As contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição (Pré-visualização)<br>- As contas externas com permissões do proprietário devem ser removidas da sua subscrição (Pré-visualização)<br>- As contas externas com permissões por escrito devem ser removidas da sua subscrição (Pré-visualização)<br>- Deve haver mais de um proprietário atribuído à sua subscrição<br>- O Controlo de Acesso baseado em funções (RBAC) deve ser utilizado nos Serviços Kubernetes (Pré-visualização)<br>- Os clusters de tecidos de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente|
|**Remediar configurações de segurança**|4|- As Políticas de Segurança do Pod devem ser definidas nos Serviços Kubernetes<br>- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas<br>- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas<br>- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- O agente de monitorização deve ser instalado em conjuntos de escala de máquinas virtuais<br>- Problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas|
|**Restringir o acesso não autorizado à rede**|4|- O reencaminhamento ip da sua máquina virtual deve ser desativado<br>- As gamas IP autorizadas devem ser definidas nos Serviços Kubernetes (Pré-visualização)<br>- (DEPRECATED) O acesso aos serviços de aplicações deve ser restringido (Pré-visualização)<br>- (DEPRECATED) As regras relativas às aplicações web relativas aos NSGs iaas devem ser endurecidas<br>- As máquinas virtuais devem ser associadas a um Grupo de Segurança da Rede<br>- O CORS não deve permitir que todos os recursos acedam à sua App API<br>- O CORS não deve permitir que todos os recursos acedam à sua App de Funções<br>- O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web<br>- Depuração remota deve ser desligada para app API<br>- Depuração remota deve ser desligada para app de função<br>- Depuração remota deve ser desligada para aplicação web<br>- O acesso deve ser restringido para grupos permissivos de segurança da rede com VMs virados para a Internet<br>- As regras do grupo de segurança da rede para as máquinas virtuais viradas para a Internet devem ser endurecidas|
|**Aplicar o controlo de aplicações adaptável**|3|- Os controlos adaptáveis de aplicações devem ser ativados em máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- Problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas|
|**Aplicar classificação de dados**|2|- Os dados sensíveis nas suas bases de dados SQL devem ser classificados (Pré-visualização)|
|**Proteger aplicações contra ataques DDoS**|2|- A Norma de Proteção DDoS deve ser ativada|
|**Ativar a proteção do ponto final**|2|- As falhas de saúde em proteção do ponto final devem ser remediadas em conjuntos de escala de máquinas virtuais<br>- Problemas de saúde de proteção do ponto final devem ser resolvidos nas suas máquinas<br>- Solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual<br>- Instalar solução de proteção de pontos finais em máquinas virtuais<br>- Problemas de saúde dos agentes de monitorização devem ser resolvidos nas suas máquinas<br>- O agente de monitorização deve ser instalado em conjuntos de escala de máquinas virtuais<br>- O agente de monitorização deve ser instalado nas suas máquinas<br>- O agente de monitorização deve ser instalado nas suas máquinas virtuais<br>- Instale a solução de proteção do ponto final nas suas máquinas|
|**Permitir a auditoria e a exploração madeireira**|1|- A auditoria ao servidor SQL deve ser ativada<br>- Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados<br>- Os registos de diagnóstico na Azure Data Lake Store devem ser ativados<br>- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados<br>- Os registos de diagnóstico nas contas do Lote devem ser ativados<br>- Os registos de diagnóstico no Data Lake Analytics devem ser ativados<br>- Os registos de diagnóstico no Event Hub devem ser ativados<br>- Os registos de diagnóstico no IoT Hub devem ser ativados<br>- Os registos de diagnóstico no Cofre de Chaves devem ser ativados<br>- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados<br>- Os registos de diagnóstico no serviço de pesquisa devem ser ativados<br>- Os registos de diagnóstico no Autocarro de Serviço devem ser ativados<br>- Os registos de diagnóstico em conjuntos de escala de máquinas virtuais devem ser ativados<br>- As regras de alerta métricodevem ser configuradas nas contas do Lote<br>- As definições de auditoria SQL devem ter grupos de ação configurados para capturar atividades críticas<br>- Os servidores SQL devem ser configurados com dias de retenção de auditoria superiores a 90 dias.|
|**Implementar as melhores práticas de segurança**|0|- Deve ser designado um máximo de 3 proprietários para a sua subscrição<br>- As contas externas com permissões de leitura devem ser removidas da sua subscrição<br>- O MFA deve ser ativado em contas com permissões de leitura na sua subscrição<br>- O acesso a contas de armazenamento com configurações de firewall e rede virtual deve ser restringido<br>- Todas as regras de autorização, com exceção do RootManageSharedAccessKey, devem ser removidas do espaço de nome do Event Hub<br>- Deve ser aprovisionado um administrador de Diretório Ativo Azure para servidores SQL<br>- Devem ser definidas as regras de autorização relativas à instância do Event Hub<br>- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure<br>- As máquinas virtuais devem ser migradas para novos recursos do Gestor de Recursos Azure<br>- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança<br>- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas<br>- Todos os tipos avançados de proteção contra ameaças devem ser ativados em definições avançadas de segurança de dados geridas pela SQL<br>- As notificações de e-mail para administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados do servidor SQL<br>- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL<br>- As subredes devem ser associadas a um Grupo de Segurança da Rede<br>- Todos os tipos avançados de proteção contra ameaças devem ser ativados em definições avançadas de segurança de dados do servidor SQL<br>- [Pré-visualização] O guarda de exploração do Windows deve ser ativado <br>- [Pré-visualização] O agente de configuração do hóspede deve ser instalado|
||||

## <a name="secure-score-faq"></a>Pontuação segura FAQ

### <a name="why-has-my-secure-score-gone-down"></a>Porque é que a minha pontuação segura baixou?
O Security Center mudou para uma pontuação segura melhorada (atualmente em estado de pré-visualização) que inclui alterações na forma como a pontuação é calculada. Agora, você deve resolver todas as recomendações para um recurso para receber pontos. Os resultados também mudaram para uma escala de 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações num controlo de segurança, a minha pontuação segura mudará?
Não. Não mudará até que reamediar todas as recomendações para um único recurso. Para obter a pontuação máxima para um controlo, você deve remediar todas as recomendações, para todos os recursos.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>A experiência anterior da pontuação segura ainda está disponível? 
Yes. Por um tempo, estarão correndo lado a lado para facilitar a transição. Espere que o modelo anterior seja eliminado gradualmente a tempo. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim, e eu desativá-la na apólice, o meu controlo de segurança será cumprido e a minha pontuação segura atualizada?
Yes. Recomendamos que sejam recomendações incapacitantes quando são inaplicáveis no seu ambiente. Para obter instruções sobre como desativar uma recomendação específica, consulte as políticas de [segurança de sactivação](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se um controlo de segurança me oferecer zero pontos para a minha pontuação segura, devo ignorá-lo?
Em alguns casos, verá um controlo máximo de pontuação superior a zero, mas o impacto é zero. Quando a pontuação incremental para a fixação de recursos é insignificante, é arredondada para zero. Não ignore estas recomendações, pois ainda trazem melhorias de segurança. A única exceção é o controlo "Práticas Adicionais". Remediar estas recomendações não aumentará a sua pontuação, mas aumentará a sua segurança global.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu a pontuação segura e os controlos de segurança que introduz. Para material relacionado, consulte os seguintes artigos:

- [Conheça os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como remediar recomendações](security-center-remediate-recommendations.md)