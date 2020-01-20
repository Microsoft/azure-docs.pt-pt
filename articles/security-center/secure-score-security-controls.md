---
title: Pontuação segura aprimorada (visualização) na central de segurança do Azure
description: Descrição da Pontuação segura aprimorada (visualização) e controles de segurança na central de segurança do Azure
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
ms.date: 12/04/2019
ms.author: memildin
ms.openlocfilehash: 0287a8011eb10120e273fb063c98ccd3c1a85782
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278025"
---
# <a name="the-enhanced-secure-score-preview"></a>A pontuação segura aprimorada (visualização) 

Este artigo apresenta a pontuação segura aprimorada (atualmente em visualização), os controles de segurança que o acompanham e as vantagens que eles trazem. Ele também explica como sua pontuação é calculada.

## <a name="introduction-to-secure-score"></a>Introdução à pontuação segura

A central de segurança do Azure tem dois objetivos principais: para ajudá-lo a entender sua situação de segurança atual e para ajudá-lo a melhorar sua segurança de forma eficiente e eficaz. O aspecto central da central de segurança que permite alcançar essas metas é a pontuação segura.

A central de segurança avalia continuamente seus recursos, suas assinaturas e a organização em busca de problemas de segurança. Em seguida, ele agrega todas as conclusões em uma única Pontuação para que você possa ver, rapidamente, sua situação de segurança atual: quanto maior a pontuação, menor o nível de risco identificado. Use a pontuação para acompanhar os esforços de segurança e os projetos em sua organização. 

A pontuação segura *aprimorada* (atualmente em visualização) é **focada na superfície de ataque** e traz três benefícios:

