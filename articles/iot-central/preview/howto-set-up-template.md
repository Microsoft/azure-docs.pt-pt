---
title: Configurar um modelo de dispositivo em um aplicativo de IoT Central do Azure | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um painel.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e3925f2d6c2a1034cc96475721de7936deeb4a9d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895634"
---
# <a name="set-up-and-manage-a-device-template-preview-features"></a>Configurar e gerenciar um modelo de dispositivo (recursos de visualização)

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

1. Os **comandos** fornecerão uma exibição com comandos de dispositivo e permitirão que o operador o envie para o dispositivo.
1. A **visão geral** fornecerá uma exibição com telemetria do dispositivo, exibindo gráficos e métricas.
1. **Sobre** o fornecerá uma exibição com informações do dispositivo, exibindo as propriedades do dispositivo.

Depois de selecionar **gerar modos de exibição padrão** , você verá que eles foram adicionados automaticamente na seção **exibições** do modelo de dispositivo.

## <a name="add-dashboards"></a>Adicionar painéis

Adicione painéis a um modelo de dispositivo para permitir que os operadores visualizem um dispositivo usando gráficos e métricas. Você pode ter vários painéis para um modelo de dispositivo.

Para adicionar um painel a um modelo de dispositivo:

1. Vá para o modelo de dispositivo e selecione **exibições**.
1. Em seguida, escolha **Visualizar o dispositivo**.
1. Insira um nome para seu painel no **nome do painel**.
1. Adicione blocos ao seu painel da lista de elementos estáticos, de propriedade, de nuvem, de telemetria e de comando. Arraste e solte os blocos que você deseja adicionar ao seu painel.
1. Para plotar vários valores de telemetria em um único bloco de gráfico, selecione os valores de telemetria e, em seguida, selecione **combinar**.
1. Configure cada bloco que você adicionar para personalizar como ele exibe dados selecionando o ícone de engrenagem ou selecionando o botão **Alterar configuração** no bloco do gráfico.
1. Organize e redimensione os blocos em seu painel.
1. Guarde as alterações.

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

Depois de publicar um modelo de dispositivo, você só poderá fazer alterações limitadas no modelo de capacidade do dispositivo. Para modificar uma interface, você precisa [criar e publicar uma nova versão](./howto-version-device-template.md).

Para publicar um modelo de dispositivo, acesse seu modelo de dispositivo e selecione **publicar**.

Depois de publicar um modelo de dispositivo, um operador pode ir para a página **dispositivos** e adicionar dispositivos reais ou simulados que usam o modelo de dispositivo. Você pode continuar a modificar e salvar o modelo de dispositivo enquanto estiver fazendo alterações, no entanto, quando desejar enviar essas alterações para o operador para exibição na página **dispositivos** , deverá selecionar **publicar** a cada vez.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como configurar um modelo de dispositivo em seu aplicativo de IoT Central do Azure, você pode:

> [!div class="nextstepaction"]
> [Criar uma nova versão de modelo de dispositivo](howto-version-device-template.md)
> [conectar um dispositivo MXChip IOT devkit ao aplicativo de IOT central do Azure](howto-connect-devkit.md)
