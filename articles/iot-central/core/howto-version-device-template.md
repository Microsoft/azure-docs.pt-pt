---
title: Compreensão da versão do modelo do dispositivo para as suas aplicações Azure IoT Central [ Microsoft Docs
description: Iterar sobre os modelos do seu dispositivo criando novas versões e sem afetar os seus dispositivos conectados ao vivo
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bb77d1a09cb2692765c4c834ce617d13465d4d67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157573"
---
# <a name="create-a-new-device-template-version"></a>Criar uma nova versão do modelo do dispositivo



A Azure IoT Central permite o rápido desenvolvimento de aplicações IoT. Pode iterar rapidamente os desenhos do modelo do seu dispositivo adicionando, editando ou apagando capacidades, vistas e personalizações do dispositivo. Uma vez publicado o modelo do dispositivo, o modelo de capacidade do dispositivo mostra como **Publicado** com ícones de bloqueio ao lado do modelo. Para fazer alterações no modelo de capacidade do dispositivo, terá de criar uma nova versão do modelo do dispositivo. Entretanto, as propriedades da nuvem, personalizações e vistas podem ser editadas a qualquer momento sem precisar em versão do modelo do dispositivo. Depois de ter guardado qualquer destas alterações, pode publicar o modelo do dispositivo para disponibilizar as mais recentes alterações para o operador visualizar no Device Explorer.

> [!NOTE]
> Para saber mais sobre como criar um modelo de dispositivo, consulte [Configurar e gerir um modelo](howto-set-up-template.md) de dispositivo

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Adicione personalizações ao modelo do dispositivo sem versonização

Certos elementos das capacidades do seu dispositivo podem ser editados sem necessidade de ver o modelo e interfaces do seu dispositivo. Por exemplo, alguns destes campos incluem nome de exibição, tipo semântico, valor mínimo, valor máximo, casas decimais, cor, unidade, unidade de exibição, comentários e descrição. Para adicionar uma destas personalizações:

1. Vá para a página modelos de **dispositivo.**
1. Selecione o modelo de dispositivo que pretende personalizar.
1. Escolha o separador **Personalizar.**
1. Todas as capacidades definidas no seu modelo de capacidade do dispositivo serão listadas aqui. Todos os campos que pode editar aqui podem ser guardados e utilizados em toda a sua aplicação, sem precisar de ver o modelo do seu dispositivo. Se houver campos que pretende editar que sejam apenas de leitura, terá de ver o modelo do seu dispositivo para os alterar. Selecione um campo que deseje editar e introduzir em quaisquer novos valores.
1. Clique em **Guardar**. Agora estes valores anularão tudo o que foi inicialmente guardado no modelo do seu dispositivo e serão utilizados em toda a aplicação.

## <a name="versioning-a-device-template"></a>Versonionando um modelo de dispositivo

A criação de uma nova versão do modelo do seu dispositivo criará uma versão de rascunho do modelo onde o modelo de capacidade do dispositivo pode ser editado. Quaisquer interfaces publicadas permanecerão publicadas até serem versões individualmente. Para modificar uma interface publicada, primeiro deve criar uma nova versão do modelo do dispositivo.

O modelo do dispositivo só deve ser versão quando estiver a tentar editar uma parte do modelo de capacidade do dispositivo que não pode editar na secção de personalização do modelo do dispositivo. 

Para ver um modelo de dispositivo:

1. Vá para a página modelos de **dispositivo.**
1. Selecione o modelo do dispositivo que está a tentar ver.
1. Clique no botão **Versão** na parte superior da página e dê ao modelo um novo nome. Sugerimos um novo nome para si que pode ser editado.
1. Clique em **Criar**.
1. Agora o modelo do seu dispositivo está no modo de projeto. Verá que as suas interfaces ainda estão bloqueadas e devem ser editadas individualmente. 

### <a name="versioning-an-interface"></a>Versonionamento de uma interface

A versão de uma interface permite-lhe adicionar, atualizar e remover as capacidades dentro da interface que já tinha criado. 

Para ver uma interface:

1. Vá para a página modelos de **dispositivo.**
1. Selecione o modelo de dispositivo que tem num modo de projeto.
1. Selecione a interface que está no modo publicado que pretende ver e editar.
1. Clique no botão **Versão** na parte superior da página da interface. 
1. Clique em **Criar**.
1. Agora a sua interface está em modo de projeto. Poderá adicionar ou editar capacidades à sua interface sem quebrar as personalizações e vistas existentes. 

> [!NOTE]
> As interfaces standard publicadas pelo Azure IoT não podem ser versonizadas ou editadas. Estas interfaces padrão são utilizadas para a certificação do dispositivo.

> [!NOTE]
> Uma vez publicada a interface, não é possível eliminar nenhuma das suas capacidades mesmo num modo de projeto. As capacidades só podem ser editadas ou adicionadas à interface no modo de projeto.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrar um dispositivo através das versões do modelo do dispositivo

Pode criar várias versões do modelo do dispositivo. Com o tempo, terá vários dispositivos conectados usando estes modelos de dispositivo. Pode migrar dispositivos de uma versão do seu modelo de dispositivo para outra. Os seguintes passos descrevem como migrar um dispositivo:

1. Vá para a página do **Device Explorer.**
1. Selecione o dispositivo necessário para migrar para outra versão.
1. Escolha **migrar**.
1. Selecione o modelo do dispositivo com o número da versão para onde pretende migrar o dispositivo e escolher **migrate**.

![Como migrar um dispositivo](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar versões de modelo de dispositivo na sua aplicação Azure IoT Central, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)
