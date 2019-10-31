---
title: Definir um novo tipo de dispositivo IoT no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra, como um construtor, como criar um novo modelo de dispositivo IoT do Azure em seu aplicativo de IoT Central do Azure. Você define a telemetria, o estado, as propriedades e os comandos para seu tipo.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 1f68ddabde58c68a4096fcc7a0b0e1dd8a2ba9e6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176200"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Tutorial: definir um novo tipo de dispositivo IoT em seu aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Um modelo de dispositivo é um plano gráfico que define as características e os comportamentos de um tipo de dispositivo que se conecta a um aplicativo de IoT Central do Azure.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado com as seguintes características:

- Envia telemetria de temperatura
- Envia a propriedade local
- Envia eventos de erro do motor do ventilador
- Envia o estado operacional do ventilador
- Propriedade de velocidade de ventilador gravável
- Comando para reiniciar o dispositivo
- Painel que fornece uma visão geral do dispositivo

Nesse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventilador reais. Todos esses ventiladores têm medidas, propriedades e comandos que os operadores usam para monitorar e gerenciar. Os operadores usam os painéis e formulários do dispositivo para interagir com os dispositivos do ventilador.

> [!NOTE]
> Somente os criadores e os administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página **dispositivos** a partir de modelos de dispositivo existentes.

O [plug and Play de IOT](../../iot-pnp/overview-iot-plug-and-play.md) permite que IOT central integre dispositivos sem escrever nenhum código de dispositivo inserido. No núcleo do Plug and Play IoT está um esquema de modelo de capacidade de dispositivo que descreve os recursos do dispositivo. Em um aplicativo IoT Central Preview, os modelos de dispositivo usam esses modelos de capacidade de dispositivo de Plug and Play IoT.

Como um construtor, você tem várias opções para criar modelos de dispositivo:

