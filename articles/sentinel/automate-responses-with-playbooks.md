---
title: Automatizar resposta de ameaça com playbooks em Azure Sentinel | Microsoft Docs
description: Este artigo explica a automação em Azure Sentinel, e mostra como usar livros de reprodução para automatizar a prevenção e resposta de ameaças.
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
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609806"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Automatizar resposta de ameaça com playbooks em Azure Sentinel

Este artigo explica o que são os playbooks do Azure Sentinel e como usá-los para implementar as suas operações de Orquestração de Segurança, Automação e Resposta (SOAR), obtendo melhores resultados enquanto poupa tempo e recursos.

## <a name="what-is-a-playbook"></a>O que é um livro de jogadas?

As equipas do SIEM/SOC são normalmente inundadas com alertas de segurança e incidentes regularmente, em volumes tão grandes que o pessoal disponível está sobrecarregado. Isto resulta demasiadas vezes em situações em que muitos alertas são ignorados e muitos incidentes não são investigados, deixando a organização vulnerável a ataques que passam despercebidos.

Muitos, se não a maioria, destes alertas e incidentes estão em conformidade com padrões recorrentes que podem ser abordados por conjuntos específicos e definidos de ações de reparação.

Um livro de jogadas é uma coleção destas ações de reparação que podem ser executadas a partir de Azure Sentinel como uma rotina. Um livro de jogadas pode ajudar a automatizar e orquestrar a sua resposta de ameaça; pode ser executado manualmente ou definido para funcionar automaticamente em resposta a alertas ou incidentes específicos, quando desencadeado por uma regra de análise ou uma regra de automação, respectivamente.

Os playbooks são criados e aplicados ao nível da subscrição, mas o **separador Playbooks** (na nova lâmina **Automation)** exibe todos os playbooks disponíveis em todas as subscrições selecionadas.

### <a name="azure-logic-apps-basic-concepts"></a>Conceitos básicos de Azure Logic Apps

Os playbooks em Azure Sentinel são baseados em fluxos de trabalho construídos em [Azure Logic Apps](../logic-apps/logic-apps-overview.md), um serviço de nuvem que o ajuda a agendar, automatizar e orquestrar tarefas e fluxos de trabalho em todos os sistemas em toda a empresa. Isto significa que os playbooks podem tirar partido de toda a potência e personalização dos modelos incorporados da Logic Apps.

