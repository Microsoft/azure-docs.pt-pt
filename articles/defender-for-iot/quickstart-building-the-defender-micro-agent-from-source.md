---
title: 'Quickstart: Construa o micro agente Defender a partir do código fonte (Pré-visualização)'
description: Neste quickstart, conheça o Micro Agent que inclui uma infraestrutura que pode ser usada para personalizar a sua distribuição.
ms.date: 1/18/2021
ms.topic: quickstart
ms.openlocfilehash: a3a8f82989d6abbaf2657e4b45638c77b3b2f704
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384602"
---
# <a name="quickstart-build-the-defender-micro-agent-from-source-code-preview"></a>Quickstart: Construa o micro agente Defender a partir do código fonte (Pré-visualização)

O Micro Agent inclui uma infraestrutura, que pode ser usada para personalizar a sua distribuição. Para ver uma lista dos parâmetros de configuração disponíveis, consulte o `configs/LINUX_BASE.conf` ficheiro.

Para uma única distribuição, modifique o `.conf` ficheiro base. 

Se necessitar de mais do que uma distribuição, pode herdar da configuração base e anular os seus valores. 

Para anular os valores:

1. Criar um novo `.dist` ficheiro.

1. Adicione `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` ao topo.
 
1. Defina novos valores para o que precisar, exemplo: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Dê `.dist` ao arquivo uma referência ao edifício. Por exemplo, 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="prerequisites"></a>Pré-requisitos

1. Contacte o seu gestor de conta para solicitar acesso ao Defender para código fonte IoT.
 
1. Clone ou extraia o código de origem para uma pasta no disco.

1. Navegue para o diretório.

1. Puxe os submodulos utilizando o seguinte código:

    ```bash
    git submodule update --init
    ```
    
1. Em seguida, puxe os submodulos para o Azure IoT SDK com o seguinte código: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Adicione uma permissão de execução e execute o script de configuração do ambiente do desenvolvedor:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Criar um diretório para as saídas de construção: 

    ```bash
    mkdir cmake 
    ```

1. (Opcional) Descarregue e instale [o VSCode](https://code.visualstudio.com/download ) 

1. (Opcional) Instale a [extensão C/C++](https://code.visualstudio.com/docs/languages/cpp ) para VSCode.- Nenhuma

## <a name="baseline-configuration-signing"></a>Assinatura de configuração de linha de base 

O agente verifica a autenticidade dos ficheiros de configuração que são colocados no disco para mitigar a adulteração, por padrão.

Pode parar este processo definindo a bandeira do `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` pré-processo.

Não recomendamos desligar a verificação de assinaturas para ambientes de produção. 

Se necessitar de uma configuração diferente para cenários de produção, contacte a equipa do Defender para a equipa IoT. 

## <a name="building-the-defender-iot-micro-agent"></a>Construção do Micro Agente Defensor IoT 

1. Abra o diretório com VSCode 

1. Navegue para o `cmake` diretório. 

1. Execute o seguinte comando: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Por exemplo: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Passos seguintes

[Configure o seu Azure Defender para a solução IoT](quickstart-configure-your-solution.md).
