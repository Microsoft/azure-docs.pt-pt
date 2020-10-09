---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184692"
---
## <a name="download-the-source-code"></a>Descarregue o código fonte

Os repositórios de código fonte de monitorização remota incluem o código fonte e os ficheiros de configuração do Docker que precisa para executar as imagens do Docker dos microserviços.

Para clonar e criar uma versão local do repositório, use o ambiente da linha de comando para navegar para uma pasta adequada na sua máquina local. Em seguida, executar um dos seguintes conjuntos de comandos para clonar o repositório .NET:

Para descarregar a versão mais recente das implementações de microserviços .NET, corra:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Estes comandos descarregam o código fonte para todos os microserviços, além dos scripts que utiliza para executar os microserviços localmente. Embora não precise do código fonte para executar os microserviços em Docker, o código fonte é útil se mais tarde planeia modificar o acelerador de solução e testar as suas alterações localmente.

## <a name="deploy-the-azure-services"></a>Implementar os serviços Azure

Embora este artigo mostre como executar os microserviços localmente, eles dependem dos serviços Azure que funcionam na nuvem. Utilize o seguinte script para implantar os serviços Azure. Os seguintes exemplos de script assumem que está a utilizar o repositório .NET numa máquina Windows. Se estiver a trabalhar noutro ambiente, ajuste os caminhos, as extensões de ficheiros e os separadores do caminho adequadamente.

### <a name="create-new-azure-resources"></a>Criar novos recursos Azure

Se ainda não criou os recursos Azure necessários, siga estes passos:

1. No seu ambiente de linha de comando, navegue para a pasta **\services\scripts\local\lançamento** na sua cópia clonada do repositório.

1. Executar os seguintes comandos para instalar a ferramenta CLI **dos computadores** e iniciar súm na sua conta Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Executar o script **start.cmd.** O script solicita-lhe as seguintes informações:
   * Um nome de solução.
   * A subscrição do Azure que deve utilizar.
   * A localização do centro de dados Azure para usar.

     O script cria um grupo de recursos em Azure com o nome da sua solução. Este grupo de recursos contém os recursos Azure que o acelerador de solução utiliza. Pode eliminar este grupo de recursos uma vez que já não necessite dos recursos correspondentes.

     O script também adiciona um conjunto de variáveis ambientais com um **PCS** prefixo à sua máquina local. Estas variáveis ambientais fornecem os detalhes para que a Monitorização Remota seja capaz de ler a partir de um recurso Azure Key Vault. Este recurso Key Vault é onde a Monitorização Remota irá ler os seus valores de configuração.

     > [!TIP]
     > Quando o script termina, também guarda as variáveis ambientais para um ficheiro chamado ** \<your home folder\> \\ .pcs \\ \<solution name\> .env**. Pode usá-las para futuras implementações de aceleradores de soluções. Note que quaisquer variáveis ambientais definidas na sua máquina local sobrepõem os valores nos ** \\ scripts de serviços \\ locais \\ .env** file quando você executar **docker-compor**.

1. Saia do seu ambiente de linha de comando.

### <a name="use-existing-azure-resources"></a>Utilizar os recursos Azure existentes

Se já criou os recursos Azure necessários, crie as variáveis ambientais correspondentes na sua máquina local.
Definir as variáveis ambientais para as seguintes:
* **PCS_KEYVAULT_NAME** - Nome do recurso Azure Key Vault
* **PCS_AAD_APPID** - O ID da aplicação da AAD
* **PCS_AAD_APPSECRET** - O segredo da aplicação da AAD

Os valores de configuração serão lidos a partir deste recurso Azure Key Vault. Estas variáveis ambientais podem ser guardadas no ficheiro ** \<your home folder\> \\ \\ \<solution name\> .pcs .env** da implementação. Note que as variáveis ambientais definidas na sua máquina local sobrepõem os valores nos ** \\ scripts de serviços \\ locais \\ .env** file quando você executar **docker-compor**.

Algumas das configurações necessárias pelo microserviço são armazenadas num caso de **Key Vault** que foi criado na implementação inicial. As variáveis correspondentes no keyvault devem ser modificadas conforme necessário.