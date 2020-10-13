---
title: Quickstart para usar configuração de aplicativos Azure com aplicações Python Microsoft Docs
description: Neste quickstart, crie uma aplicação Python com Configuração de Aplicações Azure para centralizar o armazenamento e gestão das definições de aplicações separadas do seu código.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 954f4edcd10d701d00d9cd23280aaac7c287992d
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997471"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Quickstart: Criar uma aplicação Python com configuração de aplicação Azure

Neste arranque rápido, utilizará a Configuração de Aplicações Azure para centralizar o armazenamento e gestão das definições de aplicações utilizando a biblioteca de [clientes de Configuração de Aplicações Azure para Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- Python 2.7, ou 3.5 ou mais tarde - Para obter informações sobre a configuração do Python no Windows, consulte a [piton na documentação]( https://docs.microsoft.com/windows/python/) do Windows

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Configuration Explorer**  >  **Crie**  >  **valor-chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto.

8. Selecione **Aplicar**.

## <a name="setting-up-the-python-app"></a>Criação da aplicação Python

1. Neste tutorial, irá criar um novo diretório para o projeto denominado *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Mude para o *recém-criado diretório de configuração de aplicativos-quickstart.*

    ```console
    cd app-configuration-quickstart
    ```

1. Instale a biblioteca do cliente de Configuração de Aplicação Azure utilizando o `pip install` comando.

    ```console
    pip install azure-appconfiguration
    ```

1. Crie um novo ficheiro chamado *app-configuration-quickstart.py* no diretório *de configuração-quickstart de configuração de aplicações* e adicione o seguinte código:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Os cortes de código neste quickstart irão ajudá-lo a começar com a biblioteca de clientes de Configuração de Aplicações para Python. Para a sua aplicação, deve também considerar o manuseamento de exceções de acordo com as suas necessidades. Para saber mais sobre o manuseamento de exceções, consulte a nossa [documentação Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Configure a sua cadeia de conexão de configuração de aplicação

1. Desaprote uma variável ambiental chamada **AZURE_APP_CONFIG_CONNECTION_STRING**, e desaprote-a na chave de acesso à sua loja de Configuração de Aplicações. Na linha de comando, executar o seguinte comando:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Reinicie o pedido de comando para permitir que a alteração entre em vigor. Imprima o valor da variável ambiente para validar que está definido corretamente.

## <a name="code-samples"></a>Exemplos de código

Os fragmentos de código de amostra nesta secção mostram-lhe como realizar operações comuns com a biblioteca de clientes de Configuração de Aplicação para Python. Adicione estes fragmentos de código ao `try` bloco em *app-configuration-quickstart.py* ficheiro que criou anteriormente.

> [!NOTE]
> A biblioteca de clientes de Configuração de Aplicações refere-se a um objeto de valor-chave como `ConfigurationSetting` . Portanto, neste artigo, os **valores-chave** na loja de configuração de aplicações serão referidos como **definições de configuração**.

* [Conecte-se a uma loja de configuração de aplicativos](#connect-to-an-app-configuration-store)
* [Obtenha uma configuração](#get-a-configuration-setting)
* [Adicione uma definição de configuração](#add-a-configuration-setting)
* [Obtenha uma lista de configurações](#get-a-list-of-configuration-settings)
* [Bloqueie uma definição de configuração](#lock-a-configuration-setting)
* [Desbloqueie uma definição de configuração](#unlock-a-configuration-setting)
* [Atualizar uma definição de configuração](#update-a-configuration-setting)
* [Excluir uma definição de configuração](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

O seguinte corte de código cria uma instância de **AzureAppConfigurationClient** usando a cadeia de ligação armazenada nas variáveis ambientais.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Obtenha uma configuração

O seguinte corte de código recupera uma configuração de definição pelo `key` nome.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Adicione uma definição de configuração

O seguinte corte de código cria um `ConfigurationSetting` objeto com campos e invoca o `key` `value` `add_configuration_setting` método. Este método lançará uma exceção se tentar adicionar uma definição de configuração que já existe na sua loja. Se quiser evitar esta exceção, o [método set_configuration_setting](#update-a-configuration-setting) pode ser utilizado.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Obtenha uma lista de configurações

O seguinte corte de código recupera uma lista de configurações. Os `key_filter` argumentos e argumentos podem ser fornecidos para `label_filter` filtrar valores-chave baseados `key` e `label` respectivamente. Para obter mais informações sobre a filtragem, consulte como consultar as [definições de configuração](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Bloqueie uma definição de configuração

O estado de bloqueio de um valor-chave na Configuração da Aplicação é denotado pelo `read_only` atributo do `ConfigurationSetting` objeto. Se `read_only` `True` for, a regulação está bloqueada. O `set_read_only` método pode ser invocado com argumento para bloquear a `read_only=True` definição de configuração.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Desbloqueie uma definição de configuração

Se o `read_only` atributo de um `ConfigurationSetting` `False` for, a definição é desbloqueada. O `set_read_only` método pode ser invocado com argumento para desbloquear a `read_only=False` definição de configuração.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Atualizar uma definição de configuração

O `set_configuration_setting` método pode ser usado para atualizar uma definição existente ou criar uma nova definição. O seguinte corte de código altera o valor de uma definição de configuração existente.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Excluir uma definição de configuração

O seguinte corte de código elimina uma definição de configuração pelo `key` nome.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Executar a aplicação

Neste quickstart, criou uma aplicação Python que utiliza a biblioteca de clientes Azure App Configuration para recuperar uma definição de configuração criada através do portal Azure, adicionar uma nova definição, recuperar uma lista de configurações existentes, bloquear e desbloquear uma definição, atualizar uma definição e, finalmente, apagar uma definição.

Neste momento, o seu ficheiro *app-configuration-quickstart.py* deve ter o seguinte código:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

Na janela da consola, navegue para o diretório que contém o ficheiro *app-configuration-quickstart.py* e execute o seguinte comando python para executar a aplicação:

```console
python app-configuration-quickstart.py
```

Deverá ver o seguinte resultado:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Limpar recursos


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de Configuração de Aplicações e aprendeu a aceder aos valores-chave a partir de uma aplicação Python.

Para amostras de código adicionais, visite:

> [!div class="nextstepaction"]
> [Amostras de biblioteca de clientes de configuração de aplicativos Azure](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)