- Crie o modelo de dispositivo no IoT Central e, em seguida, implemente seu modelo de capacidade de dispositivo no código do dispositivo.
- Importe um modelo de capacidade de dispositivo do [Catálogo de dispositivos Azure Certified para IOT](https://aka.ms/iotdevcat) e adicione quaisquer propriedades de nuvem, personalizações e painéis de que seu aplicativo IOT central precisa.
- Crie um modelo de capacidade de dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo. Importe manualmente o modelo de capacidade do dispositivo para seu aplicativo IoT Central e, em seguida, adicione quaisquer propriedades de nuvem, personalizações e painéis de que seu aplicativo IoT Central precisa.
- Crie um modelo de capacidade de dispositivo usando o Visual Studio Code. Implemente o código do dispositivo do modelo e conecte seu dispositivo real ao seu aplicativo IoT Central usando uma conexão do dispositivo-primeiro. IoT Central localiza e importa o modelo de capacidade de dispositivo do repositório público para você. Em seguida, você pode adicionar qualquer propriedade de nuvem, personalizações e painéis que seu aplicativo IoT Central precisa para o modelo de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma aplicação do Azure IoT Central. Siga este guia de início rápido para [criar um aplicativo de IOT central do Azure](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="create-a-device-template-from-the-device-catalog"></a>Criar um modelo de dispositivo a partir do catálogo de dispositivos

Como um construtor, você pode começar rapidamente a criar sua solução usando um dispositivo de Plug and Play de IoT certificado listado no [Catálogo de dispositivos do Azure IOT](https://catalog.azureiotsolutions.com/alldevices). O IoT Central integra-se com o catálogo de dispositivos para permitir que você importe um modelo de capacidade de dispositivo de qualquer um desses dispositivos de IoT Plug and Play certificados. Para criar um modelo de dispositivo a partir de um desses dispositivos no IoT Central:

1. Vá para a página **modelos de dispositivo** em seu aplicativo IOT central.
1. Selecione **+ novo**e, em seguida, selecione qualquer um dos dispositivos certificados para IOT plug and Play do catálogo listado abaixo. IoT Central cria um modelo de dispositivo com base nesse modelo de funcionalidade de dispositivo.
1. Adicione quaisquer propriedades, personalizações ou exibições de nuvem ao modelo de dispositivo.
1. Selecione **publicar** para publicar este modelo de dispositivo para disponibilizá-lo para que os operadores exibam e conectem dispositivos.

## <a name="create-a-device-template-from-scratch"></a>Criar um modelo de dispositivo do zero

Um modelo de dispositivo contém:

- Um _modelo de capacidade de dispositivo_ que especifica a telemetria, as propriedades e os comandos que o dispositivo implementa. Esses recursos são organizados em uma ou mais interfaces.
- _Propriedades de nuvem_ que definem informações que seu aplicativo IOT central armazena sobre seus dispositivos. Por exemplo, uma propriedade de nuvem pode registrar os dados que um dispositivo foi último atendido. Essas informações nunca são compartilhadas com o dispositivo.
- As _personalizações_ permitem que o Construtor substitua algumas das definições no modelo de funcionalidade do dispositivo. Por exemplo, o Construtor poderia substituir o nome de uma propriedade de dispositivo. Os nomes de propriedade aparecem em IoT Central painéis e formulários.
- Os _dashboards e formulários_ permitem que o Construtor crie uma interface do usuário que permite aos operadores monitorar e gerenciar os dispositivos conectados ao seu aplicativo.

Para criar um modelo de dispositivo no IoT Central:

1. Vá para a página **modelos de dispositivo** em seu aplicativo IOT central.
1. Selecione **+ novo**e, em seguida, selecione **personalizado**.
1. Insira um nome para o modelo, como **sensor ambiental**.
1. Prima **Enter**. IoT Central cria um modelo de dispositivo vazio.

## <a name="manage-a-device-template"></a>Gerenciar um modelo de dispositivo

Você pode renomear ou excluir um modelo do home page do modelo.

Depois de adicionar um modelo de funcionalidade de dispositivo ao seu modelo, você poderá publicá-lo. Não é possível conectar um dispositivo com base neste modelo para que seus operadores vejam na página **dispositivos** até que você tenha publicado o modelo.

## <a name="create-a-capability-model"></a>Criar um modelo de funcionalidade

Para criar um modelo de capacidade de dispositivo, você pode:

- Use IoT Central para criar um modelo personalizado do zero.
- Importar um modelo de um arquivo JSON. Um construtor de dispositivos pode ter usado Visual Studio Code para criar um modelo de capacidade de dispositivo para seu aplicativo.
- Selecione um dos dispositivos no catálogo de dispositivos. Essa opção importa o modelo de funcionalidade de dispositivo que o fabricante publicou para este dispositivo. Um modelo de capacidade de dispositivo importado como este é publicado automaticamente.

## <a name="manage-a-capability-model"></a>Gerenciar um modelo de funcionalidade

Depois de criar um modelo de capacidade de dispositivo, você pode:

- Adicione interfaces ao modelo. Um modelo deve ter pelo menos uma interface.
- Edite metadados de modelo, como ID, namespace e nome.
- Exclua o modelo.

## <a name="create-an-interface"></a>Criar uma interface

Uma funcionalidade de dispositivo deve ter pelo menos uma interface. Uma interface é uma coleção reutilizável de recursos.

Para criar uma interface:

1. Vá para o modelo de funcionalidade do dispositivo e escolha **+ Adicionar interface**.

1. Na página **selecionar uma interface** , você pode:

    - Crie uma interface personalizada do zero.
    - Importar uma interface existente de um arquivo. Um construtor de dispositivos pode ter usado Visual Studio Code para criar uma interface para seu dispositivo.
    - Escolha uma das interfaces padrão, como a interface de **informações do dispositivo** . As interfaces padrão especificam os recursos comuns a muitos dispositivos. Essas interfaces padrão são publicadas por Microsoft Azure IoT e não podem ter controle de versão ou edição.

1. Depois de criar uma interface, escolha **Editar identidade** para alterar o nome de exibição da interface.

1. Se você optar por criar uma interface personalizada do zero, poderá adicionar os recursos do dispositivo. Os recursos do dispositivo são telemetria, propriedades e comandos.

### <a name="telemetry"></a>Telemetria

A telemetria é um fluxo de valores enviados do dispositivo, normalmente de um sensor. Por exemplo, um sensor poderia relatar a temperatura ambiente.

A tabela a seguir mostra as definições de configuração para um recurso de telemetria:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição do valor de telemetria usado em painéis e formulários. |
| Nome | O nome do campo na mensagem de telemetria. IoT Central gera um valor para esse campo a partir do nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de funcionalidade | Métrico. |
| Tipo semântico | O tipo semântico da telemetria, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados Telemétrico, como Double, String ou Vector. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos de semântica de evento e de estado. |
| Gravidade | Disponível somente para o tipo semântico de evento. **Erro**, **informação**ou **aviso**. |
| Valores de estado | Disponível somente para o tipo semântico de estado. Defina os valores de estado possíveis, cada um dos quais com nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor de telemetria, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em painéis e formulários. |
| Comentário | Comentários sobre o recurso de telemetria. |
| Descrição | Uma descrição do recurso de telemetria. |

### <a name="properties"></a>Propriedades

As propriedades representam valores de ponto em tempo. Por exemplo, um dispositivo poderia usar uma propriedade para relatar a temperatura de destino que está tentando alcançar. Você pode definir propriedades graváveis de IoT Central.

A tabela a seguir mostra as definições de configuração para uma funcionalidade de propriedade:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição do valor da propriedade usado em painéis e formulários. |
| Nome | O nome da propriedade. IoT Central gera um valor para esse campo a partir do nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de funcionalidade | Propriedade. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade, como Double, String ou Vector. As opções disponíveis são determinadas pelo tipo semântico. O esquema não está disponível para os tipos de semântica de evento e de estado. |
| Gravável | Se a propriedade não for gravável, o dispositivo poderá relatar valores de propriedade para IoT Central. Se a propriedade for gravável, o dispositivo poderá relatar valores de propriedade para IoT Central e IoT Central poderá enviar atualizações de propriedade para o dispositivo.
| Gravidade | Disponível somente para o tipo semântico de evento. **Erro**, **informação**ou **aviso**. |
| Valores de estado | Disponível somente para o tipo semântico de estado. Defina os valores de estado possíveis, cada um dos quais com nome de exibição, nome, tipo de enumeração e valor. |
| Unidade | Uma unidade para o valor da propriedade, como **mph**, **%** ou **&deg;C**. |
| Unidade de exibição | Uma unidade de exibição para uso em painéis e formulários. |
| Comentário | Comentários sobre a funcionalidade de propriedade. |
| Descrição | Uma descrição da funcionalidade de propriedade. |

### <a name="commands"></a>Comandos

Você pode chamar comandos de dispositivo de IoT Central. Opcionalmente, os comandos passam parâmetros para o dispositivo e recebem uma resposta do dispositivo. Por exemplo, você pode chamar um comando para reinicializar um dispositivo em 10 segundos.

A tabela a seguir mostra as definições de configuração para um recurso de comando:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição do comando usado em painéis e formulários. |
| Nome | O nome do comando. IoT Central gera um valor para esse campo a partir do nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo de funcionalidade | Comando |
| Comando | SynchronousExecutionType. |
| Comentário | Comentários sobre a funcionalidade de comando. |
| Descrição | Uma descrição da funcionalidade de comando. |
| Pedir | Se habilitada, uma definição do parâmetro de solicitação, incluindo: nome, nome de exibição, esquema, unidade e unidade de exibição. |
| Resposta | Se habilitada, uma definição da resposta do comando, incluindo: nome, nome de exibição, esquema, unidade e unidade de exibição. |

## <a name="manage-an-interface"></a>Gerenciar uma interface

Desde que você não tenha publicado a interface, você pode editar os recursos definidos pela interface. Depois que a interface tiver sido publicada, você precisará criar uma nova versão do modelo de dispositivo e a versão da interface para fazer alterações. As alterações que não exigem controle de versão, como nomes ou unidades de exibição, podem ser feitas na seção **Personalizar** .

Você também pode exportar a interface como um arquivo JSON se quiser reutilizá-la em outro modelo de funcionalidade.

## <a name="add-cloud-properties"></a>Adicionar propriedades de nuvem

Use Propriedades de nuvem para armazenar informações sobre dispositivos no IoT Central. As propriedades de nuvem nunca são enviadas a um dispositivo. Por exemplo, você pode usar propriedades de nuvem para armazenar o nome do cliente que instalou o dispositivo ou a data do último serviço do dispositivo.

A tabela a seguir mostra as definições de configuração para uma propriedade de nuvem:

| Campo | Descrição |
| ----- | ----------- |
| Nome a Apresentar | O nome de exibição para o valor da propriedade de nuvem usado em painéis e formulários. |
| Nome | O nome da propriedade de nuvem. IoT Central gera um valor para esse campo a partir do nome de exibição, mas você pode escolher seu próprio valor, se necessário. |
| Tipo semântico | O tipo semântico da propriedade, como temperatura, estado ou evento. A escolha do tipo semântico determina quais dos campos a seguir estão disponíveis. |
| Esquema | O tipo de dados de propriedade de nuvem, como Double, String ou Vector. As opções disponíveis são determinadas pelo tipo semântico. |

## <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar uma interface importada ou adicionar recursos específicos de IoT Central a um recurso. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, pode:

- Personalize o nome de exibição e as unidades de um recurso.
- Adicione uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especifique os valores iniciais, mínimos e máximos para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade. Se houver alterações que não podem ser feitas na seção **Personalizar** , você precisará criar a versão do modelo de dispositivo e da interface para modificar o recurso.

### <a name="generate-default-views"></a>Gerar exibições padrão

Gerar exibições padrão é uma maneira rápida de começar a visualizar as informações importantes do dispositivo. Você terá até três exibições padrão geradas para seu modelo de dispositivo:

- Os **comandos** fornecerão uma exibição com comandos de dispositivo e permitirão que o operador o envie para o dispositivo.
- A **visão geral** fornecerá uma exibição com telemetria do dispositivo, exibindo gráficos e métricas.
- **Sobre** o fornecerá uma exibição com informações do dispositivo, exibindo as propriedades do dispositivo.

Depois de selecionar **gerar modos de exibição padrão** , você verá que eles foram adicionados automaticamente na seção **exibições** do modelo de dispositivo.

## <a name="add-dashboards"></a>Adicionar painéis

Adicione painéis a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo usando gráficos e métricas. Você pode ter vários painéis para um modelo de dispositivo.

Para adicionar um painel a um modelo de dispositivo:

- Vá para o modelo de dispositivo e selecione **exibições**.
- Em seguida, escolha **Visualizar o dispositivo**.
- Insira um nome para seu painel no **nome do painel**.
- Adicione blocos ao seu painel da lista de elementos estáticos, de propriedade, de nuvem, de telemetria e de comando. Arraste e solte os blocos que você deseja adicionar ao seu painel.
- Para plotar vários valores de telemetria em um único bloco de gráfico, selecione os valores de telemetria e, em seguida, selecione **combinar**.
- Configure cada bloco que você adicionar para personalizar como ele exibe dados selecionando o ícone de engrenagem ou selecionando o botão **Alterar configuração** no bloco do gráfico.
- Organize e redimensione os blocos em seu painel.
- Guarde as alterações.

### <a name="configure-preview-device-to-view-dashboard"></a>Configurar o dispositivo de visualização para exibir o painel

Para exibir e testar seu painel, você pode selecionar **Configurar dispositivo de visualização**, que permite que você veja o painel conforme o operador vê depois de ser publicado. Essa opção permite que você valide se suas exibições mostram os dados corretos. Você pode escolher entre um dispositivo de visualização, o dispositivo de teste real que você configurou para seu modelo de dispositivo ou um dispositivo existente em seu aplicativo usando a ID do dispositivo.

## <a name="add-forms"></a>Adicionar formulários

Adicione formulários a um modelo de dispositivo para permitir que os operadores gerenciem um dispositivo exibindo e Configurando propriedades. Os operadores só podem editar propriedades de nuvem e propriedades de dispositivo graváveis. Você pode ter vários formulários para um modelo de dispositivo.

Para adicionar um formulário a um modelo de dispositivo:

1. Vá para o modelo de dispositivo e selecione **exibições**.
1. Em seguida, escolha **Editar dados do dispositivo e da nuvem**.
1. Insira um nome para o formulário no **nome do formulário**.
1. Selecione o número de colunas a ser usado para definir o layout do formulário.
1. Adicione Propriedades a uma seção existente no formulário ou selecione Propriedades e escolha **Adicionar seção**. Use seções para agrupar Propriedades no formulário. Você pode adicionar um título a uma seção.
1. Configure cada propriedade no formulário para personalizar seu comportamento.
1. Organize as propriedades no formulário.
1. Guarde as alterações.

## <a name="publish-a-device-template"></a>Publicar um modelo de dispositivo

Antes de poder conectar um dispositivo que implementa o modelo de capacidade do dispositivo, você deve publicar o modelo do dispositivo.

Depois de publicar um modelo de dispositivo, você só poderá fazer alterações limitadas no modelo de capacidade do dispositivo. Para modificar uma interface, você precisa [criar e publicar uma nova versão](./howto-version-device-template-pnp.md).

Para publicar um modelo de dispositivo, acesse seu modelo de dispositivo e selecione **publicar**.

Depois de publicar um modelo de dispositivo, um operador pode ir para a página **dispositivos** e adicionar dispositivos reais ou simulados que usam o modelo de dispositivo. Você pode continuar a modificar e salvar o modelo de dispositivo enquanto estiver fazendo alterações, no entanto, quando desejar enviar essas alterações para o operador para exibição na página **dispositivos** , deverá selecionar **publicar** a cada vez.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definir um novo tipo de dispositivo de gateway IoT (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este tutorial mostra, como um construtor, como usar um modelo de dispositivo de gateway para definir um novo tipo de dispositivo IoT do Azure em seu aplicativo IoT Central do Azure. 

Nesta seção, você cria um modelo de dispositivo de **criação inteligente** . Um dispositivo de gateway de construção inteligente:

* Envia telemetria, como temperatura e ocupação.
* Responde às propriedades graváveis quando atualizado na nuvem, como o intervalo de envio de telemetria.
* Responde a comandos como a redefinição da temperatura.
* Permite relações com outros modelos de capacidade de dispositivo

### <a name="create-iot-device-templates"></a>Criar modelos de dispositivo IoT

Você criará modelos de dispositivo IoT. 

Clique em modelos de dispositivo no painel de navegação esquerdo, clique em **+ novo**, selecione bloco de **dispositivo IOT** e selecione o bloco sensor de ocupação e clique em **Avançar: Personalizar**

![Dispositivo IoT](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-new.png)

A página revisão será apresentada. Clique no botão **criar** . 

![Dispositivo IoT](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-review.png)

Novo modelo de dispositivo é criado. 

![Dispositivo IoT](./media/tutorial-define-iot-device-type-pnp/occupancy-sensor.png)

Você criará um modelo de dispositivo para o sensor S1. 

Clique em modelos de dispositivo no painel de navegação esquerdo, clique em **+ novo**, selecione bloco de **dispositivo IOT** e selecione o bloco sensor de ocupação e clique em **Avançar: Personalizar**

![Dispositivo IoT](./media/tutorial-define-iot-device-type-pnp/s1-sensor.png)

A página revisão será apresentada. Clique no botão **criar** . 

![Dispositivo downstream](./media/tutorial-define-iot-device-type-pnp/s1-review.png)

Novo modelo de dispositivo é criado. 

![Dispositivo downstream](./media/tutorial-define-iot-device-type-pnp/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Criar um modelo de dispositivo de gateway IoT

Você pode optar por criar um modelo de dispositivo de gateway IoT. O dispositivo de gateway terá relações com dispositivos downstream que se conectam ao IoT Central por meio do dispositivo de gateway. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Relações de dispositivo downstream com dispositivo de gateway

Os dispositivos IoT podem se conectar ao dispositivo de gateway do Azure IoT 

![Página do aplicativo Central](./media/tutorial-define-iot-device-type-pnp/gatewaypattern.png)

Como um construtor, você pode criar e editar modelos de dispositivo de gateway IoT do Azure em seu aplicativo. Depois de publicar um modelo de dispositivo, você pode conectar dispositivos reais que implementam o modelo de dispositivo.

### <a name="select-device-template-type"></a>Selecionar tipo de modelo de dispositivo 

Para adicionar um novo modelo de dispositivo ao seu aplicativo, vá para a página **modelos de dispositivo** . Para fazer isso, selecione a guia **modelos de dispositivo** no painel esquerdo.

![Página do aplicativo Central](./media/tutorial-define-iot-device-type-pnp/devicetemplate.png)

Clique em **+ novo** para começar a criar um novo modelo de dispositivo.

![Modelos de dispositivo – novo](./media/tutorial-define-iot-device-type-pnp/devicetemplatenew.png)

![Seleção de modelos de dispositivo-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

Você vai para a página seleção de tipo de modelo de dispositivo. Selecione bloco **do Azure IOT** e clique em **Avançar: botão Personalizar** na parte inferior

Selecione a caixa de seleção gateway e clique em **criar** 

![Seleção de modelos de dispositivo-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-customize.png)

Uma página de revisão será exibida, clique em **criar** 

![Modelo de dispositivo-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

Insira o modelo de gateway de **construção inteligente**nome do modelo de gateway. Clique em bloco **personalizado** .

Adicione **informações de dispositivo**de interface padrão.

### <a name="add-relationships"></a>Adicionar relações

Você pode adicionar relações de downstream a modelos de capacidade de dispositivo para dispositivos que serão conectados ao dispositivo de gateway.

Crie relações com os modelos de capacidade de dispositivo downstream. Clicar em **Guardar**

![Modelo de dispositivo-gateway](./media/tutorial-define-iot-device-type-pnp/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Adicionar propriedades de nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades de nuvem** e **+ Adicionar Propriedade de nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao modelo de dispositivo.

    | Nome a Apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhuma          | Date   |
    | Nome do cliente     | Nenhuma          | String |

2. Selecione **salvar** para salvar as alterações:

### <a name="add-customizations"></a>Adicionar personalizações

Use personalizações quando precisar modificar uma interface ou adicionar recursos específicos de IoT Central a uma funcionalidade que não exige a versão do modelo de capacidade do dispositivo. Você pode personalizar os campos quando o modelo de funcionalidade estiver em um rascunho ou em um estado publicado. Você só pode personalizar campos que não interrompem a compatibilidade de interface. Por exemplo, pode:

- Personalize o nome de exibição e as unidades de um recurso.
- Adicione uma cor padrão a ser usada quando o valor for exibido em um gráfico.
- Especifique os valores iniciais, mínimos e máximos para uma propriedade.

Você não pode personalizar o nome da funcionalidade ou o tipo de funcionalidade. Clicar em **Guardar**

### <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo do sensor ambiental para um operador. Suas personalizações permitem que o operador gerencie os dispositivos do sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador Use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="generate-default-views"></a>Gerar exibições padrão

Para este tutorial, clique em gerar exibições padrão. Visão geral & sobre os painéis são gerados. 

## <a name="publish-device-template"></a>Publicar modelo de dispositivo

Para poder criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para o modelo de dispositivo na página **modelos de dispositivo** .

2. Selecione **Publicar**.

3. Na caixa de diálogo **publicar um modelo de dispositivo** , escolha **publicar**:

Depois que um modelo de dispositivo é publicado, ele fica visível na página **dispositivos** e no operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, personalizações e exibições, em um modelo de dispositivo publicado sem controle de versão. Depois de fazer alterações, selecione **publicar** para enviar por push essas alterações para o operador.

## <a name="create-gateway-simulated-device"></a>Criar dispositivo simulado de gateway

No Gerenciador de dispositivos, crie um gateway de construção inteligente simulado. 

![Modelo de dispositivo-gateway](./media/tutorial-define-iot-device-type-pnp/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Criar dispositivos simulados downstream

No Gerenciador de dispositivos, crie um sensor de ocupação simulado. 

![Modelo de dispositivo – ocupação](./media/tutorial-define-iot-device-type-pnp/occupancydevice.png)

No Gerenciador de dispositivos, crie um sensor S1 simulado. 

![Modelo de dispositivo-S1](./media/tutorial-define-iot-device-type-pnp/s1device.png)

## <a name="add-downstream-devices-relationships-to-gateway-device"></a>Adicionar relações de dispositivos downstream ao dispositivo de gateway

Selecione sensor de ocupação e sensor S1 e clique em **conectar ao gateway**. 

![Modelo de dispositivo-S1](./media/tutorial-define-iot-device-type-pnp/connecttogateway.png)

Selecione modelo de dispositivo de gateway, instância de dispositivo de gateway e clique em **ingressar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Criar um novo gateway de IoT como um modelo de dispositivo
* Criar propriedades de nuvem.
* Criar personalizações.
* Defina uma visualização para a telemetria do dispositivo.
* Adicionar relações
* Publique seu modelo de dispositivo.

Esta é a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Conectar um dispositivo](tutorial-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
