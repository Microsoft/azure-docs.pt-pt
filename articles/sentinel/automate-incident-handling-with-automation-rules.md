---
title: Automatizar o manuseamento de incidentes em Azure Sentinel | Microsoft Docs
description: Este artigo explica como usar as regras de automatização para automatizar o manuseamento de incidentes, de forma a maximizar a eficiência e eficácia do seu SOC em resposta a ameaças à segurança.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609813"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Automatizar o manuseamento de incidentes em Azure Sentinel com regras de automação

Este artigo explica quais são as regras de automação do Azure Sentinel e como usá-las para implementar as suas operações de Orquestração de Segurança, Automação e Resposta (SOAR), aumentando a eficácia do seu SOC e poupando-lhe tempo e recursos.

> [!IMPORTANT]
>
> - A funcionalidade **de regras de automatização** encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="what-are-automation-rules"></a>O que são regras de automação?

As regras de automação são um novo conceito no Azure Sentinel. Esta funcionalidade permite aos utilizadores gerir centralmente a automatização do tratamento de incidentes. Além de permitir atribuir playbooks a incidentes (não apenas a alertas como antes), as regras de automação também permitem automatizar respostas para várias regras de análise ao mesmo tempo, marcar, atribuir ou fechar incidentes sem necessidade de playbooks, e controlar a ordem de ações que são executadas. As regras de automação irão simplificar o uso da automatização no Azure Sentinel e permitir-lhe-ão simplificar fluxos de trabalho complexos para os seus processos de orquestração de incidentes.

## <a name="components"></a>Componentes

As regras de automação são compostas por vários componentes:

### <a name="trigger"></a>Acionador

As regras de automação são desencadeadas pela criação de um incidente. 

Para revisão – os incidentes são criados a partir de alertas por regras de análise, dos quais existem quatro tipos, como explicado no tutorial [Detetar ameaças com regras de análise incorporadas em Azure Sentinel.](tutorial-detect-threats-built-in.md)

### <a name="conditions"></a>Condições

Conjuntos complexos de condições podem ser definidos para governar quando as ações (ver abaixo) devem ser executadas. Estas condições são tipicamente baseadas nos estados ou valores de atributos de incidentes e suas entidades, e podem incluir `AND` / `OR` / `NOT` / `CONTAINS` operadores.

### <a name="actions"></a>Ações

As ações podem ser definidas para serem executadas quando as condições (ver acima) forem cumpridas. Pode definir muitas ações numa regra, e pode escolher a ordem pela qual serão executadas (ver abaixo). As seguintes ações podem ser definidas usando regras de automação, sem a necessidade de [uma funcionalidade avançada de um livro de jogadas:](automate-responses-with-playbooks.md)

