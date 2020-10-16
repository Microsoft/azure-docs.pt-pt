---
title: Defina um novo tipo de dispositivo IoT no Azure IoT Central Microsoft Docs
description: Este artigo mostra-lhe, como construtor, como criar um novo modelo de dispositivo Azure IoT na sua aplicação Azure IoT Central. Define a telemetria, estado, propriedades e comandos para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperfq1
- device-developer
ms.openlocfilehash: c8fb60a4b549a7203057dd60298d2ae0540450d6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122658"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definir um novo tipo de dispositivo IoT na aplicação do Azure IoT Central

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Um modelo de dispositivo é uma planta que define as características e comportamentos de um tipo de dispositivo que se conecta a uma [aplicação Azure IoT Central](concepts-app-templates.md).

Por exemplo, um construtor pode criar um modelo de dispositivo para uma ventoinha conectada que tenha as seguintes características:

- Envia telemetria de temperatura
- Envia propriedade de localização
- Envia eventos de erro motor de fãs
- Envia estado de operação de fãs
- Fornece uma propriedade de velocidade de fãs writeable
- Fornece um comando para reiniciar o dispositivo
- Dá-lhe uma visão geral do dispositivo através de um dashboard

A partir deste modelo de dispositivo, um operador pode criar e ligar dispositivos de ventilação reais. Todos estes ventiladores têm medições, propriedades e comandos que os operadores usam para monitorizá-los e geri-los. Os operadores utilizam os painéis e [formulários do dispositivo](#add-dashboards) para interagir com os dispositivos do ventilador. Um desenvolvedor de dispositivos usa o modelo para entender como o dispositivo interage com a aplicação. Para saber mais, consulte [telemetria, propriedade e cargas de comando.](concepts-telemetry-properties-commands.md)

> [!NOTE]
> Apenas construtores e administradores podem criar, editar e eliminar modelos de dispositivos. Qualquer utilizador pode criar dispositivos na página **dispositivos** a partir dos modelos de dispositivos existentes.

Numa aplicação IoT Central, um modelo de dispositivo utiliza um modelo de capacidade do dispositivo para descrever as capacidades de um dispositivo. Como construtor, tem várias opções para criar modelos de dispositivos:

- Desenhe o modelo do dispositivo na IoT Central e, em seguida, [implemente o seu modelo de capacidade de dispositivo no código do dispositivo](concepts-telemetry-properties-commands.md).
- Importe um modelo de capacidade do dispositivo do catálogo de [dispositivos Azure Certified for IoT](https://aka.ms/iotdevcat). Em seguida, adicione quaisquer propriedades em nuvem, personalizações e dashboards que a sua aplicação IoT Central precisa.
- Crie um modelo de capacidade do dispositivo utilizando o Código de Estúdio Visual. Implemente o código do dispositivo a partir do modelo. Importe manualmente o modelo de capacidade do dispositivo na sua aplicação IoT Central e, em seguida, adicione quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita.
- Crie um modelo de capacidade do dispositivo utilizando o Código de Estúdio Visual. Implemente o código do dispositivo a partir do modelo e ligue o seu verdadeiro dispositivo à sua aplicação IoT Central utilizando uma ligação de primeiro dispositivo. A IoT Central encontra e importa o modelo de capacidade do dispositivo do repositório público para si. Em seguida, pode adicionar quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita ao modelo do dispositivo.

Também pode adicionar modelos de dispositivo a uma aplicação IoT Central utilizando a [API REST](/learn/modules/manage-iot-central-apps-with-rest-api/) ou o [CLI](howto-manage-iot-central-from-cli.md).

Alguns [modelos de aplicação](concepts-app-templates.md) já incluem modelos de dispositivo que são úteis no cenário que o modelo de aplicação suporta. Por exemplo, consulte [a arquitetura de análise na loja.](../retail/store-analytics-architecture.md)

## <a name="create-a-device-template-from-the-device-catalog"></a>Crie um modelo de dispositivo a partir do catálogo do dispositivo

Como construtor, pode começar rapidamente a construir a sua solução utilizando um dispositivo certificado IoT Plug and Play (pré-visualização). Consulte a lista no Catálogo de [Dispositivos Azure IoT](https://catalog.azureiotsolutions.com/alldevices). O IoT Central integra-se com o catálogo de dispositivos para que possa importar um modelo de capacidade do dispositivo a partir de qualquer um destes dispositivos certificados IoT Plug e Play (pré-visualização). Para criar um modelo de dispositivo a partir de um destes dispositivos na IoT Central:

1. Aceda à página **de Modelos de Dispositivo** na sua aplicação IoT Central.
1. Selecione **+ Novos**, e, em seguida, selecione qualquer um dos dispositivos certificados IoT Plug e Play (pré-visualização) do catálogo. O IoT Central cria um modelo de dispositivo baseado neste modelo de capacidade do dispositivo.
1. Adicione quaisquer propriedades na nuvem, personalizações ou vistas para o seu modelo de dispositivo.
1. **Selecione Publicar** para disponibilizar o modelo para os operadores visualizarem e ligarem dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Crie um modelo de dispositivo de raiz

Um modelo de dispositivo contém:

- Um _modelo de capacidade do dispositivo_ que especifica a telemetria, propriedades e comandos que o dispositivo implementa. Estas capacidades são organizadas em uma ou mais interfaces.
- _Propriedades em nuvem_ que definem a informação que a sua aplicação IoT Central armazena sobre os seus dispositivos. Por exemplo, uma propriedade em nuvem pode gravar a data em que um dispositivo foi reparado pela última vez. Esta informação nunca é partilhada com o dispositivo.
- _As personalizações_ permitem ao construtor sobrepor-se a algumas das definições no modelo de capacidade do dispositivo. Por exemplo, o construtor pode sobrepor-se ao nome de uma propriedade do dispositivo. Os nomes das propriedades aparecem nos dashboards e formulários da IoT Central.
- _Os dashboards e formulários_ permitem ao construtor criar uma UI que permite aos operadores monitorizar e gerir os dispositivos ligados à sua aplicação.

Para criar um modelo de dispositivo na IoT Central:

1. Aceda à página **de Modelos de Dispositivo** na sua aplicação IoT Central.
1. Selecione **+ Novo**  >  **Costume**.
1. Insira um nome para o seu modelo, como **Sensor Ambiental.**
1.  Prima **Enter**. IoT Central cria um modelo de dispositivo vazio.

## <a name="manage-a-device-template"></a>Gerir um modelo de dispositivo

Pode renomear ou eliminar um modelo da página inicial do modelo.

Depois de adicionar um modelo de capacidade do dispositivo ao seu modelo, pode publicá-lo. Até publicar o modelo, não pode ligar um dispositivo com base neste modelo para que os seus operadores possam ver na página **dispositivos.**

## <a name="create-a-capability-model"></a>Criar um modelo de capacidade

Para criar um modelo de capacidade do dispositivo, pode:

- Utilize o IoT Central para criar um modelo personalizado de raiz.
- Importe um modelo de um ficheiro JSON. Um construtor de dispositivos pode ter usado o Código do Estúdio Visual para autoria de um modelo de capacidade do dispositivo para a sua aplicação.
- Selecione um dos dispositivos do Catálogo de Dispositivos. Esta opção importa o modelo de capacidade do dispositivo que o fabricante publicou para este dispositivo. Um modelo de capacidade do dispositivo importado como este é publicado automaticamente.

## <a name="manage-a-capability-model"></a>Gerir um modelo de capacidade

Depois de criar um modelo de capacidade do dispositivo, pode:

- Adicione interfaces ao modelo. Um modelo deve ter pelo menos uma interface.
- Editar metadados de modelo, como o seu ID, espaço de nome e nome.
- Apague o modelo.

## <a name="create-an-interface"></a>Criar uma interface

A capacidade do dispositivo deve ter pelo menos uma interface. Uma interface é uma coleção reutilizável de capacidades.

Para criar uma interface:

1. Vá ao seu modelo de capacidade do dispositivo e escolha **+ Adicionar Interface**.

1. Na página **'Selecionar uma Interface',** pode:

    - Crie uma interface personalizada a partir do zero.
    - Importe uma interface existente a partir de um ficheiro. Um construtor de dispositivos pode ter usado o Código do Estúdio Visual para autoria de uma interface para o seu dispositivo.
    - Escolha uma das interfaces padrão, como a interface informação do **dispositivo.** As interfaces padrão especificam as capacidades comuns a muitos dispositivos. Estas interfaces padrão são publicadas pela Azure IoT e não podem ser editadas ou editadas.

1. Depois de criar uma interface, escolha **Editar Identidade** para alterar o nome de exibição da interface.

1. Se optar por criar uma interface personalizada de raiz, pode adicionar as capacidades do seu dispositivo. As capacidades do dispositivo são telemetria, propriedades e comandos.

### <a name="telemetry"></a>Telemetria

A telemetria é um fluxo de valores enviados do dispositivo, tipicamente a partir de um sensor. Por exemplo, um sensor pode reportar a temperatura ambiente.

A tabela a seguir mostra as definições de configuração para uma capacidade de telemetria:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome do visor para o valor da telemetria utilizado nos painéis e formulários. |
| Nome | O nome do campo na mensagem de telemetria. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico. |
| Tipo de Capacidade | A telemetria. |
| Tipo semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis. |
| Esquema | O tipo de dados de telemetria, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado. |
| Gravidade | Disponível apenas para o tipo semântico do evento. As severidades são **Erro,** **Informação**ou **Aviso**. |
| Valores do Estado | Disponível apenas para o tipo semântico do estado. Defina os valores de estado possíveis, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor da telemetria, como **mph** **%** , ou ** &deg; C**. |
| Unidade de Exibição | Uma unidade de visualização para utilização em painéis e formulários. |
| Comentário | Qualquer comentário sobre a capacidade de telemetria. |
| Descrição | Uma descrição da capacidade de telemetria. |

### <a name="properties"></a>Propriedades

As propriedades representam valores pontuais. Por exemplo, um dispositivo pode usar uma propriedade para reportar a temperatura-alvo que está a tentar alcançar. Pode definir propriedades escritas a partir da IoT Central.

A tabela a seguir mostra as definições de configuração para uma capacidade de propriedade:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição para o valor da propriedade usado em dashboards e formulários. |
| Nome | O nome da propriedade. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico. |
| Tipo de Capacidade | Propriedade. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis. |
| Esquema | O tipo de dados da propriedade, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado. |
| Gravável | Se a propriedade não for escrita, o dispositivo pode reportar valores de propriedade à IoT Central. Se a propriedade for escrita, o dispositivo pode reportar valores de propriedade à IoT Central e ioT Central pode enviar atualizações de propriedade para o dispositivo.
| Gravidade | Disponível apenas para o tipo semântico do evento. As severidades são **Erro,** **Informação**ou **Aviso**. |
| Valores do Estado | Disponível apenas para o tipo semântico do estado. Defina os valores de estado possíveis, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor da propriedade, como **mph,** **%** ou ** &deg; C**. |
| Unidade de Exibição | Uma unidade de visualização para utilização em painéis e formulários. |
| Comentário | Quaisquer comentários sobre a capacidade da propriedade. |
| Descrição | Uma descrição da capacidade da propriedade. |

### <a name="commands"></a>Comandos

Pode ligar para os comandos do dispositivo da IoT Central. Os comandos passam opcionalmente parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, pode chamar um comando para reiniciar um dispositivo em 10 segundos.

A tabela a seguir mostra as definições de configuração para uma capacidade de comando:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de visualização do comando utilizado nos painéis e formulários. |
| Nome | O nome do comando. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico. |
| Tipo de Capacidade | O comando. |
| Comando | `SynchronousExecutionType`. |
| Comentário | Qualquer comentário sobre a capacidade de comando. |
| Descrição | Uma descrição da capacidade de comando. |
| Pedir | Se ativado, uma definição do parâmetro de pedido, incluindo: nome, nome de exibição, esquema, unidade e unidade de visualização. |
| Resposta | Se ativado, uma definição da resposta do comando, incluindo: nome, nome do visor, esquema, unidade e unidade de visualização. |

#### <a name="offline-commands"></a>Comandos offline

Pode escolher comandos de fila se um dispositivo estiver atualmente offline, ativando a opção **"Fila" se** for por opção offline para um comando no modelo do dispositivo.

Esta opção utiliza mensagens nuvem-dispositivo IoT Hub para enviar notificações para dispositivos. Para saber mais, consulte o artigo IoT Hub Enviar por email o artigo IoT Hub [Enviar por email](../../iot-hub/iot-hub-devguide-messages-c2d.md)o artigo IoT Hub.

Mensagens nuvem-para-dispositivo:

- São notificações unidirecionais para o dispositivo a partir da sua solução.
- Garantia entrega de mensagem pelo menos uma vez. O IoT Hub persiste em mensagens nuvem-dispositivo em filas por dispositivo, garantindo resiliência contra a conectividade e falhas no dispositivo.
- Exija que o dispositivo implemente um manipulador de mensagens para processar a mensagem nuvem-dispositivo.

> [!NOTE]
> Esta opção só está disponível na UI web IoT Central. Esta definição não está incluída se exportar um modelo ou interface a partir do modelo do dispositivo.

## <a name="manage-an-interface"></a>Gerir uma interface

Se ainda não publicou a interface, pode editar as capacidades definidas pela interface. Depois de publicar a interface, se pretender fazer alguma alteração, terá de criar uma nova versão do modelo do dispositivo e versão da interface. Pode esporar alterações que não exijam a versão, como nomes de exibição ou unidades, na secção **Personalizar.**

Também pode exportar a interface como um ficheiro JSON se quiser reutilizá-la noutro modelo de capacidade.

## <a name="add-cloud-properties"></a>Adicionar propriedades da cloud

Utilize propriedades em nuvem para armazenar informações sobre dispositivos no IoT Central. As propriedades da nuvem nunca são enviadas para um dispositivo. Por exemplo, pode utilizar propriedades na nuvem para armazenar o nome do cliente que instalou o dispositivo, ou a última data de serviço do dispositivo.

A tabela a seguir mostra as definições de configuração de uma propriedade na nuvem:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição para o valor da propriedade na nuvem usado em dashboards e formulários. |
| Nome | O nome da propriedade na nuvem. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis. |
| Esquema | O tipo de dados de propriedade em nuvem, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. |

## <a name="add-customizations"></a>Adicionar personalizações

Utilize personalizações quando precisar de modificar uma interface importada ou adicionar funcionalidades específicas do IoT Central a uma capacidade. Só é possível personalizar campos que não quebrem a compatibilidade da interface. Pode, por exemplo:

- Personalize o nome do visor e as unidades de capacidade.
- Adicione uma cor padrão para usar quando o valor aparecer num gráfico.
- Especifique valores iniciais, mínimos e máximos para um imóvel.

Não é possível personalizar o nome de capacidade ou o tipo de capacidade. Se houver alterações que não possa fazer na secção **Personalizar,** terá de ver o modelo e a interface do dispositivo para modificar a capacidade.

### <a name="generate-default-views"></a>Gerar vistas padrão

Gerar vistas padrão é uma forma rápida de visualizar as informações importantes do dispositivo. Tem até três vistas padrão geradas para o seu modelo de dispositivo:

- **Os comandos** fornecem uma visão com comandos do dispositivo e permitem que o seu operador os despacha para o seu dispositivo.
- **A visão geral** proporciona uma vista com telemetria do dispositivo, gráficos de exibição e métricas.
- **Sobre** fornece uma vista com informações do dispositivo, exibindo propriedades do dispositivo.

Depois de ter selecionado **As vistas predefinidas**, verá que foram adicionadas automaticamente na secção **Visualizações** do modelo do seu dispositivo.

## <a name="add-dashboards"></a>Adicionar dashboards

Adicione dashboards a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo utilizando gráficos e métricas. Pode ter vários dashboards para um modelo de dispositivo.

Para adicionar um dashboard a um modelo de dispositivo:

1. Vá ao seu modelo de dispositivo e selecione **Vistas**.
1. Escolha **visualizar o dispositivo.**
1. Introduza um nome para o seu painel de **instrumentos**no Nome dashboard .
1. Adicione azulejos ao seu painel a partir da lista de estática, propriedade, propriedade em nuvem, telemetria e telhas de comando. Arraste e largue os azulejos que pretende adicionar ao seu painel de instrumentos.
1. Para traçar vários valores de telemetria num único bloco gráfico, selecione os valores da telemetria e, em seguida, selecione **Combine**.
1. Configure cada telha que adiciona para personalizar a forma como exibe dados. Pode fazê-lo selecionando o ícone de engrenagem ou selecionando a **configuração Change** no seu azulejo de gráfico.
1. Arrume e redimensione os azulejos do seu painel de instrumentos.
1. Guarde as alterações.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurar dispositivo de pré-visualização para visualizar o dashboard

Para visualizar e testar o seu painel de instrumentos, selecione **o dispositivo de pré-visualização Configure**. Isto permite-lhe ver o painel de instrumentos como o seu operador o vê depois de publicado. Utilize esta opção para validar que as suas opiniões mostram os dados corretos. Pode selecionar de entre o seguinte:

- Sem dispositivo de pré-visualização.
- O verdadeiro dispositivo de teste que configuraste para o modelo do dispositivo.
- Um dispositivo existente na sua aplicação, utilizando o ID do dispositivo.

## <a name="add-forms"></a>Adicionar formulários

Adicione formulários a um modelo de dispositivo para permitir que os operadores gerem um dispositivo visualizando e definindo propriedades. Os operadores só podem editar propriedades em nuvem e propriedades de dispositivos writeable. Pode ter várias formas para um modelo de dispositivo.

Para adicionar um formulário a um modelo de dispositivo:

1. Vá ao seu modelo de dispositivo e selecione **Vistas**.
1. Escolha os **dados do Dispositivo de Edição e da Nuvem.**
1. Insira um nome para o seu formulário no **Nome do Formulário**.
1. Selecione o número de colunas a utilizar para definir o seu formulário.
1. Adicione propriedades a uma secção existente no seu formulário, ou selecione propriedades e escolha **Seção Adicionar**. Utilize secções para agrupar propriedades no seu formulário. Pode adicionar um título a uma secção.
1. Configure cada propriedade no formulário para personalizar o seu comportamento.
1. Organize as propriedades no seu formulário.
1. Guarde as alterações.

## <a name="publish-a-device-template"></a>Publicar um modelo de dispositivo

Antes de poder ligar um dispositivo que implemente o seu modelo de capacidade do dispositivo, tem de publicar o modelo do dispositivo.

Depois de publicar um modelo de dispositivo, só pode fazer alterações limitadas no modelo de capacidade do dispositivo. Para modificar uma interface, é necessário [criar e publicar uma nova versão](./howto-version-device-template.md).

Para publicar um modelo de dispositivo, vá até si o modelo do seu dispositivo e selecione **Publicar**.

Depois de publicar um modelo de dispositivo, um operador pode ir à página dispositivos e adicionar **dispositivos** reais ou simulados que usam o modelo do seu dispositivo. Pode continuar a modificar e guardar o modelo do dispositivo à medida que está a fazer alterações. Quando pretender empurrar estas alterações para o operador para visualizar na página **Dispositivos,** tem de selecionar **Publicar** sempre.

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, um próximo passo sugerido é ler sobre a [versão do modelo do dispositivo](./howto-version-device-template.md).