- **Controles de segurança** -as recomendações de segurança agora estão agrupadas em conjuntos lógicos que refletem melhor suas superfícies de ataque vulneráveis. Para obter mais informações, consulte [como a pontuação segura é calculada](secure-score-security-controls.md#how-the-secure-score-is-calculated) abaixo.

- **A pontuação geral reflete melhor os pontos de postura geral que** foram concedidos no nível de recomendação. Com esse aprimoramento, sua pontuação só melhorará quando você corrigir *todas* as recomendações para um único recurso dentro de um controle. Isso significa que sua pontuação só melhora quando a segurança de um recurso melhora. 

- O **status de segurança de superfícies de ataque individuais é mais visível** – mostrando a pontuação por controle de segurança, a página de Pontuação segura se torna o local onde você pode obter uma visão granular de como a sua organização está protegendo cada superfície de ataque individual.

A pontuação segura avançada é mostrada como uma porcentagem, conforme mostrado na seguinte captura de tela:

[![a pontuação segura aprimorada (visualização) agora inclui uma porcentagem](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)


## <a name="locating-your-secure-score"></a>Localizando sua pontuação segura

A central de segurança exibe sua pontuação de forma proeminente: é a primeira coisa mostrada na página Visão geral. Se você clicar na página de Pontuação segura dedicada, verá a pontuação dividida por assinatura. Clique em uma única assinatura para ver a lista detalhada de recomendações priorizadas e o impacto potencial que a correção terá na pontuação da assinatura.

## <a name="how-the-secure-score-is-calculated"></a>Como a pontuação segura é calculada 

Antes dessa visualização, a central de segurança considerou cada recomendação individualmente e atribuiu um valor a ela com base em sua gravidade. As equipes de segurança que trabalham para aprimorar sua postura de segurança tinham de priorizar as respostas às recomendações da central de segurança com base na lista completa de descobertas. Sempre que você corrigir uma recomendação para um único recurso, sua pontuação segura melhorou.

Como parte dos aprimoramentos na pontuação segura, as recomendações agora são agrupadas em **controles de segurança**. Um controle é um conjunto de recomendações de segurança e as instruções que ajudam a implementar essas recomendações. Os controles são agrupamentos lógicos de recomendações relacionadas. Os pontos não são mais concedidos no nível de recomendação. Em vez disso, sua pontuação só melhorará quando você corrigir *todas* as recomendações para um único recurso dentro de um controle.

A contribuição de cada controle de segurança em direção à pontuação segura geral é mostrada claramente na página recomendações.

[![a pontuação segura aprimorada (visualização) apresenta os controles de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controle de segurança, todos os seus recursos devem estar em conformidade com todas as recomendações de segurança dentro do controle de segurança. Por exemplo, a central de segurança tem várias recomendações sobre como proteger suas portas de gerenciamento. No passado, você poderia corrigir algumas dessas recomendações relacionadas e interdependentes ao deixar outros não resolvidos e sua pontuação segura melhoraria. Quando olhamos objetivamente, é fácil argumentar que sua segurança não foi aprimorada até você ter resolvido todas elas. Agora, você deve corrigir todos eles para fazer uma diferença na sua pontuação segura.

Por exemplo, o controle de segurança chamado "aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que você pode ver na dica de ferramenta sobre o valor de aumento potencial do controle:

[![o controle de segurança "aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

O potencial para o controle de segurança "aplicar atualizações do sistema" na captura de tela acima mostra "2% (1 ponto)". Isso significa que, se você corrigir todas as recomendações nesse controle, sua pontuação aumentará em 2% (nesse caso, um ponto). Para simplificar, os valores na coluna "aumento potencial" da lista de recomendações são arredondados para números inteiros. As dicas de ferramenta mostram os valores precisos:

* **Possível aumento** -os pontos restantes disponíveis para você dentro do controle. Para obter esses pontos adicionados à sua pontuação segura, corrija todas as recomendações do controle. No exemplo acima, o ponto mostrado para o controle é, na verdade, 0,96 pontos.
* **Pontuação atual** -a pontuação atual para este controle. Cada controle contribui para a pontuação total. Neste exemplo, o controle está contribuindo com 5, 4 pontos para o total. 
* **Pontuação máxima** -a soma dos dois valores anteriores.

### <a name="calculations---understanding-your-score"></a>Cálculos-noções básicas sobre sua pontuação

|Métrica|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controle de segurança**|<br>![equação para calcular a pontuação atual de um controle de segurança](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Cada controle de segurança individual contribui para a pontuação de segurança. Cada recurso afetado por uma recomendação dentro do controle, contribui para a pontuação atual do controle. A pontuação atual de cada controle é uma medida do status dos recursos *dentro* do controle.<br>![dicas de ferramentas mostrando os valores usados ao calcular a pontuação atual do controle de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos íntegros e não íntegros.<br>6/78 = 0, 769<br>Multiplicar isso pelo número de recursos íntegros (74) resulta na pontuação atual:<br>0, 769 * 74 = **5,69**<br><br>|
|**Secure Score** (Classificação de segurança)<br>Subscrição individual|<br>![Equação para calcular a pontuação segura atual](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Pontuação segura de assinatura única com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, há uma única assinatura com todos os controles de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os 32 pontos restantes são refletidos nas figuras de "aumento da Pontuação potencial" dos controles de segurança.<br>![Lista de controles e o aumento de Pontuação potencial](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Secure Score** (Classificação de segurança)<br>Várias assinaturas|<br>A pontuação atual de todos os recursos em todas as assinaturas é adicionada e o cálculo é o mesmo que para uma única assinatura<br><br>Ao exibir várias assinaturas, a pontuação segura avalia todos os recursos em todas as políticas habilitadas e agrupa seu impacto combinado na pontuação máxima de cada controle de segurança.<br>![Pontuação segura para várias assinaturas com todos os controles habilitados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do status de todos os recursos em todas as assinaturas.<br>Aqui também, se você for para a página recomendações e adicionar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

## <a name="improving-your-secure-score"></a>Melhorando sua pontuação segura

Para melhorar sua pontuação segura, corrija as recomendações de segurança da sua lista de recomendações. Você pode corrigir cada recomendação manualmente para cada recurso ou usando a **correção rápida!** opção (quando disponível) para aplicar rapidamente uma correção para uma recomendação a um grupo de recursos. Para obter mais informações, consulte [corrigir recomendações](security-center-remediate-recommendations.md).

Somente as recomendações internas têm um impacto na pontuação segura.

## <a name="security-controls-and-their-recommendations"></a>Controles de segurança e suas recomendações

A tabela a seguir lista os controles de segurança na central de segurança do Azure. Para cada controle, você pode ver o número máximo de pontos que pode adicionar à sua pontuação segura se você corrigir *todas* as recomendações listadas no controle para *todos os* seus recursos. 

> [!TIP]
> Se você quiser filtrar ou classificar essa lista de forma diferente, copie e cole-a no Excel.

|Controle de segurança|Máximo de pontos de Pontuação segura|Recomendações|
|----------------|:-------------------:|---------------|
|**Ativar MFA**|10|-A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura<br>-A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura<br>-A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura|
|**Proteger portas de gerenciamento**|8|-O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais<br>-As máquinas virtuais devem ser associadas a um grupo de segurança de rede<br>-As portas de gerenciamento devem ser fechadas em suas máquinas virtuais|
|**Aplicar atualizações do sistema**|6|-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>-O agente de monitoramento deve ser instalado em conjuntos de dimensionamento de máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-A versão do sistema operacional deve ser atualizada para suas funções de serviço de nuvem<br>-As atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais devem ser instaladas<br>-As atualizações do sistema devem ser instaladas em seus computadores<br>-Os computadores devem ser reiniciados para aplicar atualizações do sistema<br>-Os serviços Kubernetess devem ser atualizados para uma versão não vulnerável do kubernetes<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais|
|**Remediar vulnerabilidades**|6|-A segurança de dados avançada deve ser habilitada em seus servidores SQL<br>-As vulnerabilidades nas imagens do registro de contêiner do Azure devem ser corrigidas (visualização)<br>-As vulnerabilidades em seus bancos de dados SQL devem ser corrigidas<br>-Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade<br>-A avaliação de vulnerabilidade deve ser habilitada em suas instâncias gerenciadas do SQL<br>-A avaliação de vulnerabilidade deve ser habilitada em seus servidores SQL<br>-A solução de avaliação de vulnerabilidade deve ser instalada em suas máquinas virtuais|
|**Habilitar criptografia em repouso**|4|-A criptografia de disco deve ser aplicada em máquinas virtuais<br>-Transparent Data Encryption em bancos de dados SQL deve ser habilitado<br>-As variáveis da conta de automação devem ser criptografadas<br>-Service Fabric clusters devem ter a propriedade ClusterProtectionLevel definida como EncryptAndSign<br>-O protetor de TDE do SQL Server deve ser criptografado com sua própria chave|
|**Criptografar dados em trânsito**|4|-O aplicativo de API só deve ser acessível via HTTPS<br>-Aplicativo de funções só deve ser acessível via HTTPS<br>-Somente as conexões seguras para o cache Redis devem ser habilitadas<br>-A transferência segura para contas de armazenamento deve ser habilitada<br>-O aplicativo Web só deve ser acessível via HTTPS|
|**Gerenciar acesso e permissões**|4|-Um máximo de 3 proprietários deve ser designado para sua assinatura<br>-As contas preteridas devem ser removidas da sua assinatura (versão prévia)<br>-Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura (versão prévia)<br>-Contas externas com permissões de proprietário devem ser removidas da sua assinatura (versão prévia)<br>-Contas externas com permissões de leitura devem ser removidas da sua assinatura<br>-Contas externas com permissões de gravação devem ser removidas da sua assinatura (versão prévia)<br>-Deve haver mais de um proprietário atribuído à sua assinatura<br>-RBAC (controle de acesso baseado em função) deve ser usado em serviços Kubernetess (versão prévia)<br>-Service Fabric clusters só devem usar Azure Active Directory para autenticação de cliente|
|**Remediar configurações de segurança**|4|-As políticas de segurança de Pod devem ser definidas nos serviços Kubernetess (versão prévia)<br>-Vulnerabilidades nas configurações de segurança do contêiner devem ser corrigidas<br>-Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas<br>-As vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-O agente de monitoramento deve ser instalado em conjuntos de dimensionamento de máquinas virtuais<br>-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores|
|**Restringir o acesso não autorizado à rede**|4|-O encaminhamento IP em sua máquina virtual deve ser desabilitado<br>-Os intervalos de IP autorizados devem ser definidos nos serviços Kubernetess (versão prévia)<br>-O acesso aos serviços de aplicativos deve ser restrito (versão prévia)<br>-As regras para aplicativos Web em IaaS NSGs devem ser protegidas<br>-As máquinas virtuais devem ser associadas a um grupo de segurança de rede<br>-CORS não deve permitir que todos os recursos acessem seu aplicativo de API<br>-CORS não deve permitir que todos os recursos acessem seu Aplicativo de funções<br>-CORS não deve permitir que todos os recursos acessem seu aplicativo Web<br>-A depuração remota deve ser desativada para o aplicativo de API<br>-A depuração remota deve ser desativada para Aplicativo de funções<br>-A depuração remota deve ser desativada para o aplicativo Web<br>-O acesso deve ser restrito para grupos de segurança de rede permissivos com VMs voltadas para a Internet<br>-As regras de grupo de segurança de rede para máquinas virtuais voltadas para a Internet devem ser protegidas|
|**Aplicar controle de aplicativo adaptável**|3|-Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores|
|**Aplicar classificação de dados**|2|-Os dados confidenciais em seus bancos de dado SQL devem ser classificados (versão prévia)|
|**Proteger aplicativos contra ataques de DDoS**|2|-A proteção contra DDoS Standard deve ser habilitada|
|**Habilitar o Endpoint Protection**|2|-As falhas de integridade do Endpoint Protection devem ser corrigidas nos conjuntos de dimensionamento de máquinas virtuais<br>-Problemas de integridade do Endpoint Protection devem ser resolvidos em seus computadores<br>-A solução Endpoint Protection deve ser instalada em conjuntos de dimensionamento de máquinas virtuais<br>-Instalar a solução Endpoint Protection em máquinas virtuais<br>-Problemas de integridade do agente de monitoramento devem ser resolvidos em seus computadores<br>-O agente de monitoramento deve ser instalado em conjuntos de dimensionamento de máquinas virtuais<br>-O agente de monitoramento deve ser instalado em seus computadores<br>-O agente de monitoramento deve ser instalado em suas máquinas virtuais<br>-Instalar a solução Endpoint Protection em seus computadores|
|**Habilitar auditoria e registro em log**|1|-A auditoria no SQL Server deve ser habilitada<br>-Os logs de diagnóstico nos serviços de aplicativo devem ser habilitados<br>-Os logs de diagnóstico no Azure Data Lake Store devem ser habilitados<br>-Os logs de diagnóstico no Azure Stream Analytics devem ser habilitados<br>-Os logs de diagnóstico em contas do lote devem ser habilitados<br>-Os logs de diagnóstico no Data Lake Analytics devem ser habilitados<br>-Os logs de diagnóstico no Hub de eventos devem ser habilitados<br>-Os logs de diagnóstico no Hub IoT devem ser habilitados<br>-Os logs de diagnóstico no Key Vault devem ser habilitados<br>-Os logs de diagnóstico em aplicativos lógicos devem ser habilitados<br>-Os logs de diagnóstico no serviço de pesquisa devem ser habilitados<br>-Os logs de diagnóstico no barramento de serviço devem ser habilitados<br>-Os logs de diagnóstico em conjuntos de dimensionamento de máquinas virtuais devem ser habilitados<br>-As regras de alerta de métrica devem ser configuradas em contas do lote<br>-As configurações de auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas<br>-Os servidores SQL devem ser configurados com dias de retenção de auditoria maiores que 90 dias.|
|**Implementar práticas recomendadas de segurança**|0|-O acesso a contas de armazenamento com firewall e configurações de rede virtual deve ser restrito<br>-Todas as regras de autorização, exceto RootManageSharedAccessKey, devem ser removidas do namespace do hub de eventos<br>-Um administrador de Azure Active Directory deve ser provisionado para servidores SQL<br>-As regras de autorização na instância do hub de eventos devem ser definidas<br>-As contas de armazenamento devem ser migradas para novos recursos de Azure Resource Manager<br>-As máquinas virtuais devem ser migradas para novos recursos de Azure Resource Manager<br>-As configurações de segurança de dados avançadas para o SQL Server devem conter um endereço de email para receber alertas de segurança<br>-A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas<br>-Todos os tipos de proteção avançada contra ameaças devem ser habilitados na instância gerenciada do SQL configurações de segurança de dados avançadas<br>-Notificações por email para administradores e proprietários de assinatura devem ser habilitados nas configurações de segurança de dados avançada do SQL Server<br>-Tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' nas configurações de segurança de dados avançadas do SQL Server<br>-As sub-redes devem ser associadas a um grupo de segurança de rede<br>-Todos os tipos de proteção avançada contra ameaças devem ser habilitados nas configurações de segurança de dados avançada do SQL Server|
||||

## <a name="secure-score-faq"></a>FAQ de Pontuação segura

### <a name="why-has-my-secure-score-gone-down"></a>Por que minha pontuação segura desapareceu?
Com as alterações introduzidas nessa Pontuação segura aprimorada, você deve resolver toda a recomendação de um recurso para receber pontos. As pontuações também foram alteradas para uma escala de 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações em um controle de segurança, minha pontuação de proteção será alterada?
Foi Ele não será alterado até que você corrija todas as recomendações para um único recurso. Para obter a pontuação máxima de um controle, você deve corrigir todas as recomendações, para todos os recursos.

### <a name="will-this-enhanced-secure-score-replace-the-existing-secure-score"></a>Essa Pontuação segura aprimorada substituirá a pontuação segura existente? 
Sim, mas por um tempo eles serão executados lado a lado para facilitar a transição.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim e eu desabilitá-la na política, meu controle de segurança será atendido e minha pontuação segura é atualizada?
Sim. É recomendável desabilitar as recomendações quando elas não são aplicáveis em seu ambiente. Para obter instruções sobre como desabilitar uma recomendação específica, consulte [Disable Security Policies](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se um controle de segurança me oferecer zero pontos em direção à minha pontuação segura, devo ignorá-lo?
Em alguns casos, você verá uma pontuação máxima de controle maior que zero, mas o impacto é zero. Quando a pontuação incremental para corrigir recursos é insignificante, ela é arredondada para zero. Não ignore essas recomendações, pois elas ainda trazem melhorias de segurança. A única exceção é o controle "prática recomendada adicional". A correção dessas recomendações não aumentará sua pontuação, mas irá aprimorar a segurança geral.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu a pontuação segura aprimorada e os novos controles de segurança que ele introduz. Para obter material relacionado, consulte os seguintes artigos:

- [Saiba mais sobre os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como corrigir recomendações](security-center-remediate-recommendations.md)