- Alterar o estado de um incidente, mantendo o seu fluxo de trabalho atualizado.

  - Ao mudar para "fechado", especificar o [motivo de encerramento](tutorial-investigate-cases.md#closing-an-incident) e adicionar um comentário. Isto ajuda-o a acompanhar o seu desempenho e eficácia, e afinar para reduzir falsos positivos.

- Alterar a gravidade de um incidente – pode reavaliar e repriorizar com base na presença, ausência, valores ou atributos de entidades envolvidas no incidente.

- Atribuir um incidente a um proprietário – isto ajuda-o a direcionar tipos de incidentes para o pessoal mais adequado para lidar com eles, ou para o pessoal mais disponível.

- Adicionar uma etiqueta a um incidente – isto é útil para classificar incidentes por sujeito, por intruso ou por qualquer outro denominador comum.

Além disso, pode definir uma ação para executar um [livro de jogadas](tutorial-respond-threats-playbook.md), de forma a tomar ações de resposta mais complexas, incluindo quaisquer que envolvam sistemas externos. Apenas os playbooks ativados pelo gatilho do [incidente](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) estão disponíveis para serem utilizados nas regras de automação. Pode definir uma ação para incluir vários playbooks, ou combinações de playbooks e outras ações, e a ordem pela qual serão executados.

### <a name="expiration-date"></a>Data de validade

Pode definir uma data de validade numa regra de automação. A regra será desativada depois dessa data. Isto é útil para lidar (isto é, fechar) incidentes de "ruído" causados por atividades planeadas e limitadas no tempo, tais como testes de penetração.

### <a name="order"></a>Encomenda

Pode definir a ordem em que as regras de automação serão executadas. Posteriormente, as regras de automação avaliarão as condições do incidente de acordo com o seu estado, depois de terem sido atuadas pelas regras anteriores da automação.

Por exemplo, se a "Regra da Primeira Automatização" alterar a gravidade de um incidente de Média para Baixa, e a "Segunda Regra da Automação" for definida para funcionar apenas em incidentes com severidade média ou superior, não será executado nesse incidente.

## <a name="common-use-cases-and-scenarios"></a>Casos e cenários de uso comum

### <a name="incident-triggered-automation"></a>Automatização desencadeada por incidentes

Até agora, apenas os alertas poderiam desencadear uma resposta automatizada, através da utilização de livros de jogadas. Com as regras de automação, os incidentes podem agora desencadear cadeias de resposta automatizadas, que podem incluir novos playbooks desencadeados por incidentes[(são necessárias permissões especiais](#permissions-for-automation-rules-to-run-playbooks)), quando um incidente é criado. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Trigger playbooks para fornecedores da Microsoft

As regras de automação fornecem uma forma de automatizar o manuseamento dos alertas de segurança da Microsoft aplicando estas regras a incidentes criados a partir dos alertas. As regras de automação podem chamar playbooks[(autorizações especiais são necessárias](#permissions-for-automation-rules-to-run-playbooks)) e passar-lhes os incidentes com todos os seus detalhes, incluindo alertas e entidades. Em geral, as melhores práticas de Azure Sentinel ditam o uso da fila dos incidentes como ponto focal das operações de segurança.

Os alertas de segurança da Microsoft incluem:

- Segurança da Aplicação Microsoft Cloud (MCAS)
- Azure AD Identity Protection
- Azure Defender (ASC)
- Defensor de IoT (anteriormente ASC para IoT)
- Microsoft Defender para Office 365 (anteriormente conhecido como ATP do Office 365)
- Microsoft Defender para Ponto Final (anteriormente MDATP)
- Microsoft Defender para Identidade (anteriormente Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Vários livros/ações sequenciadas numa única regra

Pode agora ter um controlo quase completo sobre a ordem de execução de ações e playbooks numa única regra de automação. Também controla a ordem de execução das próprias regras de automação. Isto permite-lhe simplificar muito os seus livros de jogadas, reduzindo-os a uma única tarefa ou a uma pequena e simples sequência de tarefas, e combinar estes pequenos livros de jogadas em diferentes combinações em diferentes regras de automação.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Atribua um livro de jogadas a várias regras de análise de uma só vez

Se tiver uma tarefa que pretende automatizar em todas as suas regras de análise – por exemplo, a criação de um bilhete de apoio num sistema de bilhética externa – pode aplicar um único livro de jogadas a qualquer uma ou todas as suas regras de análise – incluindo quaisquer regras futuras – de uma só vez. Isto torna as tarefas de manutenção e limpeza simples, mas repetitivas, muito menos de uma tarefa.

### <a name="automatic-assignment-of-incidents"></a>Atribuição automática de incidentes

Pode atribuir incidentes ao proprietário certo automaticamente. Se o seu SOC tiver um analista especializado numa determinada plataforma, quaisquer incidentes relacionados com essa plataforma podem ser automaticamente atribuídos a esse analista.

### <a name="incident-suppression"></a>Supressão de incidentes

Pode utilizar regras para resolver automaticamente incidentes conhecidos positivos falsos/benignos sem a utilização de livros de jogadas. Por exemplo, ao realizar testes de penetração, fazer manutenção ou atualizações programadas ou testar procedimentos de automação, podem ser criados muitos incidentes falsos positivos que o SOC quer ignorar. Uma regra de automação limitada no tempo pode fechar automaticamente estes incidentes à medida que são criados, enquanto os marca com um descritor da causa da sua geração.

### <a name="time-limited-automation"></a>Automação limitada no tempo

Pode adicionar datas de validade para as suas regras de automação. Pode haver casos que não a supressão de incidentes que justifiquem uma automatização limitada no tempo. Pode querer atribuir um determinado tipo de incidente a um determinado utilizador (por exemplo, um estagiário ou um consultor) por um período de tempo específico. Se o prazo for conhecido com antecedência, pode efetivamente fazer com que a regra seja desativada no final da sua relevância, sem ter de se lembrar de o fazer.

### <a name="automatically-tag-incidents"></a>Identifie automaticamente incidentes

Pode adicionar automaticamente tags de texto gratuito a incidentes para agrupar ou classificá-las de acordo com quaisquer critérios à sua escolha.

## <a name="automation-rules-execution"></a>Execução de regras de automação

As regras de automação são executadas sequencialmente, de acordo com a ordem que determina. Cada regra de automação é executada depois de a anterior ter terminado o seu percurso. Dentro de uma regra de automação, todas as ações são executadas sequencialmente na ordem em que são definidas.

Para as ações de playbook, há um atraso de dois minutos entre o início da ação do playbook e a próxima ação na lista.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Permissões para regras de automação para executar playbooks

Quando uma regra de automação Azure Sentinel executa um livro de jogadas, utiliza uma conta especial de serviço Azure Sentinel especificamente autorizada para esta ação. A utilização desta conta (ao contrário da sua conta de utilizador) aumenta o nível de segurança do serviço.

Para que uma regra de automação possa executar um livro de jogadas, esta conta deve ser concedida permissões explícitas ao grupo de recursos onde o livro de jogadas reside. Nessa altura, qualquer regra de automação será capaz de executar qualquer livro de jogadas nesse grupo de recursos.

Quando estiver a configurar uma regra de automação e adicionar uma ação **de playbook run,** aparecerá uma lista de playbooks. Os livros para os quais a Azure Sentinel não tem permissões mostrarão como indisponíveis ("acinzentados"). Pode conceder permissão ao Azure Sentinel aos grupos de recursos dos playbooks no local, selecionando o link **de permissões do livro de jogadas Manage.**

> [!NOTE]
> **Permissões em arquitetura multi-inquilino**
>
> As regras de automação suportam totalmente o espaço de trabalho cruzado e as implantações multi-arrendantes (no caso de multi-inquilinos, utilizando o [Farol Azure).](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)
>
> Portanto, se a sua implantação do Azure Sentinel utiliza uma arquitetura multi-inquilina (Se você é um MSSP, por exemplo), você pode ter uma regra de automação em um inquilino executar um livro que vive em um inquilino diferente, mas as permissões para sentinel para executar os livros de reprodução devem ser definidas no inquilino onde os livros de jogadas residem, e não no inquilino onde as regras de automação são definidas.

## <a name="creating-and-managing-automation-rules"></a>Criação e gestão de regras de automação

Pode criar e gerir regras de automação de diferentes pontos da experiência Azure Sentinel, dependendo da sua necessidade e utilização particulares.

- **Lâmina de automação**

    As regras de automatização podem ser geridas centralmente na nova lâmina **Automation** (que substitui a lâmina **Playbooks),** no  separador **regras da Automação.** A partir daí, pode criar novas regras de automação e editar as existentes. Também pode arrastar regras de automação para alterar a ordem de execução e forobilá-las ou desativá-las.

    Na **lâmina** automation, vê-se todas as regras definidas no espaço de trabalho, juntamente com o seu estado (Ativado/Desativado) e quais as regras de análise a que são aplicadas.

    Quando precisar de uma regra de automatização que se aplique a muitas regras de análise, crie-a diretamente na lâmina **de Automação.** A partir do menu superior, clique em **Criar** e **Adicionar nova regra,** que abre o **painel de regras Criar nova automatização.** A partir daqui tem total flexibilidade na configuração da regra: pode aplicá-la a quaisquer regras de análise (incluindo as futuras) e definir a mais ampla gama de condições e ações.

- **Assistente de regras de analítica**

    No separador de **resposta automatizada** do assistente de regras de análise, pode ver, gerir e criar regras de automação que se aplicam à regra de análise específica que está a ser criada ou editada no assistente.

    Quando clicar em **Criar** e num dos tipos de regras **(Regra** de consulta agendada ou regra de criação de **incidentes** da Microsoft ) a partir do menu superior da lâmina **Analytics,** ou se selecionar uma regra de análise existente e clicar em **Editar,** abrirá o assistente de regras. Quando selecionar o separador **de resposta automatizado,** verá uma secção chamada **automatização de incidentes,** segundo a qual serão apresentadas as regras de automação que se aplicam atualmente a esta regra. Pode selecionar uma regra de automação existente para editar ou clicar **em Adicionar nova** para criar uma nova.

    Irá notar que, quando criar a regra da automatização a partir daqui, o painel de **regras Create new automation** mostra a condição de **regra de análise** como indisponível, porque esta regra já está definida para aplicar apenas à regra de análise que está a editar no assistente. Todas as outras opções de configuração ainda estão disponíveis para si.

- **Lâmina de incidentes**

    Também pode criar uma regra de automação a partir da lâmina **Incidentes,** de forma a responder a um único incidente recorrente. Isto é útil ao criar uma [regra de supressão](#incident-suppression) para encerrar automaticamente incidentes "barulhentos". Selecione um incidente a partir da fila e clique em Criar regra de **automação** a partir do menu superior.

    Você vai notar que o novo painel **de regras de automação** tem povoado todos os campos com valores do incidente. Ele nomeia a regra com o mesmo nome do incidente, aplica-o à regra de análise que gerou o incidente, e utiliza todas as entidades disponíveis no incidente como condições da regra. Também sugere uma ação de supressão (fechamento) por defeito, e sugere uma data de validade para a regra. Pode adicionar ou remover condições e ações e alterar a data de validade, conforme desejar.

## <a name="auditing-automation-rule-activity"></a>Auditar atividade de regra de automação

Pode estar interessado em saber o que aconteceu a um dado incidente, e o que uma determinada regra de automação pode ou não ter feito com ele. Tem um registo completo das crónicas de incidentes disponíveis na tabela *SecurityIncident* na lâmina **logs.** Utilize a seguinte consulta para ver toda a sua atividade de regra de automação:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a usar as regras de automação para gerir a sua fila de incidentes Azure Sentinel e implementar alguma automatização básica de tratamento de incidentes.

- Para saber mais sobre opções avançadas de automatização, consulte [a resposta da ameaça automatizada com os playbooks em Azure Sentinel.](automate-responses-with-playbooks.md)
- Para obter ajuda na implementação de regras de automação e playbooks, consulte [Tutorial: Use playbooks para automatizar respostas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).
