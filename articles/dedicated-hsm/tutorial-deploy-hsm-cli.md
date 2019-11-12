---
title: Tutorial implantar em uma rede virtual existente usando o CLI do Azure-HSM dedicado do Azure | Microsoft Docs
description: Tutorial mostrando como implantar um HSM dedicado usando a CLI em uma rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 51e3bddef75bcf41b8c7a4d9693b622429130217
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930476"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Tutorial: Implantando HSMs em uma rede virtual existente usando a CLI

O HSM dedicado do Azure fornece um dispositivo físico para uso único do cliente, com controle administrativo completo e responsabilidade de gerenciamento total. O uso de dispositivos físicos cria a necessidade de o Microsoft controlar a alocação de dispositivos para garantir que a capacidade seja gerenciada com eficiência. Como resultado, em uma assinatura do Azure, o serviço HSM dedicado normalmente não estará visível para o provisionamento de recursos. Qualquer cliente do Azure que precise de acesso ao serviço HSM dedicado deve primeiro entrar em contato com seu executivo de conta Microsoft para solicitar o registro do serviço HSM dedicado. Somente quando esse processo for concluído com êxito, o provisionamento será possível. 

Este tutorial mostra um processo de provisionamento típico em que:

- Um cliente já tem uma rede virtual
- Eles têm uma máquina virtual
- Eles precisam adicionar recursos de HSM a esse ambiente existente.

Uma arquitetura de implantação típica, de alta disponibilidade e de várias regiões pode ter a seguinte aparência:

