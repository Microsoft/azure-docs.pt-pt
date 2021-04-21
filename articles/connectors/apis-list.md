---
title: Conectores do Azure Logic Apps
description: Visão geral dos conectores para a construção de fluxos de trabalho automatizados com apps Azure Logic. Saiba como funcionam diferentes tipos de conectores, gatilhos e ações.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771358"
---
# <a name="connectors-for-azure-logic-apps"></a>Conectores do Azure Logic Apps

Nas Apps Azure Logic, os conectores ajudam-no a aceder rapidamente a *eventos, dados* e ações de outras apps, serviços, sistemas, protocolos e plataformas. Muitas vezes pode fazer estas tarefas sem código adicional. Também pode usar conectores para construir fluxos de trabalho de Apps Lógicas que usam esta informação em ambientes baseados em nuvem, no local ou em ambientes híbridos.

Existem centenas de conectores disponíveis para aplicações lógicas. Como resultado, esta documentação foca-se em alguns dos conectores mais populares e comumente usados para Apps Lógicas. Para obter informações completas sobre conectores através de Aplicações Lógicas, Microsoft Power Automamate e Microsoft Power Apps, consulte [a documentação dos Conectores](/connectors). 

Para obter informações sobre preços, consulte o modelo de preços das [Aplicações Lógicas](../logic-apps/logic-apps-pricing.md)e [os detalhes dos preços das Aplicações Lógicas.](https://azure.microsoft.com/pricing/details/logic-apps/)

Para integrar a sua aplicação lógica com um serviço ou API que não tenha um conector, pode ligar para o serviço através de um protocolo, como HTTP, ou [criar um conector personalizado.](#custom-apis-and-connectors)

## <a name="what-are-connectors"></a>O que são conectores?

Nas Apps Azure Logic, os conectores fornecem *gatilhos* e *ações* que utiliza para executar tarefas no fluxo de trabalho da sua aplicação lógica. Cada gatilho e ação tem propriedades que pode configurar. Alguns gatilhos e ações requerem que [crie e configuure ligações](#connection-configuration) para que o seu fluxo de trabalho possa aceder a um serviço ou sistema específico.

### <a name="triggers"></a>Acionadores

Um *gatilho* é sempre o primeiro passo em qualquer fluxo de trabalho, que especifica o evento que inicia o fluxo de trabalho. Existem vários tipos de gatilhos:

- *Os acionadores de sondagens* verificam regularmente um serviço ou sistema específico num horário especificado para verificar se há novos dados ou um evento específico. Se novos dados estiverem disponíveis, ou o evento específico acontecer, estes gatilhos criam e executam uma nova instância do seu fluxo de trabalho. Este novo caso pode então usar os dados que são passados como entrada.
- *Os gatilhos de pressão* ouvem novos dados ou para que um evento aconteça, sem sondagens. Quando novos dados estão disponíveis, ou quando o evento acontece, estes gatilhos criam e executam uma nova instância da sua aplicação lógica. Este novo caso pode então usar os dados que são passados como entrada.

Por exemplo, é melhor construir um fluxo de trabalho que faça algo quando um ficheiro é enviado para o seu servidor FTP. Pode adicionar o gatilho do conector FTP chamado **Quando um ficheiro é adicionado ou modificado**, como o primeiro passo no seu fluxo de trabalho. Em seguida, pode especificar um horário para verificar regularmente se há eventos de upload.

Um gatilho também transmite quaisquer entradas e outros dados necessários para o seu fluxo de trabalho, onde as ações posteriores podem referenciar e usar esses dados ao longo do fluxo de trabalho. Por exemplo, é melhor utilizar o gatilho do Office 365 Outlook chamado **Quando um novo e-mail chegar**. Pode configurar este gatilho para transmitir o conteúdo de cada novo e-mail, como o remetente, linha de assunto, corpo, anexos, e assim por diante. Em seguida, pode processar essa informação na sua aplicação lógica usando ações.

### <a name="actions"></a>Ações

Uma *ação* é uma operação que segue o gatilho e executa algum tipo de tarefa no seu fluxo de trabalho. Pode utilizar várias ações na sua aplicação lógica. Por exemplo, pode ter um gatilho SQL que deteta novos dados de clientes numa base de dados SQL. O seu fluxo de trabalho pode incluir uma primeira ação SQL que obtém os dados do cliente, seguida de outra ação que não é necessariamente uma ação SQL, que processa os dados.

## <a name="connector-categories"></a>Categorias de conector

Nas Aplicações Lógicas, existem geralmente versões de conector incorporadas ou geridas de gatilhos e ações. Um pequeno número de gatilhos e ações estão disponíveis em ambas as versões. As versões específicas dependem se está a criar uma app lógica multi-inquilino ou uma aplicação lógica de inquilino único, que atualmente está disponível apenas na [Preview de Apps Lógicas.](../logic-apps/logic-apps-overview-preview.md)

[Os gatilhos e ações incorporados](built-in.md) são executados de forma nativa no tempo de execução das Aplicações Lógicas, não requerem a criação de ligações e executam este tipo de tarefas:

- [Executar código nos seus fluxos de trabalho](built-in.md#run-code-from-workflows).
- [Organize e controle os seus dados.](built-in.md#control-workflow)
- [Gerir ou manipular dados.](built-in.md#manage-or-manipulate-data)

[Os conectores geridos são implantados, hospedados](managed.md) e geridos pela Microsoft. Estes conectores fornecem gatilhos e ações para serviços na nuvem, sistemas no local, ou ambos. Incluem-se:

- [Conectores no local](managed.md#on-premises-connectors) que o ajudam a aceder a dados e recursos em sistemas no local.
- [Conectores da empresa](managed.md#enterprise-connectors), que são versões de alguns conectores de Apps Lógicas que fornecem acesso a sistemas empresariais.
- [Conectores](managed.md#integration-account-connectors)de conta de integração que suportam cenários de comunicação business-to-business (B2B).
- [Conectores ambiente de serviço de integração (ISE)](managed.md#ise-connectors) que são um pequeno grupo de [conectores geridos disponíveis apenas para ISEs](#ise-and-connectors).

## <a name="connection-configuration"></a>Configuração de ligação

A maioria dos conectores requer que crie primeiro uma *ligação* ao serviço ou sistema alvo antes de poder utilizar os seus gatilhos ou ações na sua aplicação lógica. Para criar uma ligação, tem de autenticar a sua identidade com credenciais de conta e, por vezes, outras informações de ligação. Por exemplo, antes de o seu fluxo de trabalho poder aceder e trabalhar com a sua conta de e-mail do Office 365 Outlook numa aplicação lógica, tem de autorizar uma ligação a essa conta.

Para conectores que utilizem OAuth Azure Ative Directory (Azure AD), como Office 365, Salesforce ou GitHub, deve assinar no serviço onde o seu token de acesso é [encriptado](../security/fundamentals/encryption-overview.md) e armazenado de forma segura num segredo Azure. Outros conectores, como FTP e SQL, requerem uma ligação que tenha detalhes de configuração, tais como o endereço do servidor, nome de utilizador e senha. Estes detalhes de configuração de ligação também são [encriptados e armazenados de forma segura no Azure](../security/fundamentals/encryption-overview.md).

As ligações estabelecidas podem aceder ao serviço ou sistema alvo enquanto esse serviço ou sistema permitir. Para serviços que utilizam ligações Azure AD OAuth, como o Office 365 e Dynamics, as Aplicações Lógicas atualizam o acesso a tokens indefinidamente. Outros serviços podem ter limites em quanto tempo o serviço De Aplicações Lógicas pode usar um token sem ser refrescante. Geralmente, algumas ações como alterar a sua palavra-passe, invalidam todos os tokens de acesso.

Embora crie ligações a partir de um fluxo de trabalho, as ligações são recursos Azure separados com as suas próprias definições de recursos. Para rever estas definições de recursos de conexão, [baixe a sua aplicação lógica do Azure para o Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md) Este método é a maneira mais fácil de criar um modelo de aplicação lógica válida e parametrizada que está maioritariamente pronto para ser implantado.

> [!TIP]
> Se a sua organização não permitir o acesso a recursos específicos através de conectores Logic Apps, pode [bloquear a capacidade de criar tais ligações](../logic-apps/block-connections-connectors.md) utilizando a [Azure Policy.](../governance/policy/overview.md)

## <a name="recurrence-behavior"></a>Comportamento de recorrência

Os gatilhos incorporados recorrentes, como o [gatilho Recorrence,](../connectors/connectors-native-recurrence.md)funcionam de forma nativa em Azure Logic Apps e diferem de gatilhos baseados em ligação recorrentes, como o gatilho do conector Office 365 Outlook, onde é necessário criar uma ligação primeiro.

Para ambos os tipos de gatilhos, se uma recorrência não especificar uma data e hora de início específicas, a primeira recorrência corre imediatamente quando guarda ou implementa a aplicação lógica, apesar da configuração de recorrência do gatilho. Para evitar este comportamento, forneça uma data de início e hora para quando quiser que a primeira recorrência seja executada.

### <a name="recurrence-for-built-in-triggers"></a>Recorrência para gatilhos incorporados

Os gatilhos incorporados recorrentes seguem o horário que definiu, incluindo qualquer fuso horário especificado. No entanto, se uma recorrência não especificar outras opções avançadas de agendamento, tais como tempos específicos para executar recorrências futuras, essas recorrências são baseadas na última execução do gatilho. Como resultado, os tempos de início para essas recorrências podem derivar devido a fatores como a latência durante as chamadas de armazenamento. Para obter ajuda para resolver problemas, consulte a secção [de problemas de recorrência.](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>Recorrência para gatilhos baseados em ligação

Em gatilhos de ligação recorrentes, como o Office 365 Outlook, o horário não é o único condutor que controla a execução. O fuso horário apenas determina a hora de início inicial. As execuções subsequentes dependem do calendário de recorrência, da última execução do gatilho, e de outros fatores que podem causar tempos de fuga ou produzir comportamentos inesperados. Incluem-se:

- Se o gatilho acede a um servidor que tem mais dados, que o gatilho tenta imediatamente obter.
- Quaisquer falhas ou retrações que o gatilho incorre.
- Latência durante as chamadas de armazenamento.
- Não mantendo o horário especificado quando o horário de verão (DST) começar e terminar.
- Outros fatores que podem afetar quando a próxima hora de execução acontece.

Para obter ajuda para resolver problemas, consulte a secção [de problemas de recorrência.](#recurrence-issues) 

### <a name="recurrence-issues"></a>Problemas de recorrência

Para se certificar de que o seu fluxo de trabalho funciona na hora de início especificada e não perde uma recorrência, especialmente quando a frequência é em dias ou mais, experimente as seguintes soluções.

Quando o DST fizer efeito, ajuste manualmente a recorrência de modo a que o seu fluxo de trabalho continue a funcionar no momento esperado. Caso contrário, a hora de início muda uma hora para a frente quando o DST começa e uma hora para trás quando o DST termina. Para obter mais informações e exemplos, consulte [Recorrência para horário de verão e horário normal](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

Se estiver a usar um gatilho **de recorrência,** especifique um fuso horário, uma data de início e hora. Além disso, configurar horários específicos para executar recorrências subsequentes nas propriedades **Nestas horas** e **nestas atas,** que estão disponíveis apenas para as frequências **Dia** e **Semana.** No entanto, algumas janelas de tempo ainda podem causar problemas quando a hora muda. 

Considere usar um gatilho [ **da janela deslizante**](../connectors/connectors-native-sliding-window.md) em vez de um gatilho de **recorrência** para evitar recorrências perdidas.

## <a name="custom-apis-and-connectors"></a>APIs e conectores personalizados

Para chamar APIs que executam código personalizado ou não estão disponíveis como conectores, você pode estender a plataforma De Aplicações Lógicas [criando aplicações API personalizadas](../logic-apps/logic-apps-create-api-app.md). 

Também pode [criar conectores personalizados](../logic-apps/custom-connector-overview.md) para quaisquer APIs baseadas em REST ou SOAP, que disponibilizam essas APIs a qualquer aplicação lógica na sua subscrição Azure. 

Para tornar públicas as aplicações ou conectores API personalizados para qualquer pessoa utilizar no Azure, pode [submeter conectores para certificação microsoft.](/connectors/custom-connectors/submit-certification)

## <a name="ise-and-connectors"></a>ISE e conectores

Para fluxos de trabalho que necessitem de acesso direto aos recursos numa rede virtual Azure, pode criar um ambiente de serviço de integração dedicado [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) onde pode construir, implantar e executar os seus fluxos de trabalho em recursos dedicados. Para obter mais informações sobre a criação de ISEs, consulte [as redes virtuais Connect to Azure da Azure Logic Apps.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

Os conectores personalizados criados dentro de um ISE não funcionam com o portal de dados no local. No entanto, estes conectores podem aceder diretamente a fontes de dados no local que estão ligadas a uma rede virtual Azure que hospeda o ISE. Assim, as aplicações lógicas num ISE provavelmente não precisam da porta de dados quando comunicam com esses recursos. Se tiver conectores personalizados que criou fora de um ISE que requerem o gateway de dados no local, as aplicações lógicas num ISE podem usar esses conectores.

No Logic Apps Designer, quando navega nos conectores que pretende utilizar para aplicações lógicas num ISE, aparece uma etiqueta **CORE** em gatilhos e ações incorporados, enquanto a etiqueta **ISE** aparece em alguns conectores.

:::row:::
    :::column:::
        ![Conector CORE exemplo](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        Os gatilhos incorporados e as ações com esta etiqueta funcionam no mesmo ISE que as suas aplicações lógicas.
    :::column-end:::
    :::column:::
        ![Conector ISE exemplo](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        Os conectores geridos com esta etiqueta funcionam no mesmo ISE que as suas aplicações lógicas. Se tiver um sistema no local ligado a uma rede virtual Azure, um ISE permite que as suas aplicações lógicas acedam diretamente a esse sistema sem o [portal de dados no local.](../logic-apps/logic-apps-gateway-connection.md) Em vez disso, pode utilizar o conector **ISE** do sistema, se disponível, uma ação HTTP ou um [conector personalizado.](connectors-overview.md#custom-apis-and-connectors) Para sistemas no local que não tenham conectores **ISE,** utilize o portal de dados no local. Para rever os conectores ISE disponíveis, consulte [os conectores ISE](#ise-and-connectors).
    :::column-end:::
    :::column:::
        ![Exemplo de conector multi-inquilino](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Sem rótulo     \
        \
        Todos os outros conectores sem a etiqueta **CORE** ou **ISE,** que pode continuar a utilizar, funcionam no serviço global de Aplicações Lógicas multi-arrendatários.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Problemas conhecidos

Seguem-se problemas conhecidos para os conectores de Aplicações Lógicas.

#### <a name="error-badgateway-client-request-id-guid"></a>Erro: BadGateway. Id de pedido do cliente: '{GUID}'

Este erro resulta da atualização das tags numa aplicação lógica onde uma ou mais ligações não suportam a autenticação OAuth do Azure Ative Directory (Azure AD), como a SFTP ad SQL, quebrando essas ligações. Para evitar este comportamento, evite atualizar essas etiquetas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie APIs personalizados que pode chamar a partir de Aplicações Lógicas](/logic-apps/logic-apps-create-api-app)
