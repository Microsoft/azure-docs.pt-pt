---
title: Defina um novo tipo de dispositivo IoT em Azure IoT Central | Microsoft Docs
description: Este artigo mostra-lhe, como construtor de soluções, como criar um novo modelo de dispositivo Azure IoT na sua aplicação Azure IoT Central. Define a telemetria, estado, propriedades e comandos para o seu tipo.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 236acc2ded3fcb651295e0342ab4e1e88174be46
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202968"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definir um novo tipo de dispositivo IoT na aplicação do Azure IoT Central

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Um modelo de dispositivo é uma planta que define as características e comportamentos de um tipo de dispositivo que se conecta a uma [aplicação Azure IoT Central](concepts-app-templates.md).

Por exemplo, um construtor pode criar um modelo de dispositivo para uma ventoinha conectada que tenha as seguintes características:

- Envia telemetria de temperatura
- Envia propriedade de localização
- Envia eventos de erro motor de fãs
- Envia estado de operação de fãs
- Fornece uma propriedade de velocidade de fã writable
- Fornece um comando para reiniciar o dispositivo
- Dá-lhe uma visão geral do dispositivo através de um dashboard

A partir deste modelo de dispositivo, um operador pode criar e ligar dispositivos de ventilação reais. Todos estes ventiladores têm medições, propriedades e comandos que os operadores usam para monitorizá-los e geri-los. Os operadores utilizam os painéis e [formulários do dispositivo](#add-dashboards) para interagir com os dispositivos do ventilador. Um desenvolvedor de dispositivos usa o modelo para entender como o dispositivo interage com a aplicação. Para saber mais, consulte [telemetria, propriedade e cargas de comando.](concepts-telemetry-properties-commands.md)

> [!NOTE]
> Apenas construtores e administradores podem criar, editar e eliminar modelos de dispositivos. Qualquer utilizador pode criar dispositivos na página **dispositivos** a partir dos modelos de dispositivos existentes.

Numa aplicação IoT Central, um modelo de dispositivo utiliza um modelo de dispositivo para descrever as capacidades de um dispositivo. Como construtor, tem várias opções para criar modelos de dispositivos:

- Desenhe o modelo do dispositivo na IoT Central e, em seguida, [implemente o seu modelo de dispositivo no código do dispositivo](concepts-telemetry-properties-commands.md).
- Importe um modelo de dispositivo do catálogo de [dispositivos Azure Certified for IoT](https://aka.ms/iotdevcat). Personalize o modelo do dispositivo aos seus requisitos na IoT Central.
> [!NOTE]
> O IoT Central requer o modelo completo com todas as interfaces referenciadas no mesmo ficheiro, quando importa um modelo do repositório do modelo use a palavra-chave "expandida" para obter a versão completa.
Por exemplo. https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json

- Autor de um modelo de dispositivo utilizando a [Linguagem de Definição de Gémeos Digitais (DTDL) - versão 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). O código Visual Studio tem uma extensão que suporta a autoria de modelos DTDL. Para saber mais, consulte [instalar e utilizar as ferramentas de autoria DTDL.](../../iot-pnp/howto-use-dtdl-authoring-tools.md) Em seguida, publique o modelo para o repositório de modelos públicos. Para saber mais, consulte [o repositório de modelos do Dispositivo.](../../iot-pnp/concepts-model-repository.md) Implemente o código do dispositivo a partir do modelo e ligue o seu verdadeiro dispositivo à sua aplicação IoT Central. A IoT Central encontra e importa o modelo do dispositivo do repositório público para si e gera um modelo de dispositivo. Em seguida, pode adicionar quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita ao modelo do dispositivo.
- Autore um modelo de dispositivo utilizando o DTDL. Implemente o código do dispositivo a partir do modelo. Importe manualmente o modelo do dispositivo na sua aplicação IoT Central e, em seguida, adicione quaisquer propriedades na nuvem, personalizações e dashboards que a sua aplicação IoT Central necessita.

> [!TIP]
> A IoT Central requer o modelo completo com todas as interfaces referenciadas no mesmo ficheiro. Quando importa um modelo do repositório do modelo, use a palavra-chave *expandida* para obter a versão completa.
> Por exemplo, [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json).

Também pode adicionar modelos de dispositivo a uma aplicação IoT Central utilizando a [API REST](/learn/modules/manage-iot-central-apps-with-rest-api/) ou o [CLI](howto-manage-iot-central-from-cli.md).

Alguns [modelos de aplicação](concepts-app-templates.md) já incluem modelos de dispositivo que são úteis no cenário que o modelo de aplicação suporta. Por exemplo, consulte [a arquitetura de análise na loja.](../retail/store-analytics-architecture.md)

## <a name="create-a-device-template-from-the-device-catalog"></a>Crie um modelo de dispositivo a partir do catálogo do dispositivo

Como construtor, pode começar rapidamente a construir a sua solução utilizando um dispositivo certificado. Consulte a lista no Catálogo de [Dispositivos Azure IoT](https://catalog.azureiotsolutions.com/alldevices). A IoT Central integra-se com o catálogo de dispositivos para que possa importar um modelo de dispositivo de qualquer um dos dispositivos certificados. Para criar um modelo de dispositivo a partir de um destes dispositivos na IoT Central:

1. Aceda à página **de modelos do Dispositivo** na sua aplicação IoT Central.
1. Selecione **+ Novo**, e, em seguida, selecione qualquer um dos dispositivos certificados do catálogo. A IoT Central cria um modelo de dispositivo baseado neste modelo de dispositivo.
1. Adicione quaisquer propriedades na nuvem, personalizações ou vistas para o seu modelo de dispositivo.
1. **Selecione Publicar** para disponibilizar o modelo para os operadores visualizarem e ligarem dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Crie um modelo de dispositivo de raiz

Um modelo de dispositivo contém:

- Um _modelo de dispositivo_ que especifica a telemetria, propriedades e comandos que o dispositivo implementa. Estas capacidades são organizadas em um ou mais componentes.
- _Propriedades em nuvem_ que definem a informação que a sua aplicação IoT Central armazena sobre os seus dispositivos. Por exemplo, uma propriedade em nuvem pode gravar a data em que um dispositivo foi reparado pela última vez. Esta informação nunca é partilhada com o dispositivo.
- _As personalizações_ permitem ao construtor sobrepor-se a algumas das definições no modelo do dispositivo. Por exemplo, o construtor pode sobrepor-se ao nome de uma propriedade do dispositivo. Os nomes das propriedades aparecem nos dashboards e formulários da IoT Central.
- _Os dashboards e formulários_ permitem ao construtor criar uma UI que permite aos operadores monitorizar e gerir os dispositivos ligados à sua aplicação.

Para criar um modelo de dispositivo na IoT Central:

1. Aceda à página **de Modelos de Dispositivo** na sua aplicação IoT Central.
1. Selecione **+ novo**  >  **dispositivo IoT**. Em seguida, **selecione Seguinte: Personalize**.
1. Introduza um nome para o seu modelo, como **o termóstato**. Em seguida, selecione **Seguinte: Reveja** e, em seguida, **selecione Criar**.
1. O IoT Central cria um modelo de dispositivo vazio e permite-lhe optar por criar um modelo personalizado de raiz ou importar um modelo DTDL.

## <a name="manage-a-device-template"></a>Gerir um modelo de dispositivo

Pode renomear ou eliminar um modelo da página inicial do modelo.

Depois de ter adicionado um modelo de dispositivo ao seu modelo, pode publicá-lo. Até publicar o modelo, não pode ligar um dispositivo com base neste modelo para que os seus operadores possam ver na página **dispositivos.**

## <a name="create-a-capability-model"></a>Criar um modelo de capacidade

Para criar um modelo de dispositivo, pode:

- Utilize o IoT Central para criar um modelo personalizado de raiz.
- Importe um modelo DTDL de um ficheiro JSON. Um construtor de dispositivos pode ter usado o Código do Estúdio Visual para autoria de um modelo de dispositivo para a sua aplicação.
- Selecione um dos dispositivos do Catálogo de Dispositivos. Esta opção importa o modelo do dispositivo que o fabricante publicou para este dispositivo. Um modelo de dispositivo importado como este é publicado automaticamente.

## <a name="manage-a-capability-model"></a>Gerir um modelo de capacidade

Depois de criar um modelo de dispositivo, pode:

- Adicione componentes ao modelo. Um modelo deve ter pelo menos um componente.
- Editar metadados de modelo, como o seu ID, espaço de nome e nome.
- Apague o modelo.

## <a name="create-a-component"></a>Create a component (Criar um componente)

Um modelo de dispositivo deve ter pelo menos um componente predefinido. Um componente é uma coleção reutilizável de capacidades.

Para criar um componente:

1. Vá ao modelo do dispositivo e escolha **+ Adicionar o componente.**

1. Na página **de interface de adicionar um componente,** pode:

    - Crie um componente personalizado do zero.
    - Importe um componente existente a partir de um ficheiro DTDL. Um construtor de dispositivos pode ter usado o Código do Estúdio Visual para autoria de uma interface de componentes para o seu dispositivo.

1. Depois de criar um componente, escolha **editar** a identidade para alterar o nome de visualização do componente.

1. Se optar por criar um componente personalizado de raiz, pode adicionar as capacidades do seu dispositivo. As capacidades do dispositivo são telemetria, propriedades e comandos.

### <a name="telemetry"></a>Telemetria

A telemetria é um fluxo de valores enviados do dispositivo, tipicamente a partir de um sensor. Por exemplo, um sensor pode reportar a temperatura ambiente.

A tabela a seguir mostra as definições de configuração para uma capacidade de telemetria:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome do visor para o valor da telemetria utilizado nos painéis e formulários. |
| Name | O nome do campo na mensagem de telemetria. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico. |
| Tipo de Capacidade | A telemetria. |
| Tipo semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis. |
| Esquema | O tipo de dados de telemetria, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado. |
| Gravidade | Disponível apenas para o tipo semântico do evento. As severidades são **Erro,** **Informação** ou **Aviso**. |
| Valores do Estado | Disponível apenas para o tipo semântico do estado. Defina os valores de estado possíveis, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor da telemetria, como **mph** **%** , ou **&deg; C**. |
| Unidade de Exibição | Uma unidade de visualização para utilização em painéis e formulários. |
| Comentário | Qualquer comentário sobre a capacidade de telemetria. |
| Description | Uma descrição da capacidade de telemetria. |

### <a name="properties"></a>Propriedades

As propriedades representam valores pontuais. Por exemplo, um dispositivo pode usar uma propriedade para reportar a temperatura-alvo que está a tentar alcançar. Você pode definir propriedades writable a partir de IoT Central.

A tabela a seguir mostra as definições de configuração para uma capacidade de propriedade:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição para o valor da propriedade usado em dashboards e formulários. |
| Name | O nome da propriedade. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico. |
| Tipo de Capacidade | Propriedade. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis. |
| Esquema | O tipo de dados da propriedade, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. Schema não está disponível para o evento e tipos semânticos do estado. |
| Writable (Gravável) | Se a propriedade não for credível, o dispositivo pode reportar os valores da propriedade à IoT Central. Se a propriedade for credível, o dispositivo pode reportar valores de propriedade à IoT Central e ioT Central pode enviar atualizações de propriedade para o dispositivo.
| Gravidade | Disponível apenas para o tipo semântico do evento. As severidades são **Erro,** **Informação** ou **Aviso**. |
| Valores do Estado | Disponível apenas para o tipo semântico do estado. Defina os valores de estado possíveis, cada um dos quais tem nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor da propriedade, como **mph,** **%** ou **&deg; C**. |
| Unidade de Exibição | Uma unidade de visualização para utilização em painéis e formulários. |
| Comentário | Quaisquer comentários sobre a capacidade da propriedade. |
| Description | Uma descrição da capacidade da propriedade. |

### <a name="commands"></a>Comandos

Pode ligar para os comandos do dispositivo da IoT Central. Os comandos passam opcionalmente parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, pode chamar um comando para reiniciar um dispositivo em 10 segundos.

A tabela a seguir mostra as definições de configuração para uma capacidade de comando:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de visualização do comando utilizado nos painéis e formulários. |
| Name | O nome do comando. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. Este campo tem de ser alfanumérico. |
| Tipo de Capacidade | O comando. |
| Comentário | Qualquer comentário sobre a capacidade de comando. |
| Description | Uma descrição da capacidade de comando. |
| Pedir | Se ativado, uma definição do parâmetro de pedido, incluindo: nome, nome de exibição, esquema, unidade e unidade de visualização. |
| Resposta | Se ativado, uma definição da resposta do comando, incluindo: nome, nome do visor, esquema, unidade e unidade de visualização. |

Para saber mais sobre como os dispositivos implementam comandos, consulte [telemetria, propriedade e cargas de comando > Comandos e comandos de longa duração](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Comandos offline

Pode escolher comandos de fila se um dispositivo estiver atualmente offline, ativando a opção **"Fila" se** for por opção offline para um comando no modelo do dispositivo.

Esta opção utiliza mensagens nuvem-dispositivo IoT Hub para enviar notificações para dispositivos. Para saber mais, consulte o artigo IoT Hub Enviar por email o artigo IoT Hub [Enviar por email](../../iot-hub/iot-hub-devguide-messages-c2d.md)o artigo IoT Hub.

Mensagens nuvem-para-dispositivo:

- São notificações unidirecionais para o dispositivo a partir da sua solução.
- Garantia entrega de mensagem pelo menos uma vez. O IoT Hub persiste em mensagens nuvem-dispositivo em filas por dispositivo, garantindo resiliência contra a conectividade e falhas no dispositivo.
- Exija que o dispositivo implemente um manipulador de mensagens para processar a mensagem nuvem-dispositivo.

> [!NOTE]
> Esta opção só está disponível na UI web IoT Central. Esta definição não está incluída se exportar um modelo ou componente do modelo do dispositivo.

## <a name="manage-a-component"></a>Gerir um componente

Se ainda não publicou o componente, pode editar as capacidades definidas pelo componente. Depois de publicar o componente, se pretender fazer alterações, tem de criar uma nova versão do modelo do dispositivo e [de ver o componente](howto-version-device-template.md). Pode esporar alterações que não exijam a versão, como nomes de exibição ou unidades, na secção **Personalizar.**

Também pode exportar o componente como ficheiro JSON se quiser reutilizá-lo noutro modelo de capacidade.

## <a name="add-cloud-properties"></a>Adicionar propriedades da cloud

Utilize propriedades em nuvem para armazenar informações sobre dispositivos no IoT Central. As propriedades da nuvem nunca são enviadas para um dispositivo. Por exemplo, pode utilizar propriedades na nuvem para armazenar o nome do cliente que instalou o dispositivo, ou a última data de serviço do dispositivo.

A tabela a seguir mostra as definições de configuração de uma propriedade na nuvem:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição para o valor da propriedade na nuvem usado em dashboards e formulários. |
| Name | O nome da propriedade na nuvem. O IoT Central gera um valor para este campo a partir do nome do visor, mas pode escolher o seu próprio valor se necessário. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos seguintes campos estão disponíveis. |
| Esquema | O tipo de dados de propriedade em nuvem, como duplo, string ou vetor. As escolhas disponíveis são determinadas pelo tipo semântico. |

## <a name="add-customizations"></a>Adicionar personalizações

Utilize personalizações quando precisar de modificar um componente importado ou adicionar funcionalidades específicas do IoT Central a uma capacidade. Só é possível personalizar campos que não quebram a compatibilidade dos componentes. Pode, por exemplo:

- Personalize o nome do visor e as unidades de capacidade.
- Adicione uma cor padrão para usar quando o valor aparecer num gráfico.
- Especifique valores iniciais, mínimos e máximos para um imóvel.

Não é possível personalizar o nome de capacidade ou o tipo de capacidade. Se houver alterações que não possa fazer na secção **Personalizar,** terá de ver o modelo e o componente do dispositivo para modificar a capacidade.

### <a name="generate-default-views"></a>Gerar vistas padrão

Gerar vistas padrão é uma forma rápida de visualizar as informações importantes do dispositivo. Tem até três vistas padrão geradas para o seu modelo de dispositivo:

- **Comandos**: Uma vista com comandos do dispositivo e permite que o seu operador os despacha para o seu dispositivo.
- **Visão geral**: Uma vista com telemetria do dispositivo, gráficos de exibição e métricas.
- **Sobre:** Uma vista com informações do dispositivo, exibindo propriedades do dispositivo.

Depois de ter selecionado **As vistas predefinidas**, verá que foram adicionadas automaticamente na secção **Visualizações** do modelo do seu dispositivo.

## <a name="add-dashboards"></a>Adicionar dashboards

Adicione dashboards a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo utilizando gráficos e métricas. Pode ter vários dashboards para um modelo de dispositivo.

Para adicionar um dashboard a um modelo de dispositivo:

1. Vá ao seu modelo de dispositivo e selecione **Vistas**.
1. Escolha **visualizar o dispositivo.**
1. Introduza um nome para o seu painel de **instrumentos** no Nome dashboard .
1. Adicione azulejos ao seu painel a partir da lista de estática, propriedade, propriedade em nuvem, telemetria e telhas de comando. Arraste e largue os azulejos que pretende adicionar ao seu painel de instrumentos.
1. Para traçar vários valores de telemetria num único bloco gráfico, selecione os valores da telemetria e, em seguida, selecione **Combine**.
1. Configure cada telha que adiciona para personalizar a forma como exibe dados. Aceda a esta opção selecionando o ícone de engrenagem ou selecionando a **configuração Change** no seu azulejo de gráfico.
1. Arrume e redimensione os azulejos do seu painel de instrumentos.
1. Guarde as alterações.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurar dispositivo de pré-visualização para visualizar o dashboard

Para visualizar e testar o seu painel de instrumentos, selecione **o dispositivo de pré-visualização Configure**. Esta funcionalidade permite-lhe ver o dashboard como o seu operador o vê depois de publicado. Utilize esta funcionalidade para validar que as suas opiniões mostram os dados corretos. Pode escolher de entre as seguintes opções:

- Sem dispositivo de pré-visualização.
- O verdadeiro dispositivo de teste que configuraste para o modelo do dispositivo.
- Um dispositivo existente na sua aplicação, utilizando o ID do dispositivo.

## <a name="add-forms"></a>Adicionar formulários

Adicione formulários a um modelo de dispositivo para permitir que os operadores gerem um dispositivo visualizando e definindo propriedades. Os operadores só podem editar propriedades em nuvem e propriedades de dispositivos writable. Pode ter várias formas para um modelo de dispositivo.

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

Antes de poder ligar um dispositivo que implemente o seu modelo de dispositivo, tem de publicar o modelo do dispositivo.

Depois de publicar um modelo de dispositivo, só pode fazer alterações limitadas no modelo do dispositivo. Para modificar um componente, é necessário [criar e publicar uma nova versão](./howto-version-device-template.md).

Para publicar um modelo de dispositivo, vá até si o modelo do seu dispositivo e selecione **Publicar**.

Depois de publicar um modelo de dispositivo, um operador pode ir à página dispositivos e adicionar **dispositivos** reais ou simulados que usam o modelo do seu dispositivo. Pode continuar a modificar e guardar o modelo do dispositivo à medida que está a fazer alterações. Quando pretender empurrar estas alterações para o operador para visualizar na página **Dispositivos,** tem de selecionar **Publicar** sempre.

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, um próximo passo sugerido é ler sobre a [versão do modelo do dispositivo](./howto-version-device-template.md).