![implantação em várias regiões](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Este tutorial se concentra em um par de HSMs e o gateway de ExpressRoute necessário (consulte a sub-rede 1 acima) que está sendo integrado a uma rede virtual existente (consulte VNET 1 acima).  Todos os outros recursos são recursos padrão do Azure. O mesmo processo de integração pode ser usado para HSMs na sub-rede 4 na VNET 3 acima.

## <a name="prerequisites"></a>Pré-requisitos

O HSM dedicado do Azure não está disponível no momento no portal do Azure. Toda a interação com o serviço será por linha de comando ou usando o PowerShell. Este tutorial usará a interface de linha de comando (CLI) no Azure Cloud Shell. Se você for novo no CLI do Azure, siga as instruções de introdução aqui: [CLI do Azure a 2,0 Introdução](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Suposições

- Você concluiu o processo de registro do HSM dedicado do Azure
- Você foi aprovado para uso do serviço. Caso contrário, entre em contato com seu representante de conta Microsoft para obter detalhes.
- Você criou um grupo de recursos para esses recursos e os novos implantados neste tutorial ingressarão nesse grupo.
- Você já criou a rede virtual, a sub-rede e as máquinas virtuais necessárias, de acordo com o diagrama acima, e agora deseja integrar 2 HSMs a essa implantação.

Todas as instruções abaixo pressupõem que você já navegou até a portal do Azure e abriu a Cloud Shell (selecione "\>\_" na parte superior direita do Portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionando um HSM dedicado

O provisionamento de HSMs e sua integração a uma rede virtual existente por meio do gateway de ExpressRoute será validado usando SSH. Essa validação ajuda a garantir a acessibilidade e a disponibilidade básica do dispositivo HSM para qualquer outra atividade de configuração. Os comandos a seguir usarão um modelo de Azure Resource Manager para criar os recursos de HSM e os recursos de rede associados.

### <a name="validating-feature-registration"></a>Validando o registro de recursos

Conforme mencionado acima, qualquer atividade de provisionamento requer que o serviço HSM dedicado esteja registrado para sua assinatura. Para validar isso, execute os comandos a seguir no portal do Azure cloud Shell.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

O comando a seguir verifica os recursos de rede necessários para o serviço HSM dedicado.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Os dois comandos devem retornar um status "registrado" (como mostrado abaixo). Se os comandos não retornarem "registrados", você precisará se registrar para esse serviço, entre em contato com seu representante de conta Microsoft.

![status da assinatura](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criando recursos HSM

Um HSM é provisionado na rede virtual de um cliente para que uma rede virtual e uma sub-rede sejam necessárias. Uma dependência para o HSM habilitar a comunicação entre a rede virtual e o dispositivo físico é um gateway de ExpressRoute e, finalmente, uma máquina virtual é necessária para acessar o dispositivo HSM usando o software cliente do Gemalto. Esses recursos foram coletados em um arquivo de modelo, com o arquivo de parâmetro correspondente, para facilitar o uso. Os arquivos estão disponíveis entrando em contato com a Microsoft diretamente como HSMrequest@Microsoft.com.

Depois de ter os arquivos, você deve editar o arquivo de parâmetro para inserir seus nomes preferenciais para os recursos. Editar linhas com "valor": "".

- `namingInfix` prefixo para os nomes de recursos HSM
- `ExistingVirtualNetworkName` nome da rede virtual usada para os HSMs
- `DedicatedHsmResourceName1` nome do recurso HSM no carimbo de Data Center 1
- `DedicatedHsmResourceName2` nome do recurso HSM no carimbo de Data Center 2
- intervalo de endereços IP de sub-rede `hsmSubnetRange` para HSMs
- intervalo de endereços IP de sub-rede `ERSubnetRange` para o gateway de VNET

Um exemplo dessas alterações é o seguinte:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

O arquivo de modelo Azure Resource Manager associado criará 6 recursos com essas informações:

- Uma sub-rede para os HSMs na VNET especificada
- Uma sub-rede para o gateway de rede virtual
- Um gateway de rede virtual que conecta a VNET aos dispositivos HSM
- Um endereço IP público para o gateway
- Um HSM no Stamp 1
- Um HSM no Stamp 2

Depois que os valores de parâmetro são definidos, os arquivos precisam ser carregados para portal do Azure compartilhamento de arquivos do Cloud Shell para uso. Na portal do Azure, clique no símbolo do Shell de nuvem "\>\_" na parte superior direita e isso fará com que a parte inferior da tela seja um ambiente de comando. As opções para isso são BASH e PowerShell, e você deve selecionar BASH se ainda não estiver definido.

O Shell de comando tem uma opção de upload/download na barra de ferramentas e você deve selecionar para carregar os arquivos de modelo e parâmetro em seu compartilhamento de arquivos:

![compartilhamento de arquivos](media/tutorial-deploy-hsm-cli/file-share.png)

Depois que os arquivos forem carregados, você estará pronto para criar recursos. Antes de criar novos recursos do HSM, há alguns recursos de pré-requisito que você deve garantir que estejam em vigor. Você deve ter uma rede virtual com intervalos de sub-rede para computação, HSMs e gateway. Os comandos a seguir servem como um exemplo do que criaria uma rede virtual desse tipo.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
--vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>A configuração mais importante a ser observada para a rede virtual é que a sub-rede para o dispositivo HSM deve ter delegações definidas como "Microsoft. HardwareSecurityModules/dedicatedHSMs".  O provisionamento do HSM não funcionará sem essa opção ser definida.

Depois que todos os pré-requisitos estiverem em vigor, execute o seguinte comando para usar o modelo de Azure Resource Manager, garantindo que você tenha atualizado valores com seus nomes exclusivos (pelo menos o nome do grupo de recursos):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Essa implantação deve levar aproximadamente de 25 a 30 minutos para ser concluída com a maior parte desse tempo sendo os dispositivos HSM

![Status de provisionamento](media/tutorial-deploy-hsm-cli/progress-status.png)

Quando a implantação for concluída com êxito "provisioningState": "êxito" será exibido. Você pode se conectar à máquina virtual existente e usar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificando a implantação

Para verificar se os dispositivos foram provisionados e ver atributos de dispositivo, execute o seguinte conjunto de comandos. Verifique se o grupo de recursos está definido corretamente e se o nome do recurso é exatamente o que você tem no arquivo de parâmetro.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![saída de provisionamento](media/tutorial-deploy-hsm-cli/progress-status2.png)

Agora, você também poderá ver os recursos usando o Gerenciador de [recursos do Azure](https://resources.azure.com/).   Uma vez no Explorer, expanda "assinaturas" à esquerda, expanda sua assinatura específica para HSM dedicado, expanda "grupos de recursos", expanda o grupo de recursos que você usou e, por fim, selecione o item "recursos".

## <a name="testing-the-deployment"></a>Testando a implantação

Testar a implantação é um caso de conexão a uma máquina virtual que pode acessar o HSM (s) e, em seguida, conectar-se diretamente ao dispositivo HSM. Essas ações confirmarão que o HSM está acessível.
A ferramenta SSH é usada para se conectar à máquina virtual. O comando será semelhante ao seguinte, mas com o nome do administrador e o nome DNS que você especificou no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

O endereço IP da VM também pode ser usado no lugar do nome DNS no comando acima. Se o comando for bem-sucedido, ele solicitará uma senha e você deverá inseri-la. Depois de conectado à máquina virtual, você pode entrar no HSM usando o endereço IP privado encontrado no portal para o recurso de interface de rede associado ao HSM.

![lista de componentes](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Observe a caixa de seleção "Mostrar tipos ocultos", que quando selecionado exibirá recursos de HSM.

Na captura de tela acima, clicar em "HSM1_HSMnic" ou "HSM2_HSMnic" mostraria o endereço IP privado apropriado. Caso contrário, o comando `az resource show` usado acima é uma maneira de identificar o endereço IP correto. 

Quando você tiver o endereço IP correto, execute o seguinte comando substituindo esse endereço:

`ssh tenantadmin@10.0.2.4`

Se for bem-sucedido, você será solicitado a fornecer uma senha. A senha padrão é PASSWORD e o HSM pedirá primeiro que você altere sua senha, portanto, defina uma senha forte e use qualquer mecanismo que sua organização prefira para armazenar a senha e evitar a perda.

>[!IMPORTANT]
>Se você perder essa senha, o HSM precisará ser redefinido e isso significa perder suas chaves.

Quando você estiver conectado ao HSM usando SSH, execute o comando a seguir para garantir que o HSM esteja operacional.

`hsm show`

A saída deve ter a aparência mostrada na imagem abaixo:

![lista de componentes](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Neste ponto, você alocou todos os recursos para uma implantação do HSM altamente disponível, duas implantações e um estado operacional Qualquer configuração ou teste adicional envolve mais trabalho com o próprio dispositivo HSM. Para isso, você deve seguir as instruções no guia de administração do HSM 7 de rede Gemalto Luna, capítulo 7 para inicializar o HSM e criar partições. Toda a documentação e o software estão disponíveis diretamente do Gemalto para download depois que você estiver registrado no portal de suporte ao cliente do Gemalto e tiver uma ID do cliente. Baixe o software cliente versão 7,2 para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Excluir ou limpar recursos

Se você tiver concluído apenas o dispositivo HSM, ele poderá ser excluído como um recurso e retornado para o pool gratuito. A preocupação óbvia ao fazer isso é qualquer dado de cliente confidencial que está no dispositivo. Para remover dados confidenciais do cliente, o dispositivo deve ser redefinido de fábrica usando o cliente Gemalto. Consulte o guia de administradores do Gemalto para o dispositivo de rede SafeNet Luna 7 e considere os seguintes comandos na ordem.

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `my file clear -f`
4. `my public-key clear -f`
5. `syslog rotate`


> [!NOTE]
> Se você tiver problemas com qualquer configuração de dispositivo Gemalto, entre em contato com o [suporte ao cliente do Gemalto](https://safenet.gemalto.com/technical-support/).


Se tiver concluído os recursos nesse grupo de recursos, você poderá removê-los com o seguinte comando:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Passos seguintes

Depois de concluir as etapas no tutorial, os recursos de HSM dedicados são provisionados e você tem uma rede virtual com HSMs necessários e outros componentes de rede para permitir a comunicação com o HSM.  Agora você está em uma posição para complementar essa implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferida. Para obter mais informações sobre como ajudar a planejar sua implantação, consulte os documentos de conceitos.
Um design com dois HSMs em uma região primária que aborda a disponibilidade no nível do rack e dois HSMs em uma região secundária que aborda a disponibilidade regional é recomendado. O arquivo de modelo usado neste tutorial pode ser facilmente usado como base para uma implantação de dois HSM, mas precisa ter seus parâmetros modificados para atender às suas necessidades.

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade](supportability.md)
* [Monitorização](monitoring.md)
