---
title: Tutorial para encomendar Caixa de Dados Azure / Microsoft Docs
description: Aprenda os pré-requisitos de implementação e como encomendar um Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392489"
---
# <a name="tutorial-order-azure-data-box"></a>Tutorial: Encomendar o Azure Data Box

O Azure Data Box é uma solução híbrida que permite importar os dados no local para o Azure, de forma rápida, fácil e fiável. Transfira os seus dados para um dispositivo de armazenamento de 80 TB fornecido pela Microsoft (capacidade utilizável) e, em seguida, envie novamente o dispositivo. Estes dados são então carregados para o Azure.

Este tutorial descreve como pode encomendar um Azure Data Box. Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos para implementar o Data Box
> * Encomendar um Data Box
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/portal)

Preencha os seguintes pré-requisitos de configuração para o serviço de Caixa de Dados e dispositivo antes de implementar o dispositivo:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Pode entrar no Azure e executar os comandos Azure CLI de uma de duas maneiras:

* Pode instalar o CLI e executar os comandos CLI localmente.
* Pode executar comandos CLI a partir do portal Azure, em Azure Cloud Shell.

Utilizamos o Azure CLI através do Windows PowerShell para o tutorial, mas é livre de escolher qualquer uma das opções.

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

* Instale a versão [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 2.0.67 ou posterior. Em alternativa, pode [instalar-se utilizando o MSI.](https://aka.ms/installazurecliwindows)

#### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra uma janela de comando Windows PowerShell e inicie sessão no Azure com o comando [de login az:](/cli/azure/reference-index#az-login)

```azurecli
PS C:\Windows> az login
```

Aqui está a saída de um sucesso de sindesção:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>Instale a extensão CLI da Caixa de Dados Azure

Antes de poder utilizar os comandos CLI da Caixa de Dados Azure, tem de instalar a extensão. As extensões Azure CLI dão-lhe acesso a comandos experimentais e pré-lançamento que ainda não foram enviados como parte do núcleo CLI. Para obter mais informações sobre extensões, consulte [extensões de utilização com Azure CLI](/cli/azure/azure-cli-extensions-overview).

Para instalar a extensão para Azure Data Box, execute o seguinte `az extension add --name databox` comando:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Se a extensão for instalada com sucesso, verá a seguinte saída:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

Você pode usar [Azure Cloud Shell](https://shell.azure.com/), um ambiente de concha interativa azure hospedado, através do seu navegador para executar comandos CLI. A Azure Cloud Shell suporta os serviços Bash ou Windows PowerShell com os serviços Azure. O Azure CLI está pré-instalado e configurado para ser utilizado com a sua conta. Clique no botão Cloud Shell no menu na secção superior direita do portal Azure:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

O botão lança uma concha interativa que pode usar para executar os passos descritos neste artigo de como fazer.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Encomendar o Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Faça os seguintes passos no portal Azure para encomendar um dispositivo.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão neste URL: [https://portal.azure.com](https://portal.azure.com).
2. Clique em **+ Criar um recurso** e procure *Azure Data Box*. Clique em **Azure Data Box**.

   [![Procurar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Clique em **Criar**.

4. Verifique se o serviço Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e selecione **Aplicar**.

    |Definição  |Valor  |
    |---------|---------|
    |Subscrição     | Selecione uma subscrição EA, CSP ou Azure Sponsorship para o serviço Data Box. <br> A subscrição está ligada à sua conta de faturação.       |
    |Tipo de transferência     | Selecione **Importar para o Azure**.        |
    |País/região de origem    |    Selecione o país/região onde os dados residem atualmente.         |
    |Região do Azure de destino     |     Selecione a região do Azure para onde pretende transferir os dados.        |

5. Selecione **Data Box**. A capacidade máxima utilizável para uma única encomenda é de 80 TB. Pode criar várias encomendas para tamanhos de dados maiores.

      [![Selecione a opção Caixa de Dados 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. Em **Encomenda**, especifique os **Detalhes da encomenda**. Introduza ou selecione as seguintes informações e selecione **Seguinte**.

    |Definição  |Valor  |
    |---------|---------|
    |Nome     |  Forneça um nome amigável para controlar a encomenda. <br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número.      |
    |Grupo de recursos     |    Utilize um grupo de recursos existente ou crie um novo. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto.         |
    |Região do Azure de destino     | Selecione uma região para a sua conta de armazenamento. <br> Para obter mais informações, aceda a [Disponibilidade de região](data-box-overview.md#region-availability).        |
    |Destino de armazenamento     | Escolha entre a conta de armazenamento ou os discos geridos ou ambos. <br> Com base na região do Azure especificada, selecione uma ou mais contas de armazenamento na lista filtrada de uma conta de armazenamento existente. O Data Box pode ser associado a um máximo de 10 contas de armazenamento. <br> Também pode criar uma nova conta de **Fins gerais v1**, **Fins gerais v2** ou **Conta de armazenamento de blobs**. <br>As contas de armazenamento com redes virtuais são suportadas. Para permitir que o serviço Data Box funcione com contas de armazenamento protegidas, ative os serviços fidedignos nas definições de firewall de rede da conta de armazenamento. Para mais informações, consulte como [adicionar a Azure Data Box como um serviço de confiança](../storage/common/storage-network-security.md#exceptions).|

    Se estiver a utilizar a conta de armazenamento como o destino de armazenamento, verá a seguinte captura de ecrã:

    ![Pedido de caixa de dados para conta de armazenamento](media/data-box-deploy-ordered/order-storage-account.png)

    Se utilizar a Data Box para criar discos geridos a partir dos discos rígidos virtuais (VHDs) no local, também terá de fornecer as seguintes informações:

    |Definição  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Crie novos grupos de recursos se quiser criar discos geridos a partir dos VHDs no local. Só pode utilizar um grupo de recursos existente se o grupo de recursos tiver sido criado anteriormente ao criar uma encomenda de Caixa de Dados para o disco gerido pelo serviço Data Box. <br> Especifique vários grupos de recursos separados por ponto e vírgula. É suportado um máximo de 10 grupos de recursos.|

    ![Pedido de caixa de dados para disco gerido](media/data-box-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para os discos geridos é utilizada como uma conta de armazenamento de teste. O serviço Data Box carrega os VHDs como blobs de páginas na conta de armazenamento de teste antes de os converter em discos geridos e de os mover para os grupos de recursos. Para obter mais informações, veja [Verificar o carregamento de dados no Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. No separador **Endereço para envio**, forneça o seu nome próprio e apelido, o nome e o endereço postal da empresa e um número de telefone válido. Clique em **Validar endereço**. O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito.

   Se selecionar o envio auto-gerido, receberá uma notificação de e-mail após a encomenda ser feita com sucesso. Para obter mais informações sobre o transporte auto-gerido, consulte [use o envio auto-gerido](data-box-portal-customer-managed-shipping.md).

8. Clique **em seguida** assim que os detalhes do envio tiverem sido validados com sucesso.

9. Em **Detalhes de notificação**, especifique os endereços de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados.

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

10. Reveja o **Resumo** das informações relacionadas com os termos de encomenda, contacto, notificação e privacidade. Selecione a caixa correspondente ao contrato de termos de privacidade.

11. Clique em **Encomendar**. A encomenda demora alguns minutos a ser criada.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Faça os seguintes passos utilizando o Azure CLI para encomendar um dispositivo:

1. Anote as suas definições para a sua encomenda de Caixa de Dados. Estas definições incluem as suas informações pessoais/empresariais, nome de subscrição, informações do dispositivo e informações de envio. Terá de utilizar estas definições como parâmetros ao executar o comando CLI para criar a ordem Caixa de Dados. A tabela a seguir mostra as definições de parâmetros utilizadas `az databox job create` para:

   | Definição (parâmetro) | Description |  Valor da amostra |
   |---|---|---|
   |resource-group| Utilize um grupo de recursos existente ou crie um novo. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |name| O nome da ordem que está a criar. | "mydataboxorder"|
   |nome de contacto| O nome associado ao endereço de envio. | "Gus Polónia"|
   |telefone| O número de telefone da pessoa ou empresa que receberá a encomenda.| "14255551234"
   |localização| A região de Azure mais próxima para si que irá enviar o seu dispositivo.| "Eua Oeste"|
   |sku| O dispositivo específico da Caixa de Dados que está a encomendar. Os valores válidos são: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
   |lista de e-mails| Os endereços de e-mail associados à encomenda.| "gusp@contoso.com" |
   |endereço de rua1| O endereço da rua para onde a encomenda será enviada. | "15700 NE 39th St" |
   |endereço de rua2| A informação de endereço secundário, como o número de apartamento ou o número de edifício. | "Bld 123" |
   |city| A cidade para onde o dispositivo será enviado. | "Redmond" |
   |estado ou província| O estado onde o dispositivo será enviado.| "WA" |
   |país| O país em que o dispositivo será enviado. | "Estados Unidos" |
   |código postal| O código postal ou código postal associado ao endereço de envio.| "98052"|
   |nome da empresa| O nome da sua empresa para quem trabalha.| "Contoso, LTD" |
   |conta de armazenamento| A conta de Armazenamento Azure de onde pretende importar dados.| "mystorageaccount"|
   |depurar| Incluir depurar informações para verbose logging  | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta<string>|
   |verbose| Inclua a registo verboso. | -verbose |

2. No seu pedido de comando de escolha ou terminal, utilize o [trabalho az databox criado](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) para criar a sua encomenda Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Aqui está um exemplo de uso do comando:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Todos os comandos Azure CLI usarão json como formato de saída por padrão, a menos que o altere. Pode alterar o formato de saída utilizando o parâmetro `--output <output-format>` global. Mudar o formato para "tabela" melhorará a legibilidade da saída.

   Aqui está o mesmo comando que acabamos de executar com um pequeno ajuste para mudar a formatação:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Aqui está a saída de executar o comando:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>Controlar a encomenda

# <a name="portal"></a>[Portal](#tab/portal)

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Vá à sua encomenda de Caixa de Dados e, em seguida, vá ao **Overview** para ver o estado. O portal mostra a encomenda no estado **Encomendado**.

Se o dispositivo não estiver disponível, receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identifica-o para envio e prepara o envio. Durante a preparação do dispositivo, ocorrem as ações seguintes:

* São criadas Partilhas SMB para cada conta de armazenamento associada ao dispositivo.
* Para cada partilha, são geradas credenciais de acesso, como nome de utilizador e palavra-passe.
* A palavra-passe do dispositivo, que ajuda a desbloqueá-lo, também é gerada.
* O Data Box está bloqueado para impedir o acesso não autorizado ao dispositivo a qualquer momento.

Quando a preparação do dispositivo estiver concluída, o portal mostra a encomenda no estado **Processado**.

![Encomenda do Data Box processada](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft prepara e expede os seus dispositivos através de uma transportadora regional. Receberá um número de encomenda assim que o dispositivo for enviado. O portal mostra a encomenda no estado **Expedido**.

![Encomenda do Data Box expedida](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Acompanhe uma única encomenda

Para obter informações de rastreio sobre uma única encomenda existente da Caixa de Dados Azure, executar [az databox show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show). O comando apresenta informações sobre a ordem, como, mas não se limitando a: nome, grupo de recursos, informações de rastreio, ID de subscrição, informações de contacto, tipo de envio e sku do dispositivo.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   A tabela a seguir mostra as informações do parâmetro `az databox job show` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos associado à ordem. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |nome [Obrigatório]| O nome da ordem a ser exibida. | "mydataboxorder"|
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta<string>|
   |verbose| Inclua a registo verboso. | -verbose |

   Aqui está um exemplo do comando com formato de saída definido para "tabela":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Aqui está a saída de executar o comando:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> A ordem de lista pode ser suportada ao nível da subscrição e isso torna o grupo de recursos um parâmetro opcional (em vez de um parâmetro necessário).

### <a name="list-all-orders"></a>Listar todas as encomendas

Se encomendou vários dispositivos, pode executar [a lista de trabalho da caixa de dados az](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) para visualizar todas as suas encomendas da Caixa de Dados Azure. O comando lista todas as ordens que pertencem a um grupo de recursos específicos. Também apresentado na saída: nome da encomenda, estado do envio, região Azure, tipo de entrega, estado da encomenda. As encomendas canceladas também estão incluídas na lista.
O comando também apresenta selos temporais de cada ordem.

```azurecli
az databox job list --resource-group <resource-group>
```

A tabela a seguir mostra as informações do parâmetro `az databox job list` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos que contém as encomendas. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta<string>|
   |verbose| Inclua a registo verboso. | -verbose |

   Aqui está um exemplo do comando com formato de saída definido para "tabela":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>Cancelar a encomenda

# <a name="portal"></a>[Portal](#tab/portal)

Para cancelar esta encomenda, no portal Azure, vá ao **Overview** e **selecione Cancelar** a partir da barra de comando.

Depois de efetuar uma encomenda, pode cancelá-la a qualquer momento antes de o estado desta ser marcado como processado.

Para eliminar uma encomenda cancelada, vá ao **Overview** e selecione **Delete** da barra de comando.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Cancelar uma encomenda

Para cancelar uma encomenda da Caixa de Dados Azure, executar [o trabalho da caixa de dados az cancelar](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel). É-lhe exigido que especifique a sua razão para cancelar a encomenda.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   A tabela a seguir mostra as informações do parâmetro `az databox job cancel` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos associado à ordem a eliminar. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |nome [Obrigatório]| O nome da ordem a ser apagada. | "mydataboxorder"|
   |razão [Necessário]| O motivo para cancelar a encomenda. | "Inseri informações erradas e precisei de cancelar a encomenda." |
   |sim| Não pedir confirmação. | --sim (-y)| --sim-y |
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta<string>|
   |verbose| Inclua a registo verboso. | -verbose |

   Aqui está um exemplo do comando com saída:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Apagar uma encomenda

Se tiver cancelado uma encomenda da Caixa de Dados Azure, pode executar [o trabalho de databox az](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) para eliminar a encomenda.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   A tabela a seguir mostra as informações do parâmetro `az databox job delete` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos associado à ordem a eliminar. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |nome [Obrigatório]| O nome da ordem a ser apagada. | "mydataboxorder"|
   |subscrição| O nome ou ID (GUID) da sua assinatura Azure. | "xxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxx" |
   |sim| Não pedir confirmação. | --sim (-y)| --sim-y |
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta<string>|
   |verbose| Inclua a registo verboso. | -verbose |

Aqui está um exemplo do comando com saída:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre artigos da Azure Data Box tais como:

> [!div class="checklist"]
>
> * Pré-requisitos para implementar o Data Box
> * Encomendar o Data Box
> * Controlar a encomenda
> * Cancelar a encomenda

Avance para o tutorial seguinte para saber como configurar o Data Box.

> [!div class="nextstepaction"]
> [Configurar o Azure Data Box](./data-box-deploy-set-up.md)
