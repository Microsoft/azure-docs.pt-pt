---
title: Como implementar o módulo de gestão de dispositivos do Azure IoT OPC UA para um projeto existente | Documentos da Microsoft
description: Como implementar duplo de OPC para um projeto existente.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9b1dee8ea43d01825449cb6010fbfa62e4715cff
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047267"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Implementar duplo de OPC para um projeto existente

O módulo de OPC dispositivo duplo é executado no IoT Edge e fornece vários serviços de borda para os serviços de registo e OPC dispositivo duplo. 

O serviço de micro do OPC dispositivo duplo facilita a comunicação entre as operadoras de fábrica e dispositivos de servidor OPC UA no chão de fábrica através de um módulo do IoT Edge do OPC duplo. O serviço micro expõe serviços OPC UA (procura, leitura, escrita e execução) através da sua API de REST. 

O serviço de micro do registo de dispositivo do OPC UA fornece acesso a aplicativos de OPC UA registrados e os respetivos pontos finais. Operadores e os administradores podem registar e anular o registo de novos aplicativos de OPC UA e procurar os existentes, incluindo os respetivos pontos finais. Além de gerenciamento de ponto final e de aplicativos, o serviço de registo também cataloga módulos registados do OPC dispositivo duplo do IoT Edge. A API de serviço dá-lhe controlo da borda módulo funcionalidade, por exemplo, iniciar ou parar a deteção de servidores (Serviços de análise) ou ativar o novo duplos de ponto de extremidade que podem ser acedidos através do serviço de micro OPC duplo.

O núcleo do módulo é a identidade do Supervisor. O supervisor gere duplo de ponto final, que corresponde a pontos finais do servidor OPC UA ativados com a API de registro OPC UA correspondente. Este duplos de ponto final traduzir OPC UA JSON recebidos do serviço micro OPC duplo em execução na cloud para mensagens binárias de OPC UA, o que são enviadas através de um canal seguro com monitoração de estado para o ponto de extremidade gerenciado. O supervisor também fornece serviços de deteção que enviam eventos de deteção de dispositivo para o serviço de integração de dispositivo do OPC UA para processamento, em que esses eventos resultam em atualizações para o registo de OPC UA.  Este artigo mostra-lhe como implementar o módulo duplo de OPC para um projeto existente. 

> [!NOTE]
> Para obter mais informações sobre os detalhes da implementação e instruções, consulte o GitHub [repositório](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem o PowerShell e [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) extensões instaladas.   Se não o tiver feito isso ainda, clone o repositório do GitHub.  Abra uma linha de comandos ou terminal e execute:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Implementar os serviços de IoT industriais para o Azure

1. Na linha de comandos aberta ou no terminal, execute:

   ```bash
   deploy
   ```

2. Siga as instruções para atribuir um nome para o grupo de recursos da implantação e um nome para o Web site.   O script implementa os microsserviços e suas dependências de plataforma do Azure para o grupo de recursos na sua subscrição do Azure.  O script também registra uma aplicação no seu inquilino do Azure Active Directory (AAD) para suportar a autenticação com base em OAUTH.  Implementação demorará alguns minutos.  Um exemplo de vista assim que a solução é implementada com êxito:

   ![Duplo de OPC IoT industrial implementar projeto existente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A saída inclui o URL do ponto final público. 

3. Assim que o script ser concluído com êxito, selecione se pretende guardar o ficheiro. env.  Terá do ficheiro de ambiente. env se pretender ligar ao ponto final da cloud com ferramentas como o Console ou implementar módulos para desenvolvimento e depuração.

## <a name="troubleshooting-deployment-failures"></a>Resolução de problemas de falhas de implementação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de que use um nome de grupo de recursos de curto e simples.  O nome é utilizado, também, para recursos de nome como tal, que ele deve estar em conformidade com requisitos de nomes de recursos.  

### <a name="website-name-already-in-use"></a>Nome do Web site já em utilização

É possível que o nome do Web site já está em utilização.  Caso se depare com este erro, terá de utilizar um nome de aplicação diferente.

### <a name="azure-active-directory-aad-registration"></a>Registo do Azure Active Directory (AAD)

O script de implementação tenta registrar duas aplicações do AAD no Azure Active Directory.  Consoante os seus direitos para o inquilino do AAD selecionado, a implementação poderá falhar. Existem duas opções:

1. Se optou por um inquilino do AAD de uma lista de inquilinos, reinicie o script e escolher um diferente a partir da lista.
2. Em alternativa, implemente um inquilino do AAD privado noutra subscrição, reinicie o script e selecione usá-lo.

> [!WARNING]
> NUNCA continue sem autenticação.  Se optar por fazê-lo, qualquer pessoa pode aceder aos pontos finais de gestão de dispositivos de OPC da Internet não autenticada.   Pode sempre escolher a [opção de implementação de "local"](howto-opc-twin-deploy-dependencies.md) a idéia sobre o.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Implementar uma demonstração de serviços de IoT industrial do tudo-em-um

Em vez de apenas os serviços e dependências também pode implementar uma demonstração de tudo-em-um.  O tudo numa demonstração contém três servidores OPC UA, o módulo duplo de OPC, todos os microsserviços e um exemplo de aplicativo Web.  Destina-se para fins de demonstração.

1. Certifique-se de que tem um clone do repositório (consultar acima). Abra uma linha de comandos ou terminal na raiz do repositório e execute:

   ```bash
   deploy -type demo
   ```

2. Siga as instruções para atribuir um novo nome para o grupo de recursos e um nome para o Web site.  Depois de implementado com êxito, o script irá apresentar o URL do ponto de extremidade de aplicativo web.

## <a name="deployment-script-options"></a>Opções de script de implementação

O script aceita os seguintes parâmetros:

```bash
-type
```

O tipo de implementação (demonstração de local, vm)

```bash
-resourceGroupName
```

Pode ser o nome de um existente ou um grupo de recursos.

```bash
-subscriptionId
```

Opcional, o ID de subscrição onde serão possível implementar recursos.

```bash
-subscriptionName
```

Ou o nome da subscrição.

```bash
-resourceGroupLocation
```

Opcional, uma localização do grupo de recursos. Se for especificado, irá tentar criar um novo grupo de recursos nesta localização.

```bash
-aadApplicationName
```

Um nome para a aplicação do AAD registar com. 

```bash
-tenantId
```

Inquilino do AAD para utilizar.

```bash
-credentials
```

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como implementar duplo de OPC para um projeto existente, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Proteger a comunicação do Cliente OPC e OPC PLC ](howto-opc-vault-deploy-existing-client-plc-communication.md)
