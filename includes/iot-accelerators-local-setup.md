---
title: incluir ficheiro
description: incluir ficheiro
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184692"
---
## <a name="download-the-source-code"></a>Descarregue o código fonte

Os repositórios de código de código de monitorização remota incluem o código fonte e os ficheiros de configuração do Docker que você precisa para executar as imagens do Docker de microserviços.

Para clonar e criar uma versão local do repositório, use o seu ambiente de linha de comando para navegar para uma pasta adequada na sua máquina local. Em seguida, executar um dos seguintes conjuntos de comandos para clonar o repositório .NET:

Para descarregar a versão mais recente das implementações de microserviços .NET, executar:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Estes comandos descarregam o código fonte para todos os microserviços, além dos scripts que utiliza para executar os microserviços localmente. Embora não precise do código fonte para executar os microserviços no Docker, o código fonte é útil se planeia posteriormente modificar o acelerador de soluções e testar as suas alterações localmente.

## <a name="deploy-the-azure-services"></a>Implementar os serviços Azure

Embora este artigo mostre como gerir os microserviços localmente, eles dependem dos serviços azure que funcionam na nuvem. Utilize o seguinte script para implantar os serviços Azure. Os seguintes exemplos de scripts assumem que está a usar o repositório .NET numa máquina Windows. Se estiver a trabalhar noutro ambiente, ajuste os caminhos, as extensões de ficheiros e os separadores de caminhos adequadamente.

### <a name="create-new-azure-resources"></a>Criar novos recursos Azure

Se ainda não criou os recursos azure necessários, siga estes passos:

1. No seu ambiente de linha de comando, navegue para a pasta **\services\scripts\local\launch** na sua cópia clonada do repositório.

1. Execute os seguintes comandos para instalar a ferramenta **CLI do PCS** e iniciar sessão na sua conta Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Execute o script **start.cmd.** O script solicita-lhe as seguintes informações:
   * Um nome de solução.
   * A subscrição do Azure que deve utilizar.
   * A localização do datacenter Azure a utilizar.

     O script cria um grupo de recursos em Azure com o seu nome de solução. Este grupo de recursos contém os recursos Azure que o acelerador de solução utiliza. Pode eliminar este grupo de recursos assim que deixar de precisar dos recursos correspondentes.

     O script também adiciona um conjunto de variáveis ambientais com um **PCS** prefixo à sua máquina local. Estas variáveis ambientais fornecem os detalhes para a Monitorização Remota para poder ler a partir de um recurso Azure Key Vault. Este recurso Key Vault é onde a Monitorização Remota irá ler os seus valores de configuração.

     > [!TIP]
     > Quando o script completa, também guarda as variáveis ambientais para um ficheiro chamado ** \<"your home folder\>\\".pcs\\\<solution name\>.env**. Pode usá-los para futuras implementações de aceleradores de soluções. Note que quaisquer variáveis ambientais definidas na sua máquina local sobrepõem valores nos **scripts de\\serviços\\ficheiro .env local\\** quando executa **docker-compose**.

1. Saia do seu ambiente de linha de comando.

### <a name="use-existing-azure-resources"></a>Utilizar os recursos azure existentes

Se já criou os recursos Azure necessários, crie as variáveis ambientais correspondentes na sua máquina local.
Definir as variáveis ambientais para o seguinte:
* **PCS_KEYVAULT_NAME** - Nome do recurso Azure Key Vault
* **PCS_AAD_APPID** - O ID da aplicação AAD
* **PCS_AAD_APPSECRET** - O segredo da aplicação aAD

Os valores de configuração serão lidos a partir deste recurso Azure Key Vault. Estas variáveis ambientais podem ser guardadas na ** \<sua pasta\>\\inicial\\\<.pcs nome de solução\>.env** da implementação. Note que as variáveis ambientais definidas na sua máquina local sobrepõem os valores nos **scripts de\\serviços\\ficheiro .env local\\** quando executa **docker-compor**.

Parte da configuração necessária pelo microserviço é armazenada num caso de **Key Vault** que foi criado na implementação inicial. As variáveis correspondentes no cofre devem ser modificadas conforme necessário.