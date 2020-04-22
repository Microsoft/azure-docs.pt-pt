---
title: Defina um novo tipo de dispositivo IoT no Azure IoT Central [ Central] Microsoft Docs
description: Este artigo mostra-lhe, como construtor, como criar um novo modelo de dispositivo Azure IoT na sua aplicação Azure IoT Central. Define a telemetria, estado, propriedades e comandos para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 2a99f261e1a834705d081e8197e4ae627cf1cb9f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756659"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Defina um novo tipo de dispositivo IoT na sua aplicação Azure IoT Central

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Um modelo de dispositivo é uma planta que define as características e comportamentos de um tipo de dispositivo que se conecta a uma aplicação Central Azure IoT.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado que tem as seguintes características:

- Envia telemetria de temperatura
- Envia propriedade de localização
- Envia eventos de erro do motor do ventilador
- Envia estado de exploração de fãs
- Fornece uma propriedade de velocidade de ventilador reempreensível
- Fornece um comando para reiniciar o dispositivo
- Dá-lhe uma visão geral do dispositivo através de um dashboard

A partir deste modelo de dispositivo, um operador pode criar e ligar dispositivos de ventilação reais. Todos estes ventiladores têm medições, propriedades e comandos que os operadores usam para monitorizá-los e geri-los. Os operadores utilizam os dashboards e formulários do dispositivo para interagir com os dispositivos de ventilação.

> [!NOTE]
> Apenas construtores e administradores podem criar, editar e eliminar modelos de dispositivos. Qualquer utilizador pode criar dispositivos na página **dispositivos** a partir de modelos de dispositivos existentes.

[IoT Plug and Play (pré-visualização)](../../iot-pnp/overview-iot-plug-and-play.md) permite que a IoT Central integre os dispositivos, sem que escreva qualquer código de dispositivo incorporado. No centro do IoT Plug and Play (pré-visualização) está um esquema de modelo de capacidade de dispositivo que descreve as capacidades do dispositivo. Numa aplicação IoT Central, os modelos do dispositivo utilizam estes modelos de capacidade de dispositivo IoT Plug e Play (pré-visualização).

Como construtor, tem várias opções para criar modelos de dispositivos:

- Desenhe o modelo do dispositivo na IoT Central e, em seguida, implemente o seu modelo de capacidade de dispositivo no código do seu dispositivo.
- Importar um modelo de capacidade de dispositivo do Catálogo de [dispositivos Azure Certified para ioT](https://aka.ms/iotdevcat). Em seguida, adicione quaisquer propriedades na nuvem, personalizações e dashboards as suas necessidades de aplicação IoT Central.
- Crie um modelo de capacidade de dispositivo utilizando o Código do Estúdio Visual. Implemente o código do seu dispositivo a partir do modelo. Importe manualmente o modelo de capacidade do dispositivo para a sua aplicação IoT Central e, em seguida, adicione quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita.
- Crie um modelo de capacidade de dispositivo utilizando o Código do Estúdio Visual. Implemente o código do seu dispositivo a partir do modelo e ligue o seu dispositivo real à sua aplicação IoT Central utilizando uma ligação de primeira dispositivo. A IoT Central encontra e importa o modelo de capacidade do dispositivo do repositório público para si. Em seguida, pode adicionar quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central precisa para o modelo do dispositivo.

## <a name="create-a-device-template-from-the-device-catalog"></a>Criar um modelo de dispositivo a partir do catálogo do dispositivo

Como construtor, pode começar rapidamente a construir a sua solução utilizando um dispositivo certificado IoT Plug and Play (pré-visualização). Consulte a lista no Catálogo de [Dispositivos Azure IoT](https://catalog.azureiotsolutions.com/alldevices). A IoT Central integra-se com o catálogo do dispositivo para que possa importar um modelo de capacidade de dispositivo a partir de qualquer um destes dispositivos certificados ioT Plug and Play (pré-visualização). Para criar um modelo de dispositivo a partir de um destes dispositivos na IoT Central:

1. Aceda à página **de Modelos** de Dispositivos na sua aplicação IoT Central.
1. Selecione **+ Novo**, e, em seguida, selecione qualquer um dos dispositivos certificados IoT Plug e Play (pré-visualização) do catálogo. A IoT Central cria um modelo de dispositivo baseado neste modelo de capacidade do dispositivo.
1. Adicione quaisquer propriedades na nuvem, personalizações ou vistas para o seu modelo de dispositivo.
1. **Selecione Publicar** para disponibilizar o modelo para os operadores verem e ligarem os dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Criar um modelo de dispositivo do zero

Um modelo de dispositivo contém:

- Um modelo de capacidade do _dispositivo_ que especifica a telemetria, propriedades e comandos que o dispositivo implementa. Estas capacidades são organizadas em uma ou mais interfaces.
- _Propriedades na nuvem_ que definem informações que a sua aplicação IoT Central armazena sobre os seus dispositivos. Por exemplo, uma propriedade na nuvem pode gravar a data em que um dispositivo foi servido pela última vez. Esta informação nunca é partilhada com o dispositivo.
- _As personalizações_ permitem ao construtor anular algumas das definições no modelo de capacidade do dispositivo. Por exemplo, o construtor pode anular o nome de uma propriedade de dispositivo. Os nomes de propriedade aparecem nos dashboards e formulários da Central IoT.
- _Os dashboards e formulários_ permitem ao construtor criar um UI que permite aos operadores monitorizar e gerir os dispositivos ligados à sua aplicação.

Para criar um modelo de dispositivo em IoT Central:

1. Aceda à página **de Modelos** de Dispositivos na sua aplicação IoT Central.
1. Selecione **+ Novo** > **Costume**.
1. Introduza um nome para o seu modelo, como **Sensor Ambiental**.
1. Pressione **Entrar**. IoT Central cria um modelo de dispositivo vazio.

## <a name="manage-a-device-template"></a>Gerir um modelo de dispositivo

Pode mudar o nome ou apagar um modelo da página inicial do modelo.

Depois de adicionar um modelo de capacidade do dispositivo ao seu modelo, pode publicá-lo. Até publicar o modelo, não pode ligar um dispositivo com base neste modelo para os seus operadores verem na página **dispositivos.**

## <a name="create-a-capability-model"></a>Criar um modelo de capacidade

Para criar um modelo de capacidade do dispositivo, pode:

- Utilize a IoT Central para criar um modelo personalizado de raiz.
- Importar um modelo de um ficheiro JSON. Um construtor de dispositivos pode ter usado o Visual Studio Code para autoria de um modelo de capacidade de dispositivo para a sua aplicação.
- Selecione um dos dispositivos do Catálogo de Dispositivos. Esta opção importa o modelo de capacidade do dispositivo que o fabricante publicou para este dispositivo. Um modelo de capacidade de dispositivo importado como este é automaticamente publicado.

## <a name="manage-a-capability-model"></a>Gerir um modelo de capacidade

Depois de criar um modelo de capacidade de dispositivo, pode:

- Adicione interfaces ao modelo. Um modelo deve ter pelo menos uma interface.
- Editar metadados de modelos, tais como id, espaço de nome e nome.
- Apague o modelo.

## <a name="create-an-interface"></a>Criar uma interface

A capacidade do dispositivo deve ter pelo menos uma interface. Uma interface é uma coleção reutilizável de capacidades.

Para criar uma interface:

1. Vá ao seu modelo de capacidade do dispositivo e escolha **+ Adicionar Interface**.

1. Na página **'Selecionar uma Interface',** pode:

    - Crie uma interface personalizada do zero.
    - Importar uma interface existente a partir de um ficheiro. Um construtor de dispositivos pode ter usado o Visual Studio Code para autoria de uma interface para o seu dispositivo.
    - Escolha uma das interfaces padrão, como a interface de Informação do **Dispositivo.** As interfaces padrão especificam as capacidades comuns a muitos dispositivos. Estas interfaces padrão são publicadas pelo Azure IoT, e não podem ser versonizadas ou editadas.

1. Depois de criar uma interface, escolha **Editar Identidade** para alterar o nome de exibição da interface.

1. Se optar por criar uma interface personalizada a partir do zero, pode adicionar as capacidades do seu dispositivo. As capacidades do dispositivo são telemetria, propriedades e comandos.

### <a name="telemetry"></a>Telemetria

A telemetria é um fluxo de valores enviados do dispositivo, tipicamente a partir de um sensor. Por exemplo, um sensor pode reportar a temperatura ambiente.

A tabela que se segue mostra as definições de configuração para uma capacidade de telemetria:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome do visor para o valor da telemetria utilizado nos tabliers e formulários. |
| Nome | O nome do campo na mensagem de telemetria. A IoT Central gera um valor para este campo a partir do nome de exibição, mas pode escolher o seu próprio valor se necessário. |
| Tipo de Capacidade | A telemetria. |
| Tipo semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina qual dos seguintes campos está disponível. |
| Esquema | O tipo de dados de telemetria, como duplo, corda ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado. |
| Gravidade | Disponível apenas para o tipo semântico do evento. As severidades são **Error,** **Information**ou **Warning**. |
| Valores do Estado | Disponível apenas para o tipo semântico do estado. Defina os possíveis valores de estado, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor da telemetria, como **mph,** **%** ou ** &deg;C**. |
| Unidade de exibição | Uma unidade de visualização para utilização em tabliers e formulários. |
| Comentário | Qualquer comentário sobre a capacidade de telemetria. |
| Descrição | Uma descrição da capacidade de telemetria. |

### <a name="properties"></a>Propriedades

As propriedades representam valores ponto-a-tempo. Por exemplo, um dispositivo pode usar uma propriedade para reportar a temperatura-alvo que está a tentar alcançar. Você pode definir propriedades reempreensíveis a partir da IoT Central.

A tabela que se segue mostra as definições de configuração para uma capacidade de propriedade:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição para o valor da propriedade utilizado em tabliers e formulários. |
| Nome | O nome da propriedade. A IoT Central gera um valor para este campo a partir do nome de exibição, mas pode escolher o seu próprio valor se necessário. |
| Tipo de Capacidade | A propriedade. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina qual dos seguintes campos está disponível. |
| Esquema | O tipo de dados da propriedade, como duplo, corda ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado. |
| Gravável | Se a propriedade não for reempreciável, o dispositivo pode reportar valores de propriedade à IoT Central. Se a propriedade for reempreensível, o dispositivo pode reportar valores de propriedade à IoT Central e ioT Central pode enviar atualizações de propriedade para o dispositivo.
| Gravidade | Disponível apenas para o tipo semântico do evento. As severidades são **Error,** **Information**ou **Warning**. |
| Valores do Estado | Disponível apenas para o tipo semântico do estado. Defina os possíveis valores de estado, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor da **%** propriedade, como **mph,** ou ** &deg;C**. |
| Unidade de exibição | Uma unidade de visualização para utilização em tabliers e formulários. |
| Comentário | Qualquer comentário sobre a capacidade da propriedade. |
| Descrição | Uma descrição da capacidade da propriedade. |

### <a name="commands"></a>Comandos

Pode ligar para os comandos do dispositivo da IoT Central. Os comandos passam opcionalmente parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, pode ligar para um comando para reiniciar um dispositivo em 10 segundos.

A tabela que se segue mostra as definições de configuração para uma capacidade de comando:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição do comando utilizado nos tabliers e formulários. |
| Nome | O nome do comando. A IoT Central gera um valor para este campo a partir do nome de exibição, mas pode escolher o seu próprio valor se necessário. |
| Tipo de Capacidade | O comando. |
| Comando | `SynchronousExecutionType`. |
| Comentário | Qualquer comentário sobre a capacidade de comando. |
| Descrição | Uma descrição da capacidade de comando. |
| Pedir | Se ativado, uma definição do parâmetro de pedido, incluindo: nome, nome de exibição, esquema, unidade e unidade de exibição. |
| Resposta | Se ativado, uma definição da resposta do comando, incluindo: nome, nome de exibição, esquema, unidade e unidade de exibição. |

## <a name="manage-an-interface"></a>Gerir uma interface

Se ainda não publicou a interface, pode editar as capacidades definidas pela interface. Depois de publicar a interface, se quiser fazer alterações, terá de criar uma nova versão do modelo do dispositivo e vera a interface. Pode fazer alterações que não requeiram versões, como nomes de exibição ou unidades, na secção **Personalizar.**

Também pode exportar a interface como um ficheiro JSON se quiser reutilizá-la noutro modelo de capacidade.

## <a name="add-cloud-properties"></a>Adicione propriedades da nuvem

Utilize propriedades na nuvem para armazenar informações sobre dispositivos na IoT Central. As propriedades da nuvem nunca são enviadas para um dispositivo. Por exemplo, pode utilizar propriedades na nuvem para armazenar o nome do cliente que instalou o dispositivo, ou a última data de serviço do dispositivo.

A tabela seguinte mostra as definições de configuração para uma propriedade em nuvem:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição para o valor da propriedade em nuvem utilizado em tabliers e formulários. |
| Nome | O nome da propriedade da nuvem. A IoT Central gera um valor para este campo a partir do nome de exibição, mas pode escolher o seu próprio valor se necessário. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina qual dos seguintes campos está disponível. |
| Esquema | O tipo de dados da propriedade em nuvem, como duplo, corda ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. |

## <a name="add-customizations"></a>Adicionar personalizações

Utilize personalizações quando necessitar de modificar uma interface importada ou adicionar funcionalidades específicas do IoT a uma capacidade. Só pode personalizar campos que não quebrem a compatibilidade da interface. Pode, por exemplo:

- Personalize o nome do visor e as unidades de uma capacidade.
- Adicione uma cor predefinida para usar quando o valor aparece num gráfico.
- Especificar valores iniciais, mínimos e máximos para um imóvel.

Não é possível personalizar o nome ou o tipo de capacidade. Se houver alterações que não pode fazer na secção **Personalizar,** terá de ver o modelo e interface do dispositivo para modificar a capacidade.

### <a name="generate-default-views"></a>Gerar vistas padrão

Gerar vistas padrão é uma forma rápida de visualizar as informações importantes do dispositivo. Tem até três visualizações predefinidas geradas para o seu modelo de dispositivo:

- **Os comandos** fornecem uma visão com comandos do dispositivo e permitem ao seu operador despachado para o seu dispositivo.
- **A visão geral** proporciona uma visão com telemetria do dispositivo, exibindo gráficos e métricas.
- **Sobre** fornece uma visão com informações do dispositivo, exibindo propriedades do dispositivo.

Depois de ter selecionado **As vistas por defeito,** verá que foram adicionadas automaticamente sob a secção **Views** do seu modelo de dispositivo.

## <a name="add-dashboards"></a>Adicionar dashboards

Adicione dashboards a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo utilizando gráficos e métricas. Pode ter vários dashboards para um modelo de dispositivo.

Para adicionar um dashboard a um modelo de dispositivo:

1. Vá ao modelo do seu dispositivo e selecione **Views**.
1. Escolha **visualizar o dispositivo**.
1. Introduza um nome para o seu dashboard no Nome do Painel de **Instrumentos**.
1. Adicione azulejos ao seu painel de instrumentos a partir da lista de azulejos estáticos, propriedade, nuvem, telemetria e azulejos de comando. Arraste e largue os azulejos que pretende adicionar ao seu painel de instrumentos.
1. Para traçar vários valores de telemetria num único azulejo, selecione os valores de telemetria e, em seguida, selecione **Combine**.
1. Configure cada azulejo que adiciona para personalizar a forma como exibe dados. Pode fazê-lo selecionando o ícone da engrenagem ou selecionando a **configuração De Alterar** no seu azulejo de gráfico.
1. Organize e redimensione os azulejos no seu painel de instrumentos.
1. Guarde as alterações.

### <a name="configure-preview-device-to-view-dashboard"></a>Configure o dispositivo de pré-visualização para ver o painel de instrumentos

Para visualizar e testar o seu painel de instrumentos, selecione configurar o dispositivo de **pré-visualização**. Isto permite-lhe ver o painel de instrumentos como o seu operador o vê depois de publicado. Utilize esta opção para validar que as suas opiniões mostram os dados corretos. Pode selecionar de entre o seguinte:

- Sem dispositivo de pré-visualização.
- O verdadeiro dispositivo de teste que configurapara o modelo do seu dispositivo.
- Um dispositivo existente na sua aplicação, utilizando o ID do dispositivo.

## <a name="add-forms"></a>Adicionar formulários

Adicione formulários a um modelo de dispositivo para permitir aos operadores gerir um dispositivo visualizando e definindo propriedades. Os operadores só podem editar propriedades em nuvem e propriedades de dispositivos reempreensíveis. Pode ter vários formulários para um modelo de dispositivo.

Para adicionar um formulário a um modelo de dispositivo:

1. Vá ao modelo do seu dispositivo e selecione **Views**.
1. Escolha **dispositivo de edição e dados cloud**.
1. Insira um nome para o seu formulário no **Nome do Formulário**.
1. Selecione o número de colunas a utilizar para definir o seu formulário.
1. Adicione propriedades a uma secção existente no seu formulário, ou selecione propriedades e escolha **Secção adicionar**. Utilize secções para agrupar propriedades no seu formulário. Pode adicionar um título a uma secção.
1. Configure cada propriedade no formulário para personalizar o seu comportamento.
1. Organize as propriedades no seu formulário.
1. Guarde as alterações.

## <a name="publish-a-device-template"></a>Publicar um modelo de dispositivo

Antes de poder ligar um dispositivo que implemente o seu modelo de capacidade do dispositivo, tem de publicar o modelo do seu dispositivo.

Depois de publicar um modelo de dispositivo, só pode fazer alterações limitadas no modelo de capacidade do dispositivo. Para modificar uma interface, é necessário [criar e publicar uma nova versão.](./howto-version-device-template.md)

Para publicar um modelo de dispositivo, vá até si o modelo do seu dispositivo e **selecione Publicar**.

Depois de publicar um modelo de dispositivo, um operador pode ir à página **de Dispositivos** e adicionar dispositivos reais ou simulados que usam o modelo do dispositivo. Pode continuar a modificar e a guardar o modelo do seu dispositivo à medida que está a fazer alterações. Quando pretender empurrar estas alterações para o operador para ver na página **dos Dispositivos,** deve selecionar **Publicar** sempre.

## <a name="next-steps"></a>Passos seguintes

Se é um desenvolvedor de dispositivos, um próximo passo sugerido é ler sobre [a versão do modelo](./howto-version-device-template.md)do dispositivo .
