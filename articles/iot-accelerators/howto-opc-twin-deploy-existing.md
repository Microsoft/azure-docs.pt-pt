---
title: Como implantar um módulo OPC Twin para um projeto Azure existente [ Microsoft Docs
description: Este artigo descreve como implantar a OPC Twin num projeto existente. Também pode aprender a resolver falhas de implantação.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73824122"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Implementar o OPC Twin para um projeto existente

O módulo OPC Twin funciona no IoT Edge e fornece vários serviços de borda para os serviços OPC Twin e Registry.

O microserviço OPC Twin facilita a comunicação entre operadores de fábrica e dispositivos de servidor espreitáceos oPC UA no chão da fábrica através de um módulo OPC Twin IoT Edge. O microserviço expõe os serviços da OPC UA (Navegar, Ler, Escrever e Executar) através da sua API REST. 

O microserviço de registo de dispositivos OPC UA fornece acesso a aplicações opc ua registadas e seus pontos finais. Os operadores e administradores podem registar e desregistar novas aplicações da OPC UA e navegar nas existentes, incluindo os seus pontos finais. Além da gestão de aplicações e pontos finais, o serviço de registo também cataloga módulos OPC Twin IoT Edge registados. O serviço API dá-lhe o controlo da funcionalidade do módulo de borda, por exemplo, iniciar ou parar a descoberta do servidor (serviços de digitalização), ou ativar novos gémeos endpoint que podem ser acedidos usando o microserviço OPC Twin.

O núcleo do módulo é a identidade supervisora. O supervisor gere o endpoint twin, que corresponde aos pontos finais do servidor OPC UA que são ativados usando a Correspondente API de registo opc UA. Este ponto final gémeos traduzem OPC UA JSON recebido do microserviço OPC Twin que corre na nuvem em mensagens binárias oPC UA, que são enviadas sobre um canal seguro e audato para o ponto final gerido. O supervisor também fornece serviços de descoberta que enviam eventos de descoberta de dispositivos para o serviço de embarque do dispositivo OPC UA para processamento, onde estes eventos resultam em atualizações para o registo da UA oPC.  Este artigo mostra-lhe como implementar o módulo OPC Twin para um projeto existente.

> [!NOTE]
> Para obter mais informações sobre detalhes e instruções de implementação, consulte o [repositório](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem extensões PowerShell e [AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instaladas. Se ainda não o fez, clone este repositório GitHub. Executar os seguintes comandos no PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Implementar serviços industriais de IoT para o Azure

1. Na sua sessão powerShell, corra:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Siga as instruções para atribuir um nome ao grupo de recursos da implementação e um nome para o site.   O script implanta os microserviços e as dependências da plataforma Azure no grupo de recursos na sua subscrição Azure.  O script também regista uma Aplicação no seu inquilino azure Ative Directory (AAD) para apoiar a autenticação baseada na OAUTH.  O destacamento levará vários minutos.  Um exemplo do que veria assim que a solução for implementada com sucesso:

   ![Implantação industrial IoT OPC Twin para projeto existente](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   A saída inclui o URL do ponto final público. 

3. Assim que o script estiver concluído com sucesso, `.env` selecione se pretende guardar o ficheiro.  Precisa do `.env` ficheiro ambiente se quiser ligar-se ao ponto final da nuvem utilizando ferramentas como a Consola ou implementar módulos para desenvolvimento e depuração.

## <a name="troubleshooting-deployment-failures"></a>Falhas de implementação de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de que utiliza um nome de grupo de recursos curto e simples.  O nome também é usado para nomear recursos como tal deve cumprir os requisitos de nomeação de recursos.  

### <a name="website-name-already-in-use"></a>Nome do site já em uso

É possível que o nome do site já esteja em uso.  Se encontrar este erro, terá de usar um nome de aplicação diferente.

### <a name="azure-active-directory-aad-registration"></a>Registo de Diretório Ativo Azure (AAD)

O script de implementação tenta registar duas aplicações AAD no Diretório Ativo Azure.  Dependendo dos seus direitos para com o inquilino selecionado da AAD, o destacamento pode falhar. Existem duas opções:

1. Se escolheu um inquilino da AAD de uma lista de inquilinos, reinicie o guião e escolha um diferente da lista.
2. Em alternativa, desloque um inquilino aAD privado noutra subscrição, reinicie o script e selecione para usá-lo.

> [!WARNING]
> NUNCA continue sem autenticação.  Se optar por fazê-lo, qualquer pessoa pode aceder aos seus pontos finais OPC Twin a partir da Internet sem autenticação.   Pode sempre escolher a opção de [implantação "local"](howto-opc-twin-deploy-dependencies.md) para chutar os pneus.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Implementar uma demonstração de serviços ioT industriais tudo-em-um

Em vez de apenas os serviços e dependências, também pode implementar uma demonstração tudo-em-um.  O tudo numa demonstração contém três servidores OPC UA, o módulo OPC Twin, todos os microserviços e uma amostra da Aplicação Web.  Destina-se a fins de demonstração.

1. Certifique-se de que tem um clone do repositório (ver acima). Abra um pedido PowerShell na raiz do repositório e corra:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Siga as instruções para atribuir um novo nome ao grupo de recursos e um nome para o site.  Uma vez implementado com sucesso, o script apresentará o URL do ponto final da aplicação web.

## <a name="deployment-script-options"></a>Opções de script de implementação

O guião tem os seguintes parâmetros:

```powershell
-type
```

O tipo de implantação (vm, local, demonstração)

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

Ou o nome da subscrição.

```powershell
-resourceGroupLocation
```

Opcional, uma localização de grupo de recursos. Se especificado, tentará criar um novo grupo de recursos neste local.

```powershell
-aadApplicationName
```

Um nome para o pedido de AAD para se registar.

```powershell
-tenantId
```

Inquilino da AAD para usar.

```powershell
-credentials
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar o OPC Twin num projeto existente, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Comunicação segura do Cliente UA da OPC e da OPC UA PLC](howto-opc-vault-secure.md)