> [!NOTE]
> Como as Azure Logic Apps são um recurso separado, podem ser aplicados custos adicionais. Visite a página de preços do [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais detalhes.

A Azure Logic Apps comunica com outros sistemas e serviços usando conectores. Segue-se uma breve explicação dos conectores e de alguns dos seus atributos importantes:

- **Conector gerido:** Um conjunto de ações e gatilhos que envolvem chamadas de API para um determinado produto ou serviço. A Azure Logic Apps oferece centenas de conectores para comunicar com os serviços da Microsoft e não-Microsoft.
  - [Lista de todos os conectores de Aplicações Lógicas e sua documentação](/connectors/connector-reference/)

- **Conector personalizado:** Pode querer comunicar com serviços que não estão disponíveis como conectores pré-construídos. Os conectores personalizados abordam esta necessidade permitindo-lhe criar (e até partilhar) um conector e definir os seus próprios gatilhos e ações.
  - [Crie os seus próprios conectores de Aplicações Lógicas personalizadas](/connectors/custom-connectors/create-logic-apps-connector)

- **Conector Azure Sentinel:** Para criar livros que interagem com o Azure Sentinel, utilize o conector Azure Sentinel.
  - [Documentação do conector Azure Sentinel](/connectors/azuresentinel/)

- **Gatilho:** Um componente de conector que inicia um livro de jogadas. Define o esquema que o livro de jogadas espera receber quando desencadeado. O conector Azure Sentinel tem atualmente dois gatilhos:
  - [Detonador de alerta:](/connectors/azuresentinel/#triggers)o livro de jogadas recebe o alerta como entrada.
  - [Gatilho incidente](/connectors/azuresentinel/#triggers): o livro de jogadas recebe o incidente como entrada, juntamente com todos os seus alertas e entidades incluídos.

    > [!IMPORTANT]
    >
    > - A funcionalidade **de gatilho de incidentes** para os playbooks encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

- **Ações:** As ações são todos os passos que acontecem depois do gatilho. Podem ser organizados sequencialmente, em paralelo, ou numa matriz de condições complexas.

- **Campos dinâmicos:** Campos temporários, determinados pelo esquema de saída de gatilhos e ações e povoados pela sua produção real, que podem ser usados nas ações que se seguem.

### <a name="permissions-required"></a>Permissões necessárias

 Para dar à sua equipa de SecOps a capacidade de utilizar as operações de Orquestração, Automação e Resposta (SOAR) da Lógica de Segurança - isto é, para criar e executar playbooks - em Azure Sentinel, pode atribuir funções Azure, quer a membros específicos da sua equipa de operações de segurança, quer a toda a equipa. O seguinte descreve as diferentes funções disponíveis e as tarefas para as quais devem ser atribuídas:

#### <a name="azure-roles-for-logic-apps"></a>Papéis Azure para Apps Lógicas

- **O Logic App Contributor** permite-lhe gerir aplicações lógicas e executar playbooks, mas não pode alterar o acesso a elas (para isso precisa da função **Proprietário).**
- **O Operador de Aplicações Lógica**  permite-lhe ler, ativar e desativar aplicações lógicas, mas não pode editá-las ou atualizá-las.

#### <a name="azure-roles-for-sentinel"></a>Papéis de Azure para Sentinel

- O papel **de Azure Sentinel Contributor** permite-lhe anexar um livro de jogadas a uma regra de análise.
- O papel **de Azure Sentinel Responder** permite-lhe executar um livro manualmente.
- **A Azure Sentinel Automation Contributor** permite que as regras de automação executem livros de reprodução. Não é utilizado para outros fins.

#### <a name="learn-more"></a>Saiba mais

- [Saiba mais sobre os papéis do Azure em Azure Logic Apps.](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)
- [Saiba mais sobre os papéis do Azure em Azure Sentinel.](roles.md)

## <a name="steps-for-creating-a-playbook"></a>Passos para criar um livro de jogadas

- [Defina o cenário de automatização.](#use-cases-for-playbooks)

- [Construa a App Azure Logic](tutorial-respond-threats-playbook.md).

- [Teste a sua Aplicação Lógica.](#run-a-playbook-manually-on-an-alert)

- Fixe o livro de jogadas a uma [regra de automação](#incident-creation-automated-response) ou [a uma regra de análise,](#alert-creation-automated-response)ou [corra manualmente quando necessário](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Use casos para livros de reprodução

A plataforma Azure Logic Apps oferece centenas de ações e gatilhos, pelo que quase todos os cenários de automação podem ser criados. Azure Sentinel recomenda começar com os seguintes cenários SOC:

#### <a name="enrichment"></a>Melhoramento

**Recolher dados e anexá-lo ao incidente para tomar decisões mais inteligentes.**

Por exemplo:

Um incidente do Azure Sentinel foi criado a partir de um alerta por uma regra de análise que gera entidades de endereço IP.

O incidente desencadeia uma regra de automação que executa um livro de jogadas com os seguintes passos:

- Comece quando um [novo incidente do Azure Sentinel for criado.](/connectors/azuresentinel/#triggers) As entidades representadas no incidente estão armazenadas nos campos dinâmicos do gatilho do incidente.

- Para cada endereço IP, consultar um fornecedor externo de Inteligência de Ameaças, como [o Virus Total,](https://www.virustotal.com/)para obter mais dados.

- Adicione os dados devolvidos e insights como comentários do incidente.

#### <a name="bi-directional-sync"></a>Sincronização bidisal

**Os playbooks podem ser usados para sincronizar os seus incidentes do Azure Sentinel com outros sistemas de bilhética.**

Por exemplo:

Crie uma regra de automação para toda a criação de incidentes e anexe um livro que abra um bilhete no ServiceNow:

- Comece quando um [novo incidente do Azure Sentinel for criado.](/connectors/azuresentinel/#triggers)

- Crie um novo bilhete no [ServiceNow.](/connectors/service-now/)

- Inclua no bilhete o nome do incidente, campos importantes, e uma URL para o incidente do Azure Sentinel para facilitar a votação.

#### <a name="orchestration"></a>Orquestração

**Utilize a plataforma de chat SOC para controlar melhor a fila de incidentes.**

Por exemplo:

Um incidente do Azure Sentinel foi criado a partir de um alerta por uma regra de análise que gera nome de utilizador e entidades de endereço IP.

O incidente desencadeia uma regra de automação que executa um livro de jogadas com os seguintes passos:

- Comece quando um [novo incidente do Azure Sentinel for criado.](/connectors/azuresentinel/#triggers)

- Envie uma mensagem para o seu canal de operações de segurança em [Microsoft Teams](/connectors/teams/) ou [Slack](/connectors/slack/) para se certificar de que os seus analistas de segurança estão cientes do incidente.

- Envie todas as informações em alerta por e-mail para o seu administrador de rede sénior e administração de segurança. A mensagem de e-mail incluirá botões de opção **de utilizador Block** e **Ignore.**

- Aguarde até que uma resposta seja recebida dos administradores e continue a correr.

- Se os administradores escolherem **o Bloco,** envie um comando para a firewall para bloquear o endereço IP no alerta e outro para Azure AD para desativar o utilizador.

#### <a name="response"></a>Resposta

**Responda imediatamente a ameaças, com as mínimas dependências humanas.**

Dois exemplos:

**Exemplo 1:** Responda a uma regra de análise que indica um utilizador comprometido, como descoberto pela [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md):

   - Comece quando um [novo incidente do Azure Sentinel for criado.](/connectors/azuresentinel/#triggers)

   - Para cada entidade utilizadora do incidente suspeita-se de estar comprometida:

     - Envie uma mensagem de Equipas ao utilizador, solicitando a confirmação de que o utilizador tomou as medidas suspeitas.

     - Consulte a Azure AD Identity Protection para [confirmar o estado do utilizador como comprometido](/connectors/azureadip/#confirm-a-risky-user-as-compromised). A Azure AD Identity Protection irá rotular o utilizador como **arriscado**, e aplicar qualquer política de execução já configurada - por exemplo, para exigir que o utilizador utilize MFA quando iniciar a próxima sessão.

        > [!NOTE]
        > O livro de jogadas não inicia qualquer ação de execução sobre o utilizador, nem inicia qualquer configuração da política de execução. Apenas diz à Azure AD Identity Protection para aplicar quaisquer políticas já definidas conforme apropriado. Qualquer aplicação depende inteiramente das políticas adequadas que estão a ser definidas na Proteção de Identidade AZure AD.

**Exemplo 2:** Responda a uma regra de análise que indica uma máquina comprometida, como descoberto pelo [Microsoft Defender para Endpoint:](/windows/security/threat-protection/)

   - Comece quando um [novo incidente do Azure Sentinel for criado.](/connectors/azuresentinel/#triggers)

   - Use as **Entidades - Obtenha** a ação de Anfitriões em Azure Sentinel para analisar as máquinas suspeitas que estão incluídas nas entidades incidentes.

   - Emita um comando ao Microsoft Defender para o Endpoint para [isolar as máquinas](/connectors/wdatp/#actions---isolate-machine) no alerta.

## <a name="how-to-run-a-playbook"></a>Como executar um livro de jogadas

Os livros de reprodução podem ser executados **manualmente** ou **automaticamente**.

Executá-las manualmente significa que, quando recebe um alerta, pode optar por executar um livro de jogadas a pedido como resposta ao alerta selecionado. Atualmente esta funcionalidade é suportada apenas para alertas, não para incidentes.

Executá-las automaticamente significa defini-las como uma resposta automatizada numa regra de análise (para alertas), ou como uma ação numa regra de automação (para incidentes). [Saiba mais sobre as regras de automação.](automate-incident-handling-with-automation-rules.md)

### <a name="set-an-automated-response"></a>Definir uma resposta automatizada

As equipas de operações de segurança podem reduzir significativamente a sua carga de trabalho automatizando totalmente as respostas de rotina a tipos recorrentes de incidentes e alertas, permitindo-lhe concentrar-se mais em incidentes e alertas únicos, analisando padrões, caça a ameaças e muito mais.

Definir uma resposta automatizada significa que cada vez que uma regra de análise é desencadeada, além de criar um alerta, a regra executará um playbook, que receberá como entrada o alerta criado pela regra.

Se o alerta criar um incidente, o incidente irá desencadear uma regra de automação que pode, por sua vez, executar um livro de jogadas, que receberá como entrada o incidente criado pelo alerta.

#### <a name="alert-creation-automated-response"></a>Resposta automatizada de criação de alerta

Para os playbooks que são desencadeados pela criação de alerta e recebem alertas como as suas entradas (o primeiro passo é "Quando um Alerta sentinela Azure é acionado"), anexar o livro de jogadas a uma regra de análise:

1. Edite a [regra de análise](tutorial-detect-threats-custom.md) que gera o alerta para o qual pretende definir uma resposta automatizada.

1. Em **automação de alerta** no separador de resposta **automatizado,** selecione o livro de jogadas ou os livros de reprodução que esta regra de análise irá desencadear quando um alerta for criado.

#### <a name="incident-creation-automated-response"></a>Resposta automatizada de criação de incidentes

Para os playbooks que são desencadeados pela criação de incidentes e recebem incidentes como suas entradas (o seu primeiro passo é "Quando um Incidente de Sentinela Azure é desencadeado"), criar uma regra de automação e definir uma ação **de playbook Run** nele. Isto pode ser feito de 2 maneiras:

- Edite a regra de análise que gera o incidente para o qual pretende definir uma resposta automatizada. Sob **automatização de incidentes** no separador **de resposta automatizada,** crie uma regra de automação. Isto criará uma resposta automatizada apenas para esta regra de análise.

- A partir do separador **regras** de Automatização na lâmina **de Automação,** crie uma nova regra de automatização e especifique as condições e as ações desejadas. Esta regra de automatização será aplicada a qualquer regra de análise que cumpra as condições especificadas.

    > [!NOTE]
    > Para executar um livro de jogadas a partir de uma regra de automação, o Azure Sentinel utiliza uma conta de serviço especificamente autorizada a fazê-lo. A utilização desta conta (ao contrário da sua conta de utilizador) aumenta o nível de segurança do serviço e permite que as regras de automatização API suportem casos de utilização de CI/CD.
    >
    > Esta conta deve ser concedida permissões explícitas ao grupo de recursos onde o livro de jogadas reside. Nessa altura, qualquer regra de automação será capaz de executar qualquer livro de jogadas nesse grupo de recursos.
    >
    > Quando adicionar a ação **do playbook de execução** a uma regra de automação, aparecerá uma lista de livros de reprodução. Os livros para os quais a Azure Sentinel não tem permissões mostrarão como indisponíveis ("acinzentados"). Pode conceder permissão ao Azure Sentinel no local, selecionando o link **de permissões do livro de jogadas Manage.**
    >
    > Num cenário multi-inquilino ([Farol),](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)deve definir as permissões sobre o arrendatário onde o livro de jogadas vive, mesmo que a regra de automação que chama o livro de jogadas seja de um inquilino diferente. Para isso, tem de ter permissões **do Proprietário** no grupo de recursos do livro de jogadas.

Consulte as [instruções completas para a criação de regras de automação](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Executar um livro manualmente em um alerta

O detonador manual está disponível a partir do portal Azure Sentinel nas seguintes lâminas:

- Na vista **Incidentes,** escolha um incidente específico, abra o separador **Alertas** e escolha um alerta.

- Na **Investigação,** escolha um alerta específico.

1. Clique em **Ver livros de reprodução** para o alerta escolhido. Receberá uma lista de todos os playbooks que começam com um **Alerta Sentinela quando é acionado** e a que tem acesso.

1. Clique em **Executar** na linha de um livro de jogadas específico para desencadeá-lo.

1. Selecione o separador **Executar** para visualizar uma lista de todas as vezes que qualquer livro de jogadas foi executado neste alerta. Pode levar alguns segundos para que qualquer corrida acabada de aparecer nesta lista.

1. Clicar numa execução específica abrirá o registo completo de execução em Logic Apps.

### <a name="run-a-playbook-manually-on-an-incident"></a>Executar um livro manualmente sobre um incidente

Ainda não apoiado. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Gerencie os seus livros de jogadas

No **separador Playbooks,** aparece uma lista de todos os playbooks a que tem acesso, filtrado pelas subscrições que estão atualmente apresentadas no Azure. O filtro de subscrições está disponível no menu de **subscrição Do Diretório +** no cabeçalho global da página.

Clicar num nome de livro de jogadas direciona-o para a página principal do livro de jogadas em Logic Apps. A coluna **'Estado'** indica se está ativada ou desativada.

**O tipo de gatilho** representa o gatilho de Aplicações Lógicas que inicia este livro de jogadas.

| Tipo de gatilho | Indica tipos de componentes no livro de jogadas |
|-|-|
| **Incidente/Alerta de Azure Sentinel** | O livro de jogadas é iniciado com um dos gatilhos sentinelas (alerta, incidente) |
| **Usando a ação de Sentinela Azure** | O livro é iniciado com um gatilho não-Sentinel, mas usa uma ação do Azure Sentinel |
| **Outro** | O livro não inclui componentes Sentinel |
| **Não inicializado** | O livro foi criado, mas não contém componentes (gatilhos ou ações). |
|

Na página da Aplicação Lógica do livro, pode ver mais informações sobre o livro de jogadas, incluindo um registo de todas as vezes que executou, e o resultado (sucesso ou falha, e outros detalhes). Também pode introduzir o Design de Aplicações Lógicas e editar o livro de jogadas diretamente, se tiver as permissões apropriadas.

### <a name="api-connections"></a>Conexões API

As ligações API são usadas para ligar As Aplicações Lógicas a outros serviços. Sempre que é feita uma nova autenticação a um conector De Aplicações Lógicas, é criado um novo recurso de **ligação tipo API** e contém as informações fornecidas ao configurar o acesso ao serviço.

Para ver todas as ligações API, introduza *as ligações API* na caixa de pesquisa do porta-cabeçalho do portal Azure. Note as colunas de interesse:

- Nome do visor - o nome "amigável" que dá à ligação sempre que cria um.
- Estado - indica o estado de ligação: erro, ligado.
- Grupo de recursos - As ligações API são criadas no grupo de recursos do recurso playbook (Logic Apps).

Outra forma de visualizar as ligações API seria ir à lâmina **De Todos os Recursos** e filtrá-la através da *ligação tipo API*. Desta forma permite a seleção, marcação e eliminação de múltiplas ligações ao mesmo tempo.

Para alterar a autorização de uma ligação existente, insira o recurso de ligação e selecione **a ligação API de Edição**.

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Use livros de reprodução para automatizar respostas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md)
