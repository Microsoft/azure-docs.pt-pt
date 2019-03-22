---
title: Saiba como módulos de executar a lógica nos seus dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Os módulos do Azure IoT Edge são unidades em contentores de lógica que podem ser implementados e geridos remotamente para que possam executar lógica de negócios do IoT Edge dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d1e2e35dafd90c16e9d0dbf38afb1e981653d1fe
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311106"
---
# <a name="understand-azure-iot-edge-modules"></a>Compreender os módulos do Azure IoT Edge

O Azure IoT Edge permite-lhe implementar e gerir a lógica de negócios na borda sob a forma de *módulos*. Módulos do IoT Edge do Azure são a menor unidade de computação gerenciada pelo IoT Edge e podem conter os serviços do Azure (por exemplo, o Azure Stream Analytics) ou o seu próprio código específico da solução. Para compreender como os módulos são desenvolvidos, implementados e mantidos, ajuda a pensar em quatro elementos conceituais de um módulo:

* R **imagem módulo** é um pacote que contém o software que define um módulo.
* R **instância de módulo** é a unidade específica de computação em execução a imagem de módulo num dispositivo IoT Edge. A instância de módulo é iniciada pelo runtime do IoT Edge.
* R **identidade do módulo** é uma parte da informação (incluindo credenciais de segurança) armazenada num IoT Hub, que está associado a cada instância de módulo.
* R **módulo duplo** são documentos JSON armazenados num IoT Hub, que contém informações de estado para uma instância de módulo, incluindo os metadados, configurações e condições. 

## <a name="module-images-and-instances"></a>Imagens de módulo e instâncias

Imagens de módulo do IoT Edge contêm aplicações que tiram partido da gestão, segurança e recursos de comunicação do runtime do IoT Edge. Pode desenvolver suas próprias imagens de módulo ou exportar um a partir de um serviço do Azure suportado, como o Azure Stream Analytics.
As imagens de existem na nuvem e podem ser atualizadas, alteradas e implementadas em soluções diferentes. Por exemplo, existe um módulo que utiliza machine learning para prever a saída da linha de produção como uma imagem separada que um módulo que utiliza a imagem digitalizada para controlar um drone. 

Sempre que uma imagem do módulo é implementada num dispositivo e iniciada pelo runtime do IoT Edge, é criada uma nova instância desse módulo. Dois dispositivos em diferentes partes do mundo poderiam usar a mesma imagem do módulo. No entanto, cada dispositivo teria sua própria instância do módulo quando o módulo é iniciado no dispositivo. 

![Diagrama - imagens de módulo na cloud, instâncias de módulo em dispositivos](./media/iot-edge-modules/image_instance.png)

Na implementação, as imagens de módulos existam como imagens de contentor num repositório e instâncias de módulo são contentores em dispositivos. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identidades do módulo

Quando é criada uma nova instância de módulo pelo runtime do IoT Edge, a instância está associada uma identidade de módulo correspondente. A identidade do módulo é armazenada no IoT Hub e como o âmbito de endereçamento e segurança para todos os locais e comunicações da cloud para essa instância do módulo específico.

A identidade associada uma instância de módulo depende da identidade do dispositivo no qual a instância está em execução e o nome fornecidos para esse módulo na sua solução. Por exemplo, se chamar `insight` um módulo que utiliza um Azure Stream Analytics e implementá-la num dispositivo chamado `Hannover01`, o runtime do IoT Edge cria uma identidade de módulo correspondente, chamada `/devices/Hannover01/modules/insight`.

Sem dúvida, em cenários quando precisa implantar uma imagem de módulo várias vezes no mesmo dispositivo, pode implementar a mesma imagem várias vezes com nomes diferentes.

![Diagrama - identidades do módulo são exclusivas dentro de dispositivos e em todos os dispositivos](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Duplos de módulo

Cada instância de módulo também tem um módulo duplo correspondente que pode utilizar para configurar a instância de módulo. A instância e o duplo estão associados entre si através de identidade do módulo. 

Um módulo duplo é um documento JSON que armazena as propriedades de informações e a configuração do módulo. Esse conceito faz um paralelo com o [dispositivo duplo](../iot-hub/iot-hub-devguide-device-twins.md) conceito do IoT Hub. A estrutura de um módulo duplo é o mesmo que um dispositivo duplo. As APIs usadas para interagir com os dois tipos de duplos também são os mesmos. A única diferença entre as duas é a identidade utilizada para instanciar o SDK do cliente. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Funcionalidades offline

O Azure IoT Edge suporta operações offline nos seus dispositivos IoT Edge. Estas capacidades estão limitadas por agora. Capacidades offline adicionais estão disponíveis em pré-visualização pública. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge, dispositivos, módulos e dispositivos de subordinados](offline-capabilities.md).

Módulos do IoT Edge podem ser offline por períodos prolongados, desde que são cumpridos os seguintes requisitos: 

* **Mensagem time-to-live (TTL) expirou não**. O valor predefinido para a mensagem TTL é de duas horas, mas pode ser alterada superior ou inferior no Store e reencaminhar a configuração no IoT Edge definições do hub. 
* **Módulos não precisam de autenticar com o hub IoT Edge, quando estiver offline**. Módulos só podem autenticar com os hubs IoT Edge que tenham uma conexão ativa com um hub IoT. Módulos tem de autenticar se eles forem reiniciados por qualquer motivo. Módulos podem ainda enviar mensagens para o hub IoT Edge após o respetivo token SAS expirou. Quando a retoma a conectividade, o hub do IoT Edge solicita um novo token do módulo e validá-lo com o hub IoT. Se tiver êxito, o IoT Edge hub encaminha as mensagens do módulo armazenado, até mesmo as mensagens que foram enviadas enquanto o token do módulo expirou. 
* **O módulo que enviadas as mensagens ao mesmo tempo offline ainda está funcional quando sai de conectividade**. Após a restabelecer ligação ao IoT Hub, hub do IoT Edge tem de validar um novo token de módulo (se anterior expirado) antes que possa reencaminhar as mensagens do módulo. Se o módulo não está disponível para fornecer um novo token, o hub IoT Edge não possam agir em às mensagens armazenadas do módulo. 
* **O hub do IoT Edge tem espaço em disco para armazenar as mensagens**. Por predefinição, as mensagens são armazenadas no sistema de ficheiros do contentor de hub do IoT Edge. Há uma opção de configuração para especificar um volume montado para armazenar as mensagens em vez disso. Em ambos os casos, deve haver espaço disponível para armazenar as mensagens para entrega diferida ao IoT Hub.  


## <a name="next-steps"></a>Passos Seguintes
 - [Compreender os requisitos e as ferramentas para desenvolver módulos do IoT Edge](module-development.md)
 - [Compreender o tempo de execução do Azure IoT Edge e respetiva arquitetura](iot-edge-runtime.md)

