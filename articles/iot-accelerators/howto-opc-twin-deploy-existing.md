---
title: Como implantar um módulo OPC Twin para um projeto Azure existente Microsoft Docs
description: Este artigo descreve como implantar o OPC Twin num projeto existente. Também pode aprender a resolver falhas de implementação.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a4d005b3a4712cfff0321e7a16f49c0e882cc9e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282124"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Implementar o OPC Twin para um projeto existente

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

O módulo OPC Twin funciona no IoT Edge e fornece vários serviços de borda aos serviços OPC Twin e Registry.

O microserviço OPC Twin facilita a comunicação entre os operadores de fábrica e os dispositivos de servidores OPC UA no chão da fábrica através de um módulo OPC Twin IoT Edge. O microserviço expõe os serviços da OPC UA (Navegar, Ler, Escrever e Executar) através da sua API REST. 

O microserviço de registo de dispositivos OPC UA fornece acesso a aplicações OPC UA registadas e seus pontos finais. Os operadores e administradores podem registar e não registar novas aplicações OPC UA e navegar nas existentes, incluindo os seus pontos finais. Além da gestão de aplicações e pontos finais, o serviço de registo também cataloga módulos OPC Twin IoT Edge registados. O serviço API dá-lhe o controlo da funcionalidade do módulo edge, por exemplo, iniciar ou parar a descoberta do servidor (serviços de digitalização), ou ativar novos gémeos de ponto final que podem ser acedidos através do microserviço OPC Twin.

O núcleo do módulo é a identidade do Supervisor. O supervisor gere o twin ponto final, que corresponde aos pontos finais do servidor OPC UA que são ativados usando a API de registo OPC UA correspondente. Estes gémeos endpoint traduzem o OPC UA JSON recebido do microserviço OPC Twin que corre na nuvem em mensagens binárias OPC UA, que são enviadas por um canal seguro imponente para o ponto final gerido. O supervisor também fornece serviços de descoberta que enviam eventos de descoberta de dispositivos para o serviço de a bordo do dispositivo OPC UA para processamento, onde estes eventos resultam em atualizações para o registo da UA OPC.  Este artigo mostra-lhe como implantar o módulo OPC Twin para um projeto existente.

> [!NOTE]
> Para obter mais informações sobre detalhes e instruções de implantação, consulte o [repositório](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem extensões [PowerShell e AzureRM](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instaladas. Se ainda não o fez, clone este repositório do GitHub. Executar os seguintes comandos em PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Implantar serviços de IoT industrial para Azure

1. Na sua sessão PowerShell, corra:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Siga as instruções para atribuir um nome ao grupo de recursos da implantação e um nome para o site.   O script implementa os microserviços e as suas dependências da plataforma Azure no grupo de recursos na sua subscrição Azure.  O script também regista uma Aplicação no seu inquilino Azure Ative (AAD) para apoiar a autenticação baseada em OAUTH.  A implantação levará vários minutos.  Um exemplo do que se veria assim que a solução fosse implementada com sucesso:

   ![Industrial IoT OPC Twin implantar para projeto existente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A saída inclui o URL do ponto final público. 

3. Assim que o script estiver concluído com sucesso, selecione se pretende guardar o `.env` ficheiro.  Precisa do `.env` ficheiro ambiente se pretender ligar-se ao ponto final da nuvem utilizando ferramentas como a Consola ou implementar módulos para desenvolvimento e depuração.

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de resolução de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de que utiliza um nome de grupo de recursos curto e simples.  O nome é também utilizado para nomear recursos como tal, deve cumprir os requisitos de nomeação de recursos.  

### <a name="website-name-already-in-use"></a>Nome do site já em uso

É possível que o nome do site já esteja em uso.  Se encontrar este erro, tem de utilizar um nome de aplicação diferente.

### <a name="azure-active-directory-aad-registration"></a>Registo do Azure Ative Directory (AAD)

O script de implementação tenta registar duas aplicações AAD no Azure Ative Directory.  Dependendo dos seus direitos ao inquilino selecionado da AAD, a implantação pode falhar. Existem duas opções:

1. Se escolheu um inquilino da AAD de uma lista de inquilinos, reinicie o guião e escolha um diferente da lista.
2. Em alternativa, implementar um inquilino privado da AAD em outra subscrição, reiniciar o script e selecionar para usá-lo.

> [!WARNING]
> NUNCA continue sem autenticação.  Se optar por fazê-lo, qualquer pessoa pode aceder aos seus pontos finais OPC Twin a partir da Internet não autenticada.   Pode sempre escolher a [opção de implantação "local"](howto-opc-twin-deploy-dependencies.md) para pontapear os pneus.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Implementar uma demonstração de serviços de IoT industrial tudo-em-um

Em vez de apenas os serviços e dependências, você também pode implementar uma demonstração tudo-em-um.  O tudo numa demonstração contém três servidores OPC UA, o módulo OPC Twin, todos os microserviços e uma aplicação web de amostra.  Destina-se a fins de demonstração.

1. Certifique-se de que tem um clone do repositório (ver acima). Abra um pedido PowerShell na raiz do repositório e corra:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Siga as instruções para atribuir um novo nome ao grupo de recursos e um nome para o site.  Uma vez implementado com sucesso, o script mostrará o URL do ponto final da aplicação web.

## <a name="deployment-script-options"></a>Opções de script de implementação

O script toma os seguintes parâmetros:

```powershell
-type
```

O tipo de implantação (vm, local, demo)

```powershell
-resourceGroupName
```

Pode ser o nome de um grupo de recursos existente ou novo.

```powershell
-subscriptionId
```

Opcional, o ID de subscrição onde os recursos serão implantados.

```powershell
-subscriptionName
```

Ou o nome da assinatura.

```powershell
-resourceGroupLocation
```

Opcional, uma localização de grupo de recursos. Se especificado, tentará criar um novo grupo de recursos neste local.

```powershell
-aadApplicationName
```

Um nome para o pedido de inscrição da AAD.

```powershell
-tenantId
```

Inquilino da AAD para usar.

```powershell
-credentials
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar o OPC Twin para um projeto existente, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Comunicação segura do Cliente UA da OPC e da OPC UA PLC](howto-opc-vault-secure.md)
